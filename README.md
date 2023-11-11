# Description
The example below is just to show how to create a firstboot or onshot systemd unit file and the accompanying script it will run when the host is first booted.  The code below is inserted into the kickstart process %post.

~~~bash
%post

# creat the firstboot-script that the runone systemd service will execute
cat > /var/tmp/firstboot-script.sh <<EOF
echo "This is just example code to validate that the script runs on first boot.  Replace with what you want"
touch /root/firstboot-script-ran
date > /root/firstboot-script-ran
EOF

# create systemd runonce systemd service
cat > /etc/systemd/system/firstboot-script.service <<EOF
[Unit]
Description=Execute a script via systemd the first time the host boots up
After=local-fs.target
After=network.target
ConditionPathExists=!/var/tmp/firstboot-script-complete

[Service]
ExecStartPre=/usr/bin/sleep 20
ExecStart=/usr/bin/bash /var/tmp/firstboot-script.sh
ExecStartPost=/usr/bin/touch /var/tmp/firstboot-script-complete
User=root
RemainAfterExit=true
Type=oneshot

[Install]
WantedBy=multi-user.target
EOF

# Enable the systemd runonce service
systemctl enable firstboot-script.service

%end
~~~
