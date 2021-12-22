---
title: apollo6.0-python编程流程.md
date: 2021-12-23 01:37:33
tags:
- 教程
- 自动驾驶
- apollo
---

# Apollo6.0-edu版本python编程
在使用apollo6.0时发现python的用法与之前版本有不同，所以记录一下编程以及编译的方法。

<!-- more -->
## 系统环境
系统环境：Ubuntu 18.04
apollo版本：apollo6.0-edu

## 代码参考
- 代码参考：
```
    apollo/modules/tools/mock_routing/mock_routing_request.py
    apollo/modules/tools/mock_routing/BUILD
```
- 参考这个代码可以创建一个cyber节点并且往设定的通道中发送指定信息。

## 导入模块：
```
    from cyber.python.cyber_py3 import cyber
```
apollo中的python库为cyber.python.cyber_py3，核心的类为cyber，python中创建cyber以及创建节点对会调用到这个类。
```
    from cyber.python.cyber_py3 import cyber_time
```
cyber_time类一般是用于设定消息中的timestamp_sec属性的值。
```
    from modules.routing.proto import routing_pb2
```
导入消息的类型，相当于ros中的导入话题/服务类型。消息类型的具体结构可以在proto的定义文件中查看，在本例中导入了modules/routing/proto文件夹中的routing.proto文件

## 代码分析
```
    cyber.init()
```
cyber.init()用于初始化cyber环境，程序中如果要运行cyber的模块，需要先运行这个初始化。
```
    node = cyber.Node("mock_routing_requester")
```
创建一个cyber节点，这里属性只是输入一个节点名称。
```
   routing_request = routing_pb2.RoutingRequest()
```
创建一个RoutingRequest消息类型的变量，该消息类型结构可以为：
```
    message RoutingRequest {
      optional apollo.common.Header header = 1;
      // at least two points. The first is start point, the end is final point.
      // The routing must go through each point in waypoint.
      repeated LaneWaypoint waypoint = 2;
      repeated LaneSegment blacklisted_lane = 3;
      repeated string blacklisted_road = 4;
      optional bool broadcast = 5 [default = true];
      optional apollo.hdmap.ParkingSpace parking_space = 6 [deprecated = true];
      optional ParkingInfo parking_info = 7;
    }
```
proto消息的信息可以在routing.proto中查看
```
    routing_request.header.timestamp_sec = cyber_time.Time.now().to_sec()
    routing_request.header.module_name = 'routing_request'
    routing_request.header.sequence_num = sequence_num
```
上面几行是填充header中的数据，其中需要注意时间戳数据的设置方法：cyber_time.Time.now().to_sec()
```
    waypoint = routing_request.waypoint.add()
    waypoint.pose.x = 587696.82286
    waypoint.pose.y = 4141446.66696
    waypoint.id = '1-1'
    waypoint.s = 1

    waypoint = routing_request.waypoint.add()
    waypoint.pose.x = 586948.740120
    waypoint.pose.y = 4141171.118641
    waypoint.id = '1-1'
    waypoint.s = 80
```
由于waypoint是repeated类型（可以在routing.proto中看到）所以waypoint可以有多个数据，所以在创建waypoint时使用了add()方法。
```
    writer = node.create_writer('/apollo/routing_request',
                                routing_pb2.RoutingRequest)
```
这里调用了节点中的create_writer方法创建一个“发布者”，数据写入的通道为“/apollo/routing_request”，数据的类型为routing_pb2.RoutingRequest。
```
    writer.write(routing_request)
```
最后调用发布者的write方法将数据接入到通道中。

## 编译
程序写完后会发现直接运行python mock_routing_request.py会报错，这是因为python的环境中没有配置cyber_py的库，需要按照规定的方式编译cyber系统。cyber系统使用bazel项目编译工具进行编译，编译前需要在项目文件夹中创建一个BUILD文件。
```
  load("@rules_python//python:defs.bzl", "py_binary")
```
加载bazel中的python编译规则
```
  package(default_visibility = ["//visibility:public"])
```
设置功能包为开放的
```
    py_binary(
    name = "mock_routing_request",
    srcs = ["mock_routing_request.py"],
    deps = [
        "//cyber/python/cyber_py3:cyber",
        "//cyber/python/cyber_py3:cyber_time",
        "//modules/routing/proto:routing_py_pb2",
    ],)
```
设置程序名称、源文件、依赖即可。

最后进入到apollo的docker系统中，输入命令编译即可：
  ```
  bazel build //path/to:my_demo
  ```
对于本例输入的命令为：
```
    只进行编译
    bazel build modules/tools/mock_routing:mock_routing_request
```
或
```
    编译后运行程序
    bazel run modules/tools/mock_routing:mock_routing_request
```

## 运行程序

运行程序方法为：
```
./bazel-bin/path/to/my_demo
```
在本例中输入的命令为：
```
./bazel-bin/modules/tools/mock_routing/mock_routing_request
```