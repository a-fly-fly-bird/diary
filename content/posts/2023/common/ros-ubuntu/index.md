+++
title = '什么是ROS(ROS和Ubuntu的区别)'
date = 2023-12-17T00:03:59+08:00
draft = false
categories = ['折腾']
tags = ['ROS', 'Ubuntu']
+++


## 官网

> The Robot Operating System (ROS) is a set of software libraries and tools that help you build robot applications. From drivers to state-of-the-art algorithms, and with powerful developer tools, ROS has what you need for your next robotics project. And it's all open source.

## Wiki

[Robot Operating System - Wikipedia](https://en.wikipedia.org/wiki/Robot_Operating_System)

> Robot Operating System (ROS or ros) is an open-source robotics middleware suite. Although ROS is not an operating system (OS) but a set of software frameworks for robot software development, it provides services designed for a heterogeneous computer cluster such as hardware abstraction, low-level device control, implementation of commonly used functionality, message-passing between processes, and package management. Running sets of ROS-based processes are represented in a graph architecture where processing takes place in nodes that may receive, post, and multiplex sensor data, control, state, planning, actuator, and other messages. Despite the importance of reactivity and low latency in robot control, ROS is not a real-time operating system (RTOS). However, it is possible to integrate ROS with real-time code.The lack of support for real-time systems has been addressed in the creation of ROS 2, a major revision of the ROS API which will take advantage of modern libraries and technologies for core ROS functions and add support for real-time code and embedded system hardware.

## 博客

> Ubuntu是一个以桌面应用为主的Linux操作系统，而raspbian是针对 Raspberry Pi 专门优化、基于 Debian 的 Raspbian OS。
> 
> ROS说是叫机器人操作系统，其实并不是像Ubuntu那样完整的系统，可以理解成ROS一个中间件或者一个库，它需要跑在Ubuntu系统上，或者raspbian系统上。
> 
> 树莓派是硬件，是操作系统 Ubuntu或者raspbian的载体，安装了ROS的Ubuntu系统才能使用ROS中的工具，框架等。

# 自定义消息

```
catkin_create_pkg custom_message roscpp std_msgs geometry_msgs message_generation

```

与之前不同的是，需要在packages.xml中添加如下前两行信息（注意，不是第三行，那种写法已经消失）。

```
<build_depend>message_generation</build_depend>
<exec_depend>message_generation</exec_depend>
<!-- <exec_depend>message_runtime</exec_depend> -->

```

CMakeLists.txt如下：

```
cmake_minimum_required(VERSION 3.0.2)
project(custom_message)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  message_generation
  roscpp
  std_msgs
)

# 新增
add_message_files(
  FILES
  Person.msg
)

# 新增
generate_messages(
  DEPENDENCIES
  std_msgs
)

# 新增
catkin_package(
  CATKIN_DEPENDS geometry_msgs message_generation roscpp std_msgs
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

add_executable(talker1 src/talker1.cpp)
target_link_libraries(talker1 ${catkin_LIBRARIES})

add_executable(listener1 src/listener1.cpp)
target_link_libraries(listener1 ${catkin_LIBRARIES})

```

talker代码（注意自定义消息头文件的引入方法是：功能包名 + 消息名，使用时都需要指出命名空间为功能包的名称）：

```
#include <sstream>
#include "ros/ros.h"
#include "custom_message/Person.h"
int main(int argc, char **argv)
{
    // ROS节点初始化
    ros::init(argc, argv, "talker1");
    // 创建节点句柄
    ros::NodeHandle n("hello");
    // 创建一个Publisher，发布名为chatter的topic，消息类型为custom_messages::Person
    ros::Publisher chatter_pub = n.advertise<custom_message::Person>("custom_msg", 1000);
    // 设置循环的频率
    ros::Rate loop_rate(10);
    int count = 0;
    while (ros::ok())
    {
        // 初始化std_msgs::String类型的消息
        custom_message::Person msg;
        msg.name = "Lucas Tan";
        msg.sex = custom_message::Person::unknown;
        // 发布消息
        ROS_INFO("%s", msg.name.c_str());
        chatter_pub.publish(msg);
        // 循环等待回调函数
        ros::spinOnce();
        // 按照循环频率延时
        loop_rate.sleep();
        ++count;
    }
    return 0;
}

```

listener代码：

```
#include "ros/ros.h"
#include "custom_message/Person.h"
// 接收到订阅的消息后，会进入消息回调函数
void chatterCallback(const custom_message::Person::ConstPtr& msg)
{
    // 将接收到的消息打印出来
    ROS_INFO("I heard: [%s]", msg->name.c_str());
}
int main(int argc, char **argv)
{
    // 初始化ROS节点
    ros::init(argc, argv, "listener1");
    // 创建节点句柄
    ros::NodeHandle n("hello");
    // 创建一个Subscriber，订阅名为chatter的话题，注册回调函数chatterCallback
    ros::Subscriber sub = n.subscribe("custom_msg", 1000, chatterCallback);
    // 循环等待回调函数
    ros::spin();
    return 0;
}

```

最终效果：

![Alt text](Untitled.png)

# std_msgs

通过rosmsg命令可以方便地查看有哪些msg功能包以及msg的结构。

```
root@f90027cc500f:~# rosmsg -h
rosmsg is a command-line tool for displaying information about ROS Message types.

Commands:
	rosmsg show	Show message description
	rosmsg info	Alias for rosmsg show
	rosmsg list	List all messages
	rosmsg md5	Display message md5sum
	rosmsg package	List messages in a package
	rosmsg packages	List packages that contain messages

Type rosmsg <command> -h for more detailed usage

```

比如看Image的结构（不用到处找包的位置进去看，我是🤡）

![Alt text](Untitled-2.png)

# ros::spin() 和 ros::spinOnce()区别

首先明确，这两个都是**ROS消息回调处理函数。**也就是说是用于订阅者的。

## 区别

ros::**spin()**在调用后不会再返回，也就是你的主程序到这儿就不往下执行了，而是进行下一次回调。（ros::spin()函数一般不会出现在循环中，因为程序执行到spin()后就不调用其他语句了，也就是说该循环没有任何意义）。

而 **ros::spinOnce()**后者在调用后还可以继续执行之后的程序，但往往需要考虑调用消息的时机，调用频率，以及消息池的大小。

通常和循环以及loop_rate.sleep()一起使用。

## 代码对比区别

### talker

```
#include "ros/ros.h"
#include "std_msgs/String.h"
// Why we need sstream? Because we need this to split a sentence to words.
#include <sstream>

int main(int argc, char **argv)
{
    // init, can not have namespace
    ros::init(argc, argv, "talker");
    // start, this is a c11 initialization mode
    ros::NodeHandle n("namespace");
    // Define publisher's topic name and msg type.
    ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);
    // set loop rate(per sec)
    ros::Rate loop_rate(10);
    int count = 0;
    // ros::ok()在以下几种情况下会返回false, 按下Ctrl-C时。我们被一个同名同姓的节点从网络中踢出。ros::shutdown()被应用程序的另一部分调用。所有的ros::NodeHandles都被销毁了。
    while (ros::ok())
    {
        std_msgs::String msg;
        std::stringstream ss;
        ss << "hello world " << count;
        msg.data = ss.str();
        ROS_INFO("%s", msg.data.c_str());
        /**
         * 向 Topic: chatter 发送消息, 发送频率为10Hz（1秒发10次）；消息池最大容量1000。
         */
        chatter_pub.publish(msg);
        // 如果运行到下一次loop.sleep()后未达到设置的时间，则会开始休眠，等到后再执行下一句
        loop_rate.sleep();
        ++count;
    }
    return 0;
}

```

### listener：spin()

```
#include "ros/ros.h"
#include "std_msgs/String.h"

// subscriber callback function
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
    ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
    ros::init(argc, argv, "listener");
    ros::NodeHandle n("namespace");
    ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

    /**
     * ros::spin() 将会进入循环， 一直调用回调函数chatterCallback(),每次调用1000个数据。
     * 当用户输入Ctrl+C或者ROS主进程关闭时退出，
     */
    ros::spin();
    return 0;
}

```

### listener：spinOnce()

```
#include "ros/ros.h"
#include "std_msgs/String.h"

// subscriber callback function
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
    ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
    ros::init(argc, argv, "listener");
    ros::NodeHandle n("namespace");
    ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

    ros::Rate loop_rate(5);
    while(ros::ok()){
        ros::spinOnce();
        loop_rate.sleep();
    }
    return 0;
}

```

# Ros & OpenCV

## ros to opencv

要理解，核心是在opencv。ros只是一个输入的工具。

```
#include <ros/ros.h>
// Using image_transport for publishing and subscribing to images in ROS allows you to subscribe to compressed image streams.
#include <image_transport/image_transport.h>
#include <cv_bridge/cv_bridge.h>
// some useful constants and functions related to image encodings.
#include <sensor_msgs/image_encodings.h>
// OpenCV's image processing and GUI modules.
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui.hpp>

static const std::string OPENCV_WINDOW = "Image window";

class ImageConverter
{
    ros::NodeHandle nh_;
    // image_transport类：图像传输类，其功能和ROS中的Publisher和Subscriber差不多，但是不同的是这个类在发布和订阅图片消息的同时还附带这摄像头的信息。
    // 相比较之下, 在ROS中传送图片信息，使用image_transport类要高效的多。
    image_transport::ImageTransport it_;
    image_transport::Subscriber image_sub_;
    image_transport::Publisher image_pub_;

public:
    ImageConverter()
        : it_(nh_)
    {
        // Subscrive to input video feed and publish output video feed
        image_sub_ = it_.subscribe("/camera/image_raw", 1,
                                   &ImageConverter::imageCb, this);
        image_pub_ = it_.advertise("/image_converter/output_video", 1);

        // OpenCV HighGUI calls to create/destroy a display window on start-up/shutdown.
        cv::namedWindow(OPENCV_WINDOW);
    }

    ~ImageConverter()
    {
        cv::destroyWindow(OPENCV_WINDOW);
    }

    void imageCb(const sensor_msgs::ImageConstPtr &msg)
    {
        // CvBridge defines a CvImage type containing an OpenCV image, its encoding and a ROS header.
        // 中文说的话就是： cv_bidge::CvImage类：cv_bridge中提供的数据结构，里面包括OpenCV中的cv::Mat类型的图像信息，图像编码方式，ROS头文件等等。
        cv_bridge::CvImagePtr cv_ptr;
        try
        {
            // Note that OpenCV expects color images to use BGR channel order.
            cv_ptr = cv_bridge::toCvCopy(msg, sensor_msgs::image_encodings::BGR8);
        }
        catch (cv_bridge::Exception &e)
        {
            ROS_ERROR("cv_bridge exception: %s", e.what());
            return;
        }

        // Draw an example circle on the video stream
        if (cv_ptr->image.rows > 60 && cv_ptr->image.cols > 60)
            cv::circle(cv_ptr->image, cv::Point(50, 50), 10, CV_RGB(255, 0, 0));

        // Update GUI Window
        cv::imshow(OPENCV_WINDOW, cv_ptr->image);
        cv::waitKey(3);

        // Output modified video stream
        image_pub_.publish(cv_ptr->toImageMsg());
    }
};

int main(int argc, char **argv)
{
    ros::init(argc, argv, "image_converter");
    ImageConverter ic;
    ros::spin();
    return 0;
}

```

对应的CMakeLists：

```
cmake_minimum_required(VERSION 3.0.2)
project(opencv_study)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  sensor_msgs
  cv_bridge
  std_msgs
  image_transport
)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES opencv_study
#  CATKIN_DEPENDS roscpp rospy
#  DEPENDS system_lib
)

include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)

add_executable(image_converter src/image_converter.cpp)
target_link_libraries(image_converter ${catkin_LIBRARIES})

```

## **话题名称重映射**

```
rosrun opencv_study image_converter /camera/image_raw:=/camera/color/image_raw

```

这句话将原来订阅的话题`/camera/image_raw`改名为`/camera/color/image_raw`。

`ros run` 后面的参数的意义：

- `__ns:=` : 更改节点的命名空间
- `__name:=`:更改节点的名称
- `A:=B`:话题重映射，`A → B`

## opencv to tos

```
#include <iostream>
#include <string>
#include <sstream>
using namespace std;

// OpenCV includes
#include <opencv2/video.hpp>
#include <opencv2/opencv.hpp>
#include <opencv2/core/core.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/calib3d/calib3d.hpp>
#include <opencv2/highgui/highgui.hpp>
#include "opencv2/imgcodecs/legacy/constants_c.h"

using namespace cv;

#include <ros/ros.h>
#include <cv_bridge/cv_bridge.h>
#include <image_transport/image_transport.h>
#include <sensor_msgs/image_encodings.h>

int main(int argc, char **argv)
{
	ros::init(argc, argv, "image_color");
	ros::NodeHandle nh;
	image_transport::ImageTransport it(nh);
	image_transport::Publisher pub = it.advertise("/camera_sim/image_raw", 1);
	/**************ROS与Opencv图像转换***********************/
	Mat image = imread("/home/agilex/Desktop/ty/catkin_ws/src/scout_work_1/src/index.jpeg", CV_LOAD_IMAGE_COLOR);
	sensor_msgs::ImagePtr msg = cv_bridge::CvImage(std_msgs::Header(), "bgr8", image).toImageMsg();
	ros::Rate loop_rate(5);
	while (nh.ok())
	{
		pub.publish(msg);
		ros::spinOnce();
		loop_rate.sleep();
	}
	return 0;
}

```