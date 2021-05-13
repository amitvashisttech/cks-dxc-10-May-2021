#### Check status of apparmor:
```sh
systemctl status apparmor
```
#### Sample Script:
```sh
mkdir /root/apparmor
cd /root/apparmor
```
```sh
nano myscript.sh
```
```sh
#!/bin/bash
touch /tmp/file.txt
echo "New File created"

rm -f /tmp/file.txt
echo "New file removed"
```
```sh
chmod +x myscript.sh
```
#### Install Apparmor Utils:
```sh
apt install apparmor-utils
```
#### Generate a new profile:
```sh
aa-genprof ./myscript.sh
```
```sh
./myscript.sh (from new tab run the script)
```

#### Verify the new profile:
```sh
cat /etc/apparmor.d/root.myscript.sh
aa-status
```

#### Now try to excute the myscript.sh ( Should work with any problem - already profiled ) 
```sh
./myscript.sh
```

#### Now Append the script & try to re-run ( It should failed because cat action is not profiled ) 
```
#!/bin/bash
touch /tmp/file.txt
echo "New File created"

rm -f /tmp/file.txt
echo "New file removed"


cat /etc/os-release
```

```sh
./myscript.sh
```


#### Disable a profile:
```sh
ln -s /etc/apparmor.d/root.apparmor.myscript.sh /etc/apparmor.d/disable/
apparmor_parser -R /etc/apparmor.d/root.apparmor.myscript.sh
```

#### Once Again Try & Run the script.
