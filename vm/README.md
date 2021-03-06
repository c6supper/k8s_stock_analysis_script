# setup VM environment for kubernetes 

* https://linux.m2osw.com/autostart-virtualbox-vms

* Ubuntu server for example (>= 18.04 amd64)
    1.sudo apt-get install -y virtualbox virtualbox-ext-pack
    2.import from existed ova, or install by yourself.
    3.sudo passwd, set the password for root.
    4.sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
    5.sudo service ssh restart
    
* set VM start at boot in host
    1.modify /etc/default/virtualbox
        SHUTDOWN=acpibutton
    2.sudo vim /etc/systemd/system/kubesphere.service
    
        [Unit]
        Description=kubesphere
        After=network.target virtualbox.service
        Before=runlevel2.target shutdown.target
        
        [Service]
        User=c6supper
        Group=vboxusers
        Type=forking
        Restart=no
        TimeoutSec=5min
        IgnoreSIGPIPE=no
        KillMode=process
        GuessMainPID=no
        RemainAfterExit=yes
        ExecStart=/usr/bin/VBoxManage startvm kubesphere1 kubesphere2 --type headless
        ExecStop=/usr/bin/VBoxManage controlvm kubesphere1 acpipowerbutton
        ExecStopPost=/usr/bin/VBoxManage controlvm kubesphere2 acpipowerbutton 
        [Install]
        WantedBy=multi-user.target
        
    3.sudo systemctl daemon-reload
    4.sudo systemctl enable kubesphere
    5.sudo systemctl start kubesphere
