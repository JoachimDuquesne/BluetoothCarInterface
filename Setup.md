# Setup
## Configure sd card
- Burn Raspbian image to sd card
- add ssh file in boot partition
- append "dtoverlay=pwm-2chan,pin=18,func=2,pin2=19,func2=2" to /boot/config.txt to remap pwm0-1 output to accessible gpio pins
- add wpa_passwd SSID PASS output to /etc/wpa_supplicant/wpa_supplicant.conf
- copy /etc/wpa_supplicant/ifupdown.sh to /etc/ifplugd/action.d/ifupdown

## Start RPi
- Put sd card in raspberry pi and startup
- sudo aptitude update && sudo apptitude full-upgrade && sudo aptitude install bluealsa
- replace /lib/systemd/system/bluealsa.service with :
      
      [Unit]
      Description=BluezALSA proxy
      After=bluetooth.service
      Requires=bluetooth.service

      [Service]
      Type=exec
      ExecStart=/usr/bin/bluealsa --profile=a2dp-sink
      Restart=always
      RestartSec=5

      [Install]
      WantedBy=default.target
      
- Create /lib/systemd/system/bluealsa-aplay.service with 
      
      [Unit]
      Description=BluezALSA-aplay
      After=bluetooth.service bluealsa.service
      Requires=bluetooth.service bluealsa.service

      [Service]
      Type=exec
      ExecStart=/usr/bin/bluealsa-aplay -v 00:00:00:00:00:00
      Restart=always
      RestartSec=11

      [Install]
      RequiredBy=default.target

- sudo systemctl daemon-reload
- sudo systemctl enable bluealsa.service
- sudo systemctl enable bluealsa-aplay.service
- sudo bluetoothctl
  - "show" # Check that "Audio Sink" is listed
  - "discoverable on" # Now search new device with your phone
  - "trust XX:XX:XX:XX:XX:XX" # Where XX is the MAC of your phone. Do again for every phone you want to connect
- Try to play some music on your phone through bluetooth
- If all ok : Reboot and test again without connecting to the RPi
- If still ok : Append "dtoverlay=pi3-disable-wifi" to /boot/config.txt to disable wifi 
- Reboot again, you are done !
