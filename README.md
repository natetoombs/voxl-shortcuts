# voxl-shortcuts

These files help make life easier for using the ENAE788M drones. In summary, with these files you won't have to manually set the ROS_IP on your drone or on your computer. All of the important things to source are gathered into a single file; on the drone, you source that file when in the docker image, and on your computer, you can have it source automatically. There are aliases (shortcuts) to several key commands, such as entering the docker image and switching between `station` and `softap` modes. Let me know if you have any questions/additions!

## Drone Instructions
First, add the files (`.aliases` and `.dockrc`) to the home in yocto.

### Aliases
To use these aliases, you will want to add the following to the `.bashrc` in the yocto home folder:
`source ~/.aliases`
This will allow you use any of the aliases in the file, take a look to see what they are. To use an alias, just run the alias in the terminal.

### ROS Things
Since (almost -- see VOXL_MPA_TO_ROS) everything ROS related is done in the docker image, you need to source the ROS items from the docker. To make things simple, once you are in the docker image (use alias `dock`), then source the `.dockrc` file in the yoctohome (`source ~/yoctohome/.dockrc`). This will set your `ROS_IP` address and `ROS_MASTER_URI` to your current IP address, and will source ROS Kinetic and your workspace (make sure to change the `.dockrc` to source your workspace.

One other change to make: change the run_mavros.sh file by commenting out the `source ros_environment.sh` line and adding `source ~/yoctohome/.dockrc`. This will make sure that MAVROS has the right files sourced.

#### Run MAVROS on Boot
Add the following line to `/etc/modalai/docker-autorun-script.sh` file, then run `systemctl enable docker-autorun`.

#### VOXL_MPA_TO_ROS
I think this is the only ROS-related thing we run outside of the docker image. To get the camera data published to ROS topics, run `roslaunch voxl_mpa_to_ros voxl_mpa_to_ros.launch’ 

### Date Management
The VOXL board doesn't have a way to remember the date and time upon rebooting; it always resets the date to the beginning of time (January 1, 1970). When the drone connects to the internet (in station mode), it automatically sets the date, so there aren't any problems. A problem we ran into was that if we are in SOFTAP mode, transfer files to the drone, then try to build, it would run into errors because the files were changed "in the future" (because their change dates are in 2021 while the computer date is in 1970).
A solution was to create a file in the home (`~/.`) that would store the date when connected to the internet, then reading from that file to set the system date when in SOFTAP mode. To do this, you need a `.date` file in the home. Then, when connected to the internet, run `get-date` to save the current date to the file. Then, when in SOFTAP mode, run `set-date`. This solution might not be much better than just manually setting the date (`date -s "YYYY-MM-DD HH-MM-SS`).

## Computer Instructions
Just as the drone can automatically update the `ROS_IP`, so can your computer. Put the attached file `.rosrc` in your home folder and source it in your `.bashrc`. I use the file to:
1. source ROS 
2. set the `ROS_IP` to the computer IP address
3. set the `ROS_MASTER_URI` to either the `localhost` or to the drone IP address
4. source the ROS workspaces on the computer. 

For this class, only steps 1-3 are important, unless you are running code from your computer; then you need step 4 as well.
To use the `.rosrc`, you will need to add some of your computer information. You will need to add your ROS distribution (probably melodic or kinetic), your network interface, your drone's ip address, and the path to your computer's ROS workspace, all in the indicated lines of the `.rosrc`. To find your network interface type `nmcli dev status` in your terminal and look for the wireless device name; it usually starts with "w" (e.g. wlan0, wlp3s0).

Also, here are a couple aliases you can add to your computer's `.bashrc`:
```
alias sshVOXL="sshpass -p oelinux123 ssh root@192.168.78.<drone-ip-here"

alias sshVOXL-SOFTAP="sshpass -p oelinux123 ssh root@192.168.8.1"
```
These aliases only work after you have connected with the normal ssh command once and typed "yes" to add the device to your list of authenticated devices. Dr. Conroy has the full alias that would work on the first try, but it is very long so I didn't feel like typing it all in.