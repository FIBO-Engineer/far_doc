# ROS1 Package Creation Tutorial

## Overview
Creating a ROS1 package allows you to structure your robot's software components in a modular way. A package can include nodes, libraries, launch files, and configuration files. This tutorial will guide you through the steps of creating a ROS1 package, setting up dependencies, and using tools like `vcstool` for version control of multiple repositories. We'll focus on C++ (roscpp) development as it provides better performance and type safety for robotic systems.

## Procedure

### Step 1: Setup Your ROS Workspace
Before creating a new package, make sure you have a ROS workspace. If you don't already have one, follow these steps:

1. Create a new workspace directory:

    ```bash
    mkdir -p ~/catkin_ws/src
    cd ~/catkin_ws/
    ```

2. Build the workspace:

    ```bash
    catkin_make
    ```

3. Source the workspace setup file:

    ```bash
    source devel/setup.bash
    ```

### Step 2: Create a New Package
To create a new ROS package, navigate to the `src` directory of your workspace and run the following command:

```bash
cd ~/catkin_ws/src
catkin_create_pkg tutorial_02 std_msgs roscpp
```

This will create a package named `tutorial_02` with dependencies on `std_msgs` and `roscpp` (C++ ROS client library).

### Step 3: Explore the Package Structure
After creating the package, take a moment to understand its structure:

```bash
cd tutorial_02
ls
```

You should see:
- `CMakeLists.txt`: Build instructions for CMake
- `package.xml`: Package metadata and dependencies
- `include/tutorial_02/`: Headers directory for your C++ code
- `src/`: Source directory for your C++ implementations

### Step 4: Build the Package
Return to the root of your workspace and build it using catkin_make:
```bash
cd ~/catkin_ws
catkin_make
```

### Step 5: Source the Workspace Again
Source the workspace setup file to ensure that ROS recognizes the newly created package:
```bash
source devel/setup.bash
```

### Step 6: Create a C++ Node
Create a C++ source file in the src directory, for example `talker.cpp`:

```bash
touch ~/catkin_ws/src/tutorial_02/src/talker.cpp
```

Edit the `talker.cpp` file with your favorite editor and add the following code:

```cpp
#include <ros/ros.h>
#include <std_msgs/String.h>

int main(int argc, char **argv)
{
  // Initialize ROS node
  ros::init(argc, argv, "talker");
  
  // Create a node handle
  ros::NodeHandle n;
  
  // Create a publisher object
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 10);
  
  // Set publishing rate (1 Hz)
  ros::Rate loop_rate(1);
  
  int count = 0;
  while (ros::ok())
  {
    // Create message
    std_msgs::String msg;
    std::stringstream ss;
    ss << "Hello, ROS! " << count;
    msg.data = ss.str();
    
    // Log and publish
    ROS_INFO("%s", msg.data.c_str());
    chatter_pub.publish(msg);
    
    // Process callbacks and sleep
    ros::spinOnce();
    loop_rate.sleep();
    ++count;
  }
  
  return 0;
}
```

### Step 7: Update CMakeLists.txt
You need to update the CMakeLists.txt to build your new C++ node. Add the following to your CMakeLists.txt (around line 136, after the existing boilerplate):

```cmake
add_executable(talker src/talker.cpp)
target_link_libraries(talker ${catkin_LIBRARIES})
```

### Step 8: Build the Package Again
```bash
cd ~/catkin_ws
catkin_make
```

### Step 9: Create a Launch File (Optional)
Create a launch file in the launch directory of your package to run your nodes:
```bash
mkdir -p ~/catkin_ws/src/tutorial_02/launch
```

Create the `talker.launch` file:
```xml
<launch>
    <node name="talker" pkg="tutorial_02" type="talker" output="screen" />
</launch>
```

Note that the type attribute doesn't have a file extension for C++ executables, unlike Python scripts.

