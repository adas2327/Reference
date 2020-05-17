# Reference

## Python

**Debugging python**
```sh
import pdb
```
and put *pdb.set_trace()* where ever break is needed - 

## Ubuntu and linux
#### Wifi connection related 

First, determine the name of the WiFi interface:
```sh
nmcli d
```
Make sure the WiFi radio is on (which is its default state):
```sh
nmcli r wifi on
```
Then, list the available WiFi networks:
```sh
nmcli d wifi list
```
As an example, to connect to the access point 'my_wifi', you would use the following command:
```sh
nmcli d wifi connect my_wifi password <password>
```
**Disbale sleep from SSH**
```
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```
and to bring it back 
```
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

#### Detele a nonempty directiory or folder
```sh
$sudo rm -vr folderName
-f = to ignore non-existent files, never prompt
-r = to remove directories and their contents recursively
-v = to explain what is being done
```

#### Create a new file such as python script
```sh
$ touch filename.py
```
**To change the default bash directory**, open the .bashrc file from home directory as below
```sh
nano ~/.bashrc
```
and then just add *cd <desired dir name>* at the end. For example - 
```sh
cd ~/catkin_ws/src/rpi_ros_car/src
```



## ROS
Kill roscore
```sh
killall -9 rosmaster
```
#### Create a new catkin package
```sh
cd ~/catkin_ws/src
```
Now use the catkin_create_pkg script to create a new package called 'rpi-ros_car' which depends on std_msgs, roscpp, and rospy:
```sh
catkin_create_pkg rpi_ros_car std_msgs rospy 
```
or if you already have existing package in github, you can clone it here (see git tuorials for details). 
Then go to
```sh
cd ~/catkin_ws/src
```
and invoke 
```sh
catkin_make
```
Before running any pythion file through rosrun, you have to make them executable as shown below - 

```sh
$chmod +x mypythonscript.py
```
To run a executable file with rosrun under catkin workspace
```sh
$rosrun <package_name> <executable_name>
```
To killa rosnode if ctrl+C is not working,
```sh
rosnode kill yourNodeName
```
To get a list of ros node
```sh
rosnode list
```
Similarly, to get a list of all ros topics - 
```sh
rostopic list
```
and to get more info about a node or topic - 
```sh
rosnode info <topic name/node name>
```


### Ros package update on Raspberry pi (if you have installed ROS on raspbian image)
```sh
mv -i kinetic-ros_comm-wet.rosinstall kinetic-ros_comm-wet.rosinstall.old
rosinstall_generator ros_comm --rosdistro kinetic --deps --wet-only --tar > kinetic-ros_comm-wet.rosinstall
diff -u kinetic-ros_comm-wet.rosinstall kinetic-ros_comm-wet.rosinstall.old
wstool merge -t src kinetic-ros_comm-wet.rosinstall
wstool update -t src
```

## Windows Commad line
To see all connected devices in the network
```sh
arp -a
```
### Chocolatey 
Installing Chocolatey using PowerShell (with admin priviledge) - 
```
Invoke-WebRequest https://chocolatey.org/install.ps1 -UseBasicParsing | Invoke-Expression
```

install package from config file for the first time - 
```
choco install chocolatey-packages.config -y
```
install a software with specific version or on a specific location or getting information about a package - 
```
choco install software_name --version -y
choco install 7zip --install-directory=P:\7z
choco info 7zip
```
get a list of all installed software on local drive -
```
choco list --local-only
choco list -li
```
Install multiple packages in a single command - 
```
choco install pkg1 pkg2 pkg3 -y
```
Installing a package with all versions available - 
```
choco uninstall pkg --all-versions -y
```
List outdated packages
```
choco outdated
```
Ignore Checksums
```
choco install pkg --ignore-checksums
```
Upgrade a Package
```
choco upgrade <pkg|all> [<pkg2> <pkgN>] [<options/switches>]
choco upgrade git -y
choco upgrade all -y
```

## git 
If youa re using git for the first time - 
```sh
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git config --global credential.helper store
```
adding an existing folder to github
It is always best to create a git repo on git hub first and then clone the repo on the local drive. If you are dealing with catkin packages, then before creating the package first clone the git repo and then create the package with the same name. 
```sh
git pull
git add --all
git commit -m "message"
git push 
```
Ignore a file that is being tracked currently
```sh
git rm --cached <file>
```
Creating and applying stash
```sh
git stash
git stash list
git stash apply stash@{stash_index}
```
If you want to combine git commit and push in one command then create a make file with following commands (remember to place the make file in the smae directory where .git folder resides)
```sh
git:
	git add --ignore-errors .
	git commit -m "$m"
	git push -u origin master 
```
then invoke the following - 
```sh
make git m="your message"
```
## Raspberry Pi

Which version of Raspbian do I have?
```sh
cat /etc/os-release
```
#### How to modify the swap?
First turned off the swap - 
```sh
sudo dphys-swapfile swapoff
```
Then modify the swap. As root, edit the file 
```sh
sudo nano ~/etc/dphys-swapfile 
```
and modify the variable CONF_SWAPSIZE:1024
Save and exit. 
Start the swap again - 
```sh
sudo dphys-swapfile swapon
```
#### Getting the right ubuntu+ROS image for Raspberry Pi 4B
It's better to use the Raspi 4B with 4gb ram 
As there are no standard images available for Raspberry Pi 4B + Ubuntu + ROS, I am using the image created by ubiquity robotics.
https://downloads.ubiquityrobotics.com/pi.html

There might be some issues with wireless connectivity and doing ssh from PC, I will update this section once I find a way to resolve it. 
After flshing the image with ubiquity ubuntu image for Raspberry Pi, use the following commands one by one to make the image accessible for other robots other than ubiquity. 
Connect Raspberry pi to interent with cable to access ssh from PC. Find the IP address from your router page or by doing arp -a from command window. Then follow these commands - 
```sh
sudo systemctl disable magni-base
sudo systemctl disable roscore
sudo pifi set-hostname NEWHOSTNAME
sudo reboot
```
Once it comes back live try the following - 

```sh
nmcli d wifi list
```
If it shows all the networks nearby then  -
```sh
sudo nmcli d wifi connect my_wifi password <password>
```
If for some reason it throws some error or does not show the wifi list on the command nmcli d wifi list, then try the following - 
```sh
pifi list seen
```
and then
```sh
sudo pifi add MyNetwork password
sudo reboot
```
It might take few minutes to connect to wifi. So, emain patient and keep trying to ping. 

Next, you need to free up the GPIO pins. Try,
```sh
cd /
sudo nano /etc/pifi/pifi.conf
```
and change the following lines -
```sh
status_led: None
button_device_name: None
```
save and exit. Go back to root and try
```sh
sudo nano /etc/ubiquity/robot.yaml
```
make sure you have the following, *sonars = None* and the next line with sonars = *pi_sonar_v1* is commented. 
``` sh
sonars: None
# sonars: ‘pi_sonar_v1’
```
Thats it. 



