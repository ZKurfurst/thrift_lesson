### 一、项目描述
#### 游戏匹配服务
![image](https://github.com/ZKurfurst/thrift_lesson/assets/105918870/bbd7008c-1bb2-4e1e-834c-1d79ac60a711)
- 服务共分为三部分：game、match_system、save_server
- game是用python实现的匹配客户端，可供操作有添加用户和删除用户，具体的操作实现需要调用Match_server
- match_system由两部分组成，分别为match_server和save_client，match_server为匹配的服务端，用于接收match_client的操作，并将用户添加到用户池,当用户池的数量>=2时开始匹配，匹配规则后面会说明
- save_server为保存数据的服务端，match_system匹配好两名用户后上传这两名用户的信息到save_server中(save_server调用的是远程服务器的接口)
### 二、项目完成流程
- 构建match.thrift远程通信接口:定义远程传输类User和接口Match(具体样例https://raw.githubusercontent.com/apache/thrift/HEAD/tutorial/tutorial.thrift)
- 创建match_system，用命令thrift -r --gen cpp ../../thrift/match.thrift创建通信所需接口，实现具体的add和remove业务
- 创建game,用命令thrift -r --gen py ../../thrift/match.thrift创建通信所需接口,删除服务端，参考https://thrift.apache.org/tutorial/py.html创建匹配客户端
- 构建save.thrift远程通信接口
- 在match_system中用命令thrift -r --gen cpp ../../thrift/save.thrift创建通信所需接口，删除创建的服务端，参照https://thrift.apache.org/tutorial/cpp.html实现多线程的保存数据客户端，调用远程接口save_data实现保存数据
### 三、具体细节
- 编译C++命令：g++ -c \<filename\>.cpp
- 链接C++命令：g++ *.o -o main -需要的库文件，本项目为g++ *o -o main -lthrift -pthread
- 运行C++代码：./<链接的文件名>  运行python代码：python3 \<filename\>
- 匹配规则：每次尝试匹配时将所有用户的匹配次数wt+1，遍历每个用户，如果两个用户的wt*50>=他们的分数之差，则匹配成功
