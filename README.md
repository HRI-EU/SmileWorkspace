# Smile software workspace

This repository contains the software for the project Smile that is the basis for these papers:

* Joublin, F., Ceravola, A., Smirnov, P., Ocker, F., Deigmoeller, J., Belardinelli, A., Wang, C., Hasler, S., Tanneberg, D. & Gienger, M.: [Copal: Corrective planning of robot actions with large language models.](https://hri-eu.github.io/Loom) International Conference on Robotics and Automation (ICRA), 2024.

* Tanneberg, D., Ocker, F., Hasler, S., Deigmoeller, J., Belardinelli, A., Wang, C., Wersing, H., Sendhoff, B. & Gienger, M. (2024). [To Help or Not to Help: LLM-based Attentive Support for Human-Robot Group Interactions.](https://hri-eu.github.io/AttentiveSupport) arXiv preprint arXiv:2403.12533.





## Checkout and update from git

### Initial checkout of the workspace
For an initial checkout, do:
```bash
git clone https://github.com/HRI-EU/SmileWorkspace.git
cd smile
git submodule update --init --recursive
```

### Updating the workspace
To update the workspace and all submodules, do:
```bash
cd Smile
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





## How to run

### C++ test program

```bash
cd install
bin/TestLLMSim 
```
You should now see a graphics window. If you hover the mouse over an object on the table, and type 'm' in the graphics window, you should be able to see the robot grasping it.

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

Now we can instantiate a simulator. The variables allow to configure it in various ways, please refer to the python module's documentation (src/AffAction/python/module.cpp).

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
sim.planActionSequence("get red_glass")
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

See section *Python virtual environment* in [src/AffAction/README.md](src/AffAction/python/README.md)





## License

The project is licensed under the [BSD-3-Clause License](LICENSE.md).



## Licenses of dependencies

This repository includes several files and submodules with the licenses stated [here](ThirdPartyLicenses.md). We include some mesh files of the [kinova-ros](https://github.com/Kinovarobotics/kinova-ros) package in the Meshes directory. [These mesh files](https://github.com/Kinovarobotics/kinova-ros/tree/noetic-devel/kinova_description/meshes) are licensed under the [BSD-3-Clause License](https://github.com/Kinovarobotics/kinova-ros?tab=BSD-3-Clause-1-ov-file#readme). We also include parts of the [GeometricTools library](https://www.geometrictools.com/) (Wild Magic Engine version 5.17) through the [WildMagic5p17](https://github.com/HRI-EU/WildMagic5p17) submodule. This code is licensed under the [Boost Software License - Version 1.0](https://www.boost.org/LICENSE_1_0.txt). We include the [pybind11](https://github.com/pybind/pybind11) submodule to build the python wrappers. It is licensed under a [BSD-3-Clause License](https://github.com/pybind/pybind11/blob/master/LICENSE). The [websocketpp](https://github.com/zaphoyd/websocketpp) submodule is licensed unter a [BSD-3-Clause License](https://github.com/zaphoyd/websocketpp?tab=License-1-ov-file#readme). The submodules [AffAction](https://github.com/HRI-EU/AffAction), [ESLib](https://github.com/HRI-EU/ESLib), [Tropic](https://github.com/HRI-EU/Tropic), and [Rcs](https://github.com/HRI-EU/Rcs) come from ourselves and are licensed under the [BSD-3-Clause License](https://opensource.org/license/bsd-3-clause).

