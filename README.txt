check_redis_cluster.sh 批量检查集群所有节点工具，依赖SCAN命令，带一个参数：集群中任意一节点
clear_redis_cluster.sh 批量清空集群所有节点工具，依赖FLUSHALL命令，带一个参数：集群中任意一节点

Redis Cluster C++ Client, based on hiredis, support password and standalone, it's easy to make and use, not depends on C++11 or later.
r3c::CRedisClient is not thread safe, you can use __thread to create a object of r3c::CRedisClient for every thread.

r3c基于redis官方的c库hiredis实现，全称是redis cluster C++ client，支持redis cluster，支持密码访问。非线程安全，GCC环境可以使用__thread为每个线程创建一个r3c::CRedisClient实例。

编译链接r3c时，默认认为hiredis的安装目录为/usr/local/hiredis，
但可以在执行make时指定hiredis安装目录，如假设hiredis安装目录为/tmp/hiredis：make HIREDIS=/tmp/hiredis，
或修改Makefile中变量HIREDIS的值来指定hiredis实现的安装目录。

编译r3c成功后，将生成libr3c.a静态库，没有共享库被生成。
也可以直接将r3c.h、r3c.cpp、utils.h、utils.cpp、sha1.h和sha1.cpp几个文件加入到自己项目代码中一起编译，而不独立编译r3c。

r3c_cmd.cpp是r3c的非交互式命令行工具（command line tool），具备redis-cli的一些功能，但用法不尽相同，将逐步将覆盖redis-cli的所有功能。
r3c_test.cpp是r3c的单元测试程序（unit test），执行make test即可。
r3c_and_coroutine.cpp 在协程中使用r3c示例

编译r3c（Compile r3c）：
make
或（or）
make HIREDIS=/tmp/hiredis

安装（PREFIX指定安装目录，如果不指定则为/usr/local）：
make install
或（or）
make install PREFIX=/usr/local/r3c

执行单元测试：
make test
或（or）
make test REDIS_CLUSTER_NODES=192.168.1.31:6379,192.168.1.31:6380

生成源代码间的依赖：
make dep

关于接口：
如果传给CRedisClient的nodes参数为单个节点字符串，如192.168.1.31:6379则为单机模式，为多节点字符串时则为Redis Cluster模式。

性能测试工具：
https://github.com/eyjian/mooon/blob/master/mooon/tools/r3c_stress.cpp

单机性能数据：
r3c_stress --redis=192.168.0.88:6379 --requests=100000 --threads=20 
set:
microseconds=18867143, milliseconds=18867, seconds=18
total: 2000000, success: 2000000, failure: 0
qps: 111111

get:
microseconds=16063882, milliseconds=16063, seconds=16
total: 2000000, success: 2000000, failure: 0, not exists: 0
qps: 125000

hset:
microseconds=16134011, milliseconds=16134, seconds=16
total: 1999992, success: 1999992, failure: 0
qps: 124999

hget:
microseconds=15249201, milliseconds=15249, seconds=15
total: 2000000, success: 2000000, failure: 0, not exists: 0
qps: 133333
