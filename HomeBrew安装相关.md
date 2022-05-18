### 错误
 - Running Homebrew as root is extremely dangerous and no longer supported
   - 解决方案
      ```sh
      sudo chown -R `whoami` /usr/local/Homebrew/
      sudo chown -R $(whoami) $(brew --prefix)/*
      sudo mkdir /usr/local/Frameworks
      sudo chown -R `whoami` /usr/local/Frameworks/
      ```
