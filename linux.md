# 1、关于linux 文件备份的一些思考 
    近几年在linux上工作,每次切换系统或更换硬件机器都要重新搭建一次环境，尤其是各种开发相关的环境.真的好累，
    我就索性在研究如何在不同的机器上备份和迁移系统。
## 1、最早我也是将整个系统上的所有文件全部用tar 压缩，然后在目标机器上把所有文件解压 实现整个系统的备份.
    下面是操作步骤:
### 在待备份的系统上压缩所需要文件
    tar -pzcvf old_system.tar.gz --exclude=/old_system.tar.gz --exclude=/proc --exclude=/lost+found  --exclude=/mnt --exclude=/sys  /
    命令行功能解释, 在 / 目录下(cd /),压缩除了 old_system.tar.gz 、 proc 、lost+found 、mnt 、 sys 外的所有文件.
    old_system.tar.gz 是压缩后生成的文件 当然不能再压缩了
    proc 、lost+found 、mnt 、 sys 都是与当前操作系统软、硬件相关的文件不需要 在 另一台机器上还原
### 还原系统
    解压 old_system.tar.gz 到 / 目录下
    tar -pzxvf old_system.tar.gz -C /
    创建备份时排除的目录
    mkdir proc
    mkdir lost+found
    mkdir mnt
    mkdir sys
## 注：还原到本机和新机器上 会有细微差异 具体我就不详述了.(反正我已经不用这种备份方式了，具体你可以到网上搜一下，一搜一大堆)

## 2、而我的需要求仅是备份自己安装的常见应用(如IDEA，Android Studio 等IDE，firefox、chrome等应用)和开发工具(如jdk,python等)
### 1、备份
      我的所有应用包括系统应用 都安装在 /usr/share下,桌面快捷方式 在/usr/share/applications下，因此我只要把 /usr/share 整个文件压缩还原到目标机器上就行了。
      cd /
      tar -pzcvf usr_share_snap.tar.gz usr/share
      我装开发环境通常安装在 /usr/lib下，以jdk为例，我把jdk 下载后解压到 /usr/lib 目录下 然后配置环境变量。因此这个文件也有必要迁移
      tar -pzcvf usr_lib_snap.tar.gz usr/lib
      环境变量像jdk 都是在 /root 下的 .bashra 文件里，因此这个文件都需要备份迁移
      tar -pzcvf root_snap.tar.gz root
### 2、还原
      把上一步解压的文件解压复制到目标机器上,然后解压
      tar -pzxvf usr_share_snap.tar.gz -C /
      tar -pzxvf usr_lib_snap.tar.gz   -C /
      tar -pzxvf root_snap.tar.gz      -C /
      
      完成以上步骤后，重启目标系统 reboot,所有应用和部分开发环境都能使用. 试下 java -version 就知道java环境已经配置好了.
      为什么说是部分呢，因为有些用apt install gcc 安装的 gcc编译器 是装在 /usr/bin 下面的，如果想让gcc也迁称到新机上，需要用
      同样的方法压缩和解压 /usr/bin 目录。 但我感觉没必要,一来安装gcc 很简单就一条命令，二来 /usr/bin 下是系统预装的可执行程序，
      会随着系统升级而改变,我不想通过简单的复制原系统来更改这里的文件.
      
### 到这里可能有人要问为什么 不把 /usr 下的所有文件全压缩并解压覆盖目标机器的 /usr文件呢？
    其实也能行，我试过完全可以，但是这种方式可能对系统的改变有些大，尽管目前我还没发现有什么重大影响，但我还是倾向这种精细化备份迁移的方式，需要什么就迁移什么。
    你也可以用这种方式迁移 linux 下的其他数据，linux是基于文件的操作系统，只要你把所有文件包括文件的依赖关系迁过去，这些文件所执行的功能就能要新机上运行。
