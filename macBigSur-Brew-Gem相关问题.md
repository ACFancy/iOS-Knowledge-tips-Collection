### Mac Big sur Gem Ignoring because its extensions are not built problems

#### tip: permission issues
  -check
  ```ruby
  which ruby
  ```
  - then
  ```ruby
  brew link --overwrite ruby && source ~/.bash_profile
  ```
  - or (OnMy ZSH commond tool)
   ```ruby
  brew link --overwrite ruby && source ~/.zshrc
  ```
  - then
  ```ruby
  gem pristine --all
  ```
 
 

### Mac Big sur Brew update Problem
```ruby
<tip: tip git -C "/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core" fetch --unshallow>
 cd /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
 git fetch
 git fetch --unshallow
 <tip: tip git -C "/usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask" fetch --unshallow>
 cd /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
 git fetch
 git fetch --unshallow
 brew update
```