### Step 10: Run the Package
Now you can run the package using ROS tools like `rosrun` or `roslaunch`.

To run the node directly:
```bash
rosrun tutorial_02 talker
```

Or, to launch the node using the launch file:
```bash
roslaunch tutorial_02 talker.launch
```

## Dependencies Management

### Understanding ROS Dependencies
ROS packages can have various types of dependencies, and they must be properly declared in both `package.xml` and `CMakeLists.txt`. Properly managing dependencies is crucial for ensuring your package builds correctly and can be distributed to others.

### Checking Available Dependencies

Before adding dependencies to your package, you should check if they are available for your ROS distribution. The official repository of available ROS packages is maintained at:

```
https://github.com/ros/rosdistro
```

To find available packages for your specific ROS distribution (e.g., Noetic, Melodic), navigate to:

```
https://github.com/ros/rosdistro/blob/master/[DISTRO]/distribution.yaml
```

For system dependencies (non-ROS packages), you should also check:

```
https://github.com/ros/rosdistro/tree/master/rosdep/base.yaml
```

This file lists system dependencies that can be installed using `rosdep`, which helps manage both ROS and non-ROS dependencies.

This file contains all officially available packages for your distribution, along with version information and repository URLs. Always check these resources before adding dependencies to ensure they are available for your ROS distribution.

### Types of Dependencies in package.xml

In ROS package.xml, there are different types of dependency tags, each serving a specific purpose in the build process. Understanding these differences is crucial for beginners:

1. **`<build_depend>`**: These packages are required at **build time**
   - Needed when compiling your package
   - Example: header files, compile-time libraries
   - These dependencies are NOT exposed to packages depending on yours

2. **`<build_export_depend>`**: These packages are required for building packages that depend on yours
   - When other packages build against your package, they also need these dependencies
   - Example: header files that are included in your public headers

3. **`<exec_depend>`**: These packages are required at **runtime**
   - Needed when actually running your package
   - Example: shared libraries, ROS message runtime packages
   - Required for users who just want to run your package without building it

4. **`<depend>`**: A convenience tag that combines all three dependency types
   - Equivalent to declaring `<build_depend>`, `<build_export_depend>`, and `<exec_depend>`
   - Use this when a package is needed for all three stages (common for most ROS dependencies)

#### Real-world Example

Let's use a practical example to understand these differences:

```xml
<!-- For a C++ package that uses custom messages -->
<!-- message_generation is only needed at build time -->
<build_depend>message_generation</build_depend>

<!-- message_runtime is only needed at runtime -->
<exec_depend>message_runtime</exec_depend>

<!-- roscpp is needed at all stages -->
<depend>roscpp</depend>

<!-- When using an external library -->
<build_depend>boost</build_depend>       <!-- If only used internally -->
<depend>opencv</depend>                  <!-- If used in public headers -->
```

This distinction is particularly important for:
- Custom message packages (message_generation vs message_runtime)
- Large dependencies that shouldn't be propagated to dependents
- Packages with different build and runtime components

### Package.xml Dependencies
In your `package.xml`, dependencies are declared using these tags:

```xml
<!-- For C++ packages -->
<build_depend>roscpp</build_depend>
<build_export_depend>roscpp</build_export_depend>
<exec_depend>roscpp</exec_depend>

<!-- Other common dependencies -->
<build_depend>std_msgs</build_depend>
<build_export_depend>std_msgs</build_export_depend>
<exec_depend>std_msgs</exec_depend>
```

For simplicity, when a package is needed at all stages, you can use the combined `<depend>` tag:

```xml
<depend>roscpp</depend>
<depend>std_msgs</depend>
```

### CMakeLists.txt Dependencies
In `CMakeLists.txt`, dependencies are specified using `find_package`:

```cmake
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
)
```

It's important to ensure that all packages listed in `find_package` are also listed in your `package.xml` as either `<build_depend>` or `<depend>`.

### Dependency Management Best Practices

