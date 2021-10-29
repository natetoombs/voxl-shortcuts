# voxl-shortcuts

These files help make life easier for using the ENAE788M drones.

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
Just as the drone can automatically update the `ROS_IP`, so can your computer. The attached file `.rosrc` is what I use to:
1. source ROS 
2. set the `ROS_IP` to the computer IP address
3. set the `ROS_MASTER_URI` to either the `localhost` or to the drone IP address
4. source the ROS workspaces on the computer. 
For this class, only steps 1-3 are important, unless you are running code from your computer; then you need step 4 as well.
