### 苹果文档
  - [optimizing_your_app_s_data_for_icloud_backup](https://developer.apple.com/documentation/foundation/optimizing_your_app_s_data_for_icloud_backup)
    - URLResourceValues isExcludedFromBackup
     ```swift
         func excludeItem(at url: URL) throws {
        // Create the resource values for the specified URL.
        var values = URLResourceValues()
        values.isExcludedFromBackup = true

        // Apply those values to the URL.
        var url = url
        try url.setResourceValues(values)
        }
     ```
  - [FileSystemOverview](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)
  - [about_apple_file_system](https://developer.apple.com/documentation/foundation/file_system/about_apple_file_system)
  - [synchronizing_app_preferences_with_icloud](https://developer.apple.com/documentation/foundation/icloud/synchronizing_app_preferences_with_icloud)
### app内icloud的开启
  ![image](https://user-images.githubusercontent.com/10044815/165703667-1d706637-d49a-4c93-9382-669778faee2b.png)
### api的使用key value store
 - 系统API类 NSUbiquitousKeyValueStore
 - [NSUbiquitousKeyValueStore](https://developer.apple.com/documentation/foundation/nsubiquitouskeyvaluestore)
 - [blog使用介绍](https://betterprogramming.pub/data-persistence-nsubiquitouskeyvaluestore-9cf4f97cd50c)
    - The combined key-value storage is 1 MB.
    - A single key-value pair cannot exceed 1 MB in size.
    - You can’t save more than 1024 key-value pairs.
    - A single key can’t be larger than 64 bytes using UTF-8 encoding.
  ```swift
          import UIKit

        class ViewController: UIViewController {

            @IBOutlet weak var dataLbl: UILabel!
            @IBOutlet weak var dataTextField: UITextField!

            var keyValStore = NSUbiquitousKeyValueStore()

            override func viewDidLoad() {
                super.viewDidLoad()
                // Do any additional setup after loading the view.
            }

            @IBAction func saveBtn(_ sender: Any) {

                let data = dataTextField.text

                keyValStore.set(data, forKey: "data")
                keyValStore.synchronize()

            }

            @IBAction func loadBtn(_ sender: Any) {

                let newData = keyValStore.string(forKey: "data")

                dataLbl.text = newData

            }

        }
  ```