1. **Minimize Dependencies**: Only include packages you actually need
2. **Be Specific**: Use the most specific dependency type for each package
3. **Version Management**: For non-ROS dependencies, consider specifying version requirements:
   ```xml
   <build_depend version_gte="1.2.3">some_package</build_depend>
   ```
4. **Check Dependencies**: Use `rosdep check` to verify all dependencies are installed:
   ```bash
   rosdep check --from-paths src --ignore-src --rosdistro noetic
   ```
5. **Install Missing Dependencies**: Use `rosdep` to install missing dependencies:
   ```bash
   rosdep install --from-paths src --ignore-src --rosdistro noetic -y
   ```

### Adding Custom Messages, Services, or Actions
If your package defines custom messages, services, or actions, you'll need additional configuration:

1. Add message generation dependencies:
```xml
<!-- In package.xml -->
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

2. Update CMakeLists.txt:
```cmake
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  message_generation
)

# Add message files
add_message_files(
  FILES
  MyCustomMsg.msg
)

# Generate messages
generate_messages(
  DEPENDENCIES
  std_msgs
)

# Specify catkin package dependencies
catkin_package(
  CATKIN_DEPENDS roscpp std_msgs message_runtime
)
```

After adding the dependencies, remember to run:
```bash
cd ~/catkin_ws
catkin_make
source devel/setup.bash
```

### Troubleshooting Dependencies

Common dependency issues and solutions:

1. **Missing Dependency**: If `catkin_make` fails with "Could not find a package configuration file provided by [PACKAGE]"
   - Check if the package is installed: `apt-cache policy ros-[distro]-[package]`
   - Install using rosdep: `rosdep install --from-paths src --ignore-src -y`

2. **Incompatible Versions**: If you get runtime errors about mismatched message definitions
   - Ensure all packages are from the same ROS distribution
   - Check for conflicting packages with `rospack profile`

3. **Missing Libraries**: If you get linker errors
   - Ensure the library is correctly listed in `target_link_libraries`
   - Check if library paths are correct with `catkin config --cmake-args -DCMAKE_VERBOSE_MAKEFILE=ON`

## Using External Libraries
To use external C++ libraries in your ROS package:

1. Add the dependency in `package.xml`:
```xml
<build_depend>libname</build_depend>
<exec_depend>libname</exec_depend>
```

2. Find and link the library in `CMakeLists.txt`:
```cmake
find_package(LibName REQUIRED)
include_directories(${LIBNAME_INCLUDE_DIRS})
target_link_libraries(your_node ${catkin_LIBRARIES} ${LIBNAME_LIBRARIES})
```

3. For system libraries not found by CMake, you might need to use pkg-config:
```cmake
find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBNAME REQUIRED libname)
include_directories(${LIBNAME_INCLUDE_DIRS})
link_directories(${LIBNAME_LIBRARY_DIRS})
add_definitions(${LIBNAME_CFLAGS})
target_link_libraries(your_node ${catkin_LIBRARIES} ${LIBNAME_LIBRARIES})
```

## Managing Multiple Repositories with vcstool

`vcstool` (Version Control System tool) allows you to manage multiple git repositories in your ROS workspace efficiently.

### Step 1: Install vcstool
First, install vcstool:

```bash
sudo apt-get install python3-vcstool
```

### Step 2: Create a .repos File
Create a YAML file that specifies your repositories. For example, create `my_repos.repos`:

```yaml
repositories:
  src/tutorial_02:
    type: git
    url: https://github.com/username/tutorial_02.git
    version: main
  src/dependency_package:
    type: git
    url: https://github.com/username/dependency_package.git
    version: main
