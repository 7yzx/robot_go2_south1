1.20 等扩展坞修好之后可以测试这个仓库[go2_robot](https://github.com/Unitree-Go2-Robot/go2_robot)

1.17-1.19 测试了两个repo的代码，[autonomy_stack_go2](https://github.com/jizhang-cmu/autonomy_stack_go2) 以及
[unoffice go2 sdk](https://github.com/abizovnuralem/go2_ros2_sdk)

    1. autonomy: 使用原始edu版本的go2，基本不需要额外的操作。但是尝试了很久效果并不是很好，cmu官方对他们代码的管理非常不在意，一般不会回复issue之类的，之后不要在尝试这个repo了。

    2. unofficial go2 sdk这个repo针对go2的几乎所有类型都支持，并且有两种连接方式，但是需要设置。这种方式的nav2存在问题，目前没有解决




# go2_ros2_sdk

## 连接
测试成功的设置

```bash
    export ROBOT_IP="192.168.50.116"
    export CONN_TYPE="webrtc"
```

### wifi模式（webtrc）
是wifi模式（webtrc），需要在app中设置，设置后，就不用网线与机器狗里的nx板连接即可远程控制。
1. 手机连接会与电脑的连接冲突，所以手机连接后就需要断开。
2. 使用这种方式后，机器狗里的nx板，发布的topic会改变，变的非常少，而且话题名称也会变。（目前不知道原因）[topic](./docs/topic_log.md)
3. 设备和机器狗需要连接在同一wifi


### 有线模式(cyclonedds)
测试未成功。所以只能按照上面方式，有可能是需要在app里取消wifi-mode
```bash
    export CONN_TYPE="cyclonedds"
```

## 建图
这里使用的是[slam_toolbox](https://github.com/SteveMacenski/slam_toolbox)，只是2d 而且，不太好用。因为想用3d雷达的建图效果，所以这里是需要修改的。

建图和雷达关系比较重要，因此在选择建图方案之前需要考虑算法是否与unitree lidar L1适配。

目前已知完全可以的是

- point-lio(请参考[autonomy_stack_go2](https://github.com/jizhang-cmu/autonomy_stack_go2)中的point-lio)

    这里使用的[autonomy_stack_go2](https://github.com/jizhang-cmu/autonomy_stack_go2)仓库中的point-lio，这个里面有机器狗的校准，以及point-lio，ros2.

    使用这个必须连接有线。

```bash
    cd autonomy_stack_go2/
    source install/setup.bash
    ros2 launch point_lio_unilidar mapping_utlidar.launch 
```

- rtab-map （[rtab-issue](https://github.com/introlab/rtabmap/issues/1433#issuecomment-2601047425)，[link file for lidar3d.launch.py](https://github.com/introlab/rtabmap_ros/blob/ros2/rtabmap_examples/launch/lidar3d.launch.py) ）


## 经验

### 关于刷机官方的拓展坞失败
用nvdian官方镜像无法刷机，无法进行，
也有人尝试但是基本都会失败，所以在unitree提供镜像之前都不需要尝试了。

https://forums.developer.nvidia.com/t/nvidia-jetson-orin-nano-8-gb-ssd-image/306949
- 实在解决不了的问题，重新部署说不定就好了


## 错误
### foxglove_bridge

```bash
[foxglove_bridge-9] [WARN] [1737347698.596910461] [foxglove_bridge]: Failed to add channel for topic "/qt_add_node" (unitree_interfaces/msg/QtNode): package 'unitree_interfaces' not found, searching: [/home/c/ros2_ws/install/go2_robot_sdk, /home/c/ros2_ws/install/unitree_go, /home/c/ros2_ws/install/go2_interfaces, /home/c/ros2_ws/install/coco_detector, /home/c/autonomy_stack_go2/install/waypoint_rviz_plugin, /home/c/autonomy_stack_go2/install/waypoint_example, /home/c/autonomy_stack_go2/install/visualization_tools, /home/c/autonomy_stack_go2/install/graph_decoder, /home/c/autonomy_stack_go2/install/far_planner, /home/c/autonomy_stack_go2/install/boundary_handler, /home/c/autonomy_stack_go2/install/visibility_graph_msg, /home/c/autonomy_stack_go2/install/vehicle_simulator, /home/c/autonomy_stack_go2/install/local_planner, /home/c/autonomy_stack_go2/install/calibrate_imu, /home/c/autonomy_stack_go2/install/go2_sport_api, /home/c/autonomy_stack_go2/install/unitree_go, /home/c/autonomy_stack_go2/install/unitree_api, /home/c/autonomy_stack_go2/install/point_lio_unilidar, /home/c/autonomy_stack_go2/install/transform_sensors, /home/c/autonomy_stack_go2/install/terrain_analysis_ext, /home/c/autonomy_stack_go2/install/terrain_analysis, /home/c/autonomy_stack_go2/install/teleop_rviz_plugin_plus, /home/c/autonomy_stack_go2/install/teleop_rviz_plugin, /home/c/autonomy_stack_go2/install/sensor_scan_generation, /home/c/autonomy_stack_go2/install/ros_tcp_endpoint, /home/c/autonomy_stack_go2/install/pcd2pgm, /home/c/autonomy_stack_go2/install/goalpoint_rviz_plugin, /home/c/autonomy_stack_go2/install/go2_h264_repub, /opt/ros/humble]
```


### planner_server costmap
```bash
[planner_server-13] [WARN] [1737347716.110042163] [nav2_costmap_2d]: Robot is out of bounds of the costmap!
```


### rviz
```bash
[rviz2-5] [ERROR] [1737347843.645523601] [rviz2]: Vertex Program:rviz/glsl120/indexed_8bit_image.vert Fragment Program:rviz/glsl120/indexed_8bit_image.frag GLSL link result : 
[rviz2-5] active samplers with a different type refer to the same texture image unit

```

### 摄像头
```bash
[go2_driver_node-3] [h264 @ 0x75e1a0000e40] non-existing PPS 0 referenced
[go2_driver_node-3] [h264 @ 0x75e1a0000e40] decode_slice_header error
[go2_driver_node-3] [h264 @ 0x75e1a0000e40] no frame!
[go2_driver_node-3] WARNING:aiortc.codecs.h264:H264Decoder() failed to decode, skipping package: [Errno 1094995529] Invalid data found when processing input

```