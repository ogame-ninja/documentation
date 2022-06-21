## How to setup systemd?

TEMPLATE FILE:

```
/lib/systemd/system/
[Unit]
Description= NinjaBot Service # change if you want
After=multi-user.target

[Service]
Type=idle

User=USER #change to your user
ExecStart=/path/to/ninja 2>&1 & # -port 8080 -host 0.0.0.0 --no-browser "2>&1 &" <- needed to run in backround

Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

- change user to your respecting userprofile
- change ExecStart to your ninja path (e.g. /home/user/ninja.linux.amd64) add startparameters and keep backgrounding
- save the file in /lib/systemd/system/nameofservice.service (e.g. ninjabot.service)
- reload systemd: systemctl daemon-reload
- enable autostart (after reboot) for service: systemctl enable [nameofservice.service]
- start the service: systemctl start [nameofservice.service]
- (check if running: systemctl status [nameofservice.service] , restart machine for testing, ninja software should start automatically!)
- to see the PID of service: ps -u [USER] -U, systemctl status [nameofservice.service]
- to stop the service from restarting the ninja file: systemctl stop [nameofservice.service] & systemctl disable [nameofservice.service]

YOU HAVE TO CHANGE THE PATH AFTER MANUALLY UPDATING THE NINJA SOFTWARE!
I  HIGHLY RECOMMEND in software "Auto-Update"!