```

### Step 3: Import Repositories
Use vcstool to clone all repositories:

```bash
vcs import ~/catkin_ws < my_repos.repos
```

### Step 4: Update All Repositories
To update all repositories:

```bash
cd ~/catkin_ws
vcs pull src
```

### Step 5: Check Status of All Repositories
To see the status of all repositories:

```bash
vcs status src
```

### Step 6: Export Current Configuration
To export your current repository configuration:

```bash
vcs export src > current_repos.repos
```

## Creating a Complete C++ ROS Package

Let's create a more complete example with a publisher and subscriber:

1. Create a subscriber node in `src/listener.cpp`:

```cpp
#include "ros/ros.h"
#include "std_msgs/String.h"

// Callback function
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
  // Initialize ROS node
  ros::init(argc, argv, "listener");
  
  // Create a node handle
  ros::NodeHandle n;
  
  // Subscribe to the "chatter" topic
  ros::Subscriber sub = n.subscribe("chatter", 10, chatterCallback);
  
  // Process callbacks until node shutdown
  ros::spin();
  
  return 0;
}
```

2. Update `CMakeLists.txt` to build the new node:

```cmake
add_executable(listener src/listener.cpp)
target_link_libraries(listener ${catkin_LIBRARIES})
```

3. Create a launch file to run both nodes:

```xml
<launch>
  <node name="talker" pkg="tutorial_02" type="talker" output="screen" />
  <node name="listener" pkg="tutorial_02" type="listener" output="screen" />
</launch>
```

4. Build and test your package:

```bash
cd ~/catkin_ws
catkin_make
source devel/setup.bash
roslaunch tutorial_02 talker_listener.launch
```

## Best Practices for ROS C++ Packages

1. **Use Header Files**: Organize your code with header files in the `include/<package_name>/` directory.

2. **Follow Include Conventions**: Use angle brackets for system-wide headers and quotes for project headers:
   ```cpp
   // For system and library headers (including ROS libraries)
   #include <ros/ros.h>
   #include <std_msgs/String.h>
   #include <vector>
   #include <string>
   
   // For your own package headers
   #include "my_package/MyNode.h"
   #include "my_package/utilities.h"
   ```
   This convention makes it clear which headers are from external libraries and which are from your own project.

3. **Create Class-Based Nodes**: Structure your nodes as classes for better organization:

```cpp
// include/tutorial_02/MyNode.h
#ifndef MY_NODE_H
#define MY_NODE_H

#include <ros/ros.h>
#include <std_msgs/String.h>

class MyNode
{
public:
  MyNode();
  void run();

private:
  ros::NodeHandle nh_;
  ros::Publisher pub_;
  ros::Timer timer_;
  void timerCallback(const ros::TimerEvent&);
};

#endif // MY_NODE_H
```

```cpp
// src/MyNode.cpp
#include "tutorial_02/MyNode.h"

MyNode::MyNode()
{
  pub_ = nh_.advertise<std_msgs::String>("chatter", 10);
  timer_ = nh_.createTimer(ros::Duration(1.0), &MyNode::timerCallback, this);
}

void MyNode::timerCallback(const ros::TimerEvent&)
{
  std_msgs::String msg;
  msg.data = "Hello from class-based node";
  pub_.publish(msg);
  ROS_INFO("%s", msg.data.c_str());
}

void MyNode::run()
{
  ros::spin();
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "my_node");
  MyNode node;
  node.run();
  return 0;
}
```

3. **Use Parameter Server**: Configure your nodes using ROS parameters:

```cpp
std::string topic_name;
nh_.param<std::string>("topic_name", topic_name, "default_topic");
```

4. **Handle Errors Properly**: Use try-catch blocks for error handling.

5. **Use ROS Logging**: Utilize ROS_DEBUG, ROS_INFO, ROS_WARN, ROS_ERROR, and ROS_FATAL.

6. **Write Unit Tests**: Use gtest with rostest for testing.

## Conclusion

This tutorial has guided you through creating a ROS package with C++ (roscpp), managing dependencies, and using vcstool for repository management. By following these steps, you'll be able to create modular, maintainable, and efficient ROS packages for your robotics projects.