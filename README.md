# UEye Cam Driver

This software comes with a [BSD License](./LICENSE) and provides convenience APIs
(c++ and ROS) that facilitate access to UEye Cameras via the IDS Software Suite.

## Requirements

- [IDS uEye Software for the camera](https://en.ids-imaging.com/download-details/1009054.html?os=linux&version=&bus=64&floatcalc=#anc-software-305) 
- [camera calibration files used to feed the ros2](https://github.com/ros-perception/image_pipeline/tree/humble)
- tclap (Templatized C++ Command Line Parser Library): `sudo apt install libtclap-dev`

## Usage

**Resources**

Default resource paths include:

* `~/.ros/camera_info/`:  camera calibration files (`.yaml`)
* `~/.ros/camera_conf/`:  IDS configuration files (`.ini`)

The camera calibration files are used to feed the ros2 [camera_calibration](https://github.com/ros-perception/image_pipeline/tree/ros2/camera_calibration) framework.

The IDS configuration files are the native format for configuring an IDS camera. In general, you do not need an IDS configuration file as the ROS wrapper exposes most of the configuration via dynamic ROS parameters, but an IDS configuration
file can be useful for parameters that it does not yet cover.

**Quick Start**

To get started, launch the standalone launcher. It is configured with a parameterization that should enable connection to most IDS cameras.

```
# Uses ueye_cam/config/example_ros_configuration.yaml
$ ros2 launch ueye_cam standalone.launch.py

# Play around with parameters:
$ ros2 param list
$ ros2 param set ueye_cam auto_gain false
$ ros2 param describe ueye_cam red_gain
$ ros2 param set ueye_cam red_gain 100

# To deactivate the camera via lifecycle:
$ ros2 lifecycle set /ueye 4
```

**Configuration**

In a typical launch, configuration can be traced to one or more sources, each with their own priority. Lower priorities can be overridden by higher priorities. From lowest, to highest:

* _Defaults_ : refer to `ueye_cam/node_parameters.hpp` and `ueye_cam/camera_parameters.hpp`
* _IDS_ : defined in `node_parameters.ids_configuration_filename`
    * If none is set, the default `~/.ros/camera_conf/<camera_name>.ini` is used
* _On-Launch_ : usually passed in via `.yaml` to the launcher / command line
* _Dynamic_ : modified on the fly at runtime

**Camera Calibration**

TODO

**IDS Camera Configuration Files (Optional)**

Using the ros2 node, along with ros2 parameters will serve most use cases. Making use of IDS camera configuration files however (e.g. `config/example_ids_configuration.ini`) is useful in some situations:

* If you want access to all parameters of the camera. This node only exposes the most common parameters for configuration as ROS2 parameters.
* If you want to absolutely ensure your cameras have a fully deterministic launch, these initialisation files are *complete*. On loading, they will overwrite any and all residual configuration left by the last user. The ROS2 configuration will also be as deterministic as possible, i.e. it will overwrite any and all residual configuration it knows about, but it will not be able to do so for parameters it is not aware of that may have been reconfigured via other means.

TODO - how to use the IDS gui, how to export and load the configuration files.
