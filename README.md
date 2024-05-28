# path_follower

GFOE's fork of the CCOM repository.

As part of the [CCOM's Project11](https://github.com/CCOMJHC/project11) and [GFOE's Project12](https://bitbucket.org/gfoe/project12), this repo. contains the ROS `path_follower_node`.

## `path_follower_node`

See example [project12/launch/fy21_step2_annie_common.launch](https://bitbucket.org/gfoe/project12/src/gfoe-devel/launch/fy21_step2_annie_common.launch)

### Subscribed Topics

* `enable` (std_msgs:Bool) - Turn on and off

### Crab Angle - only for unicycle mode

* `crab_angle_pid/control_effort` (?)

### Published Topics

* `cmd_vel` (geometry_msgs::TwistStamped)
* `project11/display` (geographic_visualization_msgs::GeoVizItem)

### Action Server

https://github.com/GFOE/path_follower/blob/gfoe-devel/action/path_follower.action

* Goal:
    * [geographic_msgs/GeoPath](geographic_msgs/GeoPath) path - which is an array of poses (lat/lon) and orientations (quat).  
    * float32 speed
* Result:
    * geographic_msgs/GeoPose ending_pose


### Parameters

* `dynamics_mode` (str, default: `unicycle`) - 'unicycle' or `holonomic`
* turn_in_place (bool, default: true)
* turn_in_place_threshold (float, default: 20.0)
* map_frame (str, default: `map`)
* base_frame (str, default: `base_link`)
* update_rate (float, default: 10)
* kp_surge
* kp_sway
* kp_yaw



### Theory of Operation

Operates as a ROS [action server](http://wiki.ros.org/actionlib) which provides a goal as an array of waypoints (lat/lon and heading)

Two modes of operation:

**holonomic**

[Relevant source.](https://github.com/GFOE/path_follower/blob/47c1a2c6c982c971b618d3913624156a817a1bf6/src/path_follower_node.cpp#L369)

* Target heading is the angle of the current path segment.  
* Commanded yaw rate (twist.angular.z) is proportional to yaw error: `twist.angular.z = kp_yaw * (target_heading - vessel_heading)`
* Commanded surge
    * When distance to goal is greater than `kp_surge * dist_to_goal`, then `twist.linear.x = goal_speed` where goal speed is provided in action call.
    * Otherwise, `twist.linear.x = kp_surge * dist_to_goal`
 * Commanded sway (`twist.linear.y`) is proportional (`kp_sway`) to cross_track error.


**unicycle**
Not really used by GFOE, so description may be not completely correct.

Commanded forward speed (twist.linear.x) is set as the goal speed, which is provided by the action goal set to the server.

Target heading is the sum angle of the current segment between two waypoints and the crab_angle.  This angle is set via subscricption to the ROS topic `crab_angle_pid/control_effort`.
Commanded yaw rate (twist.angular.z) is the difference between between the target heading and the current vessel heading.  Not that this seems to be a proportional feedback with K=1.0.


To determine when the current waypoint has been achieved, compares distance traveled along the path to the 'segment distance' - distance between waypoints.  when the distance traveled > segment distance, move on to next waypoint. 
