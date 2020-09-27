## Ghibra简介
Ghidra是由美国国家安全局（NSA）研究部门开发的软件逆向工程（SRE）套件，用于静态分析文件，支持多种平台文件的反汇编以及反编译。针对IDA和Ghibra的详细区别见文章：
~~~
https://zhuanlan.zhihu.com/p/59637690
~~~
## Ghibra编译安装（linux）
### 一：安装JDK11
#### 1:下载JDK11
需要说明的是不要下载最新版本的JDK，最好按照官方指定的JDK11版本，因为会影响后面使用脚本安装依赖，当然后面如果不使用脚本安装依赖的话就大于等于JDK11即可。
~~~
安装地址：https://adoptopenjdk.net/releases.html?variant=openjdk11&jvmVariant=hotspot
安装说明：根据自己的系统选择安装文件，之后解压到目录即可。
~~~
#### 2:设置环境变量
~~~
//打开文件
sudo vim ~/.bashrc

// 在文件最后添加：
export PATH=<JDK中bin所在目录>:$PATH   //export PATH=/opt/jdk11/bin:$PATH

//保存文件
:wq       

//确认是否设置成功
echo $PATH 
~~~

### 二：安装Gradle5.0
#### 1:下载地址
~~~
https://gradle.org/next-steps/?version=5.0&format=bin
~~~
#### 2:设置环境变量
同安装JDK设置相同，不同的只是在文件末尾追加如下一行保存即可(注意输出$PATH确认)：
~~~
export PATH=<Gradle的bin目录>:$PATH //export PATH=/opt/gradle-5.0/bin:$PATH
~~~
~~~
gradle --version //查看gradle版本，在其列出的信息中同时查看Jdk的版本是否为11
~~~

### 三：安装Eclipse
#### 1:下载地址
~~~
https://www.eclipse.org/downloads/packages/
解压到指定目录,之后运行eclipse即可
~~~
#### 2:确认Eclipse使用JDK版本确实是11
~~~
Window -> Prefereces -> Java -> Installed JREs 即可看到IDE所使用的JDK版本
~~~

### 四：Clone仓库
#### 1:配置Git
~~~
在Clone的时候如果提示没有密钥，需要参考如下文章进行SSH配置
https://docs.github.com/cn/free-pro-team@latest/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account
~~~
#### 2:Clone源码
~~~
bash
mkdir ~/git
cd ~/git
git clone git@github.com:NationalSecurityAgency/ghidra.git
~~~

### 五：安装依赖
#### 1:脚本自动安装
在clone的git仓库源码中提供了安装依赖的一个自动化安装脚本。
~~~
gradle --init-script gradle/support/fetchDependencies.gradle init
~~~
如果上述命令执行成功，则会在仓库生成~/git/ghidra/flatRepo/这样一个目录，其中包含如下内容
~~~
 * AXMLPrinter2
 * csframework
 * dex-ir-2.0
 * dex-reader-2.0
 * dex-reader-api-2.0
 * dex-tools-2.0
 * dex-translator-2.0
 * dex-writer-2.0
 * hfsx
 * hfsx_dmglib
 * iharder-base64
 * ~/git/ghidra/Ghidra/Features/GhidraServer/build/yajsw-stable-12.12.zip
 * ~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/PyDev 6.3.1.zip
 * ~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/cdt-8.6.0.zip
~~~
之前说的使用SDK高版本的在这里会出问题（只测试了SDK15其他的没有试过），使用官方指定SDK11不会出问题，但是由于下载速度太慢，我选择了手动安装。

#### 2:手动安装
这里就直接复制官方文档说明了，傻瓜式安装即可，Downloads目录是一个临时的下载目录，用于下载相应的文件之后复制到指定文件。
~~~
Get Dependencies for FileFormats:

Download `dex-tools-2.0.zip` from the dex2jar project's releases page on GitHub.
Unpack the `dex-*.jar` files from the `lib` directory to `~/git/ghidra/flatRepo`:

```bash
cd ~/Downloads   # Or wherever
curl -OL https://github.com/pxb1988/dex2jar/releases/download/2.0/dex-tools-2.0.zip
unzip dex-tools-2.0.zip
cp dex2jar-2.0/lib/dex-*.jar ~/git/ghidra/flatRepo/

```

Download `AXMLPrinter2.jar` from the "android4me" archive on code.google.com.
Place it in `~/git/ghidra/flatRepo`:

