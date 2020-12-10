### Mac Big sur Gem Ignoring because its extensions are not built problems
```ruby
gem pristine --all
<tip: permission issues>
 check:
 which ruby
 brew link --overwrite ruby && source ~/.bash_profile
 or 
 brew link --overwrite ruby && source ~/.zshrc(OnMy ZSH commond tool)
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
