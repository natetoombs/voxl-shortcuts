# voxl-shortcuts

These files help make life easier for using the ENAE788M drones.

## Instructions
First, add the files (`.aliases` and `.dockrc`) to the home in yocto.

### Aliases
To use these aliases, you will want to add the following to the `.bashrc` in the yocto home folder:
`source ~/.aliases`
This will allow you use any of the aliases in the file, take a look to see what they are.

### ROS Things
Since everything ROS related is done in the docker image, you need to source the ROS items from the docker. To make things simple, once you are in the docker image (use alias `dock`), then source the `.dockrc` file in the yoctohome (`source ~/yoctohome/.dockrc`). This will set your `ROS_IP` address and `ROS_MASTER_URI` to your current IP address, and will source ROS Kinetic and your workspace (make sure to change the `.dockrc` to source your workspace.

One other change to make: change the run_mavros.sh file by commenting out the `source ros_environment.sh` line and adding `source ~/yoctohome/.dockrc`. This will make sure that MAVROS has the right files sourced.
