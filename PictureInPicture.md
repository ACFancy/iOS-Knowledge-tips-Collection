### picktureInPickture 实践
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
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) { [weak self] in
            self?.startPip()
        }
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
        guard let currentItem = player.currentItem else { return }
        NotificationCenter.default.addObserver(self, selector: #selector(playerItemDidEnd(_:)), name: .AVPlayerItemDidPlayToEndTime, object: currentItem)
    }

    private func removeObserve() {
        NotificationCenter.default.removeObserver(self)
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
