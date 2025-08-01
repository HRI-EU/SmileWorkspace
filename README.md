# Smile software workspace

This repository contains the software for the project Smile that is the basis for a number of papers:

* Joublin, F., Ceravola, A., Smirnov, P., Ocker, F., Deigmoeller, J., Belardinelli, A., Wang, C., Hasler, S., Tanneberg, D. & Gienger, M.: \
  [Copal: Corrective planning of robot actions with large language models.](https://hri-eu.github.io/Loom) 
  International Conference on Robotics and Automation (ICRA), 2024.

* Tanneberg, D., Ocker, F., Hasler, S., Deigmoeller, J., Belardinelli, A., Wang, C., Wersing, H., Sendhoff, B. & Gienger, M.: \
  [To Help or Not to Help: LLM-based Attentive Support for Human-Robot Group Interactions.](https://hri-eu.github.io/AttentiveSupport) \
  IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), 2024.

* Leusmann, J., Belardinelli, A., Haliburton, L., Hasler, S., Schmidt, A., Mayer, S., Gienger, M. and Wang, C:\
  [Investigating LLM-Driven Curiosity in Human-Robot Interaction.](https://www.medien.ifi.lmu.de/pubdb/publications/pub/leusmann2025investigating/leusmann2025investigating.pdf) \
  ACM Conference on Human Factors in Computing Systems (CHI), 2025

* Kr�ger, M., Tanneberg, D., Wang, C. Hasler, S. and Gienger, M.: \
  [Mirror Eyes: Explainable Human-Robot Interaction at a Glance.](https://hri-eu.github.io/MirrorEyes) \
  IEEE International Conference on Robot and Human Interactive Communication (RO-MAN), 2025



## Checkout and update from git

### Initial checkout of the workspace
For an initial checkout, do:
```bash
git clone https://github.com/HRI-EU/SmileWorkspace.git
cd SmileWorkspace
git submodule update --init --recursive
```

### Updating the workspace
To update the workspace and all submodules, do:
```bash
cd SmileWorkspace
git fetch
git rebase
git submodule update --init --recursive
```





## How to build

### Linux

The code has been mainly developed under Ubuntu 20.04. These system packages are required to be installed before building the project:

 - Libxml2 (MIT License, Ubuntu-package libxml2-dev)
 - Qt5: LGPL (Also dual commercial license available, Ubuntu-package qt5-default)
 - qwt (LGPL with additions, Ubuntu-package libqwt-qt5-dev)
 - OpenSceneGraph (OSGPL, Ubuntu-package libopenscenegraph-dev)
 - Bullet Physics (Zlib, Ubuntu-package libbullet-dev)

```bash
mkdir -p <build-directory>
cd <build-directory>
cmake <repo-top-directory> -DCMAKE_INSTALL_PREFIX=<install-dir>
make -j
make install
```

While it is not mandatory, we recommend adding build and install directories under the repositorie's top-level directory. This eases defining the paths:

```bash
cd <repo-top-directory> 
mkdir -p build install
cd build
cmake ..-DCMAKE_INSTALL_PREFIX=../install
make -j
make install
```

### Windows

It also compiles for Windows (Visual Studio), please follow the instructions [here](https://github.com/HRI-EU/Rcs/tree/develop).


### MacOS / Apple

We use the homebrew package manager with these packages:
Mandatory: cmake graphviz open-scene-graph pkgconf qt@5 qwt-qt5 zeromq
Optional: xdot bullet asio doxygen eigen

Compile from terminal console:

```bash
cmake ../src/Smile/ -DCMAKE_INSTALL_PREFIX=../install -DCMAKE_PREFIX_PATH=/opt/homebrew/Cellar/qt@5/5.15.16/lib/cmake
```

Compile with Xcode:

```bash
cmake -G Xcode ../src/Smile/ -DCMAKE_INSTALL_PREFIX=../install -DCMAKE_PREFIX_PATH=/opt/homebrew/Cellar/qt@5/5.15.16/lib/cmake
```







## How to run

### C++ test program

```bash
cd install
bin/TestLLMSim 
```
You should now see a graphics window. If you hover the mouse over an object on 
the table, and type 'm' in the graphics window, you should be able to see the 
robot grasping it.


## How to run the Schunk PW70 PTU 

***Step 1:*** 
Start the driver. It will open two pub / sub zmq sockets on localhost. 
The connection from robot to remote process is on port 40006, the remote 
process to the robot driver is sending on port 40007. To change this, or to 
add a different connection, please refer to the file RoboNetworkInfo.h.

```bash
cd SmileWorkspace/build
bin/PTUDriver -m 1 -robo_name ptu
```

the PTUDriver also implements other modes for initializing the unit or sending 
commands from the command line. Details are displayed when running 

```bash
bin/PTUDriver -h
```

***Step 2:*** 
Start the python test program.

```bash
cd SmileWorkspace/src/AffAction/python
python -i ptu_client.py
```

This brings you to a python console, in which you can call the functions 
implemented in the ptu_client.py, for instance:

```bash
ptu_command_once(30, 10)
ptu_quit()
```

These functions are for illustration purpose only. For communicating with 
the PTUDriver in a closed loop with higher frequencies, you need to implement 
your own function that opens / closes the connection once, and then does the
command / feedback handling inside.





### Python module test

This section explains how to run the simulator from a python console. First, start a python console from the install directory. In order to find the python module and the dependent library files, the path to the installation directory's library directory needs to be added. For Linux, the so files and the python module are located in the lib directory. For Windows, the dlls and the python module are located in the bin directory. 

```
import platform
import sys

if platform.system() == "Linux":
    sys.path.append("lib")
elif platform.system() == "Windows":
    sys.path.append("bin")
```

Now we can import the pyAffaction module:

```
from pyAffaction import *
```

Now we can instantiate a simulator. The variables allow to configure it in various ways, please refer to the [python module's documentation](https://github.com/HRI-EU/AffAction/blob/develop/python/module.cpp).

```
sim = LlmSim()
sim.noTextGui = True
sim.unittest = False
sim.useWebsocket = False
sim.speedUp = 3
sim.noLimits = False
sim.verbose = False
sim.init(True)
```

This brings up a graphics window (headless with sim.init(False)). To start the simulator's endless loop, type

```
sim.run()
```

You can do the above conveniently by calling

```
python -i ../src/AffAction/python/pySim.py 
```

Now, you can send commands to it on the console, for instance

```
sim.plan("get glass_red", True)
```

To stop it, use

```
sim.stop()
```

To properly exit the python console, type

```
del b
quit()
```

Click [here](src/AffAction/python/README.md) for more information and documentation of the python API.

#### Connect to simulator with python websocket client

See section *Python virtual environment* in [src/AffAction/README.md](https://github.com/HRI-EU/AffAction/blob/develop/python/README.md)





## License

The project is licensed under the [BSD-3-Clause License](LICENSE.md).



## Licenses of dependencies

This repository includes several files and submodules with the licenses stated [here](ThirdPartyLicenses.md). We include some mesh files of the [kinova-ros](https://github.com/Kinovarobotics/kinova-ros) and [ros-kortex](https://github.com/Kinovarobotics/ros-kortex) packages in the Meshes directory. [These mesh files](https://github.com/Kinovarobotics/kinova-ros/tree/noetic-devel/kinova_description/meshes) and [these](https://github.com/Kinovarobotics/ros_kortex/tree/noetic-devel/kortex_description) are licensed under the [BSD-3-Clause License](https://github.com/Kinovarobotics/kinova-ros?tab=BSD-3-Clause-1-ov-file#readme). We also include parts of the [GeometricTools library](https://www.geometrictools.com/) (Wild Magic Engine version 5.17) through the [WildMagic5p17](https://github.com/HRI-EU/WildMagic5p17) submodule. This code is licensed under the [Boost Software License - Version 1.0](https://www.boost.org/LICENSE_1_0.txt). We include the [pybind11](https://github.com/pybind/pybind11) submodule to build the python wrappers. It is licensed under a [BSD-3-Clause License](https://github.com/pybind/pybind11/blob/master/LICENSE). The [websocketpp](https://github.com/zaphoyd/websocketpp) submodule is licensed unter a [BSD-3-Clause License](https://github.com/zaphoyd/websocketpp?tab=License-1-ov-file#readme). The submodules [AffAction](https://github.com/HRI-EU/AffAction), [ESLib](https://github.com/HRI-EU/ESLib), [Tropic](https://github.com/HRI-EU/Tropic), and [Rcs](https://github.com/HRI-EU/Rcs) come from ourselves and are licensed under the [BSD-3-Clause License](https://opensource.org/license/bsd-3-clause).

