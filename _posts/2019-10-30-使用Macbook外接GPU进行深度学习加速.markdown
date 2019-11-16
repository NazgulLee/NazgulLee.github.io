

* 电脑：MacBook Air early 2015、
* 显卡坞：Akitio Node Thunderbolt3
* 显卡：GIGABYTE GeForce GTX 1060
* 其他：Thunderbolt3到Thunderbolt2的转接器、闪迪U盘32GB

### 安装HighSierra
深度学习用到的GPU加速库是英伟达的Cuda，需配合英伟达的GPU使用。在Mac上外接英伟达的GPU需要安装对应的驱动，但是英伟达提供的驱动支持的macOS版本最高只到HighSierra。我的Air系统是卡特琳娜，需要降级到HighSierra，只能重装系统。

#### 警告：安装HighSierra后只能安装老版的Xcode，需要去[官网](https://developer.apple.com/download/more/)下载Xcode的压缩包解压安装，但可能会出现压缩包证书过期无法解压的情况，会弹窗说“Xcode.xip不是来自apple”，解决方法是，把系统时间调前。参考[Xcode xip "The archive does not come from Apple"](https://forums.developer.apple.com/thread/125108)

![](../resources/post1/xcode_unzip_failed.png)

#### 备份电脑
请参考apple官方文档[使用timemachine备份Mac](https://support.apple.com/zh-cn/HT201250)

将外接存储设备连接到Mac，打开系统设置->时间机器->选择备份磁盘->使用磁盘。

#### 创建可引导的macOS安装器
请参考apple官方文档[如何创建可引导的 macOS 安装器](https://support.apple.com/zh-cn/HT201372)、 [如何抹掉 Mac 磁盘](https://support.apple.com/zh-cn/HT208496)、[如何升级到 macOS High Sierra](https://support.apple.com/zh-cn/HT208969)

1. 下载HighSierra。用Safari打开[链接](https://itunes.apple.com/cn/app/macos-high-sierra/id1246284741?ls=1&mt=12)，Safari会调起AppStore，跳转到HighSierra下载页面，下载完成后，不要安装。此时，在应用程序文件夹下能找到形如`安装 macOS High Sierra.app`的安装器。参见[如何创建可引导的 macOS 安装器](https://support.apple.com/zh-cn/HT201372)、[如何升级到 macOS High Sierra](https://support.apple.com/zh-cn/HT208969)。
2. 准备用于可引导安装器的 USB 闪存盘。将一个至少12GB容量的USB闪存盘连接到Mac，打开`磁盘工具`app，在左侧选择要抹掉的闪存盘，在右侧点击抹掉，名称输入`MyVolume`，格式选取`Mac OS 扩展（日志式）`，点击抹掉，完成后，这个闪存盘就可以用来创建macOS安装器了。参见[如何抹掉 Mac 磁盘](https://support.apple.com/zh-cn/HT208496)。
3. 在终端执行
```
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```
提示输入密码->输入密码->提示是否确认抹掉宗卷->输入`Y`确认。命令完成后，U盘的名称将显示为`安装 macOS High Sierra`。现在可以退出“终端”并弹出宗卷。参见[如何创建可引导的 macOS 安装器](https://support.apple.com/zh-cn/HT201372)。

#### 安装新系统
1. 将可引导的macOS安装器连接到Mac，重新启动电脑，在键盘灯熄灭后，按住`cmd`+`R`直到苹果logo出现，等待读条完成，然后就会进入恢复模式。
2. 点击右上角的Wi-Fi标志，选择Wi-Fi并输入密码，使Mac能访问互联网。
3. 选择macOS实用工具里的磁盘工具，点击继续，在左侧选择Mac的内置磁盘，一般名字默认为`Macintosh HD`，选择抹掉，输入名称，选择格式APFS，点击抹掉。参见[如何抹掉 Mac 磁盘](https://support.apple.com/zh-cn/HT208496)。完成后点击左上角磁盘工具->退出磁盘工具。
4. 选择macOS实用工具里的重新安装macOS，继续。选择`安装 macOS High Sierra`，一路继续即可。

### 外接GPU
请参考[purge-wrangler](https://github.com/mayankk2308/purge-wrangler)。

注意：`purge-wrangler`需要macOS版本在10.13.4及以上，若不满足，请参考[THE BEGINNER’S EXTERNAL GRAPHICS CARD SETUP GUIDE FOR MAC](https://egpu.io/setup-guide-external-graphics-card-mac/)以及[egpu.io](https://egpu.io)顶部菜单栏和论坛里的更多资料。

1. 解除System Integrity Protection。重启系统，在键盘灯熄灭后，按住`cmd`+`R`直到苹果logo出现，进入恢复模式。点击菜单栏实用工具->终端，执行`csrutil disable; reboot`
2. 下载安装`purge-wrangler`。在终端执行命令
```
curl -qLs $(curl -qLs https://bit.ly/2WtIESm | grep '"browser_download_url":' | cut -d'"' -f4) > purge-wrangler.sh; bash purge-wrangler.sh; rm purge-wrangler.sh
```
3. 在终端执行命令`purge-wrangler`，出现提示后，选择Setup eGPU，按照提示一步一步进行即可。

注意：使用`purge-wrangler`安装的英伟达显卡不能热拔。MacBook Air在启动时如果连接了外接GPU，就无法正常启动，只能启动后热插。

可在关于本机->系统报告->图形卡/显示器查看是否成功：

![](../resources/post1/egpu.png)


### 安装可使用GPU加速的PyTorch
1. 下载安装[command line tools (macOS 10.13) for Xcode 9.2](https://download.developer.apple.com/Developer_Tools/Command_Line_Tools_macOS_10.13_for_Xcode_9.2/Command_Line_Tools_macOS_10.13_for_Xcode_9.2.dmg)。如链接失效，请自行前往[https://developer.apple.com/download/more/](https://developer.apple.com/download/more/)搜索下载。下载安装完成后，命令行执行`/usr/bin/cc --version`确认是Apple LVVM 9.0.0。
2. 前往[官网](https://developer.nvidia.com/cuda-92-download-archive?target_os=MacOSX&target_arch=x86_64&target_version=1013)下载安装CUDA9.2。安装完成后，在命令行执行`export PATH=/Developer/NVIDIA/CUDA-9.2/bin:$PATH`。
3. 前往[官网](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.2.1/prod/9.2_20180806/cudnn-9.2-osx-x64-v7.2.1.38)下载cuDNN version 7.2.1。解压：`tar -xzvf cudnn-9.2-osx-x64-v7.tgz`。拷贝文件到指定目录下：`sudo cp cuda/include/cudnn.h /usr/local/cuda/include && cp cuda/lib/libcudnn* /usr/local/cuda/lib && chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib/libcudnn*`。
4. 下载安装[Anaconda 3.7 version](https://www.anaconda.com/distribution/#download-section)。
5. 从源码编译安装[PyTorch](https://github.com/pytorch/pytorch)。创建名为ptc的conda新环境：`conda create --name ptc`；导出路径：`export CMAKE_PREFIX_PATH=~/anaconda3/envs/ptc`；激活环境：`source activate ptc`；安装依赖：`conda install numpy ninja pyyaml mkl mkl-include setuptools cmake cffi typing`。克隆PyTorch代码：

```
git clone --recursive https://github.com/pytorch/pytorch

cd pytorch

git submodule sync

git submodule update --init --recursive

```
编译安装：

```

export MACOSX_DEPLOYMENT_TARGET=10.9 CC=clang CXX=clang++ 

python setup.py install

```
编译安装需要很久时间，在我的Air上花了超过10个小时。

参考：[Accelerated Deep Learning on a MacBook with PyTorch: the eGPU (NVIDIA Titan XP)](https://medium.com/@janne.spijkervet/accelerated-deep-learning-on-a-macbook-with-pytorch-the-egpu-nvidia-titan-xp-3eb380548d91)

### 大功告成
在命令行执行命令`python`，执行python代码：

```
import torch
torch.cuda.is_available()
print(torch.cuda.get_device_name(0))
d = torch.device("cuda")
x = torch.tensor([1.0, 2.0]).to(d)
```
在我的电脑上运行结果如下：

![](../resources/post1/pytorch_check.png)