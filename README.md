# jdk8mhb
openjdk8 building on macos for study

## 环境准备；
macOS Catalina 10.15.3
xcode 11.5 (11E608c)
brew install Ant 		//用于执行JAVA编译代码中的Ant脚本
brew install llvm
brew install freetype  
XQuartz XQuartz 2.7.11（xorg-server 1.18.4）:XQuartz:https://www.xquartz.org/

## 开始编译
### 1、设置环境变量,我这里是zsh vim ~/.zshrc，修改之后source ~/.zshrc
```
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

### 2.Configure，我这里选择slow-debug版本
```
//release版本
bash ./configure  --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib/

//Xcode可断点调试的slow-debug版本   --with-boot-jdk:Java路径 
bash ./configure --enable-debug --with-target-bits=64 --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home CXX=clang++
```


