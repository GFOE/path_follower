# path_follower

GFOE's fork of the CCOM repository.

As part of the [CCOM's Project11](https://github.com/CCOMJHC/project11) and [GFOE's Project12](https://bitbucket.org/gfoe/project12), this repo. contains the ROS `path_follower_node`.

## `path_follower_node`

## Subscribed Topics

* `enable` (std_msgs:Bool) - Turn on and off

### Crab Angle - only for unicycle mode

* `crab_angle_pid/control_effort` (?)

### Published Topics

* `cmd_vel` (geometry_msgs::TwistStamped)
* `project11/display` (geographic_visualization_msgs::GeoVizItem)

### Action Server

https://github.com/GFOE/path_follower/blob/gfoe-devel/action/path_follower.action

### Parameters

* `dynamics_mode` (str, default: `unicycle`) - 'unicycle' or `holonomic`

### Theory of Operation