```bash
cd ~/git/ghidra/flatRepo
curl -OL https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/android4me/AXMLPrinter2.jar
```

Get Dependencies for DMG:

Download `hfsexplorer-0_21-bin.zip` from www.catacombae.org.
Unpack the `lib` directory to `~/git/ghidra/flatRepo`:

```bash
cd ~/Downloads   # Or wherever
curl -OL https://sourceforge.net/projects/catacombae/files/HFSExplorer/0.21/hfsexplorer-0_21-bin.zip
mkdir hfsx
cd hfsx
unzip ../hfsexplorer-0_21-bin.zip
cd lib
cp csframework.jar hfsx_dmglib.jar hfsx.jar iharder-base64.jar ~/git/ghidra/flatRepo/
```

Get Dependencies for GhidraServer

Building the GhidraServer requires "Yet another Java service wrapper" (yajsw) version 12.12.
Download `yajsw-stable-12.12.zip` from their project on www.sourceforge.net, and place it in:
`~/git/ghidra/Ghidra/Features/GhidraServer/build`:

```bash
cd ~/Downloads   # Or wherever
curl -OL https://sourceforge.net/projects/yajsw/files/yajsw/yajsw-stable-12.12/yajsw-stable-12.12.zip
mkdir -p ~/git/ghidra/Ghidra/Features/GhidraServer/build/
cp ~/Downloads/yajsw-stable-12.12.zip ~/git/ghidra/Ghidra/Features/GhidraServer/build/
```

Get Dependencies for GhidraDev

Building the GhidraDev plugin for Eclipse requires the CDT and PyDev plugins for Eclipse.
Download `cdt-8.6.0.zip` from The Eclipse Foundation, and place it in:
`~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/`:

```bash
cd ~/Downloads   # Or wherever
curl -OL 'http://www.eclipse.org/downloads/download.php?r=1&protocol=https&file=/tools/cdt/releases/8.6/cdt-8.6.0.zip'
curl -o 'cdt-8.6.0.zip.sha512' -L --retry 3 'http://www.eclipse.org/downloads/sums.php?type=sha512&file=/tools/cdt/releases/8.6/cdt-8.6.0.zip'
shasum -a 512 -c 'cdt-8.6.0.zip.sha512'
mkdir -p ~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/
cp ~/Downloads/cdt-8.6.0.zip ~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/
```

Download `PyDev 6.3.1.zip` from www.pydev.org, and place it in the same directory:

```bash
cd ~/Downloads   # Or wherever
curl -L -o 'PyDev 6.3.1.zip' https://sourceforge.net/projects/pydev/files/pydev/PyDev%206.3.1/PyDev%206.3.1.zip
cp ~/Downloads/'PyDev 6.3.1.zip' ~/git/ghidra/GhidraBuild/EclipsePlugins/GhidraDev/GhidraDevPlugin/build/
```
~~~

### 六：构建Ghibra
#### 1:使用cd切换到Ghibra根目录
#### 2:使用如下命令进行构建
~~~
gradle buildGhidra
~~~
#### 3:可能会出现的问题
~~~
Q:
No License specified for external library: lib/jsr305-1.3.9.jar in module K:\ghidracompile\ghidra4\Ghidra\Features\FileFormats. Expression: map.containsKey(relativePath)
A:
如上问题，可能出现问题的jar文件不一，但问题是一样的，解决方法（以上为例）就是在其所示的模块目录\Ghidra\Features\FileFormats中有一个文件名为Module.manifest，之后使用vim打开在其后追加如下：

MODULE FILE LICENSE: lib/jsr305-1.3.9.jar BSD

后续可能还会出现如上问题，只需要找到对应的Module.manifest文件在其后追加
MODULE FILE LICENSE: *.jar BSD即可
~~~
#### 4:更多问题
在寻找上面问题的解决办法的时候找到一篇github上的文章，其中包含了其他的问题描述，可自行查找。我在编译的过程中遇到53个错误100多的警告，不过不用惊慌在成功解决掉第三个问题之后就能够编译成功了！
~~~
https://github.com/NationalSecurityAgency/ghidra/issues/1368
~~~
#### 5:运行Ghrbra
编译成功之后会在```~/git/ghidra/build/dist/```目录中进行输出一个zip文件，其名称由版本，名称，构建日期以及运行平台构成，在解压之后的目录中有一个名为ghidraRun的文件，运行即可，如果能够成功打开，那么恭喜你，你就可以对Ghibra进行开发并使用了。