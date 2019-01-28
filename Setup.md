

- Burn Raspbian image to sd card
- add ssh file in boot partition
- add wpa_passwd SSID PASS output to /etc/wpa_supplicant/wpa_supplicant.conf
- copy /etc/wpa_supplicant/ifupdown.sh to /etc/ifplugd/action.d/ifupdown
- append "dtoverlay=pwm-2chan,pin=18,func=2,pin2=19,func2=2" to /boot/config.txt to remap pwm0-1 output to accessible gpio pins

########### IN PROGRESS ###############
- append "enable_uart=1" to /boot/config.txt to enable USB serial comm with pc
- reconfig uart pin?
- disable wifi ?? then why unable wpa_passwd?
########### IN PROGRESS ###############

- Put sd card in raspberry pi and startup
- sudo aptitude update && sudo apptitude full-upgrade && sudo aptitude install bluealsa

- replace /lib/systemd/system/bluealsa.service with :
      [Unit]
      Description=BluezALSA proxy
      After=bluetooth.service
      Requires=bluetooth.service

      [Service]
      Type=exec
      TimeoutStartSec=10
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
      TimeoutStartSec=10
      ExecStart=/usr/bin/bluealsa-aplay -v 00:00:00:00:00:00
      Restart=always
      RestartSec=11

      [Install]
      RequiredBy=default.target

sudo systemctl daemon-reload
sudo systemctl enable bluealsa.service
sudo systemctl enable bluealsa-aplay.service
