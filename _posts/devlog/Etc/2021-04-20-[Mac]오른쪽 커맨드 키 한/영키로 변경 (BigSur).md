## Mac 환경에서 오른쪽 Command 키를 한/영키로 매핑하는 방법 (BigSur)

BigSur에서는 Karabiner가 작동하고 있지 않아 애플에서 자체적으로 지원하고 있는 hidutil을 이용하여 세팅한다.

1. `시스템 환경설정  → 키보드  → 단축키  → 입력 소스  → 다음 입력소스 선택` 을 **오른쪽 커맨드 키로 변경**한다.(F18)

2. 해당 코드를 터미널에 복사하여 붙여넣는다.

   ```shell
   mkdir -p /Users/Shared/bin
   printf '%s\n' '#!/bin/sh' \
       'hidutil property --set '"'"'{"UserKeyMapping":[{"HIDKeyboardModifierMappingSrc":0x7000000e7,"HIDKeyboardModifierMappingDst":0x70000006d}]}'"'" \
       >/Users/Shared/bin/userkeymapping
   chmod 755 /Users/Shared/bin/userkeymapping
   sudo cat<<: >/Users/Shared/bin/userkeymapping.plist
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "
   http://www.apple.com/DTDs/PropertyList-1.0.dtd
   ">
   <plist version="1.0">
   <dict>
       <key>Label</key>
       <string>userkeymapping</string>
       <key>ProgramArguments</key>
       <array>
           <string>/Users/Shared/bin/userkeymapping</string>
       </array>
       <key>RunAtLoad</key>
       <true/>
   </dict>
   </plist>
   :
   sudo mv /Users/Shared/bin/userkeymapping.plist /Library/LaunchAgents/userkeymapping.plist
   sudo chown root /Library/LaunchAgents/userkeymapping.plist
   sudo launchctl load /Library/LaunchAgents/userkeymapping.plist
   ```

### 설명

`0x7000000e7`
   -> 오른쪽 CMD
`0x70000006d`
   -> F18
`[{"HIDKeyboardModifierMappingSrc":0x7000000e7,"HIDKeyboardModifierMappingDst":0x70000006d}]}'"'" \`
   -> 오른쪽 CMD를 F18로 매핑한다.

위의 커맨드를 복붙해주시면 `/Users/Shared/bin`폴더에 `userkeymapping`이라는 파일이 생성된다.  
그리고 부팅할 때마다 이 파일이 실행되게 해주는 plist파일이 `/Library/LaunchAgents/userkeymapping.plist``에 생성된다.  
재부팅을 하게 되면 오른쪽 커맨드는 F18로 매핑이 된다.  

### 해당 커맨드 재 복구시

1. 해당 코드를 터미널에 입력한다.

```shell
sudo launchctl remove userkeymapping
```

2. `Library/LaunchAgents/userkeymapping.plist` 파일과 `/Users/Shared/bin` 폴더를 삭제하시고 재부팅한다.  
이렇게 하는 이유는 `hidutil`은 재부팅하게 되면 설정값이 사라지기 때문에 부팅시 계속 저 명령어를 실행시켜줘야하기 때문이다.

---
### 출처
[BIg Sur 업데이트 후 몇몇 문제점 해결법 입니당](https://www.clien.net/service/board/cm_mac/15105447)

[Remapping Keys in macOS 10.12 Sierra](https://developer.apple.com/library/archive/technotes/tn2450/_index.html)
