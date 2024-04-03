### 快速对文件进行读取并进行md5值
```swift
import CryptoKit
static func fileMD5HashCreateWithPath(_ filePath: URL?) -> String? {
      guard let filePath = filePath, filePath.fileExists == true else {
          return nil
      }
      let cfFilePath = filePath as CFURL
      guard let readStream = CFReadStreamCreateWithFile(kCFAllocatorDefault, cfFilePath),
            CFReadStreamOpen(readStream) else {
          return nil
      }
      let chunkSizeForReadingData: Int = 16
      // Initialize the hash object
      var hashObject = Insecure.MD5.init()

      // Feed the data to the hash object
      var hasMoreData = true
      while autoreleasepool(invoking: {
          var buffer = [UInt8](repeating: 0, count: chunkSizeForReadingData)
          let bufferLength = CFIndex(chunkSizeForReadingData)
          // The number of bytes read; 0 if the stream has reached its end;
          // or -1 if either the stream is not open or an error occurs.
          let readBytesCount = CFReadStreamRead(readStream,
                                                &buffer,
                                                bufferLength)
          if readBytesCount > 0 {
              buffer.withUnsafeBytes { bufferPointer in
                  if let baseAddress = bufferPointer.baseAddress {
                      let rawBufferPointer = UnsafeRawBufferPointer(start: baseAddress, count: Int(readBytesCount))
                      hashObject.update(bufferPointer: rawBufferPointer)
                  }
              }
              return true
          }
          if readBytesCount == 0 {
              hasMoreData = false
              return false
          }
          return false
      }) {}
      guard !hasMoreData else {
          CFReadStreamClose(readStream)
          return nil
      }
      // Compute the hash digest to string result
      let digitest = hashObject.finalize()
      let result = digitest.withUnsafeBytes { (point) -> String in
          point.map { (value) -> String in
              return String(format: "%02x", value)
          }.joined()
      }
      CFReadStreamClose(readStream)
      return result
  }
```
