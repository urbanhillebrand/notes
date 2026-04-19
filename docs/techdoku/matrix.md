# Matrix

## Security key reset

https://github.com/vector-im/element-web/issues/16118#issuecomment-766757078

* Consider exporting room keys to a file as a backup in an existing session via top left menu -> Settings -> Security -> Export E2E room keys (but you’ve said you don’t have encrypted rooms at the moment)
* Reset secure backup first (this should now succeed without confusingly asking for previous keys)
* Reset cross-signing keys (this may prompt for security key / phrase, it’s expecting the new one you just made)
* On other sessions, you should be able to either verify the new session and receive new keys or go to Settings and "setup" Secure Backup (green button instead of the red "reset"), which maybe prompt for the new security key / phrase as part of downloading your keys
