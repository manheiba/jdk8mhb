# jdk8mhb
Building on macOS for Study

Base on OpenJDK8
```
   hg clone http://hg.openjdk.java.net/jdk8/jdk8 jdk8mhb
   ./get_source.sh
```
为了完成编译，针对以下文件进行了修改
- common/autoconf/generated-configure.sh
- hotspot/src/share/vm/code/relocInfo.hpp
- hotspot/src/share/vm/opto/loopPredicate.cpp
- hotspot/src/share/vm/runtime/virtualspace.cpp
- hotspot/make/bsd/makefiles/saproc.make
- jdk/make/lib/PlatformLibraries.gmk
- jdk/make/lib/Awt2dLibraries.gmk
- nashorn/make/BuildNashorn.gmk
- hotspot/src/share/vm/runtime/perfData.cpp

## 环境准备
- macOS Catalina 10.15.3
- xcode 11.5 (11E608c)
- brew install Ant 		//用于执行JAVA编译代码中的Ant脚本
- brew install llvm
- brew install freetype  
- [XQuartz](XQuartz:https://www.xquartz.org/) XQuartz 2.7.11（xorg-server 1.18.4）
- [xcode-missing-libstdc-](https://github.com/imkiwa/xcode-missing-libstdc-)
   这里需要说明的是，下载后执行install.sh命令
   - 把include/c++文件夹复制到/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
   - 把lib下面的3个文件copy放到/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib

## 开始编译
### 1、设置环境变量,我这里是zsh vim ~/.zshrc，修改之后source ~/.zshrc
```
# 为了解决乱码的问题
export LC_ALL=en_US.UTF-8
# 设定语言选项，必须设置
export LANG=C
# Mac平台，C编译器不再是GCC，而是clang
export CC=clang
export CXX=clang++
export CXXFLAGS=-stdlib=libc++
# 是否使用clang，如果使用的是GCC编译，该选项应该设置为false
export USE_CLANG=true
# 跳过clang的一些严格的语法检查，不然会将N多的警告作为Error
export COMPILER_WARNINGS_FATAL=false
# 链接时使用的参数
export LFLAGS='-Xlinker -lstdc++'
# 使用64位数据模型
export LP64=1
# 告诉编译平台是64位，不然会按照32位来编译
export ARCH_DATA_MODEL=64
# 允许自动下载依赖
export ALLOW_DOWNLOADS=true
# 并行编译的线程数，编译时长，为了不影响其他工作，可以选择2
export HOTSPOT_BUILD_JOBS=4
export PARALLEL_COMPILE_JOBS=2 #ALT_PARALLEL_COMPILE_JOBS=2
# 是否跳过与先前版本的比较
export SKIP_COMPARE_IMAGES=true
# 是否使用预编译头文件，加快编译速度
export USE_PRECOMPILED_HEADER=true
# 是否使用增量编译
export INCREMENTAL_BUILD=true
# 编译内容
export BUILD_LANGTOOL=true
export BUILD_JAXP=true
export BUILD_JAXWS=true
export BUILD_CORBA=true
export BUILD_HOTSPOT=true
export BUILD_JDK=true
# 编译版本
export SKIP_DEBUG_BUILD=true
export SKIP_FASTDEBUG_BULID=false
export DEBUG_NAME=debug
# 避开javaws和浏览器Java插件之类部分的build
export BUILD_DEPLOY=false
export BUILD_INSTALL=false

# 最后需要干掉这两个环境变量（如果你配置过），不然会发生诡异的事件
unset JAVA_HOME
unset CLASSPATH
```

### 2.Configure，我这里选择slow-debug版本,进入jdk目录，只执行下面其中一个即可
```
//release版本
bash ./configure  --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib/

//Xcode可断点调试的slow-debug版本   --with-boot-jdk:Java路径 
bash ./configure --enable-debug --with-target-bits=64 --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home CXX=clang++
```
成功后会输出
```
A new configuration has been successfully created in
/Users/[Useraname]/openjdk/jdk8mhb/build/macosx-x86_64-normal-server-fastdebug
using configure arguments '--enable-debug --with-target-bits=64 --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home CXX=clang++'.

Configuration summary:
* Debug level:    fastdebug
* JDK variant:    normal
* JVM variants:   server
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64

Tools summary:
* Boot JDK:       java version "1.8.0_202" Java(TM) SE Runtime Environment (build 1.8.0_202-b08) Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)  (at /Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home)
* C Compiler:     Apple clang version (clang-1103.0.32.59) version 11.0.3 (clang-1103.0.32.59) (at /usr/bin/clang)
* C++ Compiler:   Apple clang version (clang-1103.0.32.59) version 11.0.3 (clang-1103.0.32.59) (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   5
* Memory limit:   16384 MB
* ccache status:  installed, but disabled (version older than 3.1.4)
```

### 3.编译
进入jdk目录
执行 make all
执行成功后会有如下输出，编译后jdk目录将近3G左右，编译的过程中存在很多的Warning，直接忽略了
```
## Finished docs (build time 00:01:13)

----- Build times -------
Start 2020-07-28 15:39:08
End   2020-07-28 15:46:45
00:00:12 corba
00:00:29 demos
00:01:13 docs
00:01:54 hotspot
00:00:48 images
00:00:09 jaxp
00:00:14 jaxws
00:02:14 jdk
00:00:17 langtools
00:00:06 nashorn
00:07:37 TOTAL
-------------------------
Finished building OpenJDK for target 'all'
```

### 4.查看 java -version
编译之后的jdk在这个目录下：build/macosx-x86_64-normal-server-fastdebug/jdk
```
./build/macosx-x86_64-normal-server-fastdebug/jdk/bin/java -version

openjdk version "1.8.0-internal-fastdebug"
OpenJDK Runtime Environment (build 1.8.0-internal-fastdebug-manheiba_2020_07_28_15_38-b00)
OpenJDK 64-Bit Server VM (build 25.0-b70-fastdebug, mixed mode)
```

## 祝成功
