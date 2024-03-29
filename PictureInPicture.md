### pictureInPicture 实践
- ![image](https://user-images.githubusercontent.com/10044815/237030358-05b01a12-0371-492b-b54a-cf691da2cc05.png)
- https://developer.apple.com/documentation/avkit/adopting_picture_in_picture_in_a_custom_player

```swift

import Foundation
import AVKit

class PicktureInPicktureManager: NSObject {
    // MARK: - Properties
    private lazy var playerLayer: AVPlayerLayer = {
        let this = AVPlayerLayer()
        this.frame = .init(x: 0, y: 0, width: 1, height: 1)
        this.player = player
        return this
    }()
    private var autoHomeScreen: Bool = false
    private var pipVC: AVPictureInPictureController?
    private lazy var player: AVPlayer = {
        let player = AVPlayer(playerItem: nil)
        player.allowsExternalPlayback = true
        player.actionAtItemEnd = .none
        return player
    }()
    private var timeObserverToken: Any?

    static let shared: PicktureInPicktureManager = .init()

    func startPip(forView view: UIView, url: URL?, autoHomeScreen: Bool = true) {
        guard let url = url,
              AVPictureInPictureController.isPictureInPictureSupported() else { return }
        guard pipVC == nil else {
            backHomeScreenIfNeeded()
            return
        }
        stopPlay()
        player.replaceCurrentItem(with: .init(url: url))
        playerLayer.isHidden = false
        playerLayer.frame = CGRect(x: view.frame.width - 1, y: 0, width: 1, height: 1)
        view.layer.addSublayer(playerLayer)
        guard let pinpVC = AVPictureInPictureController(playerLayer: playerLayer) else {
            playerLayer.removeFromSuperlayer()
            stopPlay()
            return
        }
        do {
            try AVAudioSession.sharedInstance().setCategory(.playback, mode: .moviePlayback, options: .mixWithOthers)
            try AVAudioSession.sharedInstance().setActive(true)
        } catch {
            debugPrint("\(error.localizedDescription)")
        }
        self.pipVC = pinpVC
        if #available(iOS 14.2, *) {
            pinpVC.canStartPictureInPictureAutomaticallyFromInline = true
        }
        pinpVC.delegate = self
        self.autoHomeScreen = autoHomeScreen
        addObserve()
        player.play()
    }

    func stopPip() {
        guard let pipVC = pipVC, pipVC.isPictureInPictureActive else {
            self.pipVC = nil
            return
        }
        pipVC.stopPictureInPicture()
        self.pipVC = nil
    }

    // MARK: - Private Functions
    private func startPip() {
        guard let pipVC = pipVC, !pipVC.isPictureInPictureActive else { return }
        pipVC.startPictureInPicture()
    }

    private func stopPlay() {
        if player.currentItem != nil {
            player.replaceCurrentItem(with: nil)
        }
    }

    private func backHomeScreenIfNeeded() {
        if autoHomeScreen {
            UIApplication.back2HomeScreen()
        }
    }

    private func addObserve() {
        addTimeObserver()
        guard let currentItem = player.currentItem else { return }
        NotificationCenter.default.addObserver(self, selector: #selector(playerItemDidEnd(_:)), name: .AVPlayerItemDidPlayToEndTime, object: currentItem)
    }

    private func removeObserve() {
        // swiftlint:disable:next notification_center_detachment
        NotificationCenter.default.removeObserver(self)
        removeTimeObserver()
    }

    private func addTimeObserver() {
        removeTimeObserver()
        let interval = CMTime(seconds: 0.01,
                              preferredTimescale: CMTimeScale(NSEC_PER_SEC))
        var isTriggered: Bool = false
        timeObserverToken = player.addPeriodicTimeObserver(forInterval: interval, queue: .main) { [weak self] _ in
            guard let self = self else { return }
            guard !isTriggered else { return }
            isTriggered = true
            self.startPip()
            self.removeTimeObserver()
        }
    }

    private func removeTimeObserver() {
        guard let timeObserverToken = timeObserverToken else { return }
        self.timeObserverToken = nil
        player.removeTimeObserver(timeObserverToken)
    }

    @objc private func playerItemDidEnd(_ notify: Notification) {
        guard let playerItem = notify.object as? AVPlayerItem else { return }
        playerItem.seek(to: .zero, completionHandler: nil)
    }
}

// MARK: - AVPictureInPictureControllerDelegate
extension PicktureInPicktureManager: AVPictureInPictureControllerDelegate {
    func pictureInPictureControllerDidStartPictureInPicture(_ pictureInPictureController: AVPictureInPictureController) {
        backHomeScreenIfNeeded()
    }

    func pictureInPictureController(_ pictureInPictureController: AVPictureInPictureController, failedToStartPictureInPictureWithError error: Error) {
        stopPip()
        playerLayer.isHidden = true
        playerLayer.removeFromSuperlayer()
        stopPlay()
        removeObserve()
    }

    func pictureInPictureControllerDidStopPictureInPicture(_ pictureInPictureController: AVPictureInPictureController) {
        playerLayer.isHidden = true
        playerLayer.removeFromSuperlayer()
        pipVC = nil
        stopPlay()
        removeObserve()
    }
}

extension UIApplication {
    static func back2HomeScreen(delay: TimeInterval = 0.25) {
        let str = String(format: "%@%@%@", "s", "usp", "end")
        let target = Selector(str)
        guard shared.responds(to: target) else { return }
        DispatchQueue.main.asyncAfter(deadline: .now() + delay) {
            shared.perform(target)
        }
    }
}


```

### 其他case
```swift
import Foundation
import AVKit

class PicktureInPicktureManager: NSObject {
    // MARK: - Properties
    private lazy var playerLayer: AVPlayerLayer = {
        let this = AVPlayerLayer()
        this.frame = .init(x: 0, y: 0, width: 1, height: 1)
        this.player = player
        return this
    }()
    private var autoHomeScreen: Bool = false
    private var pipVC: AVPictureInPictureController?
    private lazy var player: AVPlayer = {
        let player = AVPlayer(playerItem: nil)
        player.allowsExternalPlayback = true
        player.actionAtItemEnd = .none
        return player
    }()
    private var timeObserverToken: Any?

    static let shared: PicktureInPicktureManager = .init()

    func startPip(forView view: UIView, url: URL?, autoHomeScreen: Bool = true) {
        guard let url = url,
              AVPictureInPictureController.isPictureInPictureSupported() else { return }
        guard pipVC == nil else {
            backHomeScreenIfNeeded()
            return
        }
        stopPlay()
        player.replaceCurrentItem(with: .init(url: url))
        playerLayer.isHidden = false
        playerLayer.frame = CGRect(x: view.frame.width - 1, y: 0, width: 1, height: 1)
        view.layer.addSublayer(playerLayer)
        guard let pinpVC = AVPictureInPictureController(playerLayer: playerLayer) else {
            playerLayer.removeFromSuperlayer()
            stopPlay()
            return
        }
        do {
            try AVAudioSession.sharedInstance().setCategory(.playback, mode: .moviePlayback, options: .mixWithOthers)
            try AVAudioSession.sharedInstance().setActive(true)
        } catch {
            debugPrint("\(error.localizedDescription)")
        }
        self.pipVC = pinpVC
        if #available(iOS 14.2, *) {
            pinpVC.canStartPictureInPictureAutomaticallyFromInline = true
        }
        pinpVC.delegate = self
        self.autoHomeScreen = autoHomeScreen
        addObserve()
        player.play()
    }

    func stopPip() {
        guard let pipVC = pipVC, pipVC.isPictureInPictureActive else {
            self.pipVC = nil
            return
        }
        pipVC.stopPictureInPicture()
    }

    // MARK: - Private Functions
    private func startPip() {
        guard let pipVC = pipVC, !pipVC.isPictureInPictureActive else { return }
        pipVC.startPictureInPicture()
    }

    private func reset() {
        pipVC = nil
        playerLayer.isHidden = true
        playerLayer.removeFromSuperlayer()
        stopPlay()
        removeObserve()
        AudioPlayManager.shared.resumeBgm(force: true)
    }

    private func stopPlay() {
        player.pause()
        if player.currentItem != nil {
            player.replaceCurrentItem(with: nil)
        }
        do {
            try AVAudioSession.sharedInstance().setCategory(.soloAmbient)
        } catch {
            debugPrint("\(error.localizedDescription)")
        }
    }

    private func backHomeScreenIfNeeded() {
        if autoHomeScreen {
            UIApplication.back2HomeScreen {
                AudioPlayManager.shared.pause(.background)
            }
        }
    }

    private func addObserve() {
        addTimeObserver()
        guard let currentItem = player.currentItem else { return }
        NotificationCenter.default.addObserver(self, selector: #selector(playerItemDidEnd(_:)), name: .AVPlayerItemDidPlayToEndTime, object: currentItem)
    }

    private func removeObserve() {
        // swiftlint:disable:next notification_center_detachment
        NotificationCenter.default.removeObserver(self)
        removeTimeObserver()
    }

    private func addTimeObserver() {
        removeTimeObserver()
        let interval = CMTime(seconds: 0.2,
                              preferredTimescale: CMTimeScale(NSEC_PER_SEC))
        var isTriggered: Bool = false
        timeObserverToken = player.addPeriodicTimeObserver(forInterval: interval, queue: .main) { [weak self] _ in
            guard let self = self else { return }
            guard !isTriggered else { return }
            isTriggered = true
            self.startPip()
            self.removeTimeObserver()
        }
    }

    private func removeTimeObserver() {
        guard let timeObserverToken = timeObserverToken else { return }
        self.timeObserverToken = nil
        player.removeTimeObserver(timeObserverToken)
    }

    @objc private func playerItemDidEnd(_ notify: Notification) {
        guard let playerItem = notify.object as? AVPlayerItem else { return }
        playerItem.seek(to: .zero, completionHandler: nil)
    }
}

// MARK: - AVPictureInPictureControllerDelegate
extension PicktureInPicktureManager: AVPictureInPictureControllerDelegate {
    func pictureInPictureControllerDidStartPictureInPicture(_ pictureInPictureController: AVPictureInPictureController) {
        backHomeScreenIfNeeded()
    }

    func pictureInPictureController(_ pictureInPictureController: AVPictureInPictureController, failedToStartPictureInPictureWithError error: Error) {
        stopPip()
        reset()
    }

    func pictureInPictureControllerDidStopPictureInPicture(_ pictureInPictureController: AVPictureInPictureController) {
        reset()
    }
}
```
