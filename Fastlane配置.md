### build_ios_ipa
- use_system_scm property to the scan and gym commands in previous version!
But it seems like it's not working correctly since the flag is not added to the xcodebuild command. There should be -scmProvider system flag added there to force the xcode to use the system git configuration.
