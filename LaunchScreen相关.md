## 1.Launch Screen Storyboard 文件修改，比如加一个label显示版本和build编号
```
#   ON/OFF Script Toggle (script ON with #, script OFF without #)
#exit 0
#   Increment Build Number Bool (Increment ON with true, increment OFF with false)
shouldIncrement=true
#   App vesion / Build version constants
versionNumber=$(/usr/libexec/PlistBuddy -c "Print CFBundleShortVersionString" "$INFOPLIST_FILE")
buildNumber=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "$INFOPLIST_FILE")
#   Increment build number
if [ "$shouldIncrement" = true ]; then
    buildNumber=$(($buildNumber + 1))
    /usr/libexec/PlistBuddy -c "Set :CFBundleVersion $buildNumber" "$INFOPLIST_FILE"
fi
#   Output version & build numbers into a label on Launch Screen.storyboard
sed -i "" -e "/userLabel=\"APP_VERSION\"/s/text=\"[^\"]*\"/text=\"Version: $versionNumber Build: $buildNumber\"/" "$PROJECT_DIR/$PROJECT_NAME/Base.lproj/Launch Screen.storyboard"
```
