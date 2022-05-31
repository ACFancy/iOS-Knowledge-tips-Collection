### 更新Launch.storyboard的版本号的脚本
```sh
# Output version & build number to the APP_VERSION label on LaunchScreen.storyboard
versionNumber="$MARKETING_VERSION"

sed -i "" -e "/userLabel=\"APP_VERSION\"/s/text=\"[^\"]*\"/text=\"V$versionNumber\"/" "$PROJECT_DIR/$PROJECT_NAME/Resources/Launch Screen.storyboard"
```
