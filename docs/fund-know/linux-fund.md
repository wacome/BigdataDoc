# Linux基础

> [!NOTE]
> Linux笔记来自于dselegent的[Github笔记](https://github.com/dselegent/Learning-Notes/tree/master/Linux)

# 01 【入门篇-介绍和安装】

## 1.Linux课程介绍

### 1.1 学习方向

* linux运维工程师： 维护linux的服务器（一般大型企业）
* linux嵌入式工程师： linux做驱动开发，或者linux的嵌入式
* linux下开发项目
  ![学习方向](https://i0.hdslb.com/bfs/album/08c3b1b814156e29bf4e53e0b59f4961a9f0bc41.jpg)

### 1.2 应用领域

* 个人桌面
* 服务器应用
  * 免费，稳定，高效
* 侵入式应用
  * 机顶盒，手机，数字电视，智能家居等

### 1.3 学习进阶

1. 学习基本指令
   * 文件操作指令
   * 编辑工具
   * 用户管理
2. linux系统配置
   * 环境变量，网络配置，服务配置
3. linux环境下搭建开发环境
   * 大数据 JavaEE Python 等
4. 编写shell脚本，对Linux服务器维护
5. 安全设置，防止攻击，保证服务器正常运行，系统调优
6. 深入理解Linux，对内核有研究，掌握大型网站架构、熟悉各环节部署方法

## 2.Linux介绍及安装

### 2.1 Linux介绍

* Linux 是一款免费，开源，安全，高效，稳定，处理高斌发很强悍的操作系统
* Linux创始人——linux（林纳斯）
* Linux主要发行版本

![发行版本](https://i0.hdslb.com/bfs/album/810e414ba869dd66f88b92a607e46ba32df68a10.jpg)

### 2.2 Unix与Linux的关系

**Unix来源**

![unix来源](https://i0.hdslb.com/bfs/album/3e6990c8adb641d8ec6d9a617fc0eb37ab505715.jpg)

**Linux来源**

![Linux来源](https://i0.hdslb.com/bfs/album/d46ae88ce01f8cc54b1775c7a10378cb3d8fed6e.jpg)

**Linux与Unix关系**

![Linux与Unix关系](https://i0.hdslb.com/bfs/album/dd8ce4f9ca7bebf60a2681d69d80bcee4db33279.jpg)

**Linux与Windows关系**

![Linux与Windows关系](https://i0.hdslb.com/bfs/album/9ce17216d979da5d1afacd8669750a3815b8d787.jpg)

### 2.3 CentOS的安装

#### 2.3.1 无脑安装vmware workstation

<img src="https://i0.hdslb.com/bfs/album/439efd46a01d9dc2f79669b4c4a655142b43b6df.png" alt="image-20211112085949513" style="zoom: 67%;" />

#### 2.3.2 编辑vmware workstation 的虚拟网卡 

##### 2.3.2.1 进行便捷的远程开发需要满足的两个条件

![image-20211112090629524](https://i0.hdslb.com/bfs/album/8ec4fc0bd23e569c057782ccd3680dfab82d7ee2.png)

##### 2.3.2.2 三种类型的网卡的含义

**1) 桥接模式：虚拟机电脑在网络环境中的地位和宿主环境（开发电脑）是一样的，虚拟机可以上网，但是ip地址会变来变去，因为虚拟机的ip地址是由DHCP动态分配的**

**2) NAT模式：开发电脑（宿主环境）会通过虚拟网卡构建一个局域网，虚拟机电脑作为局域网中一个成员，由于局域网受开发电脑的控制，因此虚拟机电脑的ip地址可以是固定的，局域网中的成员（虚拟机）可以通过开发电脑（宿主环境）间接的连到外面的互联网**

**3）仅主机模式：虚拟机相当于黑户，完全和外界隔绝，因此不能上网**

![image-20211112092206452](https://i0.hdslb.com/bfs/album/49ac3fa86025844928494ca67958e16a83c2d607.png)



##### 2.3.2.3 规划局域网

##### 网段         192.168.10.xx

##### 子网掩码  255.255.255.0

##### 网关         192.168.10.2

##### 2.3.2.4 编辑虚拟网卡（大家使用10的网段，而不是使用图片中的19网段）

> 大家使用10的网段，而不是使用图片中的19网段

1. 以管理员身份打开vmware workstation

![image-20211112093605466](https://i0.hdslb.com/bfs/album/55e14e9ccb1c9cc2f79bb77c23d05fe10e470f50.png)

2. 打开VMware Workstation 编辑虚拟网卡
3. 打开VMware Workstation 编辑虚拟网卡

![](https://i0.hdslb.com/bfs/album/f0356e47a4d2a7d8a53b298b0125efaccda79bb4.png)

4. （做了第1步就不需要再做第3步）由于需要管理员权限才能修改网络配置，我们点击更改设置

![](https://i0.hdslb.com/bfs/album/112a4c4e2c6e928a191f2df2e0869e1169ee06de.png)

5. 仅保留NAT模式的虚拟网卡，并参照图片完成设置

![](https://i0.hdslb.com/bfs/album/dea824b77e46505f9f21da65e30a2617cd5f71f8.png)

6. 设置网关

![](https://i0.hdslb.com/bfs/album/aa333ee41fb6258018130ab5c3721cb6c0b412c1.png)

7. 点击确定生效

![](https://i0.hdslb.com/bfs/album/d08e96203db9cedb7f70ad9a5ec371c77e330fa4.png)

8. 直接使用windows查看虚拟网卡设置生效情况，如果没有正常生效的部分，需要我们手动修改，然后点击确定生效

![](https://i0.hdslb.com/bfs/album/09851836beeb1a97cc6c4f93f9c31c9caa06f834.png)

#### 2.3.3 安装centos的linux操作系统 

##### 2.3.3.1  创建一个虚拟机（虚拟电脑）

![image-20211112101126528](https://i0.hdslb.com/bfs/album/327bd63f00e7ea4cd0fc4702b07290d28b1a9998.png)



![image-20211112101207152](https://i0.hdslb.com/bfs/album/0e97c3b98bb8f16f9d28197228aa67d7bb322da1.png)

![image-20211112101419743](https://i0.hdslb.com/bfs/album/05c1e097d8a58ffa0718fd616b7b2b7ef80f4adb.png)

![image-20211112101633715](https://i0.hdslb.com/bfs/album/ec6bd1daafdd330d8170e41a5564dcc39cb2d514.png)

![image-20211112101910920](https://i0.hdslb.com/bfs/album/fb753f20a21ffaea28caa615a959f52a0050d757.png)

![image-20211112102039077](https://i0.hdslb.com/bfs/album/1450560a7fc552e7328d66f6dfc9b7f0e1d714d6.png)

![image-20211112102252690](https://i0.hdslb.com/bfs/album/2be2c87928f931e121fe073507ebc6d57038e948.png)

![image-20211112102338211](https://i0.hdslb.com/bfs/album/1db17867aca2be6b0ca891356c3a3f38a540c6eb.png)

##### 2.3.3.2 在虚拟机上安装centos的操作系统

![image-20211112102422321](https://i0.hdslb.com/bfs/album/cefa59adabbed2bfdb3c0d1101e5bf2611a16d74.png)

![image-20211112102523270](https://i0.hdslb.com/bfs/album/c11688ec2c99957d2c9b093e88a9ac15e9a8eef3.png)

![image-20211112102943673](https://i0.hdslb.com/bfs/album/f71ad0c435f08954acb8a7a76a59566d4e5aea54.png)



![image-20211112103018047](https://i0.hdslb.com/bfs/album/fd9b7d92cafa61c0b55568bd20ebbaa0e9b96c70.png)



![image-20211112103124685](https://i0.hdslb.com/bfs/album/80df58dff80c4bde862fd7a9b53b7250a43b38c3.png)

![image-20211112103316020](https://i0.hdslb.com/bfs/album/31167e2a0ca9b00f4e403bc93c473f858c53a5bb.png)

![image-20211112103402101](https://i0.hdslb.com/bfs/album/207a4689064acd44c452d351fa128c6913a7f940.png)

![image-20211112103428107](https://i0.hdslb.com/bfs/album/1031c8ca689bea9bc940b874f29ea6a97d942720.png)

![image-20211112152234468](https://i0.hdslb.com/bfs/album/dba8970f77a6334823236f5f93ddeaa89c035085.png)

![image-20211112103624114](https://i0.hdslb.com/bfs/album/7f4bd9b8c3f4372f53b0526c7d6a1e399d4d4902.png)

![image-20211112103706278](https://i0.hdslb.com/bfs/album/85a1f9c8023b7f7a952c1d346a5cafdfcc062150.png)



![image-20211112103733703](https://i0.hdslb.com/bfs/album/dc549197a1fed76ff0ceca28c88b26f9736046f9.png)

![image-20211112104318911](https://i0.hdslb.com/bfs/album/610ce3d9ba79d61662b2163ca2040444874812c3.png)

![image-20211112104520575](https://i0.hdslb.com/bfs/album/f8ba300fcfd03e9f5147392b6a258b83e1483b4b.png)

![image-20211112104549330](https://i0.hdslb.com/bfs/album/1efe220ba6fb8c0bd79187350964dbcc7be39217.png)

![image-20211112104611613](https://i0.hdslb.com/bfs/album/db41a79086008bfbfc183e73173e2599cfc0a3de.png)



![image-20211112105044673](https://i0.hdslb.com/bfs/album/8b887fbd050ea6598335b12d6e7b01d41a1f436e.png)

![image-20211112105126584](https://i0.hdslb.com/bfs/album/a4be3a272635f5aa0869cec1046b0d8f136d4866.png)

![image-20211112104647373](https://i0.hdslb.com/bfs/album/68e27a837c01d40e96d75c34ea10dc3166e0d3f4.png)

![image-20211112110654155](https://i0.hdslb.com/bfs/album/e71a1714c4866266136f123f9fd33b61602f53ed.png)

![image-20211112111741098](https://i0.hdslb.com/bfs/album/f1909a9859a12e23cd87e1c6278e4228a10a523a.png)



## 3.Linux基本目录机构

### 3.1 基本介绍

* Linux的文件系统采用级层式子的树状目录结构，
* 最上层是根目录“/”
* **Linux世界里，一切皆文件。**
  ![目录结构](https://i0.hdslb.com/bfs/album/9b6a7fb1fdb9796228fec46327ff0b69953ccaf0.jpg)

### 3.2 目录用途

* `/bin：` 是Binary的缩写，这个目录存放着最经常使用的命令。
* /sbin：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。
* `/home：`存放普通用户的主目录，在Linux中每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。
* `/root：`该目录为系统管理员，也称作超级权限者的用户主目录。
* /lib：系统开机所需要最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库。
* /lost+found：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。
* /etc：所有的系统管理所需要的配置文件和子目录my.conf。
* `/usr/local`：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似与windows下的program files目录。
* `/boot：`存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。
* /proc：这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息。
* /srv：service的缩写，该目录存放一些服务启动之后需要提供的数据。
* /sys：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统sysfs。
* /tmp：这个目录是用来存放一些临时文件的。
* /dev：类似windows的设备管理器，把所有的硬件用文件的形式存储。
* `/media：`linux系统会自动识别一些设备，例如U盘光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。
* `/mnt：`系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将外部的存储挂载在/mnt/上，然后进入该目录就可以查看里面的内容了。
* `/opt：`这是给主机额外安装软件所摆放的目录，如安装ORACLE数据库就可放到该目录下。默认为空。
* /usr/local：这是另一个给主机额外安装软件所安装的目录，一般是通过编译源码的方式安装的程序。
* `/var：`这个目录中存放着在不断扩充着的东西，习惯将经常被修改的目录放在这个目录下，包括各种日志文件。
* /selinux：SELinux是一种安全子系统，它能控制程序只能访问特定文件。

### 3.3 总结

1. Linux的目录中有且只有一个根目录。
2. Linux的各个目录存放的内容是规划好，不用乱放文件。
3. Linux是以文件的形式管理我们的设备，因此linux系统，一切皆为文件。
4. Linux的各个文件目录下存放什么内容，必须有一个认识。

# 02 【基础篇-vim编辑器 网络配置 远程登录】

## 1.vi和vim编辑器

### 1.1 vi和vim的基本介绍

* 所有Linux系统都会内置vi文本编辑器
* vim是vi的升级版，可以主动以字体颜色分辨语法的正确性，代码补完和编译，错误跳转等功能。

![image-20220815123840409](https://i0.hdslb.com/bfs/album/a0196d78f8e7e4af8150fc199185b84c90fc644a.png)

### 1.2 vi和vim的三种模式

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode）**，**输入模式（Insert mode）**和**底线命令模式（Last line mode）**。 这三种模式的作用分别是：

#### 1.2.1 正常模式

* 用户刚刚启动 vi/vim，便进入了正常模式。

  此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。

  以下是常用的几个命令：

  - **i** 切换到输入模式，以输入字符。
  - **x** 删除当前光标所在处的字符。
  - **:** 切换到底线命令模式，以在最底一行输入命令。

若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。

命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

#### 1.2.2 插入/编辑模式

* 在命令模式下按下i就进入了输入模式。

  在输入模式中，可以使用以下按键：

  - **字符按键以及Shift组合**，输入字符
  - **ENTER**，回车键，换行
  - **BACK SPACE**，退格键，删除光标前一个字符
  - **DEL**，删除键，删除光标后一个字符
  - **方向键**，在文本中移动光标
  - **HOME**/**END**，移动光标到行首/行尾
  - **Page Up**/**Page Down**，上/下翻页
  - **Insert**，切换光标为输入/替换模式，光标将变成竖线/下划线
  - **ESC**，退出输入模式，切换到命令模式

#### 1.2.3 命令行模式

* 在命令模式下按下:（英文冒号）就进入了底线命令模式。

  底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

  在底线命令模式中，基本的命令有（已经省略了冒号）：

  - q 退出程序
  - w 保存文件

  按ESC键可随时退出底线命令模式。

#### 1.2.4 三种模式转换示意图

![3.2vim模式转换.jpg](https://i0.hdslb.com/bfs/album/be02de7ca3ef734ffc094598a07193a7ddc56b7a.jpg)

### 1.3 vi/vim 使用实例

**使用 vi/vim 进入一般模式**

如果你想要使用 vi 来建立一个名为 runoob.txt 的文件时，你可以这样做：

```
$ vim runoob.txt
```

直接输入 **vi 文件名** 就能够进入 vi 的一般模式了。请注意，记得 vi 后面一定要加文件名，不管该文件存在与否！

![image-20220815124239030](https://i0.hdslb.com/bfs/album/6e4689bed198cb377cd7341650f9927ae4002295.png)

**按下 i 进入输入模式(也称为编辑模式)，开始编辑文字**

在一般模式之中，只要按下 i, o, a 等字符就可以进入输入模式了！

在编辑模式当中，你可以发现在左下角状态栏中会出现 –INSERT- 的字样，那就是可以输入任意字符的提示。

这个时候，键盘上除了 **Esc** 这个按键之外，其他的按键都可以视作为一般的输入按钮了，所以你可以进行任何的编辑。

![image-20220815124308987](https://i0.hdslb.com/bfs/album/cdfc99dc399b6fc3b0e83a6280eb816535e64ed7.png)

**按下 ESC 按钮回到一般模式**

好了，假设我已经按照上面的样式给他编辑完毕了，那么应该要如何退出呢？是的！没错！就是给他按下 **Esc** 这个按钮即可！马上你就会发现画面左下角的 – INSERT – 不见了！

**在一般模式中按下 :wq 储存后离开 vi**

OK，我们要存档了，存盘并离开的指令很简单，输入 **:wq** 即可保存离开！

### 1.4 Vim 按键说明

除了上面简易范例的 i, Esc, :wq 之外，其实 vim 还有非常多的按键可以使用。

#### 1.4.1 一般模式的光标移动、搜索替换、复制粘贴

| 移动光标的方法         |                                                              |
| :--------------------- | ------------------------------------------------------------ |
| ==h 或 向左箭头键(←)== | ==光标向左移动一个字符==                                     |
| ==j 或 向下箭头键(↓)== | ==光标向下移动一个字符==                                     |
| ==k 或 向上箭头键(↑)== | ==光标向上移动一个字符==                                     |
| ==l 或 向右箭头键(→)== | ==光标向右移动一个字符==                                     |
| [Ctrl] + [f]           | 屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)          |
| [Ctrl] + [b]           | 屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)           |
| [Ctrl] + [d]           | 屏幕『向下』移动半页                                         |
| [Ctrl] + [u]           | 屏幕『向上』移动半页                                         |
| +                      | 光标移动到非空格符的下一行                                   |
| -                      | 光标移动到非空格符的上一行                                   |
| ==`n<space>`==         | ==那个 n 表示『数字』，例如 20 。按下数字后再按空格键，光标会向右移动这一行的 n 个字符。例如 `20<space>` 则光标会向后面移动 20 个字符距离。== |
| 0 或功能键[Home]       | 这是数字『 0 』：移动到这一行的最前面字符处 (常用)           |
| $ 或功能键[End]        | 移动到这一行的最后面字符处(常用)                             |
| H                      | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                      | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                      | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| G                      | 移动到这个档案的最后一行(常用)                               |
| nG                     | n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu) |
| gg                     | 移动到这个档案的第一行，相当于 1G 啊！ (常用)                |
| ==`n<Enter>`==         | ==n 为数字。光标向下移动 n 行(常用)==                        |

| 搜索      |                                                              |
| :-------- | ------------------------------------------------------------ |
| ==/word== | ==向光标之下寻找一个名称为 word 的字符串。例如要在档案内搜寻 vbird 这个字符串，就输入 /vbird 即可！ (常用)== |
| `?word    | 向光标之上寻找一个字符串名称为 word 的字符串。               |
| /^abc     | 查找以 abc 为行首的行                                        |
| /abc$     | 查找以 abc 为行尾的行                                        |
| n         | ==这个 n 是英文按键。代表重复前一个搜寻的动作。举例来说， 如果刚刚我们执行 /vbird 去向下搜寻 vbird 这个字符串，则按下 n 后，会向下继续搜寻下一个名称为 vbird 的字符串。如果是执行 ?vbird 的话，那么按下 n 则会向上继续搜寻名称为 vbird 的字符串！== |
| ==N==     | ==这个 N 是英文按键。与 n 刚好相反，为『反向』进行前一个搜寻动作。 例如 /vbird 后，按下 N 则表示『向上』搜寻 vbird 。== |

在查找过程中需要注意的是，要查找的字符串是严格区分大小写的，如查找 "shenchao" 和 "ShenChao" 会得到不同的结果。

如果想忽略大小写，则输入命令 ":set ic"；调整回来输入":set noic"。

如果在字符串中出现特殊符号，则需要加上转义字符 "\"。常见的特殊符号有 \、*、?、$ 等。如果出现这些字符，例如，要查找字符串 "10$"，则需要在命令模式中输入 "/10\$"。

| 替换文本        | 功能描述                                                     |
| --------------- | ------------------------------------------------------------ |
| r               | 替换光标所在位置的字符                                       |
| R               | 从光标所在位置开始替换字符，其输入内容会覆盖掉后面等长的文本内容，按“Esc”可以结束 |
| :s/a1/a2/g      | 将当前光标所在行中的所有 a1 用 a2 替换                       |
| :n1,n2s/a1/a2/g | 将文件中 n1 到 n2 行中所有 a1 都用 a2 替换                   |
| :g/a1/a2/g      | 将文件中所有的 a1 都用 a2 替换例如，要将某文件中所有的 "root" 替换为 "liudehua"，则有两种输入命令，分别为： |

```bash
:1, $s/root/liudehua/g
或
:%s/root/liudehua/g
```

上述命令是在编辑模式下操作的，表示的是从第一行到最后一行，即全文查找 "root"，然后替换成 "liudehua"。

如果刚才的命令变成 `:10,20 s/root/liudehua/g`，则只替换从第 10 行到第 20 行的 "root"。

| 复制粘贴  | 功能描述                                                   |
| --------- | ---------------------------------------------------------- |
| p         | 将剪贴板中的内容粘贴到光标后                               |
| P（大写） | 将剪贴板中的内容粘贴到光标前                               |
| y         | 复制已选中的文本到剪贴板                                   |
| yy        | 将光标所在行复制到剪贴板，此命令前可以加数字 n，可复制多行 |
| yw        | 将光标位置的单词复制到剪贴板                               |

| 删除文本 | 功能描述                               |
| -------- | -------------------------------------- |
| x        | 删除光标所在位置的字符                 |
| dd       | 删除光标所在行                         |
| ndd      | 删除当前行（包括此行）后 n 行文本      |
| dG       | 删除光标所在行一直到文件末尾的所有内容 |
| D        | 删除光标位置到行尾的内容               |
| :a1,a2d  | 函数从 a1 行到 a2 行的文本内容         |

> 注意，被删除的内容并没有真正删除，都放在了剪贴板中。将光标移动到指定位置处，按下 "p" 键，就可以将刚才删除的内容又粘贴到此处。

#### 1.4.2 一般模式切换到编辑模式

| 进入输入或取代的编辑模式 |                                                              |
| :----------------------- | ------------------------------------------------------------ |
| ==i, I==                 | ==进入输入模式(Insert mode)： i 为『从目前光标所在处输入』， I 为『在目前所在行的第一个非空格符处开始输入』。 (常用)== |
| a, A                     | 进入输入模式(Insert mode)： a 为『从目前光标所在的下一个字符处开始输入』， A 为『从光标所在行的最后一个字符处开始输入』。(常用) |
| o, O                     | 进入输入模式(Insert mode)： 这是英文字母 o 的大小写。o 为在目前光标所在的下一行处输入新的一行； O 为在目前光标所在的上一行处输入新的一行！(常用) |
| r, R                     | 进入取代模式(Replace mode)： r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止；(常用) |
| ==[Esc]==                | ==退出编辑模式，回到一般模式中(常用)==                       |

#### 1.4.3 一般模式切换到指令行模式

| 指令行的储存、离开等指令                                     |                                                              |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| ==:w==                                                       | ==将编辑的数据写入硬盘档案中(常用)==                         |
| ==:w!==                                                      | ==若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入， 还是跟你对该档案的档案权限有关啊！== |
| ==:q==                                                       | ==离开 vi (常用)==                                           |
| ==:q!==                                                      | ==若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。== |
| 注意一下啊，那个惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～ |                                                              |
| ==:wq==                                                      | ==储存后离开，若为 :wq! 则为强制储存后离开 (常用)==          |
| ==ZZ==                                                       | ==这是大写的 Z 喔！如果修改过，保存当前文件，然后退出！效果等同于(保存并退出)== |
| ZQ                                                           | 不保存，强制退出。效果等同于 **:q!**。                       |
| :w [filename]                                                | 将编辑的数据储存成另一个档案（类似另存新档）                 |
| :r [filename]                                                | 在编辑的数据中，读入另一个档案的数据。亦即将 『filename』 这个档案内容加到游标所在行后面 |
| :n1,n2 w [filename]                                          | 将 n1 到 n2 的内容储存成 filename 这个档案。                 |
| :! command                                                   | 暂时离开 vi 到指令行模式下执行 command 的显示结果！例如 『:! ls /home』即可在 vi 当中察看 /home 底下以 ls 输出的档案信息！ |

| vim 环境的变更 |                                                        |
| :------------- | ------------------------------------------------------ |
| ==:set nu==    | ==显示行号，设定之后，会在每一行的前缀显示该行的行号== |
| :set nonu      | 与 set nu 相反，为取消行号！                           |

### 1.5 总结Vim 的常见指令

**必须掌握**

1. yy：复制光标当前一行
2. 5yy：拷贝当前5行
3. p：箭头移动到目的行粘贴
4. u：撤销上一步
5. dd：删除当前行
6. 5dd：删除当前行向下的5行
7. x：剪切一个字母，相当于del
8. X：剪切一个字母，相当于退格键
9. yw：复制一个词
10. dw：删除一个词
11. 在文件中查找某个单词：命令行输入 /（查找内容），按n查找下一个
12. 设置文件行号：set nu，取消文件行号：set nonu
13. 编辑文件，正常模式下使用快捷键到达文档最末行：G，最首行：gg
14. 编辑文件，光标移动到某行：shift+g
    * 显示行号：set nu
    * 输入行号这个数
    * 输入shift+g
15. w：向前移动一个单词（光标停在单词首部）
16. b：向后移动一个单词 2b 向后移动2个单词    
                       

**插入命令**

| 指令 | 说明                 |
| ---- | -------------------- |
| i    | 在当前位置生前插入   |
| I    | 在当前行首插入       |
| a    | 在当前位置后插入     |
| A    | 在当前行尾插入       |
| o    | 在当前行之后插入一行 |
| O    | 在当前行之前插入一行 |

**游标移动**

| 指令            | 说明                                                      |
| --------------- | --------------------------------------------------------- |
| gg              | 移动到文件头。 = [[                                       |
| G（shift + g）  | 移动到文件尾。 = ]]                                       |
| 行數 → G        | 移動到第 n 行                                             |
| 冒号+行号，回车 | 比如跳到240行就是 :240回车                                |
| h               | 左移一个字符                                              |
| l               | 右移一个字符，这个命令很少用，一般用w代替。               |
| k               | 上移一个字符                                              |
| j               | 下移一个字符                                              |
| w               | 向前移动一个单词（光标停在单词首部）                      |
| b               | 向后移动一个单词 2b 向后移动2个单词                       |
| e               | 同w，只不过是光标停在单词尾部                             |
| ge              | 同b，光标停在单词尾部。                                   |
| ^               | 移动到本行第一个非空白字符上。                            |
| 0               | 移动到本行第一个字符上                                    |
| HOME            | 移动到本行第一个字符。同0健。                             |
| $               | 移动到行尾 3$ 移动到下面3行的行尾                         |
| f（find）       | fx将找到光标后第一个为x的字符，3fd将找到第三个为d的字符。 |
| F               | 同f，反向查找                                             |

**撤销和重做**

| 指令     | 说明                         |
| -------- | ---------------------------- |
| u        | 撤销（Undo）                 |
| U        | 撤销对整行的操作             |
| Ctrl + r | 重做（Redo），即撤销的撤销。 |

**删除命令**

| 指令                | 说明                                   |
| ------------------- | -------------------------------------- |
| x                   | 删除当前字符                           |
| 3x                  | 删除当前光标开始向后三个字符           |
| X                   | 删除当前字符的前一个字符。X=dh         |
| dl                  | 删除当前字符， dl=x                    |
| dh                  | 删除前一个字符                         |
| dd                  | 删除当前行                             |
| dj                  | 删除上一行                             |
| dk                  | 删除下一行                             |
| 10d                 | 删除当前行开始的10行。                 |
| D                   | 删除当前字符至行尾。D=d$               |
| d$                  | 删除当前字符之后的所有字符（本行）     |
| kdgg                | 删除当前行之前所有行（不包括当前行）   |
| jdG（jd shift + g） | 删除当前行之后所有行（不包括当前行）   |
| :1,10d              | 删除1-10行                             |
| :11,$d              | 删除11行及以后所有的行                 |
| :1,$d               | 删除所有行                             |
| J(shift + j)        | 删除两行之间的空行，实际上是合并两行。 |

**拷贝，剪贴和粘贴**

| 指令        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| yy          | 拷贝当前行                                                   |
| nyy         | 拷贝当前后开始的n行，比如2yy拷贝当前行及其下一行。           |
| p           | 在当前光标后粘贴,如果之前使用了yy命令来复制一行，那么就在当前行的下一行粘贴。 |
| shift+p     | 在当前行前粘贴                                               |
| :1,10 co 20 | 将1-10行插入到第20行之后。                                   |
| :1,$ co $   | 将整个文件复制一份并添加到文件尾部。                         |
| ddp         | 交换当前行和其下一行                                         |
| xp          | 交换当前字符和其后一个字符                                   |
| ndd         | 剪切当前行之后的n行。利用p命令可以对剪切的内容进行粘贴       |
| :1,10d      | 将1-10行剪切。利用p命令可将剪切后的内容进行粘贴。            |
| :1, 10 m 20 | 将第1-10行移动到第20行之后。                                 |

正常模式下按v（逐字）或V（逐行）进入可视模式，然后用jklh命令移动即可选择某些行或字符，再按y即可复制

**退出命令**



| 指令 | 说明                           |
| ---- | ------------------------------ |
| :wq  | 保存并退出                     |
| ZZ   | 保存并退出                     |
| :q!  | 强制退出并忽略所有更改         |
| :e!  | 放弃所有修改，并打开原来文件。 |
| :q   | 未修改直接退出                 |

## 2.网络配置

### 2.1 三种网络模式详解

vmware为我们提供了三种网络工作模式，它们分别是：**Bridged（桥接模式）**、**NAT（网络地址转换模式）**、**Host-Only（仅主机模式）**。

打开vmware虚拟机，我们可以在选项栏的“编辑”下的“虚拟网络编辑器”中看到VMnet0（桥接模式）、VMnet1（仅主机模式）、VMnet8（NAT模式），那么这些都是有什么作用呢？其实，我们现在看到的VMnet0表示的是用于桥接模式下的虚拟交换机；VMnet1表示的是用于仅主机模式下的虚拟交换机；VMnet8表示的是用于NAT模式下的虚拟交换机。

![image-20220815195704711](https://i0.hdslb.com/bfs/album/ee24cee5cdad25bb2a992ba665553b822473b126.png)

同时，在主机上对应的有VMware Network Adapter VMnet1和VMware Network Adapter VMnet8两块虚拟网卡，它们分别作用于仅主机模式与NAT模式下。在“网络连接”中我们可以看到这两块虚拟网卡，如果将这两块卸载了，可以在vmware的“编辑”下的“虚拟网络编辑器”中点击“还原默认设置”，可重新将虚拟网卡还原。

![image-20220815195743297](https://i0.hdslb.com/bfs/album/3244dc5ef61faac2bfee5a5940d7a4883718fcf6.png)

小伙伴看到这里，肯定有疑问，为什么在真机上没有VMware Network Adapter VMnet0虚拟网卡呢？那么接下来，我们就一起来看一下这是为什么。

#### 2.1.1 Bridged（桥接模式）

什么是桥接模式？桥接模式就是将主机网卡与虚拟机虚拟的网卡利用虚拟网桥进行通信。在桥接的作用下，类似于把物理主机虚拟为一个交换机，所有桥接设置的虚拟机连接到这个交换机的一个接口上，物理主机也同样插在这个交换机当中，所以所有桥接下的网卡与网卡都是交换模式的，相互可以访问而不干扰。在桥接模式下，虚拟机ip地址需要与主机在同一个网段，如果需要联网，则网关DNS需要与主机网卡一致。其网络结构如下图所示：

![image-20220815195824917](https://i0.hdslb.com/bfs/album/da53965814800fc4c6ed509d342a98c92ebaf73f.png)

点击“网络适配器”，选择“桥接模式”，然后“确定”

![image-20220815195911118](https://i0.hdslb.com/bfs/album/bbf1feb660af180fbc6758834b276e4a757c2a16.png)

在进入系统之前，我们先确认一下主机的ip地址、网关、DNS等信息。

![image-20220815195920630](https://i0.hdslb.com/bfs/album/12ea5f96f660c11d2949098d8c7f7af7af0e281e.png)

然后，进入系统编辑网卡配置文件，在终端输入命令`vim /etc/sysconfig/network-scripts/ifcfg-eth0`

添加内容如下：

![image-20220815200013770](https://i0.hdslb.com/bfs/album/feecd44f3a8c421c6340e1bc9b07467b36a5be64.png)

编辑完成，保存退出，然后重启虚拟机网卡，使用ping命令ping外网ip，测试能否联网。

> 基本语法: ping [主机地址]
>
> 例如： ping www.baidu.com

![image-20220815200027788](https://i0.hdslb.com/bfs/album/f82d7bd3be7abb5b18cba1d35570023ba9e259e4.png)

能ping通外网ip，证明桥接模式设置成功。

能ping通外网ip，证明桥接模式设置成功。

那主机与虚拟机之间的通信是否正常呢？我们就用远程工具来测试一下。

![image-20220815200102291](https://i0.hdslb.com/bfs/album/3a720a028a54a7d98aaff5f55799aef20d59b671.png)

主机与虚拟机通信正常。

这就是桥接模式的设置步骤，相信大家应该学会了如何去设置桥接模式了。桥接模式配置简单，但如果你的网络环境是ip资源很缺少或对ip管理比较严格的话，那桥接模式就不太适用了。这时，我们就来认识vmware的另一种网络模式：NAT模式。

#### 2.1.2 NAT（地址转换模式）

如果你的网络ip资源紧缺，但是你又希望你的虚拟机能够联网，这时候NAT模式是最好的选择。NAT模式借助虚拟NAT设备和虚拟DHCP服务器，使得虚拟机可以联网。其网络结构如下图所示：

![image-20220815200210830](https://i0.hdslb.com/bfs/album/e9688aa206ae1003042fe5256a25497b91c05c05.png)

在NAT模式中，主机网卡直接与虚拟NAT设备相连，然后虚拟NAT设备与虚拟DHCP服务器一起连接在虚拟交换机VMnet8上，这样就实现了虚拟机联网。那么我们会觉得很奇怪，为什么需要虚拟网卡VMware Network Adapter VMnet8呢？原来我们的VMware Network Adapter VMnet8虚拟网卡主要是为了实现主机与虚拟机之间的通信。在之后的设置步骤中，我们可以加以验证。

首先，设置虚拟机中NAT模式的选项，打开vmware，点击“编辑”下的“虚拟网络编辑器”，设置NAT参数及DHCP参数。

![image-20220815200228608](https://i0.hdslb.com/bfs/album/d62e2bb3d5a15dc2dbca6d594b875cd1b62bd5e0.png)

![image-20220815200238861](https://i0.hdslb.com/bfs/album/19e5972a9d9e6f888cf7f7997add7ecb5c989d22.png)

![image-20220815200259125](https://i0.hdslb.com/bfs/album/a9468789e50fe61d80e12ee4a1cb73855533c1fc.png)

将虚拟机的网络连接模式修改成NAT模式。

点击“网络适配器”，选择“NAT模式”

![image-20220815200327743](https://i0.hdslb.com/bfs/album/980fb0a5071db9d1bf88cb672f7cd7d414423d98.png)

然后开机启动系统，编辑网卡配置文件，在终端输入命令`vim /etc/sysconfig/network-scripts/ifcfg-eth0`

具体配置如下：

![image-20220815200401415](https://i0.hdslb.com/bfs/album/72bd28d00d8b752da7078c509706c890d3d6d859.png)

编辑完成，保存退出，然后重启虚拟机网卡，动态获取ip地址，使用ping命令ping外网ip，测试能否联网。

![image-20220815200410949](https://i0.hdslb.com/bfs/album/5ae0c3d927dc8b5ad61e6ae06483f728f295c7a9.png)

之前，我们说过VMware Network Adapter VMnet8虚拟网卡的作用，那我们现在就来测试一下。

![image-20220815200426008](https://i0.hdslb.com/bfs/album/b9b4a398da088d5ae0f99344ab197a184b9e4b97.png)

![image-20220815200435870](https://i0.hdslb.com/bfs/album/c4d4d256a74d094167a1605c5c506950b864920e.png)

如此看来，虚拟机能联通外网，确实不是通过VMware Network Adapter VMnet8虚拟网卡，那么为什么要有这块虚拟网卡呢？

之前我们就说VMware Network Adapter VMnet8的作用是主机与虚拟机之间的通信，接下来，我们就用远程连接工具来测试一下。

![image-20220815200451232](https://i0.hdslb.com/bfs/album/f9c0ef7cd879419b3f4147040ee600336aa4d59d.png)

然后，将VMware Network Adapter VMnet8启用之后，发现远程工具可以连接上虚拟机了。

那么，这就是NAT模式，利用虚拟的NAT设备以及虚拟DHCP服务器来使虚拟机连接外网，而VMware Network Adapter VMnet8虚拟网卡是用来与虚拟机通信的。

#### 2.1.3 Host-Only（仅主机模式）

Host-Only模式其实就是NAT模式去除了虚拟NAT设备，然后使用VMware Network Adapter VMnet1虚拟网卡连接VMnet1虚拟交换机来与虚拟机通信的，Host-Only模式将虚拟机与外网隔开，使得虚拟机成为一个独立的系统，只与主机相互通讯。其网络结构如下图所示：

![image-20220815200523595](https://i0.hdslb.com/bfs/album/9112bf9968122839aabf8048a0ea86cca6cb333f.png)

通过上图，我们可以发现，如果要使得虚拟机能联网，我们可以将主机网卡共享给VMware Network Adapter VMnet1网卡，从而达到虚拟机联网的目的。接下来，我们就来测试一下。

首先设置“虚拟网络编辑器”，可以设置DHCP的起始范围。

![image-20220815200545448](https://i0.hdslb.com/bfs/album/fbee609ea8624fb198bf20ff154b959e92303bd2.png)

设置虚拟机为Host-Only模式。

![image-20220815200602718](https://i0.hdslb.com/bfs/album/6e25cf1fcebd6c40f3b0fa5938b1df63caccaf43.png)

开机启动系统，然后设置网卡文件。

![image-20220815200612317](https://i0.hdslb.com/bfs/album/c64bde49346573bd5fc768b9196f0d03d11c75b5.png)

保存退出，然后重启网卡，利用远程工具测试能否与主机通信。

![image-20220815200622408](https://i0.hdslb.com/bfs/album/5be5c99244429c5375411d5165ba9ae583d3cc41.png)

主机与虚拟机之间可以通信，现在设置虚拟机联通外网。

![image-20220815200634892](https://i0.hdslb.com/bfs/album/bfd966fe4c9a9904684a1c02155a864590fe2df9.png)

我们可以看到上图有一个提示，强制将VMware Network Adapter VMnet1的ip设置成192.168.137.1，那么接下来，我们就要将虚拟机的DHCP的子网和起始地址进行修改，点击“虚拟网络编辑器”

![image-20220815200653975](https://i0.hdslb.com/bfs/album/26af0bbe03bc68d3fc8e56d25bedfe3b36b90820.png)

重新配置网卡，将VMware Network Adapter VMnet1虚拟网卡作为虚拟机的路由。

![image-20220815200721717](https://i0.hdslb.com/bfs/album/4a2332b3944ce97b635ee3c4d71d43bd1e1cc191.png)

重启网卡，然后通过 远程工具测试能否联通外网以及与主机通信。

![image-20220815200752317](https://i0.hdslb.com/bfs/album/511329168b2bac89e79940628e20a0b5f48db59f.png)

测试结果证明可以使得虚拟机连接外网。

### 2.2 查看网络 IP 和 网关

1）查看虚拟网络编辑器

![image-20220815201209483](https://i0.hdslb.com/bfs/album/29862c5ca010e080847f2d3a5903178d888487c6.png)

2）修改虚拟网卡 Ip

![image-20220815204412976](https://i0.hdslb.com/bfs/album/4ef964e9fa39db2a76e44431fa75098ede6b9423.png)

3）查看网关

![image-20220815204439960](https://i0.hdslb.com/bfs/album/20163e57c1c6b82448af52cc1ff6c536f4a60052.png)

![image-20220815204455124](https://i0.hdslb.com/bfs/album/ab2b830cd96bb69e9f86fef52dec08e9e1654fba.png)

4）查看 windows 环境的中 VMnet8 网络配置

![image-20220815204605529](https://i0.hdslb.com/bfs/album/a3a76ec9a2880387198da9a307fdd4845abd1948.png)

### 2.3 配置网络 ip 地址

#### 2.3.1 自动抓取

![image-20220815204635593](https://i0.hdslb.com/bfs/album/1dd45ae232ec2f3bc35b8cc2fda3ef8e708aa8c9.png)

**`缺点：`** 每次自动获取的ip地址可能不一样，不适用于做服务器

#### 2.3.2 指定ip地址

`ifconfig` 查看当前网络配置

> BOOTPROTO="static" #IP 的配置方法[none|static|bootp|dhcp]（引导 时不 使用协议|静态分配 IP|BOOTP 协议|DHCP 协议）
>
> ONBOOT="yes" #系统启动的时候网络接口是否有效（yes/no）
>
> #IP 地址
> IPADDR=192.168.2.100 #网段必须符合要求，后面的主机地址自己设置
> #网关
> GATEWAY=192.168.2.2 #网关要和vm8虚拟交换机网关一样
> #域名解析器
> DNS1=192.168.2.2  #这个设置成和网关一样就行
>
> 子网掩码默认255.255.255.0

直接修改配置文件来指定IP，并可以连接到外网，编辑：vim /etc/sysconfig/network-scripts/ifcfg-ens160

> centos7是ifcfg-ens33

![image-20220815204909611](https://i0.hdslb.com/bfs/album/a414c7903674fcdaf58a3f3e8ab13725f9a4b2ae.png)![image-20220815204852826](https://i0.hdslb.com/bfs/album/46b13351b7f4804b7dd921392fe0114aedd6685d.png)

重启网络服务：service network restart

> centos8重启网卡的方法
>
> 1.重启⽹卡之前⼀定要重新载⼊⼀下配置⽂件，不然不能⽴即⽣效
>
> ```bash
> nmcli c reload
> ```
>
> 2.重启⽹卡（下⾯的三条命令都可以）：
>
> ```bash
> nmcli c up ens160
> nmcli d reapply ens160
> nmcli d connect ens160
> ```

### 2.3 修改 IP 地址后可能会遇到的问题

（1）物理机能 ping 通虚拟机，但是虚拟机 ping 不通物理机,一般都是因为物理机的 防火墙问题,把防火墙关闭就行 （

（2）虚拟机能 Ping 通物理机,但是虚拟机 Ping 不通外网,一般都是因为 DNS 的设置有 问题 

（3）虚拟机 Ping www.baidu.com 显示域名未知等信息,一般查看 GATEWAY 和 DNS 设 置是否正确 

（4）如果以上全部设置完还是不行，需要关闭 NetworkManager 服务 

> - systemctl stop NetworkManager 关闭  
> - systemctl disable NetworkManager 禁用 

（5）如果检查发现 systemctl status network 有问题 需要检查 ifcfg-ens160

### 2.4 配置主机名

**修改主机名称**

`hostname` （功能描述：查看当前服务器的主机名称）

如果感觉此主机名不合适，我们可以进行修改。通过编辑`vim /etc/hostname` 文文件

修改完成后重启生效。

> 如果想立即生效可以通过`hostnamectl set-hostname dselegent-study【要修改的主机名】`这个命令，然后重启终端就可以看到效果了

**修改 hosts 映射文件**

1）修改 linux 的主机映射文件（hosts 文件） 后续在 hadoop 阶段，虚拟机会比较多，配置时通常会采用主机名的方式配置， 比较简单方便。 不用刻意记 ip 地址。`vim /etc/host`

添加如下内容

```tex
192.168.2.100 ds100
192.168.2.101 ds101
192.168.2.102 ds102
192.168.2.103 ds103
192.168.2.104 ds104
192.168.2.105 ds105
```

重启设备，重启后，查看主机名，已经修改成功

2）修改 windows 的主机映射文件（hosts 文件）

​	进入 `C:\Windows\System32\drivers\etc` 路径

​	打开 hosts 文件并添加如下内容

```tex
192.168.2.100 ds100
192.168.2.101 ds101
192.168.2.102 ds102
192.168.2.103 ds103
192.168.2.104 ds104
192.168.2.105 ds105
```

> 先将该文件只读关闭，然后写入内容保存，最后恢复到只读状态

这时可以在windows通过`ping ds100`来测试是否连通虚拟机

## 3.远程登录

通常在工作过程中，公司中使用的真实服务器或者是云服务器，都不允许除运维人员之 外的员工直接接触，因此就需要通过远程登录的方式来操作。所以，远程登录工具就是必不 可缺的，目前，比较主流的有 Xshell，SSH Secure Shell，SecureCRT，FinalShell 等，同学们 可以根据自己的习惯自行选择，以下以 Xshell5 为例。

### 3.1 远程登录方法

> 两个文件的安装步骤都在01文章中的`2.3centos安装`下的网盘链接中

**配置 Xshell 终端**

1）打开安装好的 Xshell

![image-20220816142329675](https://i0.hdslb.com/bfs/album/8799ba5a5c1af42a2035903f81415919331e9c3b.png)

2） 编辑新链接

![image-20220816142358241](https://i0.hdslb.com/bfs/album/d4c918530216bbab5bba6a4371a5f586a9108f29.png)

![image-20220816142412603](https://i0.hdslb.com/bfs/album/acac2d2e7ba90c6fb72d9e35acdc249d80e90720.png)

3）测试链接

![image-20220816142430521](https://i0.hdslb.com/bfs/album/0bfe335ebe98c8982111d6eb38edd560be4b673c.png)

4）连接成功

![image-20220816142441896](https://i0.hdslb.com/bfs/album/7d08f6bb4ef68e360b5476a27a087571e7f9d894.png)

5）断开连接

![image-20220816142541451](https://i0.hdslb.com/bfs/album/77b167490a8cdbf0f531d99ecc0758f677963a8b.png)

6）添加右键点击粘贴和选中复制

`工具 -> 选项`

![image-20220816142850894](https://i0.hdslb.com/bfs/album/28158d380acad7463c82ac2037f31ea97057c649.png)

![image-20220816142931574](https://i0.hdslb.com/bfs/album/ce6971bffebe4a0db5901c765ab182a61cf0bce8.png)

### 3.2 ftp文件传输

> 主要用于向服务上传或下载文件

**配置 Xftp**

1）创建新链接

![image-20220816143139643](https://i0.hdslb.com/bfs/album/8ad9550dc1d1cb181f1a5546d357bb2dc3ac14dd.png)

2）编辑新链接

![image-20220816143220540](https://i0.hdslb.com/bfs/album/1ec1f5d4049e4290ae9110fc0b2390e99d3f38f3.png)

> 如果配置hosts，主机可以填和名称一样的

![image-20220816143307410](https://i0.hdslb.com/bfs/album/cf14f548fc511fa35b3823e7fba82aa91dd4d7b8.png)

出现以上界面表示链接成功，可以进行 windows 和 linux 系统互传数据。

**注：有可能会有部分人右边是乱码，可以使用以下方式修改，修改后重启 Xftp。**

`文件-当前会话属性--选项--编码：UTF-8`

![image-20220816143434490](https://i0.hdslb.com/bfs/album/45e7ef25dd37c8ed35859e6e5e146a3d08f0ebb5.png)

# 03 【基础篇-系统管理】

## 1.Linux 中的进程和服务

计算机中，一个正在执行的程序或命令，被叫做“进程”（process）。 

启动之后一只存在、常驻内存的进程，一般被称作“服务”（service）。

> 详细操作后面说明

## 2.systemctl 服务管理

> service 服务管理（CentOS 6 版本-了解）
>
> systemctl （CentOS 7 版本-重点掌握）

CentOS 7使用Systemd管理守护进程。centos7采用 systemd管理，服务独立的运行在内存中，服务响应速度快，但占用更多内存。独立服务的服务启动脚本都在目录 /usr/lib/systemd/system里。Systend的新特性：

- 系统引导时实现服务的并行启动；
- 按需激活进程；
- 系统实现快照；
- 基于依赖关系定义服务的控制逻辑；

  systemctl可用于内省和控制“systemd”系统和服务管理器的状态。centos7.x系统环境下我们经常使用此命令启停服务，实际上此命令除了其他独立服务还有很多其他用途。

### 2.1 systemctl参数说明

> 基本语法：`systemctl start | stop | restart | status | reload 服务名`
>
> `systemctl` 指令管理的服务在 /`usr/lib/systemd/system` 
>
> 查看查看服务的方法：`pwd /usr/lib/systemd/system`

**1、使用语法**

用法：systemctl [OPTIONS…] {COMMAND} …

**2 、参数说明**

| 参数            | 参数说明                                                     |
| :-------------- | :----------------------------------------------------------- |
| start           | 立刻启动后面接的unit                                         |
| stop            | 立刻关闭后面接的unit                                         |
| restart         | 立刻关闭后启动后面接的unit，亦即执行stop再start的意思        |
| reload          | 不关闭后面接的unit的情况下，重载配置文件，让设定生效         |
| enable          | 设定下次开机时，后面接的unit会被启动                         |
| disable         | 设定下次开机时，后面接的unit 不会被启动                      |
| status          | 目前后面接的这个unit 的状态，会列出是否正在执行、是否开机启动等信息。 |
| is-active       | 目前有没有正在运行中                                         |
| is-enable       | 开机时有没有预设要启用这个unit                               |
| kill            | 不要被kill这个名字吓着了,它其实是向运行unit的进程发送信号    |
| show            | 列出unit的配置。                                             |
| mask            | 注销unit,注销后你就无法启动这个unit了                        |
| unmask          | 取消对unit的注销                                             |
| list-units      | 依据unit列出目前有启动的unit。若加上–all才会列出没启动的。（等价于无参数） |
| list-unit-files | 列出所有以安装unit以及他们的开机启动状态（enabled、disabled、static、mask）。 |
| –type=TYPE      | 就是unit type，主要有service，socket，target等               |
| get-default     | 取得目前的 target                                            |
| set-default     | 设定后面接的 target 成为默认的操作模式                       |
| isolate         | 切换到后面接的模式                                           |

**3、unit file结构**

文件通常由三部分组成：

- Unit: 定义与Unit类型无关的通用选项；用于提供unit的描述信息，unit行为及依赖关系等。

- Service：与特定类型相关的专用选项；此处为Service类型。

- Install：定义由"systemctl enable"及"systemctl disable"命令在实现服务启用或禁用时用到的一些选项。

**4、Unit段的常用选项**

- Description：描述信息，意义性描述；
- After：定义unit的启动次序；表示当前unit应晚于哪些unit启动；其功能与Before相反；
- Requies：依赖到其它的units；强依赖，被依赖的units无法激活时，当前的unit即无法激活；
- Wants：依赖到其它的units；弱依赖；
- Confilcts：定义units 的冲突关系；

**5、Service段的常用选项**

- Type：用于定义影响ExecStart及相关参数的功能的unit进程类型；
  类型有：simple、forking、oneshot、dbus、notify、idle。
- EnvironmentFile：环境配置文件；
- ExecStart：指明启动unit要运行的命令或脚本；ExecStart, ExecStartPost
- ExecStop：指明停止unit要运行的命令或脚本；
- Restart:

**6、Install段的常用配置：**

- Alias：
- RequiredBy：被哪些unit所依赖；
- WantBy：被哪些unit所依赖；

**7、Unit文件样例**

> [root@s153 system]# cat chronyd.service
> [Unit]
> Description=NTP client/server
> Documentation=man:chronyd(8) man:chrony.conf(5)
> After=ntpdate.service sntp.service ntpd.service
> Conflicts=ntpd.service systemd-timesyncd.service
> ConditionCapability=CAP_SYS_TIME
>
> [Service]
> Type=forking
> PIDFile=/var/run/chronyd.pid
> EnvironmentFile=-/etc/sysconfig/chronyd
> ExecStart=/usr/sbin/chronyd $OPTIONS
> ExecStartPost=/usr/libexec/chrony-helper update-daemon
> PrivateTmp=yes
> ProtectHome=yes
> ProtectSystem=full
>
> [Install]
> WantedBy=multi-user.target

### 2.2 systemctl使用示例

1.查看开机启动列表

```
 systemctl list-unit-files [ | grep 服务名] (查看服务开机启动状态, grep 可以进行过滤)
[root@localhost ~]# systemctl list-unit-files
[root@localhost ~]# systemctl list-unit-files | grep firewalld
firewalld.service                             disabled

#查看已启动的服务列表
systemctl list-unit-files|grep enabled
#
显示所有已启动的服务
systemctl list-units --type=service
```

![image-20220816153223542](https://i0.hdslb.com/bfs/album/8f3ccb6e4eb3773f018eafbcc5958c7f307c256d.png)

> 可以**写一半**再查看完整的服务名，一般也可以简写：`firewalld.service = firewall`

![image-20220816153519618](https://i0.hdslb.com/bfs/album/d6f8f45c8d6d676c9037ae76599649939b44d19c.png)

说明防火墙是一个自启的状态，Linux系统启动的时候防火墙也会自启。

2.设置开机启动

> systemctl在enable、disable、mask子命令里面增加了–now选项，可以激活同时启动服务，激活同时停止服务等。

```
# 设置开机启动并现在启动
## 相当于同时执行了systemctl start 服务名
systemctl enable --now firewalld

# 查看服务启动状态
root@localhost ~]# systemctl status firewalld
```

3. 取消开机启动

```
# 取消开机启动并现在就停止服务
systemctl disable --now firewalld
## 查看服务状态是否停止
[root@localhost ~]# systemctl status firewalld
# 查看启动列表
[root@localhost ~]# systemctl list-unit-files |grep firewalld
firewalld.service                             disabled
```

 使用 `systemctl disable firewalld`时，下次重启系统时防火墙还是处于关闭的状态

![image-20220816153845865](https://i0.hdslb.com/bfs/album/d0a44045e2e5d9b285645fcc1b1acfdd484cac29.png)

重新打开自启动防火墙：

![image-20220816153905563](https://i0.hdslb.com/bfs/album/d7c296ec3443d9ab5ddc54905e7daf6ea7dec6da.png)

1. `systemctl enable 服务名` (设置服务开机启动)，对 `3` （无界面）和 `5` （GUI）运行级别都生效
2. `systemctl disable 服务名` (关闭服务开机启动)，对 `3` （无界面）和 `5` （GUI）运行级别都生效

4.开启服务

```
systemctl start firewall
```

开启防火墙：

![image-20220816153707677](https://i0.hdslb.com/bfs/album/f87769820ac96afcfb6482b5cfe93585251e0821.png)

5.关闭服务(但是下次开机还是会启动)

```
systemctl stop firewall
```

关闭防火墙：

![image-20220816153641546](https://i0.hdslb.com/bfs/album/ebacad6bc276ef0657f682f6fc16c3d429630be4.png)

6.重启服务

```
systemctl restart 服务名
```

7.重新加载配置

```
systemctl reload 服务名
```

8.输出服务运行的状态

```
systemctl status 服务名
systemctl status firewalld
```

查看防火墙的状态，现在是运行中：

![image-20220816153614211](https://i0.hdslb.com/bfs/album/2ab17ff6081e34c5e95ab4371c18afbbb3dbf6fd.png)

9.检查service是否在启动状态

> 写脚本是判断服务器是否启动很管用

```
# systemctl is-active 服务名
systemctl is-active NetworkManager
# active
```

10.检测unit单元是否为自动启动

> 写脚本时判断服务器是否开机自启很管用

```
# systemctl is-enabled 服务名
systemctl is-enabled firewalld
# enabled
```

![image-20220816153416785](https://i0.hdslb.com/bfs/album/71cc922dae47090936df0308581c83ee41d1e350.png)

11.注销一个服务(service)

> systemctl mask 是注销服务的意思。
> 注销服务意味着：
> 该服务在系统重启的时候不会启动
> 该服务无法进行做systemctl start/stop操作
> 该服务无法进行systemctl enable/disable操作

```
systemctl mask firewalld
```

12.取消注销服务(service)

```
systemctl unmask firewalld
```

13.显示单元的手册页（前提是由unit提供）

```
systemctl help
```

14.当新增或修改service单元文件时，需要系统重新加载所有修改过的配置文件

```
systemctl daemon-reload
```

15.查看systemd资源使用率

```
systemd-cgtop
```

16.杀死服务

```
[root@s153 system]# systemctl kill xinetd
[root@s153 system]# systemctl is-failed xinetd
inactive
```

## 3.系统运行级别

1）Linux 运行级别 CentOS 6

![image-20220816154334839](https://i0.hdslb.com/bfs/album/9e03760af35f6c5a47222c2cba602d0b10b0f797.png)

Centos7的启动流程图

![](https://i0.hdslb.com/bfs/album/42bd95859fd306d1d3182e5701374d77f733c980.png)

>   CentOS7中我们的初始化进程变为了systemd。执行默认target配置文件/etc/systemd/system/default.target（这是一个软链接，与默认运行级别有关）。然后执行sysinit.target来初始化系统和basic.target来准备操作系统。接着启动multi-user.target下的本机与服务器服务，并检查/etc/rc.d/rc.local文件是否有用户自定义脚本需要启动。最后执行multi-user下的getty.target及登录服务，检查default.target是否有其他的服务需要启动。
>
>   　　注意：/etc/systemd/system/default.target指向了/lib/systemd/system/目录下的graphical.target或multiuser.target。而graphical.target依赖multiuser.target，multiuser.target依赖basic.target，basic.target依赖sysinit.target，所以倒过来执行。

2）CentOS7 的运行级别简化为:

- multi-user.target 等价于原运行级别 3（多用户有网，无图形界面） 

- graphical.target 等价于原运行级别 5（多用户有网，有图形界面）

3） 查看当前运行级别:

```
[root@localhost etc]# systemctl get-default
multi-user.target
```

4）修改当前运行级别

```
[root@localhost etc]# systemctl set-default graphical.target
```

> centos7中取消了通过修改配置文件设置系统默认运行级别
>
> ```php
> [root@localhost etc]# cat /etc/inittab 
> # inittab is no longer used when using systemd.
> #
> # ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
> #
> # Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
> #
> # systemd uses 'targets' instead of runlevels. By default, there are two main targets:
> #
> # multi-user.target: analogous to runlevel 3    #类似运行级别3
> # graphical.target: analogous to runlevel 5     #类似运行级别5
> #
> # To view current default target, run:
> # systemctl get-default                    #查看系统运行级别
> #
> # To set a default target, run:
> # systemctl set-default TARGET.target      #修改系统默认运行级别
> ```

## 4.关机重启命令

### 4.1 关机重启命令汇总

| halt      | **关机**                                                     | **root用户**      | **halt：只关闭系统，电源还在运行**<br/>**halt -p：关闭系统，关闭电源（先执行halt，再执行poweroff）** |
| --------- | ------------------------------------------------------------ | ----------------- | ------------------------------------------------------------ |
| poweroff  | 关机                                                         | root用户          | poweroff会发送一个关闭电源的信号给acpi                       |
| reboot    | 重启                                                         | root用户          |                                                              |
| shutdown  | -h：关机<br/>-r：重启<br/>-c：取消shutdown操作               | root用户          | shutdown实际上是调用init 0, init 0会cleanup一些工作然后调用halt或者poweroff<br/>shutdown -r now：一分钟后重启<br/>shutdown -r 05:30：最近的5:30重启<br/>shutdown -r +10：十分钟后重启 |
| init      | init 0：关机<br/>init 6：重启                                | root用户          | init：切换系统的运行级别                                     |
| systemctl | systemctl halt [-i]：关机 systemctl poweroff [-i]：关机 systemctl reboot [-i]：重启 | 普通用户 超级用户 | 普通用户需要加-i root用户不需要加-i                          |

> （1）sync （功能描述：将数据由内存同步到硬盘中） 
>
> （2）halt （功能描述：停机，关闭系统，但不断电） 
>
> （3）poweroff （功能描述：关机，断电） （
>
> (4）reboot （功能描述：就是重启，等同于 shutdown -r now）

在关机或者重启之前，执行3至4次sync，将在内存中还未保存到硬盘的数据更新到硬盘中，否则会造成数据的丢失。执行sync时要以管理员的身份运行，因为管理员具有所有文件的权限，而普通用户只具有自己的部分文件的权限。

最经常使用的关机重启的命令是shutdown，因此下面详细学习的使用。

### 4.2 shutdown命令

基本格式：shutdown [选项] [时间] [警告信息]

*选项：*

1. -h：关机
2. -r：重启
3. -c：取消shutdown执行的关机或者重启命令
4. -k：不关机，发出警告

*时间：*

1. shutdown：一分钟后关机（默认）
2. shutdown now：立刻关机
3. shutdown 10：10分钟后关机
4. shutdown 05:00：5点关机

示例：

shutdown -r now：系统立马重启（等同于 reboot
shutdown -r 05:30：最近的5:30重启
shutdown -r 10：十分钟后重启

shutdown -h now：立马关机（等同于 poweroff
shutdown -h 05:30：最近的5:30关机
shutdown -h +10：十分钟后关机

shutdown -c：取消上面的关机重启操作

shutdown -k +10 “I will shutdown in 10 minutes”：10分钟后并不会真的关机，但是会把警告信息发给所有的用户。

### 4.3 sync命令

sync ：linux同步数据命令，**将数据由内存同步到硬盘中**，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件。如果不去手动的输入sync命令来真正的去写磁盘，linux系统也会周期性的去sync数据。

```
[root@hadoop100 桌面]#sync 
```

使用场景：
1.在 关机或者开机之前最好多执行这个几次，以确保数据写入硬盘。
2.挂载时，需要很长时间的操作动作（比如，cp 大文件，检测文件），在这个动作之后接sync。
3.卸载U盘或其他存储设备，需要很长时间，使用sync。

**经验技巧**

​	Linux 系统中为了提高磁盘的读写效率，对磁盘采取了 “预读迟写”操作方式。当用户 保存文件时，Linux 核心并不一定立即将保存数据写入物理磁盘中，而是将数据保存在缓 冲区中，等缓冲区满时再写入磁盘，这种方式可以极大的提高磁盘写入数据的效率。但是， 也带来了安全隐患，如果数据还未写入磁盘时，系统掉电或者其他严重问题出现，则将导 致数据丢失。使用 sync 指令可以立即将缓冲区的数据写入磁盘。

# 04 【实操篇-帮助命令 文件目录管理基础知识】

## 1.帮助命令

通常linux命令都十分简单，但是有些还是有些复杂度的。比如`find`，`ps`这种命令，如果要照顾到所有的场合，可能需要非常巨大的篇幅。但是，万一用到这种偏门的场合怎么办？

全面了解一下是非常有必要的，以便在使用的时候能够唤起记忆中最浅显的印象。然后剩下的，就可以交给类似于`man `的这种命令了。Linux上的每一个命令，都会有配套的帮助文件，这远比网络上那些转来转去的信息，正确的多。

正式介绍一下下面的两个命令：

- `man` 用来显示某个命令的文档信息。比如：`man ls`
- `info` 你可以认为和man是一样的，虽然有一些能够互补的内容。它们会在内容中进行提示的
- `--help` 很多命令通过参数`--help`提供非常简短的帮助信息。这通常是最有用最快捷的用例展示。如果你根本就记不住一个非常拗口的单词，那就找找这些地方吧

> 注意：这些帮助信息，仅集中在命令的作用域本身。对于它的组合使用场景，并没有过多信息。也就是说，它教会了你怎么用，但并没有告诉你用它能够来做什么。

> ### TAB补全
>
> 现在，在终端里，输入`ca`，然后快速按2次`<TAB>`键盘，命令行会进入补全模式，显示以ca打头的所有命令。
>
> ```
> [root@localhost ~]# ca
> cacertdir_rehash     cache_dump           cache_repair         cache_writeback      
> ca-legacy            capsh                case                 catchsegv
> cache_check          cache_metadata_size  cache_restore        cal                  caller               captoinfo            cat                  catman
> ```
>
> 如果你对某个命令，只有模糊的印象，只记得前面的几个字母，这个功能是极好的，命令范围会一步步缩减。

## 2.文件和目录管理基础知识

### 2.1 文件系统的层次结构

平时打交道的都是文件，那么，应该如何找到它们呢？很简单，在 Linux 操作系统中，所有的文件和目录都被组织成以一个根节点“/”开始的倒置的树状结构。

![image-20220817210102283](https://i0.hdslb.com/bfs/album/fa695b742d612fe2cae3a2058e37bf268db12a56.png)

其中，目录就相当于 Windows 中的文件夹，目录中存放的既可以是文件，也可以是其他的子目录，而文件中存储的是真正的信息。

文件系统的最顶层是由根目录开始的，系统使用“/”来表示根目录，在根目录之下的既可以是目录，也可以是文件，而每一个目录中又可以包含（子）目录或文件。如此反复就可以构成一个庞大的文件系统。

> 其实，使用这种树状、具有层次的文件结构主要目的是方便文件系统的管理和维护，想象一下，如果所有的文件都放在一个目录下，其文件系统的管理和维护将变成一场噩梦。

现实中也有许多类似的例子，例如在整个行政管理体制中，村民就相当于文件，他们住在一个村庄中，村庄就是存储村民的目录。许多村又组成了个乡，这个乡就相当于存储村的目录，依此类推，最终就构建出了一个庞大的行政区域管理结构图。

注意，目录名或文件名都是区分大小写的，如 dog、DOG 和 Dog 为 3 个不同的目录或文件。完整的目录或文件路径是由一连串的目录名所组成的，其中每一个目录由“/”来分隔。如 cat 的完整路径是 /home/cat。

在文件系统中，有两个特殊的目录，一个是用户所在的工作目录，即当前目录，可用一个点“.”表示；另一个是当前目录的上一层目录，也叫父目录，用两个点“..”表示。

> 如果一个目录或文件名是以一个点开始，就表示这个目录或文件是一个隐藏目录或文件。即以默认方式査找（后续会讲查找命令）时，不显示该目录或文件。

为了方便管理和维护，Linux 系统采用了文件系统层次标准，也称为 FHS 标准，它规定了根目录下各个目录应该存在哪些类型的文件（或子目录），比如说，在 /bin 和 /sbin 目录中存放的应该是可执行文件，有关各个目录存放文件的类型。

### 2.2 绝对路径和相对路径详解

在 Linux 中，简单的理解一个文件的路径，指的就是该文件存放的位置，例如， /home/cat 就表示的是 cat 文件所存放的位置。只要我们告诉 Linux 系统某个文件存放的准确位置，那么它就可以找到这个文件。

指明一个文件存放的位置，有 2 种方法，分别是使用绝对路径和相对路径。

我们知道，Linux 系统中所有的文件（目录）都被组织成以根目录“/”开始的倒置的树状结构。



![img](http://c.biancheng.net/uploads/allimg/190506/2-1Z5061A1003X.gif)

绝对路径一定是由根目录 / 开始写起。例如，使用绝对路径的表示方式指明 bin 文件所在的位置，该路径应写为 /usr/bin，测试代码如下：

```bash
[root@localhost ~]# bin
bash： bin： command not found  <-- 没有找到
[root@localhost ~]# /usr/bin
bash: /usr/bin: is a directory  <-- 是一个文件
```

可以看到，如果仅传递给 Linux 系统一个文件名，它无法找到指定文件；而当将 bin 文件的绝对路径传递 Linux 系统时，它就可以成功找到。

和绝对路径不同，相对路径不是从根目录 / 开始写起，而是从当前所在的工作目录开始写起。使用相对路径表明某文件的存储位置时，经常会用到前面讲到的 2 个特殊目录，即当前目录（用 . 表示）和父目录（用 .. 表示）。

举个例子，当我们使用 root 身份登录 Linux 系统时，当前工作目录默认为 /root，如果此时需要将当前工作目录调整到 root 的子目录 Desktop 中，当然可以使用绝对路径，示例代码如下：

```bash
[root@localhost ~]# pwd   <-- 显示当前所在的工作路径
/root
[root@localhost ~]# cd /root/Desktop
[root@localhost Desktop]# pwd
/root/Desktop
```

> 注意，这里所使用的 pwd 和 cd 命令，目前只需知道它们的功能即可，具体用法会在后续文章中作详细讲解。

可以看到，通过使用绝对路径，我们成功地改变了当前工作路径。但除此之外，使用相对路径的方式会更简单。因为目前处于 /root 的位置，而 Desktop 就位于当前目录下，所以：

```bash
[root@localhost ~]# pwd  <-- 显示当前所在的工作路径
/root
[root@localhost ~]# cd ./Desktop
[root@localhost Desktop]# pwd
/root/Desktop
```

此代码中，./Desktop 表示的就是 Destop 文件相对于 /root 所在的路径。

再举一个例子，如果以 root 身份登录 Linux 系统，并实现将当前工作目录由 /root 转换为 /usr 目录，有以下 2 种方式：

```bash
#使用绝对路径
[root@localhost ~]# pwd <-- 显示当前所在的工作路径
/root
[root@localhost ~]# cd /usr
[root@localhost ~]# pwd
/usr
#使用相对路径
[root@localhost ~]# pwd <-- 显示当前所在的工作路径
/root
[root@localhost ~]# cd ../usr <-- 相对 root，usr 位于其父目录 /，因此这里要用到 ..
[root@localhost ~]# pwd
/usr
```

总之，绝对路径是相对于根路径 / 的，只要文件不移动位置，那么它的绝对路径是恒定不变的；而相对路径是相对于当前所在目录而言的，随着程序的执行，当前所在目录可能会改变，因此文件的相对路径不是固定不变的。

### 2.3 文件（目录）命名规则

介绍完 Linux 系统中目录结构之后，读者一定想知道如何为文件或目录命名。

我们知道，在 Linux 系统中，一切都是文件，既然是文件，就必须要有文件名。同其他系统相比，Linux 操作系统对文件或目录命名的要求相对比较宽松。

Linux 系统中，文件和目录的命名规则如下：

1. 除了字符“/”之外，所有的字符都可以使用，但是要注意，在目录名或文件名中，使用某些特殊字符并不是明智之举。例如，在命名时应避免使用 <、>、？、* 和非打印字符等。如果一个文件名中包含了特殊字符，例如空格，那么在访问这个文件时就需要使用引号将文件名括起来。
2. 目录名或文件名的长度不能超过 255 个字符。
3. 目录名或文件名是区分大小写的。如 DOG、dog、Dog 和 DOg ，是互不相同的目录名或文件名，但使用字符大小写来区分不同的文件或目录，也是不明智的。
4. 与 Windows 操作系统不同，文件的扩展名对 Linux 操作系统没有特殊的含义，换句话说，Linux 系统并不以文件的扩展名开分区文件类型。例如，dog.exe 只是一个文件，其扩展名 .exe 并不代表此文件就一定是可执行文件。
   需要注意的是，在 Linux 系统中，硬件设备也是文件，也有各自的文件名称。Linux 系统内核中的 udev 设备管理器会自动对硬件设备的名称进行规范，目的是让用户通过设备文件的名称，就可以大致猜测处设备的属性以及相关信息。

udev 设备管理器会一直以进程的形式运行，并侦听系统内核发出的信号来管理位于 /dev 目录下的设备文件。

表 1 罗列出了Linux 系统中常见硬件设备的文件名。

| 硬件设备      | 文件名称                                                     |
| ------------- | ------------------------------------------------------------ |
| IDE设备       | /dev/hd[a-d]，现在的 IDE设备已经很少见了，因此一般的硬盘设备会以 /dev/sd 开头。 |
| SCSI/SATA/U盘 | /dev/sd[a-p]，一台主机可以有多块硬盘，因此系统采用 a~p 代表 16 块不同的硬盘。 |
| 软驱          | /dev/fd[0-1]                                                 |
| 打印机        | /dev/lp[0-15]                                                |
| 光驱          | /dev/cdrom                                                   |
| 鼠标          | /dev/mouse                                                   |
| 磁带机        | /dev/st0 或 /dev/ht0                                         |

### 2.4 命令基本格式

本节开始，我们不会再见到图形界面了，因为对服务器来讲，图形界面会占用更多的系统资源，而且会安装更多的服务、开放更多的端口，这对服务器的稳定性和安全性都有负面影响。其实，服务器是一个连显示器都没有的家伙，要图形界面干十么？

说到这里，有很多人会很崩溃。笔者就经常听到抱怨 Linux 是落后于时代的老古董，就像笔者的白头发一样！但是，大家要理解，对服务器来讲，稳定性、可靠性、安全性才是最主要的。而简单易用不是服务器需要考虑的事情，所以学习 Linux，这些枯燥的命令是必须学习和记忆的内容。

### 2.5 命令提示符

登录系统后，第一眼看到的内容是：

[root@localhost ~]#

这就是 Linux 系统的命令提示符。那么，这个提示符的含义是什么呢？

- []：这是提示符的分隔符号，没有特殊含义。
- root：显示的是当前的登录用户，笔者现在使用的是 root 用户登录。
- @：分隔符号，没有特殊含义。
- localhost：当前系统的简写主机名（完整主机名是 localhost.localdomain）。
- ~：代表用户当前所在的目录，此例中用户当前所在的目录是家目录。
- \#：命令提示符，Linux 用这个符号标识登录的用户权限等级。如果是超级用户，提示符就是 #；如果是普通用户，提示符就是 $。


家目录（又称主目录）是什么？ Linux 系统是纯字符界面，用户登录后，要有一个初始登录的位置，这个初始登录位置就称为用户的家：

- 超级用户的家目录：/root。
- 普通用户的家目录：/home/用户名。


用户在自己的家目录中拥有完整权限，所以我们也建议操作实验可以放在家目录中进行。我们切换一下用户所在目录，看看有什么效果。

[root@localhost ~]# cd /usr/local
[root@localhost local]#

仔细看，如果切换用户所在目录，那么命令提示符中的会变成用户当前所在目录的最后一个目录（不显示完整的所在目录 /usr/ local，只显示最后一个目录 local)。

### 2.6 命令的基本格式

接下来看看 Linux 命令的基本格式:

`[root@localhost ~]# 命令[选项][参数]`

命令格式中的 [] 代表可选项，也就是有些命令可以不写选项或参数，也能执行。那么，我们就用 Linux 中最常见的 ls 命令来解释一下命令的格式（有关 ls 命令的具体用法，后续章节会详细介绍）。如果按照命令的分类，那么 ls 命令应该属于目录操作命令。

`[root@localhost ~]# ls
anaconda-ks.cfg install.log install.log.syslog`

#### 2.6.1 选项的作用

ls 命令之后不加选项和参数也能执行，不过只能执行最基本的功能，即显示当前目录下的文件名。那么加入一个选项，会出现什么结果？

```bash
[root@localhost ~]# Is -l
总用量44
-rw-------.1 root root 1207 1 月 14 18:18 anaconda-ks.cfg
-rw-r--r--.1 root root 24772 1 月 14 18:17 install.log
-rw-r--r--.1 root root 7690 1 月 14 18:17 install.log.syslog
```



如果加一个"-l"选项，则可以看到显示的内容明显增多了。"-l"是长格式（long list）的意思，也就是显示文件的详细信息。至于 "-l" 选项的具体含义，我们稍后再详细讲解。可以看到选项的作用是调整命令功能。如果没有选项，那么命令只能执行最基本的功能；而一旦有选项，则可以显示更加丰富的数据。

Linux 的选项又分为短格式选项（-l）和长格式选项（--all）。短格式选项是英文的简写，用一个减号调用，例如：

`[root@localhost ~]# ls -l`

而长格式选项是英文完整单词，一般用两个减号调用，例如：

`[root@localhost ~]# ls --all`

一般情况下，短格式选项是长格式选项的缩写，也就是一个短格式选项会有对应的长格式选项。当然也有例外，比如 ls 命令的短格式选项 -l 就没有对应的长格式选项。所以具体的命令选项可以通过后面我们要学习的帮助命令来进行査询。

#### 2.6.2 参数的作用

参数是命令的操作对象，一般文件、目录、用户和进程等可以作为参数被命令操作。例如：

`[root@localhost ~]# ls -l anaconda-ks.cfg
-rw-------.1 root root 1207 1 月 14 18:18 anaconda-ks.cfg`

但是为什么一开始 ls 命令可以省略参数？那是因为有默认参数。命令一般都需要加入参数，用于指定命令操作的对象是谁。如果可以省略参数，则一般都有默认参数。例如：

`[root@localhost ~]# ls
anaconda-ks.cfg install.log install.log.syslog`

这个 ls 命令后面没有指定参数，默认参数是当前所在位置，所以会显示当前目录下的文件名。

总结一下：命令的选项用于调整命令功能，而命令的参数是这个命令的操作对象。

# 05【实操篇-文件目录类命令】

## 1.pwd 显示当前工作目录的绝对路径

> pwd:print working directory 打印工作目录

到现在为止，我们还不知道自己在系统的什么地方。在浏览器上，我们能够通过导航栏上的url，了解到自己在互联网上的具体坐标。相似的功能，是由`pwd`命令提供的，它能够输出当前的工作目录。

`pwd`命令是非常非常常用的命令，尤其是在一些`命令提示符`设置不太友好的机器上。另外，它也经常用在shell脚本中，用来判断当前的运行目录是否符合需求。

有很多线上事故，都是由于没有确认当前目录所引起的。比如`rm -rf *`这种危险的命令。在执行一些高危命令时，随时确认当前目录，是个好的习惯。

```
[root@www ~]# pwd [-P]
```

选项与参数：

- **-P** ：显示出确实的路径，而非使用链接 (link) 路径。

实例：单纯显示出目前的工作目录：

```
[root@www ~]# pwd
/root   <== 显示出目录啦～
```

> 我们使用root用户默认登陆后，就停留在`/root`目录中。Linux中的目录层次，是通过`/`进行划分的。

实例显示出实际的工作目录，而非链接档本身的目录名而已。

```bash
[root@www ~]# cd /var/mail   <==注意，/var/mail是一个链接档
[root@www mail]# pwd
/var/mail         <==列出目前的工作目录
[root@www mail]# pwd -P
/var/spool/mail   <==怎么回事？有没有加 -P 差很多～
[root@www mail]# ls -ld /var/mail
lrwxrwxrwx 1 root root 10 Sep  4 17:54 /var/mail -> spool/mail
# 看到这里应该知道为啥了吧？因为 /var/mail 是链接档，链接到 /var/spool/mail 
# 所以，加上 pwd -P 的选项后，会不以链接档的数据显示，而是显示正确的完整路径啊！
```

## 2.ls 列出目录的内容

> ls:list 列出目录内容

`ls`命令，能够列出相关目录的文件信息。可以被评为linux下最勤劳的命令标兵。

语法：

```bash
[root@www ~]# ls [-aAdfFhilnrRSt] 目录名称
[root@www ~]# ls [--color={never,auto,always}] 目录名称
[root@www ~]# ls [--full-time] 目录名称
```

选项与参数：

- -a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)
- -d ：仅列出目录本身，而不是列出目录内的文件数据(常用)
- -l ：长数据串列出，包含文件的属性与权限等等数据；(常用)

```bash
[root@localhost /]# ls /
# 注意：ls可以接受路径参数，你不用先跳转，就可以输出相关信息
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@localhost /]# ls -l /
# 带上 -l参数，能够看到文件的一些权限信息已经更新日期等。
total 20
lrwxrwxrwx.   1 root root    7 Nov  3 20:24 bin -> usr/bin
dr-xr-xr-x.   5 root root 4096 Nov  3 20:34 boot
drwxr-xr-x.  19 root root 3080 Nov  3 21:19 dev
drwxr-xr-x.  74 root root 8192 Nov  3 20:34 etc
drwxr-xr-x.   2 root root    6 Apr 11  2018 home
lrwxrwxrwx.   1 root root    7 Nov  3 20:24 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Nov  3 20:24 lib64 -> usr/lib64
drwxr-xr-x.   2 root root    6 Apr 11  2018 media
drwxr-xr-x.   2 root root    6 Apr 11  2018 mnt
drwxr-xr-x.   2 root root    6 Apr 11  2018 opt
dr-xr-xr-x. 108 root root    0 Nov  3 21:19 proc
dr-xr-x---.   2 root root  135 Nov  4 07:53 root
drwxr-xr-x.  24 root root  740 Nov  3 21:20 run
lrwxrwxrwx.   1 root root    8 Nov  3 20:24 sbin -> usr/sbin
drwxr-xr-x.   2 root root    6 Apr 11  2018 srv
dr-xr-xr-x.  13 root root    0 Nov  3 21:19 sys
drwxrwxrwt.   9 root root 4096 Nov  4 03:40 tmp
drwxr-xr-x.  13 root root  155 Nov  3 20:24 usr
drwxr-xr-x.  19 root root  267 Nov  3 20:34 var
```

> 每行列出的信息依次是： 文件类型与权限 链接数 文件属主 文件属组 文件大小用byte 来表示 建立或最近修改的时间 名字
>
> ![image-20220816220526783](https://i0.hdslb.com/bfs/album/0b901b1573479aada8d856c091084281de5c1b7b.png)

直接在你的/root目录里，执行`ls -al`，你会看到更多东西。这些额外的隐藏文件，都是以`.`开头，以配置文件居多。这就是参数`a`的作用。

```bash
[root@localhost ~]# ls -al
total 28
dr-xr-x---.  2 root root  135 Nov  4 07:53 .
dr-xr-xr-x. 17 root root  224 Nov  3 20:28 ..
-rw-------.  1 root root 1273 Nov  3 20:28 anaconda-ks.cfg
-rw-------.  1 root root  246 Nov  4 11:41 .bash_history
-rw-r--r--.  1 root root   18 Dec 28  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 28  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 28  2013 .bashrc
-rw-r--r--.  1 root root  100 Dec 28  2013 .cshrc
-rw-r--r--.  1 root root  129 Dec 28  2013 .tcshrc
```

ls最常用的，就是加参数`l`或者参数`a`。

细心的同学，应该会注意到两个特殊的目录。`.`和`..`。前者表示的是当前目录，而后者表示的是上层目录。

使用`cd`命令，将在这些目录中，自由穿梭。

> 小技巧：如果你对英文日期阅读困难，可以使用`ls -al --full-time`查看可读的日期。

## 3.cd 切换目录

> cd:Change Directory 切换路径

执行cd命令，可以将工作目录切换到目标文件夹。为了展示cd命令的效果。请在root用户下，执行下面的命令，这将创建一个7层的目录。

```bash
cd
mkdir -p a1/b2/c3/d4/e5/f6/{g7,g8,g9,g10}
```

我们使用cd命令，切换到最后一层。然后，我们使用`..`切换到上层目录。

```bash
[root@localhost ~]# cd a1/b2/c3/d4/e5/f6/g7
[root@localhost g7]# pwd
/root/a1/b2/c3/d4/e5/f6/g7

[root@localhost g7]# cd ..
[root@localhost f6]# pwd
/root/a1/b2/c3/d4/e5/f6
```

所以，切换到上面n层目录，只需使用多层级的`../`即可。有几个特殊的变量，需要说明一下。

- `../` 指的是上层目录
- `../../` 指的是上两层目录
- `./` 指的是当前目录
- `~` 指的是当前的用户目录，这是一个缩写符号
- `-` 使用它，可以在最近两次的目录中来回切换

我们来使用命令把上面这些特殊变量验证一下。

```bash
# 跳转到用户根目录
[root@localhost tmp]# cd ~
[root@localhost ~]# pwd
/root

# 进入到第三层目录
[root@localhost ~]# cd a1/b2/c3/
[root@localhost c3]# pwd
/root/a1/b2/c3

# 跳回到前三层目录
[root@localhost c3]# cd ../../..
[root@localhost ~]# pwd
/root

# 跳到上次访问的目录
[root@localhost ~]# cd -
/root/a1/b2/c3
[root@localhost c3]# pwd
/root/a1/b2/c3

# 进入当前目录：等于什么都没干
[root@localhost c3]# cd ./
[root@localhost c3]# pwd
/root/a1/b2/c3
```

## 4.mkdir 创建一个新的目录

> mkdir:Make directory 建立目录

语法：

```
mkdir [-mp] 目录名称
```

选项与参数：

- -m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
- -p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！

实例：请到/tmp底下尝试创建数个新目录看看：

```
[root@www ~]# cd /tmp
[root@www tmp]# mkdir test    <==创建一名为 test 的新目录
[root@www tmp]# mkdir test1/test2/test3/test4
mkdir: cannot create directory `test1/test2/test3/test4': 
No such file or directory       <== 没办法直接创建此目录啊！
[root@www tmp]# mkdir -p test1/test2/test3/test4
```

加了这个 -p 的选项，可以自行帮你创建多层目录！

实例：创建权限为 **rwx--x--x** 的目录。

```
[root@www tmp]# mkdir -m 711 test2
[root@www tmp]# ls -l
drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
```

上面的权限部分，如果没有加上 -m 来强制配置属性，系统会使用默认属性。

如果我们使用 -m ，如上例我们给予 -m 711 来给予新的目录 drwx--x--x 的权限。

## 5.rmdir 删除空的目录

> rmdir:Remove directory 移除目录

语法：

```
 rmdir [-p] 目录名称
```

选项与参数：

- **-p ：**从该目录起，一次删除多级空目录

删除 runoob 目录

```
[root@www tmp]# rmdir runoob/
```

将 mkdir 实例中创建的目录(/tmp 底下)删除掉！

```
[root@www tmp]# ls -l   <==看看有多少目录存在？
drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
[root@www tmp]# rmdir test   <==可直接删除掉，没问题
[root@www tmp]# rmdir test1  <==因为尚有内容，所以无法删除！
rmdir: `test1': Directory not empty
[root@www tmp]# rmdir -p test1/test2/test3/test4
[root@www tmp]# ls -l        <==您看看，底下的输出中test与test1不见了！
drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
```

利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 一次删除。

> 删除完test4发现test3是空目录继续删除，以此类推。

不过要注意的是，这个 rmdir 仅能删除空的目录，你可以使用 rm 命令来删除非空目录。

## 6.touch 创建空文件

1）基本语法

touch 文件名称

2）案例实操

```bash
[root@hadoop101 ~]# touch xiyou/dssz/sunwukong.txt
```

## 7.cp 复制文件或目录

cp 即拷贝文件和目录。

语法:

```
[root@www ~]# cp [-adfilprsu] 来源档(source) 目标档(destination)
[root@www ~]# cp [options] source1 source2 source3 .... directory
```

选项与参数：

- **-i：**若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
- **-p：**连同文件的属性一起复制过去，而非使用默认属性(备份常用)；
- **-r：**递归持续复制，用於目录的复制行为；(常用)
- **-f：**为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；

用 root 身份，将 root 目录下的 .bashrc 复制到 /tmp 下，并命名为 bashrc

```bash
[root@www ~]# cp ~/.bashrc /tmp/bashrc
[root@www ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite `/tmp/bashrc'? n  <==n不覆盖，y为覆盖
```

## 8.rm 删除文件或目录

rm 是强大的删除命令，它可以永久性地删除文件系统中指定的文件或目录。在使用 rm 命令删除文件或目录时，系统不会产生任何提示信息。

语法：

```
 rm [-fir] 文件或目录
```

选项与参数：

- -f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
- -i ：互动模式，在删除前会询问使用者是否动作
- -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！


注意，rm 命令是一个具有破坏性的命令，因为 rm 命令会永久性地删除文件或目录，这就意味着，如果没有对文件或目录进行备份，一旦使用 rm 命令将其删除，将无法恢复，因此，尤其在使用 rm 命令删除目录时，要慎之又慎。

【例 1】基本用法。
rm 命令如果任何选项都不加，则默认执行的是"rm -i 文件名"，也就是在删除一个文件之前会先询问是否删除。例如：

```bash
[root@localhost ~]# touch cangls
[root@localhost ~]# rm cangls
rm:是否删除普通空文件"cangls"?y
#删除前会询问是否删除
```


【例 2】 删除目录。
如果需要删除目录，则需要使用"-r"选项。例如:

```bash
[root@localhost ~]# mkdir -p /test/lm/movie/jp
#递归建立测试目录
[root@localhost ~]# rm /test
rm:无法删除"/test/": 是一个目录
#如果不加"-r"选项，则会报错
[root@localhost ~]# rm -r /test
rm:是否进入目录"/test"?y
rm:是否进入目录"/test/lm/movie"?y
rm:是否删除目录"/test/lm/movie/jp"?y
rm:是否删除目录"/test/lm/movie"?y
rm:是否删除目录"/test/lm"?y
rm:是否删除目录"/test"?y
#会分别询问是否进入子目录、是否删除子目录
```

大家会发现，如果每级目录和每个文件都需要确认，那么在实际使用中简直是灾难！

【例 3】强制删除。
如果要删除的目录中有 1 万个子目录或子文件，那么普通的 rm 删除最少需要确认 1 万次。所以，在真正删除文件的时候，我们会选择强制删除。例如：

```bash
[root@localhost ~]# mkdir -p /test/lm/movie/jp
#重新建立测试目录
[root@localhost ~]# rm -rf /test
#强制删除，一了百了
```

加入了强制功能之后，删除就会变得很简单，但是需要注意，数据强制删除之后无法恢复，除非依赖第三方的数据恢复工具，如 extundelete 等。但要注意，数据恢复很难恢复完整的数据，一般能恢复 70%~80% 就很难得了。所以，与其把宝压在数据恢复上，不如养成良好的操作习惯。

虽然 "-rf" 选项是用来删除目录的，但是删除文件也不会报错。所以，为了使用方便，一般不论是删除文件还是删除目录，都会直接使用 "-rf" 选项。

## 9.mv 移动文件与目录或重命名

语法：

```bash
[root@www ~]# mv [-fiu] source destination
[root@www ~]# mv [options] source1 source2 source3 .... directory
```

选项与参数：

- -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
- -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
- -u ：若目标文件已经存在，且 source 比较新，才会升级 (update)

复制一文件，创建一目录，将文件移动到目录中

```bash
[root@www ~]# cd /tmp
[root@www tmp]# cp ~/.bashrc bashrc
[root@www tmp]# mkdir mvtest
[root@www tmp]# mv bashrc mvtest
```

将某个文件移动到某个目录去，就是这样做！

将刚刚的目录名称更名为 mvtest2

```bash
[root@www tmp]# mv mvtest mvtest2
```

## 10.cat 查看文件内容

为了查看文件的生成效果，可以使用cat命令检测。cat命令将会把文件的内容，输出打印到终端上。如果加上参数`n`，甚至可以打印行号。效果如下：

```csharp
[root@localhost ~]# cat spring
10
11
12
13
14
15
16
17
18
19
20
[root@localhost ~]# cat -n spring
1	10
2	11
3	12
4	13
5	14
6	15
7	16
8	17
9	18
10	19
11	20
```

除了查看文件内容，cat命令通常用在更多的地方。只有和其他命令联合起来，它才会觉得生活有意义。

```bash
# 合并a文件和b文件到c文件
cat a  b>> c

# 把a文件的内容作为输入，使用管道处理。我们在后面介绍
cat a | cmd

# 写入内容到指定文件。在shell脚本中非常常用。我们在后面会多次用到这种写法
cat > index.html <<EOF
<html>
    <head><title></title></head>
    <body></body>
</html>
EOF
```

由于我们的文件不大，cat命令没有什么危害。但假如文件有几个`GB`，使用cat就危险的多，这只叫做`猫`的小命令，会在终端上疯狂的进行输出，你可以通过多次按`ctrl+c`来终止它。

## 11.less 分屏显示文件内容

既然cat命令不适合操作大文件，那一定有替换的方案。less和more就是。由于less的加载速度比more快一些，所以现在一般都使用`less`。它最主要的用途，是用来分页浏览文件内容，并提供一些快速查找的方式。less是一个`交互式`的命令，你需要使用一些快捷键来控制它。

不仅如此，为了方面用户浏览文本内容，less 命令还提供了以下几个功能：

- 使用光标键可以在文本文件中前后（左后）滚屏；
- 用行号或百分比作为书签浏览文件；
- 提供更加友好的检索、高亮显示等操作；
- 兼容常用的字处理程序（如 Vim、Emacs）的键盘操作；
- 阅读到文件结束时，less 命令不会退出；
- 屏幕底部的信息提示更容易控制使用，而且提供了更多的信息。

less 命令的基本格式如下：

`[root@localhost ~]# less [选项] 文件名`

此命令可用的选项以及各自的含义如表所示。

| 选项            | 选项含义                                               |
| --------------- | ------------------------------------------------------ |
| -N              | 显示每行的行号。                                       |
| -S              | 行过长时将超出部分舍弃。                               |
| -e              | 当文件显示结束后，自动离开。                           |
| -g              | 只标志最后搜索到的关键同。                             |
| -Q              | 不使用警告音。                                         |
| -i              | 忽略搜索时的大小写。                                   |
| -m              | 显示类似 more 命令的百分比。                           |
| -f              | 强迫打开特殊文件，比如外围设备代号、目录和二进制文件。 |
| -s              | 显示连续空行为一行。                                   |
| -b <缓冲区大小> | 设置缓冲区的大小。                                     |
| -o <文件名>     | 将 less 输出的内容保存到指定文件中。                   |
| -x <数字>       | 将【Tab】键显示为规定的数字空格。                      |

在使用 less 命令查看文件内容的过程中，和 more 命令一样，也会进入交互界面，因此需要读者掌握一些常用的交互指令，如表所示。

- `空格` 向下滚屏翻页
- `b` 向上滚屏翻页
- `/` 进入查找模式，比如`/1111`将查找1111字样
- `q` 退出less
- `g` 到开头
- `G` 去结尾
- `j` 向下滚动
- `k` 向上滚动，这两个按键和vim的作用非常像

【例 1】使用 less 命令查看 /boot/grub/grub.cfg 文件中的内容。

```bash
[root@localhost ~]# less /boot/grub/grub.cfg
#
#DO NOT EDIT THIS FILE
#
#It is automatically generated by grub-mkconfig using templates from /etc/grub.d and settings from /etc/default/grub
#

### BEGIN /etc/grub.d/00_header ###
if [ -s $prefix/grubenv ]; then
 set have_grubenv=true
 load_env
fi
set default="0"
if [ "$ {prev_saved_entry}" ]; then
 set saved_entry="${prev_saved_entry}"
 save_env saved_entry
 set prev_saved_entry= save_env prev_saved_entry
 set boot_once=true
fi

function savedefault {
 if [ -z "${boot_once}" ]; then
:
```

可以看到，less 在屏幕底部显示一个冒号（：），等待用户输入命令，比如说，用户想向下翻一页，可以按空格键；如果想向上翻一页，可以按 b 键。

## 12.echo 输出内容到控制台

echo 输出内容到控制台

**基本语法**

echo [选项] [输出内容]

**选项：**

- -e： 支持反斜线控制的字符转换

| 控制字符 | 作用               |
| -------- | ------------------ |
| \\       | 输出\本身          |
| \n       | 换行符             |
| \t       | 制表符，也就是 Tab |

**案例实操**

```bash
[atguigu@hadoop101 ~]$ echo “hello\tworld”
hello\tworld
[atguigu@hadoop101 ~]$ echo -e “hello\tworld”
hello world
```

## 13.head 显示文件头部内容

取出文件前面几行

语法：

```
head [-n number] 文件 
```

选项与参数：

- -n ：后面接数字，代表显示几行的意思

```
[root@www ~]# head /etc/man.config
```

默认的情况中，显示前面 10 行！若要显示前 20 行，就得要这样：

```
[root@www ~]# head -n 20 /etc/man.config
```

## 14.tail 输出文件尾部内容

取出文件后面几行

语法：

```
tail [-n number] 文件 
```

选项与参数：

- -n ：后面接数字，代表显示几行的意思
- -f ：表示持续侦测后面所接的档名，要等到按下[ctrl]-c才会结束tail的侦测

```bash
[root@www ~]# tail /etc/man.config
# 默认的情况中，显示最后的十行！若要显示最后的 20 行，就得要这样：
[root@www ~]# tail -n 20 /etc/man.config
```

对于部分程序员来说，`tail -f`或许是最常用的命令之一。它可以在控制终端，实时监控文件的变化，来看一些滚动日志。比如查看nginx或者tomcat日志等等。

```bash
# 滚动查看系统日志
[root@localhost ~]#tail -f anaconda-ks.cfg
@server-platform
@server-policy
pax
oddjob
sgpio
certmonger
pam_krb5
krb5-workstation
perl-DBD-SQLite
%end
#光标不会退出文件，而会一直监听在文件的结尾处
```

这条命令会显示文件的最后 10 行内容，而且光标不会退出命令，每隔一秒会检查一下文件是否增加新的内容，如果增加就追加到原来的输出结果后面并显示。因此，这时如果向文件中追加一些数据（需要开启一个新终端）：

```bash
[root@localhost ~]# echo 2222222222 >> anaconda-ks.cfg
[root@localhost ~]# echo 3333333333 >> anaconda-ks.cfg
#在新终端中通过echo命令向文件中追加数据
```

那么，在原始的正在监听的终端中，会看到如下信息：

```bash
[root@localhost ~]# tail -f anaconda-ks.cfg @server-platforin
@server-policy
pax
oddjob
sgpio
certmonger
pam_krb5
krb5-workstation
perl-DBD-SQLite
%end
2222222222
33333333333
#在文件的结尾处监听到了新増数据
```

如果想终止输出，按【Ctrl+c】键中断 tail 命令即可。

通常情况下，日志滚动的过快，依然会造成一些困扰，需要配合grep命令达到过滤效果。

```bash
# 滚动查看包含info字样的日志信息
tail -f /var/log/messages | grep info
```

> 对于tail命令来说，还有一个大写的参数`F`。这个参数，能够监控到重新创建的文件。比如像一些log4j等日志是按天滚动的，`tail -f`无法监控到这种变化。

## 15.> 输出重定向和 >> 追加

**1）基本语法**

（1）ls -l > 文件 （功能描述：列表的内容写入文件 a.txt 中（覆盖写））

（2）ls -al >> 文件 （功能描述：列表的内容追加到文件 aa.txt 的末尾）

（3）cat 文件 1 > 文件 2 （功能描述：将文件 1 的内容覆盖到文件 2）
		 cat 文件1 文件2 > 文件3（功能描述：将文件1 和 2的内容合并后输出到文件3中。）

（4）echo “内容” >> 文件

**2）案例实操**

（1）将 ls 查看信息写入到文件中

` [root@hadoop101 ~]# ls -l>houge.txt `

（2）将 ls 查看信息追加到文件中 

`[root@hadoop101 ~]# ls -l>>houge.txt `

（3）采用 echo 将 hello 单词追加到文件中 

`[root@hadoop101 ~]# echo hello>>houge.txt`

（4）将文件 file1.txt 和 file2.txt 的内容合并后输出到文件 file3.txt 中。

```bash
[root@localhost base]# ls
file1.txt    file2.txt
[root@localhost base]# cat file1.txt
ds(file1.txt)
[root@localhost base]# cat file2.txt
is great(file2.txt)
[root@localhost base]# cat file1.txt file2.txt > file3.txt
[root@localhost base]# more file3.txt
#more 命令可查看文件中的内容
ds(file1.txt)
is great(file2.txt)
[root@localhost base]# ls
file1.txt    file2.txt    file3.txt
```

## 16.history 查看已经执行过历史命令

**1）基本语法**

history （功能描述：查看已经执行过历史命令）

**2）案例实操**

（1）查看已经执行过的历史命令

`[root@hadoop101 test1]# history`

（2）显示最近3条命令历史

`histroy 3`

（3）清除历史记录

`history -c`

## 17.ln软链接

软链接也称为符号链接，类似于 windows 里的快捷方式，有自己的数据块，主要存放 了链接其他文件的路径。

1）基本语法

`	ln -s [原文件或目录] [软链接名] `（功能描述：给原文件创建一个软链接）

2）经验技巧

​	删除软链接： rm -rf 软链接名，而不是 `rm -rf 软链接名/`

​	**如果使用 rm -rf 软链接名/ 删除，会把软链接对应的真实目录下内容删掉**

​	查询：通过 ll 就可以查看，列表属性第 1 位是 l，尾部会有位置指向。

3）案例实操

​	（1）创建软连接

```bash
[root@hadoop101 ~]# mv houge.txt xiyou/dssz/
[root@hadoop101 ~]# ln -s xiyou/dssz/houge.txt ./houzi
[root@hadoop101 ~]# ll
lrwxrwxrwx. 1 root root 20 6 月 17 12:56 houzi ->
xiyou/dssz/houge.txt
```

​	（2）删除软连接(注意不要写最后的/)

​	`[root@hadoop101 ~]# rm -rf houzi`

​	（3）进入软连接实际物理路径

```bash
[root@hadoop101 ~]# ln -s xiyou/dssz/ ./dssz
[root@hadoop101 ~]# cd -P dssz/
```

## 18.总结

### 18.1 文件剪贴删除复制重名等

* pwd：Print Working Directory，显示当前工作目录的绝对路径。
* ls：-a：显示当前目录所有的文件和目录，包括隐藏的；
  * -l：以列表的方式显示信息。
* cd：cd ~：回到自己的家目录；cd …：回到当前目录的上一级目录。
* mkdir：创建目录；-p：创建多级目录。
* rmdir：删除空目录。rmdir不能删除非空的目录。如果需要删除非空的目录，需要使用rm -rf。
* cp：拷贝文件到指定目录；
  * -r：递归复制整个文件夹。强制覆盖不提示的方法：
  * cp命令改为\cp
* rm：移除文件或目录；
  * -r：递归删除整个文件夹；
  * -f：强制删除不提示。
* mv：移动文件与目录或重命名，两种功能！
* touch：创建空文件。可以一次性创建多个文件
* ln 给文件创建一个软连接
  * 用法:ln -s \[源文件或目录][软连接名]

### 18.2 文件查看

* cat：查看文件内容。只能浏览文件，而不能修改文件。
  * -n：显示行号。
  * 结尾加上 | more：分页显示，不会全部一下显示完。
* more：是一个基于VI编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容。more还内置了很多快捷键：
  * 空白键（Space）：向下翻一页
  * Enter：向下翻一行
  * q：立刻离开more，不再显示该文件内容
  * Ctrl + F：向下滚动一屏
  * Ctrl + B：返回上一屏
  * = :输出当前行的行号
  * :f 输出文件名和当前行的行号
* less：用来分屏查看文件内容，与more相似，但是更强大，支持各种显示终端。less指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是根据显示需要加载内容。对于显示大型文件具有较高的效率。
* head：显示文件的开头部分。-n 5：看前面5行内容。
* tail：输出文件中尾部的内容。
  * -n 5：看后面5行内容。
  * -f：时事追踪该文档的所有更新
* \>指令：输出重定向。如果不存在会创建文件，否则会将原来的文件内容覆盖。
* \>>指令：追加。如果不存在会创建文件，否则不会覆盖原来的文件内容，而是追加到文件的尾部。
* echo：输出内容到控制台。
* history：查看历史指令																	



# 06 【实操篇-时间日期类 用户和用户组管理】

## 1.时间日期类

### 1.1 显示当前日期

- 基本语法

1. `date` (功能描述:显示当前时间)
2. `date +%Y`(功能描述:显示当前年份)
3. `date +%m`(功能描述:显示当前月份)
4. `date +%d` (功能描述:显示当前是哪一天)
5. `date "+%Y-%m-%d %H:%M:%S"` (功能描述:显示年月日时分秒)
6. `date +%s` (功能描述:显示当前日期时间戳)

- 应用实例

1. 案例1:显示当前时间信息 `date`
   ![image-20220818183300193](https://i0.hdslb.com/bfs/album/c12b70d022270de4da3dd92c92ad478e9ccbf628.png)

2. 案例2:显示当前时间年月日 `date "+%Y-%m-%d"`

   ![image-20220818183319080](https://i0.hdslb.com/bfs/album/6e6836c747446e0666f3cfcf3f3b2f2eb1a2391b.png)

3. 案例3:显示当前时间年月日时分秒 `date "+%Y-%m-%d %H:%M:%S"`
   ![image-20220818183341393](https://i0.hdslb.com/bfs/album/833a2ada5703f9de72428fe59e2b9933604f2e61.png)

4. 案例3:显示当前时间戳 `date "+%s"`

   ```bash
   [root@localhost ~]# date +%s
   1660892982
   ```

### 1.2 显示非当前时间

**1）基本语法**

​	（1）date -d '1 days ago' （功能描述：显示前一天时间）

​	（2）date -d '-1 days ago' （功能描述：显示明天时间）

**2）案例实操**

​	（1）显示前一天

```bash
[root@localhost ~]# date -d '1 days ago'
2021 年 06 月 18 日 星期日 21:07:22 CST
```

​	（2）显示明天时间

```bash
[root@localhost ~]#date -d '-1 days ago'
2017 年 06 月 20 日 星期日 21:07:22 CST
```

### 1.3 设置日期

- 基本语法

```bash
date -s 字符串时间
```

- 应用实例

1. 案例1:设置系统当前时间，比如设置成 `2030-1-01 20:00:10`

```powershell
date -s "2030-1-01 20:00:10"
```

### 1.4 cal 指令

- 查看日历指令 `cal`
- 基本语法
  `cal [选项]`(功能描述:不加选项，显示本月日历)
- 应用实例

1. 案例1:显示当前日历`cal`
   ![image-20220818183422730](https://i0.hdslb.com/bfs/album/e68b0f52518e68a272c4ebe53135aac2a336aef0.png)

2. 案例2:显示2021年日历: `cal 2021`

   ![image-20220818183444685](https://i0.hdslb.com/bfs/album/d069209b86a00489f29d83e4b33e7a72ed8e3c72.png)

## 2.用户管理命令

### 2.1 useradd 添加新用户

Linux 系统中，可以使用 useradd 命令新建用户

**1）基本语法** 

useradd 用户名 （功能描述：添加新用户） 

useradd -g 组名 用户名 （功能描述：添加新用户到某个组）

 **2）案例实操** 

（1）添加一个用户 

```bash
[root@localhost ~]# useradd lamp  [root@localhost ~]#ll /home/ 
```

![image-20220819151225564](https://i0.hdslb.com/bfs/album/912a27281aafd43d039ca6c3e3f82d593d58be04.png)

### 2.2 passwd 设置用户密码

学习 useradd 命令我们知道，使用此命令创建新用户时，并没有设定用户密码，因此还无法用来登陆系统，本节就来学习 passwd 密码配置命令 。

**1）基本语法** 

passwd 用户名 （功能描述：设置用户密码)

**2）案例实操** 

例如，我们使用 root 账户修改 lamp 普通用户的密码，可以使用如下命令：

```bash
[root@localhost ~]#passwd lamp
Changing password for user lamp.
New password: <直接输入新的口令，但屏幕不会有任何反应
BAD PASSWORD: it is WAY too short <口令太简单或过短的错误！这里只是警告信息，输入的密码依旧能用
Retype new password: <再次验证输入的密码，再输入一次即可
passwd: all authentication tokens updated successfully. <提示修改密码成功
```

当然，也可以使用 passwd 命令修改当前系统已登录用户的密码，但要注意的是，需省略掉 "选项" 和 "用户名"。例如，我们登陆 lamp 用户，并使用 passwd 命令修改 lamp 的登陆密码，执行过程如下：

```bash
[root@localhost ~]#passwd
#passwd直接回车代表修改当前用户的密码
Changing password for user vbird2.
Changing password for vbird2
(current) UNIX password: <这里输入『原有的旧口令』
New password: <这里输入新口令
BAD PASSWORD: it is WAY too short <口令检验不通过，请再想个新口令
New password: <这里再想个来输入吧
Retype new password: <通过口令验证！所以重复这个口令的输入
passwd: all authentication tokens updated successfully. <成功修改用户密码
```

注意，普通用户只能使用 passwd 命令修改自己的密码，而不能修改其他用户的密码。

可以看到，与使用 root 账户修改普通用户的密码不同，普通用户修改自己的密码需要先输入自己的旧密码，只有旧密码输入正确才能输入新密码。不仅如此，此种修改方式对密码的复杂度有严格的要求，新密码太短、太简单，都会被系统检测出来并禁止用户使用。

> 很多Linux 发行版为了系统安装，都使用了 PAM 模块进行密码的检验，设置密码太短、与用户名相同、是常见字符串等，都会被 PAM 模块检查出来，从而禁止用户使用此类密码。有关 PAM 模块，后续章节会进行详细介绍。

而使用 root 用户，无论是修改普通用户的密码，还是修改自己的密码，都可以不遵守 PAM 模块设定的规则，就比如我刚刚给 lamp 用户设定的密码是 "123"，系统虽然会提示密码过短和过于简单，但依然可以设置成功。当然，在实际应用中，就算是 root 身份，在设定密码时也要严格遵守密码规范，因为只有好的密码规范才是服务器安全的基础。

### 2.3 id 查看用户是否存在

id 命令可以查询用户的UID、GID 和附加组的信息。命令比较简单，格式如下：

`[root@localhost ~]# id 用户名`
**【例 1】**

```bash
[root@localhost ~]# id lamp
uid=501(lamp) gid=501(lamp) groups=501(lamp)
#能看到uid(用户ID)、gid(初始组ID), groups是用户所在组，这里既可以看到初始组，如果有附加组，则也能看到附加组
```


**【例 2】**

```bash
[root@localhost ~]# usermod -G root lamp
#把用户加入root组
[root@localhost ~]# id lamp
uid=501(lamp) gid=501(lamp) groups=501(lamp),0(root)
#大家发现root组中加入了lamp用户的附加组信息
```

### 2.4 cat /etc/passwd 查看创建了哪些用户

Linux 系统中的 /etc/passwd 文件，是系统用户配置文件，存储了系统中所有用户的基本信息，并且所有用户都可以对此文件执行读操作。

首先我们来打开这个文件，看看到底包含哪些内容，执行命令如下：

```bash
[root@localhost ~]# vim /etc/passwd
#查看一下文件内容
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
...省略部分输出...
```

可以看到，/etc/passwd 文件中的内容非常规律，每行记录对应一个用户。

读者可能会问，Linux 系统中默认怎么会有这么多的用户？这些用户中的绝大多数是系统或服务正常运行所必需的用户，这种用户通常称为系统用户或伪用户。系统用户无法用来登录系统，但也不能删除，因为一旦删除，依赖这些用户运行的服务或程序就不能正常执行，会导致系统问题。

不仅如此，每行用户信息都以 "：" 作为分隔符，划分为 7 个字段，每个字段所表示的含义如下：

用户名：密码：UID（用户ID）：GID（组ID）：描述性信息：主目录：默认Shell

接下来，给大家逐个介绍这些字段。

#### 2.4.1 用户名

用户名，就是一串代表用户身份的字符串。

前面讲过，用户名仅是为了方便用户记忆，Linux 系统是通过 UID 来识别用户身份，分配用户权限的。/etc/passwd 文件中就定义了用户名和 UID 之间的对应关系。

#### 2.4.2 密码

"x" 表示此用户设有密码，但不是真正的密码，真正的密码保存在 /etc/shadow 文件中。

在早期的 UNIX 中，这里保存的就是真正的加密密码串，但由于所有程序都能读取此文件，非常容易造成用户数据被窃取。

虽然密码是加密的，但是采用暴力破解的方式也是能够进行破解的。

因此，现在 Linux 系统把真正的加密密码串放置在 /etc/shadow 文件中，此文件只有 root 用户可以浏览和操作，这样就最大限度地保证了密码的安全。

需要注意的是，虽然 "x" 并不表示真正的密码，但也不能删除，如果删除了 "x"，那么系统会认为这个用户没有密码，从而导致只输入用户名而不用输入密码就可以登陆（只能在使用无密码登录，远程是不可以的），除非特殊情况（如破解用户密码），这当然是不可行的。

#### 2.4.3 UID

UID，也就是用户 ID。每个用户都有唯一的一个 UID，Linux 系统通过 UID 来识别不同的用户。

实际上，UID 就是一个 0~65535 之间的数，不同范围的数字表示不同的用户身份，具体如表 1 所示。

| UID 范围  | 用户身份                                                     |
| --------- | ------------------------------------------------------------ |
| 0         | 超级用户。UID 为 0 就代表这个账号是管理员账号。在 Linux 中，如何把普通用户升级成管理员呢？只需把其他用户的 UID 修改为 0 就可以了，这一点和 Windows 是不同的。不过不建议建立多个管理员账号。 |
| 1~499     | 系统用户（伪用户）。也就是说，此范围的 UID 保留给系统使用。其中，1~99 用于系统自行创建的账号；100~499 分配给有系统账号需求的用户。  其实，除了 0 之外，其他的 UID 并无不同，这里只是默认 500 以下的数字给系统作为保留账户，只是一个公认的习惯而已。 |
| 500~65535 | 普通用户。通常这些 UID 已经足够用户使用了。但不够用也没关系，2.6.x 内核之后的 Linux 系统已经可以支持 232 个 UID 了。 |

#### 2.4.4 GID

全称“Group ID”，简称“组ID”，表示用户初始组的组 ID 号。这里需要解释一下初始组和附加组的概念。

初始组，指用户登陆时就拥有这个用户组的相关权限。每个用户的初始组只能有一个，通常就是将和此用户的用户名相同的组名作为该用户的初始组。比如说，我们手工添加用户 lamp，在建立用户 lamp 的同时，就会建立 lamp 组作为 lamp 用户的初始组。

附加组，指用户可以加入多个其他的用户组，并拥有这些组的权限。每个用户只能有一个初始组，除初始组外，用户再加入其他的用户组，这些用户组就是这个用户的附加组。附加组可以有多个，而且用户可以有这些附加组的权限。

举例来说，刚刚的 lamp 用户除属于初始组 lamp 外，我又把它加入了 users 组，那么 lamp 用户同时属于 lamp 组和 users 组，其中 lamp 是初始组，users 是附加组。

当然，初始组和附加组的身份是可以修改的，但是我们在工作中不修改初始组，只修改附加组，因为修改了初始组有时会让管理员逻辑混乱。

需要注意的是，在 /etc/passwd 文件的第四个字段中看到的 ID 是这个用户的初始组。

#### 2.4.5 描述性信息

这个字段并没有什么重要的用途，只是用来解释这个用户的意义而已。

#### 2.4.6 主目录

也就是用户登录后有操作权限的访问目录，通常称为用户的主目录。

例如，root 超级管理员账户的主目录为 /root，普通用户的主目录为 /home/yourIDname，即在 /home/ 目录下建立和用户名相同的目录作为主目录，如 lamp 用户的主目录就是 /home/lamp/ 目录。

#### 2.4.7 默认的Shell

Shell 就是 Linux 的命令解释器，是用户和 Linux 内核之间沟通的桥梁。

我们知道，用户登陆 Linux 系统后，通过使用 Linux 命令完成操作任务，但系统只认识类似 0101 的机器语言，这里就需要使用命令解释器。也就是说，Shell 命令解释器的功能就是将用户输入的命令转换成系统可以识别的机器语言。

通常情况下，Linux 系统默认使用的命令解释器是 bash（/bin/bash），当然还有其他命令解释器，例如 sh、csh 等。

在 /etc/passwd 文件中，大家可以把这个字段理解为用户登录之后所拥有的权限。如果这里使用的是 bash 命令解释器，就代表这个用户拥有权限范围内的所有权限。例如：

```bash
[root@localhost ~]# vim /etc/passwd
lamp:x:502:502::/home/lamp:/bin/bash
```

我手工添加了 lamp 用户，它使用的是 bash 命令解释器，那么这个用户就可以使用普通用户的所有权限。

如果我把 lamp 用户的 Shell 命令解释器修改为 /sbin/nologin，那么，这个用户就不能登录了，例如：

```bash
[root@localhost ~]# vim /etc/passwd
lamp:x:502:502::/home/lamp:/sbin/nologin
```

因为 /sbin/nologin 就是禁止登录的 Shell。同样，如果我在这里放入的系统命令，如 /usr/bin/passwd，例如：

```bash
[root@localhost ~]#vim /etc/passwd
lamp:x:502:502::/home/lamp:/usr/bin/passwd
```

那么这个用户可以登录，但登录之后就只能修改自己的密码。但是，这里不能随便写入和登陆没有关系的命令（如 ls），系统不会识别这些命令，同时也就意味着这个用户不能登录。

### 2.5 su 临时切换用户身份

#### 2.5.1 基本使用

su 是最简单的用户切换命令，通过该命令可以实现任何身份的切换，包括从普通用户切换为 root 用户、从 root 用户切换为普通用户以及普通用户之间的切换。

普通用户之间切换以及普通用户切换至 root 用户，都需要知晓对方的密码，只有正确输入密码，才能实现切换；从 root 用户切换至其他用户，无需知晓对方密码，直接可切换成功。
su 命令的基本格式如下：

`[root@localhost ~]# su [选项] 用户名`
选项：

- -：当前用户不仅切换为指定用户的身份，同时所用的工作环境也切换为此用户的环境（包括 PATH 变量、MAIL 变量等），使用 - 选项可省略用户名，默认会切换为 root 用户。
- -l：同 - 的使用类似，也就是在切换用户身份的同时，完整切换工作环境，但后面需要添加欲切换的使用者账号。
- -p：表示切换为指定用户的身份，但不改变当前的工作环境（不使用切换用户的配置文件）。
- -m：和 -p 一样；
- -c 命令：仅切换用户执行一次命令，执行后自动切换回来，该选项后通常会带有要执行的命令。

【例 1】

```bash
[lamp@localhost ~]$ su 
或者
[lamp@localhost ~]$ su - root
或者
[lamp@localhost ~]$ su - 
密码： <-- 输入 root 用户的密码
#"-"代表连带环境变量一起切换，不能省略
```

【例 2】

```bash
[lamp@localhost ~]$ whoami
lamp
#当前我是lamp
[lamp@localhost ~]$ su - -c "useradd user1" root
密码：
#不切换成root，但是执行useradd命令添加user1用户
[lamp@localhost ~]$ whoami
lamp
#我还是lamp
[lamp@localhost ~]$ grep "user1' /etc/passwd
userl:x:502:504::/home/user1:/bin/bash
#user用户已经添加了
```


除了像例 2 这样，执行一条命令后用户身份会随即自动切换回来，其他切换用户的方式不会自动切换，只能使用 exit 命令进行手动切换，例如：

```bash
[lamp@localhost ~]$ whoami
lamp
#当前我是lamp
[lamp@localhost ~]$ su - lamp1
Password:  <--输入lamp1用户的密码
#切换至 lamp1 用户的工作环境
[lamp@localhost ~]$ whoami
lamp1
#什么也不做，立即退出切换环境
[lamp1@localhost ~]$ exit
logout
[lamp@localhost ~]$ whoami
lamp
```

#### 2.5.2 su 和 su - 的区别

注意，使用 su 命令时，有 - 和没有 - 是完全不同的，- 选项表示在切换用户身份的同时，连当前使用的环境变量也切换成指定用户的。我们知道，环境变量是用来定义操作系统环境的，因此如果系统环境没有随用户身份切换，很多命令无法正确执行。

举个例子，普通用户 lamp 通过 su 命令切换成 root 用户，但没有使用 - 选项，这样情况下，虽然看似是 root 用户，但系统中的 $PATH 环境变量依然是 lamp 的（而不是 root 的），因此当前工作环境中，并不包含 /sbin、/usr/sbin等超级用户命令的保存路径，这就导致很多管理员命令根本无法使用。不仅如此，当 root 用户接受邮件时，会发现收到的是 lamp 用户的邮件，因为环境变量 $MAIL 也没有切换。

初学者可以这样理解它们之间的区别，即有 - 选项，切换用户身份更彻底；反之，只切换了一部分，这会导致某些命令运行出现问题或错误（例如无法使用 service 命令）。

通过下面这个例子，可直观的看到 su 和 su - 的区别：

```bash
[lamp@localhost ~]$ whoami
lamp
#查询用户身份，我是lamp
[lamp@localhost ~]$ su root
密码：
<-输入root密码
#切换到root，但是没有切换环境变量。注意：普通用户切换到root需要密码
[root@localhost ~]# env | grep lamp
#查看环境变量，提取包含lamp的行
USER=lamp
#用户名还是lamp，而不是root
PATH=/usr/lib/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/lamp/bin
#命令査找的路径不包含超级用户路径
MAIL=/var/spool/mail/lamp
PWD=/home/lamp
LOGNAME=lamp
#邮箱、主目录、目前用户名还是lamp
```

可以看到，在不使用 su - 的情况下，虽然用户身份成功切换，但环境变量依旧用的是原用户的，切换并不完整。

### 2.6 userdel  删除用户

userdel 命令功能很简单，就是删除用户的相关数据。此命令只有 root 用户才能使用。

通过前面的学习我们知道，用户的相关数据包含如下几项：

- 用户基本信息：存储在 /etc/passwd 文件中；
- 用户个人文件：主目录默认位于 /home/用户名

其实，userdel 命令的作用就是从以上文件中，删除与指定用户有关的数据信息。

userdel 命令的语法很简单，基本格式如下：

`[root@localhost ~]# userdel -r 用户名`

-r 选项表示在删除用户的同时删除用户的家目录。

注意，在删除用户的同时如果不删除用户的家目录，那么家目录就会变成没有属主和属组的目录，也就是垃圾文件。

例如，删除前面章节中创建的 lamp 用户，只需执行如下命令：

`[root@localhost ~]# userdel -r lamp`


除了使用 userdel 命令删除用户，还可以手动方式删除，毕竟通过前面的学习，我们已经知道与用户相关信息的存储位置。虽然这样做没有实际意义，但对于初学者来说，可以加深对 userdel 命令的理解。

手动删除用户，仅是为了让读者对 userdel 命令理解地更透彻，实际使用中，使用 userdel 删除用户更方便。

（1）删除用户但保存用户主目录

```bash
[root@localhost ~]#userdel tangseng
[root@localhost ~]#ll /home/
```

（2）删除用户和用户主目录，都删除

```bash
[root@localhost ~]#useradd zhubajie [root@localhost ~]#ll /home/ [root@localhost ~]#userdel -r zhubajie [root@localhost ~]#ll /home/
```

最后需要大家注意的是，如果要删除的用户已经使用过系统一段时间，那么此用户可能在系统中留有其他文件，因此，如果我们想要从系统中彻底的删除某个用户，最好在使用 userdel 命令之前，先通过 `find -user 用户名` 命令查出系统中属于该用户的文件，然后在加以删除。

### 2.7 who 查看登录用户信息

**基本语法**

（1）whoami （功能描述：显示自身用户名称） 

（2）who am i （功能描述：显示登录用户的用户名以及登陆时间）

whoami 命令和 who am i 命令是不同的 2 个命令，前者用来打印当前执行操作的用户名，后者则用来打印登陆当前 Linux 系统的用户名。

为了能够更好地区分这 2 个命令的功能，给大家举个例子，我们首先使用用户名为“lamp”登陆 Linux 系统，然后执行如下命令：

```bash
[lamp@localhost ~]$ whoami
lamp
[lamp@localhost ~]$ who am i
lamp  pts/0  2021-10-09 15:30 (:0.0)
```

在此基础上，使用 su 命令切换到 root 用户下，再执行一遍上面的命令：

```bash
[lamp@localhost ~] su - root
[root@localhost ~]$ whoami
root
[root@localhost ~]$ who am i
lamp  pts/0  2017-10-09 15:30 (:0.0)
```

看到了吗？在未切换用户身份之前，whoami 和 who am i 命令的输出是一样的，但使用 su 命令切换用户身份后，使用 whoami 命令打印的是切换后的用户名，而 who am i 命令打印的仍旧是登陆系统时所用的用户名。

> 执行 whoami 命令，等同于执行 id -un 命令；执行 who am i 命令，等同于执行 who -m 命令。

也就是说，使用 su 或者 sudo 命令切换用户身份，骗得过 whoami，但骗不过 who am i。要解释这背后的运行机制，需要搞清楚什么是实际用户（UID）和有效用户（EUID，即 Effective UID）。

所谓实际用户，指的是登陆 Linux 系统时所使用的用户，因此在整个登陆会话过程中，实际用户是不会发生变化的；而有效用户，指的是当前执行操作的用户，也就是说真正决定权限高低的用户，这个是能够利用 su 或者 sudo 命令进行任意切换的。

一般情况下，实际用户和有效用户是相同的，如果出现用户身份切换的情况，它们会出现差异。需要注意的是，实际用户和有效用户出现差异，切换用户并不是唯一的触发机制，至于其他的触发条件，后续章节会做详细介绍。

那么，whoami 和 who am i通常应用在哪些场景中呢？通常，对那些经常需要切换用户的系统管理员来说，经常需要明确当前使用的是什么身份；另外，对于某些 shell 脚本，或者需要特别的用户才能执行，这时就需要利用 whoami 命令来搞清楚执行它的用户是谁；甚至还有一些 shell 脚本，一定要某个特别用户才能执行，即便使用 su 或者 sudo 命令切换到此身份都不行，此时就需要利用 who am i 来确认。

### 2.8 sudo 设置普通用户具有 root 权限

我们知道，使用 su 命令可以让普通用户切换到 root 身份去执行某些特权命令，但存在一些问题，比如说：

- 仅仅为了一个特权操作就直接赋予普通用户控制系统的完整权限；
- 当多人使用同一台主机时，如果大家都要使用 su 命令切换到 root 身份，那势必就需要 root 的密码，这就导致很多人都知道 root 的密码；


考虑到使用 su 命令可能对系统安装造成的隐患，最常见的解决方法是使用 sudo 命令，此命令也可以让你切换至其他用户的身份去执行命令。

相对于使用 su 命令还需要新切换用户的密码，sudo 命令的运行只需要知道自己的密码即可，甚至于，我们可以通过手动修改 sudo 的配置文件，使其无需任何密码即可运行。

sudo 命令默认只有 root 用户可以运行。

**例1**

**1）添加 lamp 用户，并对其设置密码。**

```bash
[root@localhost ~]#useradd lamp
[root@localhost ~]#passwd lamp
```

**2）修改配置文件**

前面说过，默认情况下 sudo 命令只有 root 身份可以使用，那么，如何让普通用户也能使用它呢？

解决这个问题之前，先给大家分析一下 sudo 命令的执行过程。sudo命令的运行，需经历如下几步：

- 当用户运行 sudo 命令时，系统会先通过 /etc/sudoers 文件，验证该用户是否有运行 sudo 的权限；
- 确定用户具有使用 sudo 命令的权限后，还要让用户输入自己的密码进行确认。出于对系统安全性的考虑，如果用户在默认时间内（默认是 5 分钟）不使用 sudo 命令，此后使用时需要再次输入密码；
- 密码输入成功后，才会执行 sudo 命令后接的命令。


显然，能否使用 sudo 命令，取决于对 /etc/sudoers 文件的配置（默认情况下，此文件中只配置有 root 用户）。所以接下来，我们学习对 /etc/sudoers 文件进行合理的修改。

```bash
[root@localhost ~]#vim /etc/sudoers 
```

修改 /etc/sudoers 文件，找到下面一行(91 行)，在 root 下面添加一行，如下所示：

```bash
## Allow root to run any commands anywhere
root ALL=(ALL) ALL
lamp ALL=(ALL) ALL
#用户名 被管理主机的地址=(可使用的身份) 授权命令(绝对路径)
#%wheel ALL=(ALL) ALL
#%组名 被管理主机的地址=(可使用的身份) 授权命令(绝对路径)
```

或者配置成采用 sudo 命令时，不需要输入密码

```bash
## Allow root to run any commands anywhere
root ALL=(ALL) ALL
lamp ALL=(ALL) NOPASSWD:ALL
```

修改完毕，现在可以用 lamp 帐号登录，然后用命令 sudo ，即可获得 root 权限进行操作。

对以上 2 个模板的各部分进行详细的说明。

| 模块             | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| 用户名或群组名   | 表示系统中的那个用户或群组，可以使用 sudo 这个命令。         |
| 被管理主机的地址 | 用户可以管理指定 IP 地址的服务器。这里如果写 ALL，则代表用户可以管理任何主机；如果写固定 IP，则代表用户可以管理指定的服务器。如果我们在这里写本机的 IP 地址，不代表只允许本机的用户使用指定命令，而是代表指定的用户可以从任何 IP 地址来管理当前服务器。 |
| 可使用的身份     | 就是把来源用户切换成什么身份使用，（ALL）代表可以切换成任意身份。这个字段可以省略。 |
| 授权命令         | 表示 root 把什么命令命令授权给用户，换句话说，可以用切换的身份执行什么命令。需要注意的是，此命令必须使用绝对路径写。默认值是 ALL，表示可以执行任何命令。 |

**3）案例实操**

用普通用户在/opt 目录下创建一个文件夹

```bash
[lamp@localhost opt]$ sudo mkdir module
[root@localhost opt]# chown lamp:lamp module/
```

**例2**

假设现在有 pro1，pro2，pro3 这 3 个用户，还有一个 group 群组，我们可以通过在 /etc/sudoers 文件配置 wheel 群组信息，令这 3 个用户同时拥有管理系统的权限。

首先，向 /etc/sudoers 文件中添加群组配置信息：

```bash
....(前面省略)....
%group     ALL=(ALL)    ALL
#在 84 行#wheel这一行后面写入
```

此配置信息表示，group 这个群组中的所有用户都能够使用 sudo 切换任何身份，执行任何命令。接下来，我们使用 usermod 命令将 pro1 加入 group 群组，看看有什么效果：

```bash
[root@localhost ~]# usermod -a -G group pro1
[pro1@localhost ~]# sudo tail -n 1 /etc/shadow <==注意身份是 pro1
....(前面省略)....
Password:  <==输入 pro1 的口令喔！
pro3:$1$GfinyJgZ$9J8IdrBXXMwZIauANg7tW0:14302:0:99999:7:::
[pro2@localhost ~]# sudo tail -n 1 /etc/shadow <==注意身份是 pro2
Password:
pro2 is not in the sudoers file.  This incident will be reported.
#此错误信息表示 pro2 不在 /etc/sudoers 的配置中。
```

可以看到，由于 pro1 加入到了 group 群组，因此 pro1 就可以使用 sudo 命令，而 pro2 不行。同样的道理，如果我们想让 pro3 也可以使用 sudo 命令，不用再修改 /etc/sudoers 文件，只需要将 pro3 加入 group 群组即可。

### 2.9 usermod 修改用户

前面章节介绍了如何利用 useradd 命令添加用户，但如果不小心添错用户信息，后期如何修改呢？

**1）基本语法** 

usermod -g 用户组 用户

**2）选项说明**

`-g` 修改用户的初始登录组，给定的组必须存在。默认组 id 是 1。

**3）案例实操**

将用户加入到用户组

````bash
[root@dselegent-study /]# groupadd group
[root@dselegent-study lamp]# usermod -g group lamp
[root@dselegent-study lamp]# id lamp
uid=1001(lamp) gid=1002(group) 组=1002(group)
````

## 3.用户组管理命令

### 3.1 用户和用户组

Linux 是多用户多任务操作系统，换句话说，Linux 系统支持多个用户在同一时间内登陆，不同用户可以执行不同的任务，并且互不影响。

例如，某台 Linux 服务器上有 4 个用户，分别是 root、www、ftp 和 mysql，在同一时间内，root 用户可能在查看系统日志、管理维护系统；www 用户可能在修改自己的网页程序；ftp 用户可能在上传软件到服务器；mysql 用户可能在执行自己的 SQL 查询，每个用户互不干扰，有条不紊地进行着自己的工作。与此同时，每个用户之间不能越权访问，比如 www 用户不能执行 mysql 用户的 SQL 查询操作，ftp 用户也不能修改 www 用户的网页程序。

不同用户具有不问的权限，毎个用户在权限允许的范围内完成不间的任务，Linux 正是通过这种权限的划分与管理，实现了多用户多任务的运行机制。

因此，如果要使用 Linux 系统的资源，就必须向系统管理员申请一个账户，然后通过这个账户进入系统（账户和用户是一个概念）。通过建立不同属性的用户，一方面可以合理地利用和控制系统资源，另一方面也可以帮助用户组织文件，提供对用户文件的安全性保护。

每个用户都有唯一的用户名和密码。在登录系统时，只有正确输入用户名和密码，才能进入系统和自己的主目录。

用户组是具有相同特征用户的逻辑集合。简单的理解，有时我们需要让多个用户具有相同的权限，比如查看、修改某一个文件的权限，一种方法是分别对多个用户进行文件访问授权，如果有 10 个用户的话，就需要授权 10 次，那如果有 100、1000 甚至更多的用户呢？

显然，这种方法不太合理。最好的方式是建立一个组，让这个组具有查看、修改此文件的权限，然后将所有需要访问此文件的用户放入这个组中。那么，所有用户就具有了和组一样的权限，这就是用户组。

将用户分组是 Linux 系统中对用户进行管理及控制访问权限的一种手段，通过定义用户组，很多程序上简化了对用户的管理工作。

**Linux用户和组的关系**

用户和用户组的对应关系有以下 4 种：

1. 一对一：一个用户可以存在一个组中，是组中的唯一成员；
2. 一对多：一个用户可以存在多个用户组中，此用户具有这多个组的共同权限；
3. 多对一：多个用户可以存在一个组中，这些用户具有和组相同的权限；
4. 多对多：多个用户可以存在多个组中，也就是以上 3 种关系的扩展。

用户和组之间的关系可以用图来表示：

![image-20220818202321858](https://i0.hdslb.com/bfs/album/5eb7555ab9ab251c75a5256b8aba9b794ca2acec.png)

### 3.2 UID和GID（用户ID和组ID）

登陆 Linux 系统时，虽然输入的是自己的用户名和密码，但其实 Linux 并不认识你的用户名称，它只认识用户名对应的 ID 号（也就是一串数字）。Linux 系统将所有用户的名称与 ID 的对应关系都存储在 /etc/passwd 文件中。

> 说白了，用户名并无实际作用，仅是为了方便用户的记忆而已。

要论证 "Linux系统不认识用户名" 也很简单，在前面章节，我们曾经在网络上下载过 ".tar.gz" 或 ".tar.bz2" 格式的文件，在解压缩之后的文件中，你会发现文件拥有者的属性显示的是一串数字，这很正常，就是因为系统只认识代表你身份的 ID，这串数字就是用户的 ID（UID）号。

Linux 系统中，每个用户的 ID 细分为 2 种，分别是用户 ID（User ID，简称 UID）和组 ID（Group ID，简称 GID），这与文件有拥有者和拥有群组两种属性相对应。

![image-20220818202415800](https://i0.hdslb.com/bfs/album/72fabc90a9dff974f9e2a4abeae34f4e3fb6bf63.png)


从图中可以看到，该文件的拥有者是超级管理员 root，拥有群组也是 root。读者可能会问，既然 Linux 系统不认识用户名，文件是如何判别它的拥有者名称和群组名称的呢？

每个文件都有自己的拥有者 ID 和群组 ID，当显示文件属性时，系统会根据 /etc/passwd 和 /etc/group 文件中的内容，分别找到 UID 和 GID 对应的用户名和群组名，然后显示出来。

/etc/passwd 文件和 /etc/group 文件，后续文章会做详细讲解，这里只需要知道，在 /etc/passwd 文件中，利用 UID 可以找到对应的用户名；在 /etc/group 文件中，利用 GID 可以找到对应的群组名。

### 3.3 groupadd 新增组

添加用户组的命令是 groupadd，命令格式如下:

`[root@localhost ~]# groupadd [选项] 组名`

选项：

- -g GID：指定组 ID；
- -r：创建系统群组。


使用 groupadd 命令创建新群组非常简单，例如：

```bash
[root@localhost ~]# groupadd group1
#添加group1组
[root@localhost ~]# grep "group1" /etc/group
/etc/group:group1:x:502:
/etc/gshadow:group1:!::
```

### 3.4 groupdel 删除组

groupdel 命令用于删除用户组（群组），此命令基本格式为：

`[root@localhost ~]#groupdel 组名`

通过前面的学习不难猜测出，使用 groupdel 命令删除群组，其实就是删除 /etc/gourp 文件和 /etc/gshadow 文件中有关目标群组的数据信息。

例如，删除前面章节中用 groupadd 命令创建的群组 group1，执行命令如下：

```bash
[root@localhost ~]#grep "group1" /etc/group /etc/gshadow
/etc/group:group1:x:505:
/etc/gshadow:group1:!::
[root@localhost ~]#groupdel group1
[root@localhost ~]#grep "group1" /etc/group /etc/gshadow
[root@localhost ~]#
```


注意，不能使用 groupdel 命令随意删除群组。此命令仅适用于删除那些 "不是任何用户初始组" 的群组，换句话说，如果有群组还是某用户的初始群组，则无法使用 groupdel 命令成功删除。例如：

```bash
[root@localhost ~]# useradd temp
#运行如下命令，可以看到 temp 用户建立的同时，还创建了 temp 群组，且将其作为 temp用户的初始组（组ID都是 505）
[root@localhost ~]# grep "temp" /etc/passwd /etc/group /etc/gshadow
/etc/passwd:temp:x:505:505::/home/temp:/bin/bash
/etc/group:temp:x:505:
/etc/gshadow:temp:!::
#下面尝试删除 temp 群组
[root@localhost ~]# groupdel temp
groupdel:cannot remove the primary group of user 'temp'
```

可以看到，groupdel 命令删除 temp 群组失败，且提示“不能删除 temp 用户的初始组”。如果一定要删除 temp 群组，要么修改 temp 用户的 GID，也就是将其初始组改为其他群组，要么先删除 temp 用户。

切记，虽然我们已经学了如何手动删除群组数据，但胡乱地删除群组可能会给其他用户造成不小的麻烦，因此更改文件数据要格外慎重。

### 3.5 groupmod 修改

groupmod 命令用于修改用户组的相关信息，命令格式如下：

`[root@localhost ~]# groupmod [选现] 新组名 旧组名`

选项：

- -g GID：修改组 ID；
- -n 新组名：修改组名；

例子：

```bash
[root@localhost ~]# groupmod -n testgrp group1
#把组名group1修改为testgrp
[root@localhost ~]# grep "testgrp" /etc/group
testgrp:x:502:
#注意GID还是502，但是组名已经改变
```

不过大家还是要注意，用户名不要随意修改，组名和 GID 也不要随意修改，因为非常容易导致管理员逻辑混乱。如果非要修改用户名或组名，则建议大家先删除旧的，再建立新的。

### 3.6 cat /etc/group 查看创建了哪些组

/ect/group 文件是用户组配置文件，即用户组的所有信息都存放在此文件中。

此文件是记录组 ID（GID）和组名相对应的文件。前面讲过，etc/passwd 文件中每行用户信息的第四个字段记录的是用户的初始组 ID，那么，此 GID 的组名到底是什么呢？就要从 /etc/group 文件中查找。

/etc/group 文件的内容可以通过 Vim 看到：

```bash
[root@localhost ~]#vim /etc/group
root:x:0:
bin:x:1:bin,daemon
daemon:x:2:bin,daemon
…省略部分输出…
lamp:x:502:
```

可以看到，此文件中每一行各代表一个用户组。在前面章节中，我们曾创建 lamp 用户，系统默认生成一个 lamp 用户组，在此可以看到，此用户组的 GID 为 502，目前它仅作为 lamp 用户的初始组。

各用户组中，还是以 "：" 作为字段之间的分隔符，分为 4 个字段，每个字段对应的含义为：

组名：密码：GID：该用户组中的用户列表

接下来，分别介绍各个字段具体的含义。

#### 3.6.1 组名

也就是是用户组的名称，有字母或数字构成。同 /etc/passwd 中的用户名一样，组名也不能重复。

#### 3.6.2 组密码

和 /etc/passwd 文件一样，这里的 "x" 仅仅是密码标识，真正加密后的组密码默认保存在 /etc/gshadow 文件中。

不过，用户设置密码是为了验证用户的身份，那用户组设置密码是用来做什么的呢？用户组密码主要是用来指定组管理员的，由于系统中的账号可能会非常多，root 用户可能没有时间进行用户的组调整，这时可以给用户组指定组管理员，如果有用户需要加入或退出某用户组，可以由该组的组管理员替代 root 进行管理。但是这项功能目前很少使用，我们也很少设置组密码。如果需要赋予某用户调整某个用户组的权限，则可以使用 sudo 命令代替。

#### 3.6.3 组ID (GID)

就是群组的 ID 号，Linux 系统就是通过 GID 来区分用户组的，同用户名一样，组名也只是为了便于管理员记忆。

这里的组 GID 与 /etc/passwd 文件中第 4 个字段的 GID 相对应，实际上，/etc/passwd 文件中使用 GID 对应的群组名，就是通过此文件对应得到的。

#### 3.6.4 组中的用户

此字段列出每个群组包含的所有用户。需要注意的是，如果该用户组是这个用户的初始组，则该用户不会写入这个字段，可以这么理解，该字段显示的用户都是这个用户组的附加用户。

举个例子，lamp 组的组信息为 `"lamp:x:502:"`，可以看到，第四个字段没有写入 lamp 用户，因为 lamp 组是 lamp 用户的初始组。如果要查询这些用户的初始组，则需要先到 /etc/passwd 文件中查看 GID（第四个字段），然后到 /etc/group 文件中比对组名。

每个用户都可以加入多个附加组，但是只能属于一个初始组。所以我们在实际工作中，如果需要把用户加入其他组，则需要以附加组的形式添加。例如，我们想让 lamp 也加入 root 这个群组，那么只需要在第一行的最后一个字段加入 lamp，即 `root:x:0:lamp` 就可以了。

一般情况下，用户的初始组就是在建立用户的同时建立的和用户名相同的组。

到此，我们已经学习了/etc/passwd、/etc/shadow、/etc/group，它们之间的关系可以这样理解，即先在 /etc/group 文件中查询用户组的 GID 和组名；然后在 /etc/passwd 文件中查找该 GID 是哪个用户的初始组，同时提取这个用户的用户名和 UID；最后通过 UID 到 /etc/shadow 文件中提取和这个用户相匹配的密码。

# 07 【实操篇-文件权限和搜索查找类命令】

## 1.文件权限类

### 1.1 权限管理的重要性

和 Windows 系统不同，Linux 系统为每个文件都添加了很多的属性，最大的作用就是维护数据的安全。举个简单的例子，在你的 Linux 系统中，和系统服务相关的文件通常只有 root 用户才能读或写，就拿 /etc/shadow 这个文件来说，此文件记录了系统中所有用户的密码数据，非常重要，因此绝不能让任何人读取（否则密码数据会被窃取），只有 root 才可以有读取权限。

此外，如果你有一个软件开发团队，你希望团队中的每个人都可以使用某一些目录下的文件，而非团队的其他人则不予以开放。通过前面章节的学习我们知道，只需要将团队中的所有人加入新的群组，并赋予此群组读写目录的权限，即可实现要求。反之，如果你的目录权限没有做好，就很难防止其他人在你的系统中乱搞。

比如说，本来 root 用户才能做的开关机、ADSL 拨接程序，新增或删除用户等命令，一旦允许任何人拥有这些权限，系统很可能会经常莫名其妙的挂掉。而且，万一 root 用户的密码被其他人获取，他们就可以登录你的系统，从事一些只有 root 用户才能执行的操作，这是绝对不允许发生的。

因此，在服务器上，绝对不是所有的用户都使用 root 身份登录，而要根据不同的工作需要和职位需要，合理分配用户等级和权限等级。

在Linux中我们可以使用ll或者ls -l命令来显示一个文件的属性以及文件所属 的用户和组。

```bash
[root@localhost ~]# ls -al
total 156
drwxr-x---.   4    root   root     4096   Sep  8 14:06 .
drwxr-xr-x.  23    root   root     4096   Sep  8 14:21 ..
-rw-------.   1    root   root     1474   Sep  4 18:27 anaconda-ks.cfg
-rw-------.   1    root   root      199   Sep  8 17:14 .bash_history
-rw-r--r--.   1    root   root       24   Jan  6  2007 .bash_logout
...									
```

### 1.2 权限位

Linux 系统，最常见的文件权限有 3 种，即对文件的读（用 r 表示）、写（用 w 表示）和执行（用 x 表示，针对可执行文件或目录）权限。在 Linux 系统中，每个文件都明确规定了不同身份用户的访问权限，通过 ls 命令即可看到。

> 除此之外，我们有时会看到 s（针对可执行文件或目录，使文件在执行阶段，临时拥有文件所有者的权限）和 t（针对目录，任何用户都可以在此目录中创建文件，但只能删除自己的文件），文件设置 s 和 t 权限，会占用 x 权限的位置。

例如，我们以 root 的身份登陆 Linux，并执行如下指令：

```bash
[root@localhost ~]# ls -al
total 156
drwxr-x---.   4    root   root     4096   Sep  8 14:06 .
drwxr-xr-x.  23    root   root     4096   Sep  8 14:21 ..
-rw-------.   1    root   root     1474   Sep  4 18:27 anaconda-ks.cfg
-rw-------.   1    root   root      199   Sep  8 17:14 .bash_history
-rw-r--r--.   1    root   root       24   Jan  6  2007 .bash_logout
...
```

可以看到，每行的第一列表示的就是各文件针对不同用户设定的权限，一共 11 位，但第 1 位用于表示文件的具体类型，最后一位此文件受 SELinux 的安全规则管理，不是本节关心的内容。

因此，为文件设定不同用户的读、写和执行权限，仅涉及到 9 位字符，以 ls 命令输出信息中的 .bash_logout 文件为例，设定不同用户的访问权限是 rw-r--r--，各权限位的含义如图所示。

![image-20220819131621876](https://i0.hdslb.com/bfs/album/96869fad16ee7c68cfda85376370a6260a5ff219.png)

从图中可以看到，Linux 将访问文件的用户分为 3 类，分别是文件的所有者，所属组（也就是文件所属的群组）以及其他人。

> 除了所有者，以及所属群组中的用户可以访问文件外，其他用户（其他群组中的用户）也可以访问文件，这部分用户都归为其他人范畴。

很显然，Linux 系统为 3 种不同的用户身份，分别规定了是否对文件有读、写和执行权限。拿图 1 来说，文件所有者拥有对文件的读和写权限，但是没有执行权限；所属群组中的用户只拥有读权限，也就是说，这部分用户只能读取文件内容，无法修改文件；其他人也是只能读取文件。

Linux 系统中，多数文件的文件所有者和所属群组都是 root（都是 root 账户创建的），这也就是为什么，root 用户是超级管理员，权限足够大的原因。

### 1.3 读写执行权限（-r、-w、-x）的含义

**从左到右的 10 个字符表示**

![image-20220819131621876](https://i0.hdslb.com/bfs/album/96869fad16ee7c68cfda85376370a6260a5ff219.png)

如果没有权限，就会出现减号[ - ]而已。从左至右用0-9这些数字来表示:

（1）0 首位表示类型

​	在Linux中第一个字符代表这个文件是目录、文件或链接文件等等

> - \- 代表文件 
>
> - d 代表目录 
>
> - l 链接文档(link file)；

（2）第1-3位确定属主（该文件的所有者）拥有该文件的权限。---User

（3）第4-6位确定属组（所有者的同组用户）拥有该文件的权限，---Group

（4）第7-9位确定其他用户拥有该文件的权限 ---Other

**rwx 作用文件和目录的不同解释**

（1）作用到文件：

- [ r ]代表可读(read): 可以读取，查看 

- [ w ]代表可写(write): 可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件

- [ x ]代表可执行(execute):可以被系统执行

（2）作用到目录：

- [ r ]代表可读(read): 可以读取，ls查看目录内容 

- [ w ]代表可写(write): 可以修改，目录内创建+删除+重命名目录 

- [ x ]代表可执行(execute):可以进入该目录

**案例实操**

```bash
[root@hadoop101 ~]# ll
总用量 104
-rw-------. 1 root root 1248 1 月 8 17:36 anaconda-ks.cfg
drwxr-xr-x. 2 root root 4096 1 月 12 14:02 dssz
lrwxrwxrwx. 1 root root 20 1 月 12 14:32 houzi -> xiyou/dssz/houge.tx
```

![image-20220819132433035](https://i0.hdslb.com/bfs/album/08071bb7f281eb00cbb25e476322ae1ebaf83277.png)

（1）如果查看到是文件：链接数指的是硬链接个数。

（2）如果查看的是文件夹：链接数指的是子文件夹个数。

### 1.4 chmod 改变权限

既然我们已经知道文件权限对于一个系统的重要性，也知道每个文件都设定了针对不同用户的访问权限，那么，是否可以手动修改文件的访问权限呢？

可以，通过 chmod 命令即可。chmod 命令设定文件权限的方式有 2 种，分别可以使用数字或者符号来进行权限的变更。

#### 1.4.1 chmod命令使用数字修改文件权限

Linux 系统中，文件的基本权限由 9 个字符组成，以 `rwxrw-r-x` 为例，我们可以使用数字来代表各个权限，各个权限与数字的对应关系如下：

```bash
r --> 4
w --> 2
x --> 1
```

由于这 9 个字符分属 3 类用户，因此每种用户身份包含 3 个权限（r、w、x），通过将 3 个权限对应的数字累加，最终得到的值即可作为每种用户所具有的权限。

拿 rwxrw-r-x 来说，所有者、所属组和其他人分别对应的权限值为：

```bash
所有者 = rwx = 4+2+1 = 7
所属组 = rw- = 4+2 = 6
其他人 = r-x = 4+1 = 5
```

所以，此权限对应的权限值就是 765。

使用数字修改文件权限的 chmod 命令基本格式为：

`[root@localhost ~]# chmod [-R] 权限值 文件名`

- -R（注意是大写）选项表示连同子目录中的所有文件，也都修改设定的权限。

例如，使用如下命令，即可完成对 .bashrc 目录文件的权限修改：

```bash
[root@localhost ~]# ls -al .bashrc
-rw-r--r--. 1 root root 176 Sep 22 2004 .bashrc
[root@localhost ~]# chmod 777 .bashrc
[root@localhost ~]# ls -al .bashrc
-rwxrwxrwx. 1 root root 176 Sep 22 2004 .bashrc
```


再举个例子，通常我们以 Vim 编辑 Shell 文件批处理文件后，文件权限通常是 rw-r--r--（644），那么，如果要将该文件变成可执行文件，并且不让其他人修改此文件，则只需将此文件的权限该为 rwxr-xr-x（755）即可。

**修改整个文件夹里面的所有文件的所有者、所属组、其他用户都具有可读可写可 执行权限。**

```bash
[root@localhost ~]# chmod -R 777 .bashrc
```

#### 1.4.2 chmod命令使用字母修改文件权限

既然文件的基本权限就是 3 种用户身份（所有者、所属组和其他人）搭配 3 种权限（rwx），chmod 命令中用 u、g、o 分别代表 3 种身份，还用 a 表示全部的身份（all 的缩写）。另外，chmod 命令仍使用 r、w、x 分别表示读、写、执行权限。

使用字母修改文件权限的 chmod 命令，其基本格式如图所示。

![image-20220819133048560](https://i0.hdslb.com/bfs/album/d23d9415f6b458accf29eba74ba0c53de5fe8cce.png)

例如，如果我们要设定 .bashrc 文件的权限为 rwxr-xr-x，则可执行如下命令：

```bash
[root@localhost ~]# chmod u=rwx,go=rx .bashrc
[root@localhost ~]# ls -al .bashrc
-rwxr-xr-x. 1 root root 176 Sep 22 2004 .bashrc
```


再举个例子，如果想要增加 .bashrc 文件的每种用户都可做写操作的权限，可以使用如下命令：

```bash
[root@localhost ~]# ls -al .bashrc
-rwxr-xr-x. 1 root root 176 Sep 22 2004 .bashrc
[root@localhost ~]# chmod a+w .bashrc
[root@localhost ~]# ls -al .bashrc
-rwxrwxrwx. 1 root root 176 Sep 22 2004 .bashrc
```

> 实际还是以数字修改为主

### 1.5 chown 改变所有者和所属组

chown 命令，可以认为是 "change owner" 的缩写，主要用于修改文件（或目录）的所有者，除此之外，这个命令也可以修改文件（或目录）的所属组。

当只需要修改所有者时，可使用如下 chown 命令的基本格式：

`[root@localhost ~]# chown [-R] 所有者 文件或目录`

- -R（注意大写）选项表示连同子目录中的所有文件，都更改所有者。

如果需要同时更改所有者和所属组，chown 命令的基本格式为：

`[root@localhost ~]# chown [-R] 所有者:所属组 文件或目录`

注意，在 chown 命令中，所有者和所属组中间也可以使用点（.），但会产生一个问题，如果用户在设定账号时加入了小数点（例如 zhangsan.temp），就会造成系统误判。因此，建议大家使用冒号连接所有者和所属组。

当然，chown 命令也支持单纯的修改文件或目录的所属组，例如 `chown :group install.log` 就表示修改 install.log 文件的所属组，但修改所属组通常使用 chgrp 命令，因此并不推荐大家使用 chown 命令。

另外需要注意的一点是，使用 chown 命令修改文件或目录的所有者（或所属者）时，要保证使用者用户（或用户组）存在，否则该命令无法正确执行，会提示 "invalid user" 或者 "invaild group"。

【例 1】
其实，修改文件的所有者，更多时候是为了得到更高的权限，举一个实例：

```bash
[root@localhost ~]# touch file
#由root用户创建file文件
[root@localhost ~]# ll file
-rw-r--r--. 1 root root 0 Apr 17 05:12 file
#文件的所有者是root，普通用户user对这个文件拥有只读权限
[root@localhost ~]# chown user file
#修改文件的所有者
[root@localhost ~]# ll file
-rw-r--r--. 1 user root 0 Apr 17 05:12 file
#所有者变成了user用户，这时user用户对这个文件就拥有了读、写权限
```

可以看到，通过修改 file 文件的所有者，user 用户从其他人身份（只对此文件有读取权限）转变成了所有者身份，对此文件拥有读和写权限。

【例 2】
Linux 系统中，用户等级权限的划分是非常清楚的，root 用户拥有最高权限，可以修改任何文件的权限，而普通用户只能修改自己文件的权限（所有者是自己的文件），例如：

```bash
[root@localhost ~]# cd /home/user
#进入user用户的家目录
[root@localhost user]# touch test
#由root用户新建文件test
[root@localhost user]# ll test
-rw-r--r--. 1 root root 0 Apr 17 05:37 test
#文件所有者和所属组都是root用户
[root@localhost user]# su - user
#切换为user用户
[user@localhost ~]$ chmod 755 test
chmod:更改"test"的权限：不允许的操作 #user用户不能修改test文件的权限
[user@localhost ~]$ exit
#退回到root身份
[root@localhost user]# chown user test
#由root用户把test文件的所有者改为user用户
[root@localhost user]# su - user
#切换为user用户
[user@localhost ~]$ chmod 755 test
#user用户由于是test文件的所有者，所以可以修改文件的权限
[user@localhost ~]$ ll test
-rwxr-xr-x. 1 user root 0 Apr 17 05:37 test
#查看权限
```

可以看到，user 用户无权更改所有者为 root 用户文件的权限，只有普通用户是这个文件的所有者，才可以修改文件的权限。

【例 3】

```bash
[root@localhost ~]# chown user:group file
[root@localhost ~]# ll file
-rw-r--r--. 1 user group 0 Apr 17 05:12 file
```

### 1.6 chgrp 改变所属组

hgrp 命令用于修改文件（或目录）的所属组。

为了方便初学者记忆，可以将 chgrp 理解为是 "change group" 的缩写。

chgrp 命令的用法很简单，其基本格式为：

`[root@localhost ~]# chgrp [-R] 所属组 文件名（目录名）`

- -R（注意是大写）选项长作用于更改目录的所属组，表示更改连同子目录中所有文件的所属组信息。

使用此命令需要注意的一点是，要被改变的群组名必须是真实存在的，否则命令无法正确执行，会提示 "invaild group name"。

举个例子，当以 root 身份登录 Linux 系统时，主目录中会存在一个名为 install.log 的文件，我们可以使用如下方法修改此文件的所属组：

```bash
[root@localhost ~]# groupadd group1
#新建用于测试的群组 group1
[root@localhost ~]# chgrp group1 install.log
#修改install.log文件的所属组为group1
[root@localhost ~]# ll install.log
-rw-r--r--. 1 root group1 78495 Nov 17 05:54 install.log
#修改生效
[root@localhost ~]# chgrp testgroup install.log
chgrp: invaild group name 'testgroup'
```

可以看到，在具有 group1 群组的前提下，我们成功修改了 install.log 文件的所属组，但我们再次试图将所属组修改为 testgroup 时，命令执行失败，就是因为系统的 /etc/group 文件中，没有 testgroup 群组。

## 2.搜索查找类

### 2.1 find 查找文件或者目录

Linux find 命令用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则 find 命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。find 命令有非常大的灵活性，可以向其指定丰富的搜索条件（如文件权限、属主、属组、文件类型、日期和大小等）来定位系统中的文件和目录。此外，find 还支持对搜索到的结果进行多种类型的命令操作。

**1）基本语法**

find [搜索范围] [选项]

**2）选项说明**

- -name<查询方式> 按照指定的文件名查找模式查找文件
- -user<用户名> 查找属于指定用户名所有文件
- -size<文件大小> 按照指定的文件大小查找文件,单位为
  - b —— 块（512 字节）
  - c —— 字节 
  - w —— 字（2 字节） 
  - k —— 千字节 
  - M —— 兆字节 
  - G —— 吉字节

**3）案例实操**

（1）按文件名：根据名称查找/目录下的filename.txt文件。

`[root@hadoop101 ~]# find xiyou/ -name "*.txt" `

  (2) 将当前目录及其子目录下所有文件后缀为 **.c** 的文件列出来

`find . -name "*.c"`

（3）按拥有者：查找/opt目录下，用户名称为-user的文件

`[root@hadoop101 ~]# find xiyou/ -user atguigu `

（4）按文件大小：在/home目录下查找大于200m的文件（+n 大于 -n小于 n等于）

`[root@hadoop101 ~]find /home -size +204800`

### 2.2 locate 快速定位文件路径

Linux locate命令用于查找符合条件的文档，他会去保存文档和目录名称的数据库内，查找合乎范本样式条件的文档或目录。

一般情况我们只需要输入 **locate your_file_name** 即可查找指定文件。

Locate 指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确 度，管理员必须定期更新 locate 时刻。

**1）基本语法**

​	locate 搜索文件

**2）经验技巧**

locate 与 find 不同: find 是去硬盘找，locate 只在 /var/lib/slocate 资料库中找。

locate 的速度比 find 快，它并不是真的查找，而是查数据库，一般文件数据库在 /var/lib/slocate/slocate.db 中，所以 locate 的查找并不是实时的，而是以数据库的更新为准，一般是系统自己维护，也可以手工升级数据库 ，命令为：

```bash
updatedb
```

**3）案例实操**

查询文件夹

```bash
[root@hadoop101 ~]# updatedb
[root@hadoop101 ~]#locate tmp
```

查找 passwd 文件，输入以下命令：

```bash
[root@hadoop101 ~]# locate passwd
```

### 2.3 grep 过滤查找

Linux grep 命令用于查找文件里符合条件的字符串。

grep 指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设 grep 指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为 **-**，则 grep 指令会从标准输入设备读取数据。

**基本语法**

`grep 选项 查找内容 源文件`

- **-n 或 --line-number** : 在显示符合样式的那一行之前，标示出该行的列数编号。

**实例**

1、在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令：

```
grep test *file 
```

查找前缀有“test”的文件包含“test”字符串的文件

```bash
$ grep test test* #查找前缀有“test”的文件包含“test”字符串的文件  
testfile1:This a Linux testfile! #列出testfile1 文件中包含test字符的行  
testfile_2:This is a linux testfile! #列出testfile_2 文件中包含test字符的行  
testfile_2:Linux test #列出testfile_2 文件中包含test字符的行 
```

2、系统报警显示了时间，但是日志文件太大无法直接 cat 查看。(查询含有特定文本的文件，并拿到这些文本所在的行)

```bash
grep -n '2021-10-24 00:01:11' *.log
```

### 2.4 “|”管道符

> 管道符主要用于多重命令处理，前面命令的打印结果作为后面命令的输入。简单点说就是，就像工厂的流水线一样，进行完一道工序后，继续传送给下一道工序处理…

![image-20220819143831999](https://i0.hdslb.com/bfs/album/16d256b0723671e30bec77446903a97ff201a3c6.png)

`# cat /etc/passwd | grep /bin/bash`

这条命令使用了两个管道，利用第一个管道将cat命令（显示passwd文件的内容）的输出送给grep命令，grep命令找出含有`/bin/bash`的所有行；

**例	**

查找某文件在第几行

`[root@hadoop101 ~]# ls | grep -n test`

### 2.5 wc 计算字数

Linux wc命令用于计算字数。

利用wc指令我们可以计算文件的Byte数、字数、或是列数，若不指定文件名称、或是所给予的文件名为"-"，则wc指令会从标准输入设备读取数据。

**语法**

```bash
wc [-clw][--help][--version][文件...]
```

- -c或--bytes或--chars 只显示Bytes数。
- -l或--lines 显示行数。
- -w或--words 只显示字数。
- --help 在线帮助。
- --version 显示版本信息。

**例1**

在默认的情况下，wc将计算指定文件的行数、字数，以及字节数。使用的命令为：

```
wc testfile 
```

先查看testfile文件的内容，可以看到：

```bash
$ cat testfile  
Linux networks are becoming more and more common, but scurity is often an overlooked  
issue. Unfortunately, in today’s environment all networks are potential hacker targets,  
fro0m tp-secret military research networks to small home LANs.  
Linux Network Securty focuses on securing Linux in a networked environment, where the  
security of the entire network needs to be considered rather than just isolated machines.  
It uses a mix of theory and practicl techniques to teach administrators how to install and  
use security applications, as well as how the applcations work and why they are necesary. 
```

使用 wc统计，结果如下：

```bash
$ wc testfile           # testfile文件的统计信息  
3 92 598 testfile       # testfile文件的行数为3、单词数92、字节数598 
```

其中，3 个数字分别表示testfile文件的行数、单词数，以及该文件的字节数。

**例2**

如果想同时统计多个文件的信息，例如同时统计testfile、testfile_1、testfile_2，可使用如下命令：

```
wc testfile testfile_1 testfile_2   #统计三个文件的信息 
```

输出结果如下：

```bash
$ wc testfile testfile_1 testfile_2  #统计三个文件的信息  
3 92 598 testfile                    #第一个文件行数为3、单词数92、字节数598  
9 18 78 testfile_1                   #第二个文件的行数为9、单词数18、字节数78  
3 6 32 testfile_2                    #第三个文件的行数为3、单词数6、字节数32  
15 116 708 总用量                    #三个文件总共的行数为15、单词数116、字节数708 
```

# 08 【实操篇-压缩和解压类】

## 1.打包（归档）和压缩

归档，也称为打包，指的是一个文件或目录的集合，而这个集合被存储在一个文件中。归档文件没有经过压缩，因此，它占用的空间是其中所有文件和目录的总和。

> 通常，归档总是会和系统（数据）备份联系在一起，不过，有关数据备份的内容，留到后续章节讲，本章仅学习归档命令的基本使用。

和归档文件类似，压缩文件也是一个文件和目录的集合，且这个集合也被存储在一个文件中，但它们的不同之处在于，压缩文件采用了不同的存储方式，使其所占用的磁盘空间比集合中所有文件大小的总和要小。

压缩是指利用算法将文件进行处理，已达到保留最大文件信息，而让文件体积变小的目的。其基本原理为，通过查找文件内的重复字节，建立一个相同字节的词典文件，并用一个代码表示。比如说，在压缩文件中，有不止一处出现了 "C语言中文网"，那么，在压缩文件时，这个词就会用一个代码表示并写入词典文件，这样就可以实现缩小文件体积的目的。

由于计算机处理的信息是以二进制的形式表示的，因此，压缩软件就是把二进制信息中相同的字符串以特殊字符标记，只要通过合理的数学计算，文件的体积就能够被大大压缩。把一个或者多个文件用压缩软件进行压缩，形成一个文件压缩包，既可以节省存储空间，有方便在网络上传送。

如果你能够理解文件压缩的基本原理，那么很容易就能想到，对文件进行压缩，很可能损坏文件中的内容，因此，压缩又可以分为有损压缩和无损压缩。无损压缩很好理解，指的是压缩数据必须准确无误；有损压缩指的是即便丢失个别的数据，对文件也不会造成太大的影响。有损压缩广泛应用于动画、声音和图像文件中，典型代表就是影碟文件格式 mpeg、音乐文件格式 mp3 以及图像文件格式 jpg。

采用压缩工具对文件进行压缩，生成的文件称为压缩包，该文件的体积通常只有原文件的一半甚至更小。需要注意的是，压缩包中的数据无法直接使用，使用前需要利用压缩工具将文件数据还原，此过程又称解压缩。

Linux 下，常用归档命令有 2 个，分别是 tar 和 dd（相对而言，tar 的使用更为广泛）；常用的压缩命令有很多，比如 gzip、zip、bzip2 等。这些命令的详细用法，后续文件会做一一介绍。

注意，tar 命令也可以作为压缩命令，也很常用。

## 2.gzip/gunzip 压缩

### 2.1 gzip压缩文件

gzip 是 Linux 系统中经常用来对文件进行压缩和解压缩的命令，通过此命令压缩得到的新文件，其扩展名通常标记为“.gz”。

> 再强调一下，gzip 命令只能用来压缩文件，不能压缩目录，即便指定了目录，也只能压缩目录内的所有文件。

gzip 命令的基本格式如下：

`[root@localhost ~]# gzip [选项] 源文件`

命令中的源文件，当进行压缩操作时，指的是普通文件；当进行解压缩操作时，指的是压缩文件。

| 选项  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| -c    | 将压缩数据输出到标准输出中，并保留源文件。                   |
| -d    | 对压缩文件进行解压缩。                                       |
| -r    | 递归压缩指定目录下以及子目录下的所有文件。                   |
| -v    | 对于每个压缩和解压缩的文件，显示相应的文件名和压缩比。       |
| -l    | 对每一个压缩文件，显示以下字段：压缩文件的大小；未压缩文件的大小；压缩比；未压缩文件的名称。 |
| -数字 | 用于指定压缩等级，-1 压缩等级最低，压缩比最差；-9 压缩比最高。默认压缩比是 -6。 |

【例 1】基本压缩。
gzip 压缩命令非常简单，甚至不需要指定压缩之后的压缩包名，只需指定源文件名即可。我们来试试：

```bash
[root@localhost ~]# gzip install.log
#压缩instal.log 文件
[root@localhost ~]# ls
anaconda-ks.cfg install.log.gz install.log.syslog
#压缩文件生成，但是源文件也消失了
```

【例 2】保留源文件压缩。
在使用 gzip 命令压缩文件时，源文件会消失，从而生成压缩文件。能不能在压缩文件的时候，不让源文件消失？

```bash
[root@localhost ~]# gzip -c anaconda-ks.cfg >anaconda-ks.cfg.gz
#使用-c选项，但是不让压缩数据输出到屏幕上，而是重定向到压缩文件中，这样可以缩文件的同时不删除源文件
[root@localhost ~]# ls
anaconda-ks.cfg anaconda-ks.cfg.gz install.log.gz install.log.syslog
#可以看到压缩文件和源文件都存在
```

【例 3】 压缩目录。
我们可能会想当然地认为 gzip 命令可以压缩目录。 我们来试试：

```bash
[root@localhost ~]# mkdir test
[root@localhost ~]# touch test/test1
[root@localhost ~]# touch test/test2
[root@localhost ~]# touch test/test3 #建立测试目录，并在里面建立几个测试文件
[root@localhost ~]# gzip -r test/
#压缩目录，并没有报错
[root@localhost ~]# ls
anaconda-ks.cfg anaconda-ks.cfg.gz install.log.gz install.log.syslog test
#但是查看发现test目录依然存在，并没有变为压缩文件
[root@localhost ~]# ls test/
testl .gz test2.gz test3.gz
#原来gzip命令不会打包目录，而是把目录下所有的子文件分别压缩
```

在 Linux 中，打包和压缩是分开处理的。而 gzip 命令只会压缩，不能打包，所以才会出现没有打包目录，而只把目录下的文件进行压缩的情况。

### 2.2 gunzip解压缩文件

gunzip 是一个使用广泛的解压缩命令，它用于解压被 gzip 压缩过的文件（扩展名为 .gz）。

对于解压被 gzip 压缩过的文件，还可以使用 gzip 自己，即 gzip -d 压缩包。

gunzip 命令的基本格式为：

`[root@localhost ~]# gunzip [选项] 文件`

| 选项 | 含义                                               |
| ---- | -------------------------------------------------- |
| -r   | 递归处理，解压缩指定目录下以及子目录下的所有文件。 |
| -c   | 把解压缩后的文件输出到标准输出设备。               |
| -f   | 强制解压缩文件，不理会文件是否已存在等情况。       |
| -l   | 列出压缩文件内容。                                 |
| -v   | 显示命令执行过程。                                 |
| -t   | 测试压缩文件是否正常，但不对其做解压缩操作。       |


【例 1】直接解压缩文件。

`[root@localhost ~]# gunzip install.log.gz`

当然，"gunzip -r"依然只会解压缩目录下的文件，而不会解打包。要想解压缩".gz"格式，还可以使用 "gzip -d"命令，例如：

`[root@localhost ~]# gzip -d anaconda-ks.cfg.gz`

【例 2】要解压缩目录下的内容，则需使用 "-r" 选项，例如：

`[root@localhost ~]# gunzip -r test/`

注意，如果我们压缩的是一个纯文本文件，则可以直接使用 zcat 命令在不解压缩的情况下查看这个文本文件中的内容。例如：

`[root@localhost ~]# zcat anaconda-ks.cfg.gz`

**总结**

（1）只能压缩文件不能压缩目录 

（2）不保留原来的文件 

（3）同时多个文件会产生多个压缩包

## 3.zip/unzip 压缩

### 3.1 zip压缩文件或目录

我们经常会在 Windows 系统上使用 “.zip”格式压缩文件，其实“.zip”格式文件是 Windows 和 Linux 系统都通用的压缩文件类型，属于几种主流的压缩格式（zip、rar等）之一，是一种相当简单的分别压缩每个文件的存储格式，

本节要讲的 zip 命令，类似于 Windows 系统中的 winzip 压缩程序，其基本格式如下：

`[root@localhost ~]#zip [选项] 压缩包名 源文件或源目录列表`

注意，zip 压缩命令需要手工指定压缩之后的压缩包名，注意写清楚扩展名，以便解压缩时使用。

| 选项      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| -r        | 递归压缩目录，及将制定目录下的所有文件以及子目录全部压缩。   |
| -m        | 将文件压缩之后，删除原始文件，相当于把文件移到压缩文件中。   |
| -v        | 显示详细的压缩过程信息。                                     |
| -q        | 在压缩的时候不显示命令的执行过程。                           |
| -压缩级别 | 压缩级别是从 1~9 的数字，-1 代表压缩速度更快，-9 代表压缩效果更好。 |
| -u        | 更新压缩文件，即往压缩文件中添加新文件。                     |


下面给大家举几个例子。

【例 1】zip 命令的基本使用。

```bash
[root@localhost ~]# zip ana.zip anaconda-ks.cfg
adding: anaconda-ks.cfg (deflated 37%)
#压缩
[root@localhost ~]# ll ana.zip
-rw-r--r-- 1 root root 935 6月 1716:00 ana.zip
#压缩文件生成
```

不仅如此，所有的压缩命令都可以同时压缩多个文件，例如：

```bash
[root@localhost ~]# zip test.zip install.log install.log.syslog
adding: install.log (deflated 72%)
adding: install.log.syslog (deflated 85%)
#同时压缩多个文件到test.zip压缩包中
[root@localhost ~]#ll test.zip
-rw-r--r-- 1 root root 8368 6月 1716:03 test.zip
#压缩文件生成
```

【例 2】使用 zip 命令压缩目录，需要使用“-r”选项，例如：

```bash
[root@localhost ~]# mkdir dir1
#建立测试目录
[root@localhost ~]# zip -r dir1.zip dir1
adding: dir1/(stored 0%)
#压缩目录
[root@localhost ~]# ls -dl dir1.zip
-rw-r--r-- 1 root root 160 6月 1716:22 dir1.zip
#压缩文件生成
```

### 3.2 unzip解压zip文件

unzip 命令可以查看和解压缩 zip 文件。该命令的基本格式如下：

`[root@localhost ~]# unzip [选项] 压缩包名`

| 选项        | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| -d 目录名   | 将压缩文件解压到指定目录下。                                 |
| -n          | 解压时并不覆盖已经存在的文件。                               |
| -o          | 解压时覆盖已经存在的文件，并且无需用户确认。                 |
| -v          | 查看压缩文件的详细信息，包括压缩文件中包含的文件大小、文件名以及压缩比等，但并不做解压操作。 |
| -t          | 测试压缩文件有无损坏，但并不解压。                           |
| -x 文件列表 | 解压文件，但不包含文件列表中指定的文件。                     |


【例 1】不论是文件压缩包，还是目录压缩包，都可以直接解压缩，例如：

```bash
[root@localhost ~]# unzip dir1.zip
Archive: dir1.zip
creating: dirl/
#解压缩
```


【例 2】使用 -d 选项手动指定解压缩位置，例如：

```bash
[root@localhost ~]# unzip -d /tmp/ ana.zip
Archive: ana.zip
inflating: /tmp/anaconda-ks.cfg
#把压缩包解压到指定位置
```

**总结**

zip 压缩命令在windows/linux都通用，可以压缩目录且保留源文件。

## 4.tar 打包

Linux 系统中，最常用的归档（打包）命令就是 tar，该命令可以将许多文件一起保存到一个单独的磁带或磁盘中进行归档。不仅如此，该命令还可以从归档文件中还原所需文件，也就是打包的反过程，称为解打包。

使用 tar 命令归档的包通常称为 tar 包（tar 包文件都是以“.tar”结尾的）。

### 4.1 tar命令做打包操作

当 tar 命令用于打包操作时，该命令的基本格式为：

`[root@localhost ~]#tar [选项] 源文件或目录`

| 选项    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| -z      | 打包同时压缩                                                 |
| -c      | 将多个文件或目录进行打包。                                   |
| -A      | 追加 tar 文件到归档文件。                                    |
| -f 包名 | 指定包的文件名。包的扩展名是用来给管理员识别格式的，所以一定要正确指定扩展名； |
| -v      | 显示打包文件过程；                                           |

需要注意的是，在使用 tar 命令指定选项时可以不在选项前面输入“-”。例如，使用“cvf”选项和 “-cvf”起到的作用一样。

下面给大家举几个例子，一起看看如何使用 tar 命令打包文件和目录。

【例 1】打包文件和目录。

```bash
[root@localhost ~]# tar -cvf anaconda-ks.cfg.tar anaconda-ks.cfg
#把anacondehks.cfg打包为 anacondehks.cfg.tar文件
```

选项 "-cvf" 一般是习惯用法，记住打包时需要指定打包之后的文件名，而且要用 ".tar" 作为扩展名。打包目录也是如此：

```bash
[root@localhost ~]# ll -d test/
drwxr-xr-x 2 root root 4096 6月 17 21:09 test/
#test是我们之前的测试目录
[root@localhost ~]# tar -cvf test.tar test/
test/
test/test3
test/test2
test/test1
#把目录打包为test.tar文件
tar命令也可以打包多个文件或目录，只要用空格分开即可。例如:
[root@localhost ~]# tar -cvf ana.tar anaconda-ks.cfg /tmp/
#把anaconda-ks.cfg文件和/tmp目录打包成ana.tar文件包
```


【例 2】打包并压缩多个文件。

```bash
[root@hadoop101 opt]# tar -zcvf houma.tar.gz houge.txt bailongma.txt
houge.txt
bailongma.txt
[root@hadoop101 opt]# ls
houma.tar.gz houge.txt bailongma.txt
```

【例 3】打包压缩目录。

```bash
[root@hadoop101 ~]# tar -zcvf xiyou.tar.gz xiyou/
xiyou/
xiyou/mingjie/
xiyou/dssz/
xiyou/dssz/houge.txt
```

### 4.2 tar命令做解打包操作

当 tar 命令用于对 tar 包做解打包操作时，该命令的基本格式如下：

`[root@localhost ~]#tar [选项] 压缩包`
当用于解打包时，常用的选项与含义如表 2 所示。

| 选项    | 含义                                                       |
| ------- | ---------------------------------------------------------- |
| -x      | 对 tar 包做解打包操作。                                    |
| -f      | 指定要解压的 tar 包的包名。                                |
| -t      | 只查看 tar 包中有哪些文件或目录，不对 tar 包做解打包操作。 |
| -C 目录 | 指定解打包位置。                                           |
| -v      | 显示解打包的具体过程。                                     |


其实解打包和打包相比，只是把打包选项 "-cvf" 更换为 "-xvf"。我们来试试：

```bash
[root@localhost ~]# tar -xvf anaconda-ks.cfg. tar
#解打包到当前目录下
```

如果使用 "-xvf" 选项，则会把包中的文件解压到当前目录下。如果想要指定解压位置，则需要使用 "-C(大写)" 选项。例如：

```bash
[root@localhost ~]# tar -xvf test.tar -C /tmp
#把文件包test.tar解打包到/tmp/目录下
```

如果只想查看文件包中有哪些文件，则可以把解打包选项 "-x" 更换为测试选项 "-t"。例如：

```bash
[root@localhost ~]# tar -tvf test.tar
drwxr-xr-x root/root 0 2016-06-17 21:09 test/
-rw-r-r- root/root 0 2016-06-17 17:51 test/test3
-rw-r-r- root/root 0 2016-06-17 17:51 test/test2
-rw-r-r- root/root 0 2016-06-17 17:51 test/test1
#会用长格式显示test.tar文件包中文件的详细信息
```

# 09 【实操篇-磁盘查看和分区类】

## 1.du 查看文件和目录占用的磁盘空间

du: disk usage 磁盘占用情况

du 是统计目录或文件所占磁盘空间大小的命令。

需要注意的是，使用"ls -r"命令是可以看到文件的大小的。但是大家会发现，在使用"ls -r"命令査看目录大小时，目录的大小多数是 4KB，这是因为目录下的子目录名和子文件名是保存到父目录的 block（默认大小为 4KB）中的，如果父目录下的子目录和子文件并不多，一个 block 就能放下，那么这个父目录就只占用了一个 block 大小。

大家可以将其想象成图书馆的书籍目录和实际书籍。如果我们用"ls-l"命令査看，则只能看到这些书籍占用了 1 页纸的书籍目录，但是实际书籍到底有多少是看不到的，哪怕它堆满了几个房间。

但是我们在统计目录时，不是想看父目录下的子目录名和子文件名到底占用了多少空间，而是想看父目录下的子目录和子文件的总磁盘占用量大小，这时就需要使用 du 命令才能统计目录的真正磁盘占用量大小。

du 命令的格式如下：

`[root@localhost ~]# du [选项] [目录或文件名]`

选项：

- -a：显示每个子文件的磁盘占用量。默认只统计子目录的磁盘占用量
- -h：使用习惯单位显示磁盘占用量，如 KB、MB 或 GB 等；
- -s：统计总磁盘占用量，而不列出子目录和子文件的磁盘占用量


**【例 1】**

```bash
[root@localhost ~]# du
#统计当前目录的总磁盘占用量大小，同时会统计当前目录下所有子目录的磁盘占用量大小，不统计子文件
#磁盘占用量的大小。默认单位为KB
20 ./.gnupg
#统计每个子目录的大小
24 ./yum.bak
8 ./dtest
28 ./sh
188
#统计当前目录总大小
```


**【例 2】**

```bash
[root@localhost ~]# du -a
#统计当前目录的总大小，同时会统计当前目录下所有子文件和子目录磁盘占用量的大小。默认单位为 KB

4 ./.bashjogout
36 ./install.log
4 ./.bash_profile
4 ./.cshrc
…省略部分输出…
188

```

**【例 3】**

```bash
[root@localhost ~]# du -sh
#只统计磁盘占用量总的大小，同时使用习惯单位显示
188K .
```

## 2.df 查看磁盘空间使用情况

df: disk free 空余磁盘

df 命令，用于显示 Linux 系统中各文件系统的硬盘使用情况，包括文件系统所在硬盘分区的总容量、已使用的容量、剩余容量等。

 df 命令主要读取的数据几乎都针对的是整个文件系统。

df 命令的基本格式为：

`[root@localhost ~]# df [选项] [目录或文件名]`

| 选项 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 显示所有文件系统信息，包括系统特有的 /proc、/sysfs 等文件系统； |
| -m   | 以 MB 为单位显示容量；                                       |
| -k   | 以 KB 为单位显示容量，默认以 KB 为单位；                     |
| -h   | 使用人们习惯的 KB、MB 或 GB 等单位自行显示容量；             |
| -T   | 显示该分区的文件系统名称；                                   |
| -i   | 不用硬盘容量显示，而是以含有 inode 的数量来显示。            |


【例 1】

```bash
[root@localhost ~]# df
Filesystem      1K-blocks      Used Available Use% Mounted on
/dev/hdc2         9920624   3823112   5585444  41% /
/dev/hdc3         4956316    141376   4559108   4% /home
/dev/hdc1          101086     11126     84741  12% /boot
tmpfs              371332         0    371332   0% /dev/shm
```

不使用任何选项的 df 命令，默认会将系统内所有的文件系统信息，以 KB 为单位显示出来。

本例中，由 df 命令显示出的各列信息的含义分别是：

- Filesystem：表示该文件系统位于哪个分区，因此该列显示的是设备名称；
- 1K-blocks：此列表示文件系统的总大小，默认以 KB 为单位；
- Used：表示用掉的硬盘空间大小；
- Available：表示剩余的硬盘空间大小；
- Use%：硬盘空间使用率。如果使用率高达 90% 以上，就需要额外注意，因为容量不足，会严重影响系统的正常运行；
- Mounted on：文件系统的挂载点，也就是硬盘挂载的目录位置。


【例 2】

```bash
[root@localhost ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/hdc2             9.5G  3.7G  5.4G  41% /
/dev/hdc3             4.8G  139M  4.4G   4% /home
/dev/hdc1              99M   11M   83M  12% /boot
tmpfs                 363M     0  363M   0% /dev/shm
```

同例 1 不同，这里使用了 -h 选项，因此文件系统的各种容量数据，会以人们习惯的单位（通常使用 GB 或 MB）显示出来。

【例 3】

```bash
[root@localhost ~]# df -h /etc
Filesystem            Size  Used Avail Use% Mounted on
/dev/hdc2             9.5G  3.7G  5.4G  41% /
```

同之前的 2 个例子不同，这里在 df 命令后添加了目录名，在这种情况下，df 命令会自动分析该目录所在的分区，并将所在分区的有关信息显示出来。由此，我们就可以知道，该目录下还可以使用多少容量。

【例 4】

```
[root@localhost ~]# df -aT
Filesystem    Type 1K-blocks    Used Available Use% Mounted on
/dev/hdc2     ext3   9920624 3823112   5585444  41% /
proc          proc         0       0         0   -  /proc
sysfs        sysfs         0       0         0   -  /sys
devpts      devpts         0       0         0   -  /dev/pts
/dev/hdc3     ext3   4956316  141376   4559108   4% /home
/dev/hdc1     ext3    101086   11126     84741  12% /boot
tmpfs        tmpfs    371332       0    371332   0% /dev/shm
none   binfmt_misc         0       0         0   -  /proc/sys/fs/binfmt_misc
sunrpc  rpc_pipefs         0       0         0   -  /var/lib/nfs/rpc_pipefs
```

注意，使用 -a 选项，会将很多特殊的文件系统显示出来，这些文件系统包含的大多是系统数据，存在于内存中，不会占用硬盘空间，因此你会看到，它们所占据的硬盘总容量为 0。

## 3.du命令和df命令的区别

有时我们会发现，使用 du 命令和 df 命令去统计分区的使用情况时，得到的数据是不一样的。那是因为df命令是从文件系统的角度考虑的，通过文件系统中未分配的空间来确定文件系统中已经分配的空间大小。也就是说，在使用 df 命令统计分区时，不仅要考虑文件占用的空间，还要统计被命令或程序占用的空间（最常见的就是文件已经删除，但是程序并没有释放空间）。

而 du 命令是面向文件的，只会计算文件或目录占用的磁盘空间。也就是说，df 命令统计的分区更准确，是真正的空闲空间。

## 4.lsblk 查看设备挂载情况

  lsblk命令的英文是“list block”，即用于列出所有可用块设备的信息，而且还能显示他们之间的依赖关系，但是它不会列出RAM盘的信息。块设备有硬盘，闪存盘，CD-ROM等等。lsblk命令包含在util-linux-ng包中，现在该包改名为util-linux。

【例1】列出所有块设备

直接输入lsblk命令和lsblk -a输出相同

```bash
[root@test1 ~]# lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda 8:0 0 40G 0 disk
├─sda1 8:1 0 300M 0 part /boot
├─sda2 8:2 0 2G 0 part [SWAP]
└─sda3 8:3 0 37.7G 0 part /
sr0 11:0 1 1024M 0 rom
```

- NAME：这是块设备名。
- MAJ:MIN：本栏显示主要和次要设备号。
- RM：本栏显示设备是否可移动设备。注意，在本例中设备
- sdb和sr0的RM值等于1，这说明他们是可移动设备。
- SIZE：本栏列出设备的容量大小信息。例如298.1G表明该设备大小为298.1GB，而1K表明该设备大小为1KB。
- RO：该项表明设备是否为只读。在本案例中，所有设备的
- RO值为0，表明他们不是只读的。
- TYPE：本栏显示块设备是否是磁盘或磁盘上的一个分区。在本例中，sda和sdb是磁盘，而sr0是只读存储（rom）。
- MOUNTPOINT：本栏指出设备挂载的挂载点。

## 5.mount/umount 挂载/卸载

对于Linux用户来讲，不论有几个分区，分别分给哪一个目录使用，它总归就是一个根 目录、一个独立且唯一的文件结构。 

Linux中每个分区都是用来组成整个文件系统的一部分，它在用一种叫做“挂载”的处理 方法，它整个文件系统中包含了一整套的文件和目录，并将一个分区和一个目录联系起来， 要载入的那个分区将使它的存储空间在这个目录下获得。

### 5.1 mount 挂载

所有的硬件设备必须挂载之后才能使用，只不过，有些硬件设备（比如硬盘分区）在每次系统启动时会自动挂载，而有些（比如 U 盘、光盘）则需要手动进行挂载。

通过学习 Linux 文件系统，我们可以对挂载的含义进行引申，挂载指的是将硬件设备的文件系统和 Linux 系统中的文件系统，通过指定目录（作为挂载点）进行关联。而要将文件系统挂载到 Linux 系统上，就需要使用 mount 挂载命令。

mount 命令的常用格式有以下几种：

`[root@localhost ~]# mount [-l]`

单纯使用 mount 命令，会显示出系统中已挂载的设备信息，使用 -l 选项，会额外显示出卷标名称（读者可自行运行，查看输出结果）；

`[root@localhost ~]# mount -a`

-a 选项的含义是自动检查 /etc/fstab 文件中有无疏漏被挂载的设备文件，如果有，则进行自动挂载操作。这里简单介绍一下 /etc/fstab 文件，此文件是自动挂载文件，系统开机时会主动读取 /etc/fstab 这个文件中的内容，根据该文件的配置，系统会自动挂载指定设备。

`[root@localhost ~]# mount [-t 系统类型] [-L 卷标名] [-o 特殊选项] [-n] 设备文件名 挂载点`

各选项的含义分别是：

- -t 系统类型：指定欲挂载的文件系统类型。Linux 常见的支持类型有 EXT2、EXT3、EXT4、iso9660（光盘格式）、vfat、reiserfs 等。如果不指定具体类型，挂载时 Linux 会自动检测。
- -L 卷标名：除了使用设备文件名（例如 /dev/hdc6）之外，还可以利用文件系统的卷标名称进行挂载。
- -n：在默认情况下，系统会将实际挂载的情况实时写入 /etc/mtab 文件中，但在某些场景下（例如单人维护模式），为了避免出现问题，会刻意不写入，此时就需要使用这个选项；
- -o 特殊选项：可以指定挂载的额外选项，比如读写权限、同步/异步等，如果不指定，则使用默认值（defaults）。具体的特殊选项参见表 1；

| 选项        | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| rw/ro       | 是否对挂载的文件系统拥有读写权限，rw 为默认值，表示拥有读写权限；ro 表示只读权限。 |
| async/sync  | 此文件系统是否使用同步写入（sync）或异步（async）的内存机制，默认为异步 async。 |
| dev/nodev   | 是否允许从该文件系统的 block 文件中提取数据，为了保证数据安装，默认是 nodev。 |
| auto/noauto | 是否允许此文件系统被以 mount -a 的方式进行自动挂载，默认是 auto。 |
| suid/nosuid | 设定文件系统是否拥有 SetUID 和 SetGID 权限，默认是拥有。     |
| exec/noexec | 设定在文件系统中是否允许执行可执行文件，默认是允许。         |
| user/nouser | 设定此文件系统是否允许让普通用户使用 mount 执行实现挂载，默认是不允许（nouser），仅有 root 可以。 |
| defaults    | 定义默认值，相当于 rw、suid、dev、exec、auto、nouser、async 这 7 个选项。 |
| remount     | 重新挂载已挂载的文件系统，一般用于指定修改特殊权限。         |


【例 1】

```bash
[root@localhost ~]# mount
#查看系统中已经挂载的文件系统，注意有虚拟文件系统
/dev/sda3 on / type ext4 (rw) <--含义是，将 /dev/sda3 分区挂载到了 / 目录上，文件系统是 ext4，具有读写权限
proc on /proc type proc (rw)
sysfe on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw, gid=5, mode=620)
tmpfs on /dev/shm type tmpfs (rw)
/dev/sda1 on /boot type ext4 (rw)
none on /proc/sys/fe/binfmt_misc type binfmt_misc (rw)
sunrpc on /var/lib/nfe/rpc_pipefs type rpc_pipefs (rw)
```


【例 2】
修改特殊权限。通过例 1 我们查看到，/boot 分区已经被挂载了，而且采用的是 defaults 选项。这里我们重新挂载分区，并采用 noexec 权限禁止执行文件执行，看看会出现什么情况（注意不要用 / 分区做实验，否则系统命令也就不能执行了。

```bash
[root@localhost ~]# mount -o remount noexec /boot
#重新挂载 /boot 分区，并使用 noexec 权限
[root@localhost sh]# cd /boot
#写一个 shell 脚本，看是否会运行
[root@localhost boot]#vi hello.sh
#!/bin/bash
echo "hello!!"
[root@localhost boot]# chmod 755 hello.sh
[root@localhost boot]# ./hello.sh
-bash:./hello.sh:权限不够
#虽然赋予了hello.sh执行权限，但是仍然无法执行
[root@localhost boot]# mount -o remount exec /boot
#记得改回来，否则会影响系统启动
```

对于特殊选项的修改，除非特殊场景下需要，否则不建议大家随意修改，非常容易造成系统出现问题，而且还找不到问题的根源。

【例 3】挂载分区。

```bash
[root@localhost ~]# mkdir /mnt/disk1
#建立挂载点目录
[root@localhost ~]# mount /dev/sdb1 /mnt/disk1
#挂载分区
```

/dev/sdb1 分区还没有被划分。我们在这里只看看挂载分区的方式，非常简单，甚至不需要使用 "-ext4" 命令指定文件系统，因为系统可以自动检测。

为什么使用 Linux 系统的硬盘分区这么麻烦，而不能像 Windows 系统那样，硬盘安装上就可以使用？

其实，硬盘分区（设备）挂载和卸载（使用 umount 命令）的概念源自 UNIX，UNIX 系统一般是作为服务器使用的，系统安全非常重要，特别是在网络上，最简单有效的方法就是“不使用的硬盘分区（设备）不挂载”，因为没有挂载的硬盘分区是无法访问的，这样系统也就更安全了。

另外，这样也可以减少挂载的硬盘分区数量，相应地，也就可以减少系统维护文件的规模，当然也就减少了系统的开销，即提高了系统的效率。

### 5.2 umount 卸载

硬盘分区是否需要卸载，取决于你下次是否还需要使用，一般不对硬盘分区执行卸载操作。

umount 命令用于卸载已经挂载的硬件设备，该命令的基本格式如下：

`[root@localhost ~]# umount 设备文件名或挂载点`
注意，卸载命令后面既可以加设备文件名，也可以加挂载点，不过只能二选一，比如：

```bash
[root@localhost ~]# umount /mnt/usb
#卸载U盘
[root@localhost ~]# umount /mnt/cdrom
#卸载光盘
[root@localhost ~]# umount /dev/sr0
#命令加设备文件名同样是可以卸载的
```


如果加了两个（如下所示），从理论上分析，会对光驱卸载两次，当然，卸载第二次的时候就会报错。

`[root@localhost ~]# mount /dev/sr0 /mnt/cdrom/`
另外，我们在卸载时有可能会出现以下情况：

```bash
[root@localhost ~]# cd /mnt/cdrom/
#进入光盘挂载点
[root@localhost cdrom]# umount /mnt/cdrom/
umount: /mnt/cdrom: device is busy.
#报错，设备正忙
```

这种报错是因为我们已经进入了挂载点，因此，如果要卸载某硬件设备，在执行 umount 命令之前，用户须退出挂载目录。

卸载硬件设备成功与否，除了执行 umount 命令不报错之外，还可以使用 df 命令或 `mount -l` 来查看目标设备是否还挂载在系统中。

## 6.fdisk 分区

我们在安装操作系统的过程中已经对系统硬盘进行了分区，但如果新添加了一块硬盘，想要正常使用，难道需要重新安装操作系统才可以分区吗？

当然不是，在 Linux 中有专门的分区命令 fdisk 和 parted。其中 fdisk 命令较为常用，但不支持大于 2TB 的分区；如果需要支持大于 2TB 的分区，则需要使用 parted 命令，当然 parted 命令也能分配较小的分区。我们先来看看如何使用 fdisk 命令进行分区。

fdisk 命令的格式如下：

```bash
[root@localhost ~]# fdisk ~l
#列出系统分区
[root@localhost ~]# fdisk 设备文件名
#给硬盘分区
```

**该命令必须在 root 用户下才能使用**

使用 "fdisk -l" 查看分区信息，能够看到我们添加的两块硬盘（/dev/sda 和 /dev/sdb）的信息。我们解释一下这些信息，其上半部分态是硬盘的整体状态，/dev/sda 硬盘的总大小是 32.2 GB，共有 3916 个柱面，每个柱面由 255 个磁头读/写数据，每个磁头管理 63 个扇区。每个柱面的大小是 8225280 Bytes，每个扇区的大小是 512 Bytes。

信息的下半部分是分区的信息，共 7 列，含义如下：

- Device：分区的设备文件名。
- Boot：是否为启动引导分区，在这里 /dev/sda1 为启动引导分区。
- Start：起始柱面，代表分区从哪里开始。
- End：终止柱面，代表分区到哪里结束。
- Blocks：分区的大小，单位是 KB。
- id：分区内文件系统的 ID。在 fdisk 命令中，可以 使用 "i" 查看。
- System：分区内安装的系统是什么。

在 fdisk 交互界面中输入 m 可以得到帮助，帮助里列出了 fdisk 可以识别的交互命令，我们来解释一下这些命令

| 命令 | 说 明                                                        |
| ---- | ------------------------------------------------------------ |
| a    | 设置可引导标记                                               |
| b    | 编辑 bsd 磁盘标签                                            |
| c    | 设置 DOS 操作系统兼容标记                                    |
| d    | 删除一个分区                                                 |
| 1    | 显示已知的文件系统类型。82 为 Linux swap 分区，83 为 Linux 分区 |
| m    | 显示帮助菜单                                                 |
| n    | 新建分区                                                     |
| 0    | 建立空白 DOS 分区表                                          |
| P    | 显示分区列表                                                 |
| q    | 不保存退出                                                   |
| s    | 新建空白 SUN 磁盘标签                                        |
| t    | 改变一个分区的系统 ID                                        |
| u    | 改变显示记录单位                                             |
| V    | 验证分区表                                                   |
| w    | 保存退出                                                     |

# 10 【实操篇-进程管理类】

无论是 Linux 系统管理员还是普通用户，监视系统进程的运行情况并适时终止一些失控的进程，是每天的例行事务。和 Linux 系统相比，进程管理在 Windows 中更加直观，它主要是使用"任务管理器"来进行进程管理的。

通常，使用"任务管理器"主要有 3 个目的：

1. 利用"应用程序"和"进程"标签来査看系统中到底运行了哪些程序和进程；
2. 利用"性能"和"用户"标签来判断服务器的健康状态；
3. 在"应用程序"和"进程"标签中强制中止任务和进程；


Linux 中虽然使用命令进行进程管理，但是进程管理的主要目的是一样的，即查看系统中运行的程序和进程、判断服务器的健康状态和强制中止不需要的进程。

那么，到底什么是进程呢？它和我们平时所说的“程序”又有什么联系呢？

## 1.什么是进程和程序

进程是正在执行的一个程序或命令，每个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源。程序是人使用计算机语言编写的可以实现特定目标或解决特定问题的代码集合。

这么讲很难理解，那我们换一种说法。程序是人使用计算机语言编写的，可以实现一定功能，并且可以执行的代码集合。而进程是正在执行中的程序。当程序被执行时，执行人的权限和属性，以及程序的代码都会被加载入内存，操作系统给这个进程分配一个 ID，称为 PID（进程 ID）。

也就是说，在操作系统中，所有可以执行的程序与命令都会产生进程。只是有些程序和命令非常简单，如 ls 命令、touch 命令等，它们在执行完后就会结束，相应的进程也就会终结，所以我们很难捕捉到这些进程。但是还有一些程和命令，比如 httpd 进程，启动之后就会一直驻留在系统当中，我们把这样的进程称作常驻内存进程。

某些进程会产生一些新的进程，我们把这些进程称作子进程，而把这个进程本身称作父进程。比如，我们必须正常登录到 Shell 环境中才能执行系统命令，而 Linux 的标准 Shell 是 bash。我们在 bash 当中执行了 ls 命令，那么 bash 就是父进程，而 ls 命令是在 bash 进程中产生的进程，所以 ls 进程是 bash 进程的子进程。也就是说，子进程是依赖父进程而产生的，如果父进程不存在，那么子进程也不存在了。

## 2.进程管理的作用

在使用 Windows 系统的过程中，使用任务管理器，很大程度上是为了强制关闭“未反应”的软件，也就是杀死进程。的确，这是很多使用进程管理工具或进程管理命令的人最常见的使用方法。不过，杀死进程（强制中止进程）只是进程管理工作中最不常用的手段，因为每个进程都有自己正确的结束方法，而杀死进程是在正常方法已经失效的情况下的后备手段。

那么，进程管理到底应该是做什么的呢？我以为，进程管理主要有以下 3 个作用。

**1) 判断服务器的健康状态**

运维工程师最主要的工作就是保证服务器安全、稳定地运行。理想的状态是，在服务器出现问题，但是还没有造成服务器宕机或停止服务时，就人为干预解决了问题。

进程管理最主要的工作就是判断服务器当前运行是否健康，是否需要人为干预。如果服务器的 CPU 占用率、内存占用率过高，就需要人为介入解决问题了。这又出现了一个问题，我们发现服务器的 CPU 或内存占用率很高，该如何介入呢？是直接终止高负载的进程吗？

当然不是，应该判断这个进程是否是正常进程，如果是正常进程，则说明你的服务器已经不能满足应用需求，你需要更好的硬件或搭建集群了；如果是非法进程占用了系统资源，则更不能直接中止进程，而要判断非法进程的来源、作用和所在位置，从而把它彻底清除。

当然，如果服务器数量很少，我们完全可以人为通过进程管理命令来进行监控与干预，但如果服务器数量较多，那么人为手工监控就变得非常困难了，这时我们就需要相应的监控服务，如 cacti 或 nagios。总之，进程管理工作中最重要的工作就是判断服务器的健康状 态，最理想的状态是服务器宕机之前就解决问题，从而避免服务器的宕机。

**2) 查看系统中所有的进程**

我们需要查看看系统中所有正在运行的进程，通过这些进程可以判断系统中运行了哪些服务、是否有非法服务在运行。

**3) 杀死进程**

这是进程管理中最不常用的手段。当需要停止服务时，会通过正确关闭命令来停止服务（如 apache 服务可以通过 service httpd stop 命令来关闭）。只有在正确终止进程的手段失效的情况下，才会考虑使用 kill 命令杀死进程。

其实，进程管理和 Windows 中任务管理器的作用非常类似，不过大家在使用任务管理器时是为了杀死进程，而不是为了判断服务器的健康状态。

## 3.ps 查看当前系统进程状态

ps 命令是最常用的监控进程的命令，通过此命令可以查看系统中所有运行进程的详细信息。

ps 命令有多种不同的使用方法，这常常给初学者带来困惑。在各种 Linux 论坛上，询问 ps 命令语法的帖子屡见不鲜，而出现这样的情况，还要归咎于 UNIX 悠久的历史和庞大的派系。在不同的 Linux 发行版上，ps 命令的语法各不相同，为此，Linux 采取了一个折中的方法，即融合各种不同的风格，兼顾那些已经习惯了其它系统上使用 ps 命令的用户。

ps 命令的基本格式如下：

```bash
[root@localhost ~]# ps aux
#查看系统中所有的进程
[root@localhost ~]# ps -ef
#可以查看子父进程之间的关系
```

选项：

- a：显示一个终端的所有进程，除会话引线外；
- u：显示进程的归属用户及内存的使用情况；
- x：显示没有控制终端的进程；
- -l：长格式显示更加详细的信息；
- -e：显示所有进程；
- -f：显示完整格式的进程列表

可以看到，ps 命令有些与众不同，它的部分选项不能加入"-"，比如命令"ps aux"，其中"aux"是选项，但是前面不能带“-”。

大家如果执行 "man ps" 命令，则会发现 ps 命令的帮助为了适应不同的类 UNIX 系统，可用格式非常多，不方便记忆。所以，我建议大家记忆几个固定选项即可。比如：

- "ps aux" 可以查看系统中所有的进程；
- "ps -ef" 可以查看系统中所有的进程，而且还能看到进程的父进程的 PID 和进程优先级；
- "ps -l" 只能看到当前 Shell 产生的进程；


有这三个命令就足够了，下面分别来查看。

【例 1】

```
[root@localhost ~]# ps aux
#查看系统中所有的进程
USER PID %CPU %MEM  VSZ  RSS   TTY STAT START TIME COMMAND
root   1  0.0  0.2 2872 1416   ?   Ss   Jun04 0:02 /sbin/init
root   2  0.0  0.0    0    0   ?    S   Jun04 0:00 [kthreadd]
root   3  0.0  0.0    0    0   ?    S   Jun04 0:00 [migration/0]
root   4  0.0  0.0    0    0   ?    S   Jun04 0:00 [ksoftirqd/0]
…省略部分输出…
```

以上输出信息中各列的具体含义。

| 表头    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| USER    | 该进程是由哪个用户产生的。                                   |
| PID     | 进程的 ID。                                                  |
| %CPU    | 该进程占用 CPU 资源的百分比，占用的百分比越高，进程越耗费资源。 |
| %MEM    | 该进程占用物理内存的百分比，占用的百分比越高，进程越耗费资源。 |
| VSZ     | 该进程占用虚拟内存的大小，单位为 KB。                        |
| RSS     | 该进程占用实际物理内存的大小，单位为 KB。                    |
| TTY     | 该进程是在哪个终端运行的。其中，tty1 ~ tty7 代表本地控制台终端（可以通过 Alt+F1 ~ F7 快捷键切换不同的终端），tty1~tty6 是本地的字符界面终端，tty7 是图形终端。pts/0 ~ 255 代表虚拟终端，一般是远程连接的终端，第一个远程连接占用 pts/0，第二个远程连接占用 pts/1，依次増长。 |
| STAT    | 进程状态。常见的状态有以下几种：-D：不可被唤醒的睡眠状态，通常用于 I/O 情况。-R：该进程正在运行。-S：该进程处于睡眠状态，可被唤醒。-T：停止状态，可能是在后台暂停或进程处于除错状态。-W：内存交互状态（从 2.6 内核开始无效）。-X：死掉的进程（应该不会出现）。-Z：僵尸进程。进程已经中止，但是部分程序还在内存当中。-<：高优先级（以下状态在 BSD 格式中出现）。-N：低优先级。-L：被锁入内存。-s：包含子进程。-l：多线程（小写 L）。-+：位于后台。 |
| START   | 该进程的启动时间。                                           |
| TIME    | 该进程占用 CPU 的运算时间，注意不是系统时间。                |
| COMMAND | 产生此进程的命令名。                                         |

【例 2】"ps aux"命令可以看到系统中所有的进程，"ps -ef"命令也能看到系统中所有的进程。

```bash
[root@localhost ~]# ps -ef
F S UID PID PPID C  PRI Nl ADDR  SZ WCHAN TTY      TIME  CMD
4 S   0   1    0 0  80   0 -    718 -     ?    00:00:02  init
1 S   0   2    0 0  80   0 -      0 -     ?    00:00:00  kthreadd
1 S   0   3    2 0 -40   - -      0 -     ?    00:00:00  migration/0
1 S   0   4    2 0  80   0 -      0 -     ?    00:00:00  ksoflirqd/0
1 S   0   5    2 0 -40   - -      0 -     ?    00:00:00  migration/0
…省略部分输出…
```

以上输出信息中各列的含义。

| 表头  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| F     | 进程标志，说明进程的权限，常见的标志有两个: 1：进程可以被复制，但是不能被执行；4：进程使用超级用户权限； |
| S     | 进程状态。具体的状态和"psaux"命令中的 STAT 状态一致；        |
| UID   | 运行此进程的用户的 ID；                                      |
| PID   | 进程的 ID；                                                  |
| PPID  | 父进程的 ID；                                                |
| C     | 该进程的 CPU 使用率，单位是百分比；                          |
| PRI   | 进程的优先级，数值越小，该进程的优先级越高，越早被 CPU 执行； |
| NI    | 进程的优先级，数值越小，该进程越早被执行；                   |
| ADDR  | 该进程在内存的哪个位置；                                     |
| SZ    | 该进程占用多大内存；                                         |
| WCHAN | 该进程是否运行。"-"代表正在运行；                            |
| TTY   | 该进程由哪个终端产生；                                       |
| TIME  | 该进程占用 CPU 的运算时间，注意不是系统时间；                |
| CMD   | 产生此进程的命令名；                                         |

【例 3】如果不想看到所有的进程，只想查看一下当前登录产生了哪些进程，那只需使用 "ps -l" 命令就足够了：

```bash
[root@localhost ~]# ps -l
#查看当前登录产生的进程
F S UID   PID  PPID C PRI NI ADDR SZ WCHAN TTY       TIME CMD
4 S 0   18618 18614 0  80  0 - 1681  -     pts/1 00:00:00 bash
4 R 0   18683 18618 4  80  0 - 1619  -     pts/1 00:00:00 ps
```

可以看到，这次从 pts/1 虚拟终端登录，只产生了两个进程：一个是登录之后生成的 Shell，也就是 bash；另一个是正在执行的 ps 命令。

我们再来说说僵尸进程。僵尸进程的产生一般是由于进程非正常停止或程序编写错误，导致子进程先于父进程结束，而父进程又没有正确地回收子进程，从而造成子进程一直存在于内存当中，这就是僵尸进程。

僵尸进程会对主机的稳定性产生影响，所以，在产生僵尸进程后，一定要对产生僵尸进程的软件进行优化，避免一直产生僵尸进程；对于已经产生的僵尸进程，可以在查找出来之后强制中止。

## 4.kill 终止进程

### 4.1 kill 终止进程

kill 从字面来看，就是用来杀死进程的命令，但事实上，这个或多或少带有一定的误导性。从本质上讲，kill 命令只是用来向进程发送一个信号，至于这个信号是什么，是用户指定的。

也就是说，kill 命令的执行原理是这样的，kill 命令会向操作系统内核发送一个信号（多是终止信号）和目标进程的 PID，然后系统内核根据收到的信号类型，对指定进程进行相应的操作。

kill 命令的基本格式如下：

`[root@localhost ~]# kill [信号] PID`

kill 命令是按照 PID 来确定进程的，所以 kill 命令只能识别 PID，而不能识别进程名。Linux 定义了几十种不同类型的信号，读者可以使用 kill -l 命令查看所有信号及其编号，这里仅列出几个常用的信号。

| 信号编号 | 信号名 | 含义                                                         |
| -------- | ------ | ------------------------------------------------------------ |
| 0        | EXIT   | 程序退出时收到该信息。                                       |
| 1        | HUP    | 挂掉电话线或终端连接的挂起信号，这个信号也会造成某些进程在没有终止的情况下重新初始化。 |
| 2        | INT    | 表示结束进程，但并不是强制性的，常用的 "Ctrl+C" 组合键发出就是一个 kill -2 的信号。 |
| 3        | QUIT   | 退出。                                                       |
| 9        | KILL   | 杀死进程，即强制结束进程。                                   |
| 11       | SEGV   | 段错误。                                                     |
| 15       | TERM   | 正常结束进程，是 kill 命令的默认信号。                       |


需要注意的是，表中省略了各个信号名称的前缀 SIG，也就是说，SIGTERM 和 TERM 这两种写法都对，kill 命令都可以理解。

下面，我们举几个例子来说明一下 kill 命令。

【例 1】 标准 kill 命令。


```bash
[root@localhost ~]# kill 2248
#杀死PID是2248的httpd进程，默认信号是15，正常停止
#如果默认信号15不能杀死进程，则可以尝试-9信号，强制杀死进程
```


【例 2】使用“-1”信号，让进程重启。

```bash
[root@localhost ~]# kill -1 2246
使用“-1 (数字1)”信号，让httpd的主进程重新启动
```


学会如何使用 kill 命令之后，再思考一个问题，使用 kill 命令一定可以终止一个进程吗？

答案是否定的。文章开头说过，kill 命令只是“发送”一个信号，因此，只有当信号被程序成功“捕获”，系统才会执行 kill 命令指定的操作；反之，如果信号被“封锁”或者“忽略”，则 kill 命令将会失效。

### 4.2  killall 终止特定的一类进程

killall 也是用于关闭进程的一个命令，但和 kill 不同的是，killall 命令不再依靠 PID 来杀死单个进程，而是通过程序的进程名来杀死一类进程，也正是由于这一点，该命令常与 ps、pstree 等命令配合使用。

killall 命令的基本格式如下：

`[root@localhost ~]# killall [选项] 进程名`

注意，此命令的信号类型同 kill 命令一样，因此这里不再赘述，此命令常用的选项有如下 2 个：

- -i：交互式，询问是否要杀死某个进程；
- -I：忽略进程名的大小写；


接下来，给大家举几个例子。

【例 1】杀死 httpd 进程。

```bash
[root@localhost ~]# killall httpd
#杀死所有进程名是httpd的进程
[root@localhost ~]# ps aux | grep "httpd" | grep -v "grep"
#查询发现所有的httpd进程都消失了
```

## 5.pstree 查看进程树

pstree 命令是以树形结构显示程序和进程之间的关系，此命令的基本格式如下：

`[root@localhost ~]# pstree [选项] [PID或用户名]`

 pstree 命令常用选项以及各自的含义。

| 选项 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 显示启动每个进程对应的完整指令，包括启动进程的路径、参数等。 |
| -c   | 不使用精简法显示进程信息，即显示的进程中包含子进程和父进程。 |
| -n   | 根据进程 PID 号来排序输出，默认是以程序名排序输出的。        |
| -p   | 显示进程的 PID。                                             |
| -u   | 显示进程对应的用户名称。                                     |

需要注意的是，在使用 pstree 命令时，如果不指定进程的 PID 号，也不指定用户名称，则会以 init 进程为根进程，显示系统中所有程序和进程的信息；反之，若指定 PID 号或用户名，则将以 PID 或指定命令为根进程，显示 PID 或用户对应的所有程序和进程。

init 进程是系统启动的第一个进程，进程的 PID 是 1，也是系统中所有进程的父进程。
【例 1】

```bash
[root@1ocalhost ~]# pstree
init──┬──abrc-dump-oopa
├──abrtd
├──acpid
...省略部分输出...
├──rayslogd───3[{rsyslogrd}]
#有3个rsyslogd进程存在
├──sshd───sshd───bash───pstree
#Pstree命令进程是在远程连接中被执行的
├──udevd───2[udevd
└──xinecd
```


【例 2】如果想知道某个用户都启动了哪些进程，使用 pstree 命令可以很容易实现，以 mysql 用户为例：

```bash
[root@1ocalhost ~]# pstree mysql
mysqid---6*[{mysqid}]
```

此输出结果显示了 mysql 用户对应的进程为 mysqid，并且 mysqid 进程拥有 5 个子进程（外加 1 个父进程，共计 6 个进程）。

## 6.top 实时监控系统进程状态

ps 命令可以一次性给出当前系统中进程状态，但使用此方式得到的信息缺乏时效性，并且，如果管理员需要实时监控进程运行情况，就必须不停地执行 ps 命令，这显然是缺乏效率的。

为此，Linux 提供了 top 命令。top 命令可以动态地持续监听进程地运行状态，与此同时，该命令还提供了一个交互界面，用户可以根据需要，人性化地定制自己的输出，进而更清楚地了进程的运行状态。

使用权限：所有使用者。

top 命令的基本格式如下：

`[root@localhost ~]#top [选项]`

选项：

- -d 秒数：指定 top 命令每隔几秒更新。默认是 3 秒；
- -b：使用批处理模式输出。一般和"-n"选项合用，用于把 top 命令重定向到文件中；
- -n 次数：指定 top 命令执行的次数。一般和"-"选项合用；
- -p 进程PID：仅查看指定 ID 的进程；
- -s：使 top 命令在安全模式中运行，避免在交互模式中出现错误；
- -u 用户名：只监听某个用户的进程；

在 top 命令的显示窗口中，还可以使用如下按键，进行一下交互操作：

- ? 或 h：显示交互模式的帮助；
- P：按照 CPU 的使用率排序，默认就是此选项；
- M：按照内存的使用率排序；
- N：按照 PID 排序；
- T：按照 CPU 的累积运算时间排序，也就是按照 TIME+ 项排序；
- k：按照 PID 给予某个进程一个信号。一般用于中止某个进程，信号 9 是强制中止的信号；
- r：按照 PID 给某个进程重设优先级（Nice）值；
- q：退出 top 命令；

我们看看 top 命令的执行结果，如下：

```bash
[root@localhost ~]# top
top - 12:26:46 up 1 day, 13:32, 2 users, load average: 0.00, 0.00, 0.00
Tasks: 95 total, 1 running, 94 sleeping, 0 stopped, 0 zombie
Cpu(s): 0.1%us, 0.1%sy, 0.0%ni, 99.7%id, 0.1%wa, 0.0%hi, 0.1%si, 0.0%st
Mem: 625344k total, 571504k used, 53840k free, 65800k buffers
Swap: 524280k total, 0k used, 524280k free, 409280k cached
PID   USER PR NI VIRT  RES  SHR S %CPU %MEM   TIME+ COMMAND
19002 root 20  0 2656 1068  856 R  0.3  0.2 0:01.87 top
1     root 20  0 2872 1416 1200 S  0.0  0.2 0:02.55 init
2     root 20  0    0    0    0 S  0.0  0.0 0:00.03 kthreadd
3     root RT  0    0    0    0 S  0.0  0.0 0:00.00 migration/0
4     root 20  0    0    0    0 S  0.0  0.0 0:00.15 ksoftirqd/0
5     root RT  0    0    0    0 S  0.0  0.0 0:00.00 migration/0
6     root RT  0    0    0    0 S  0.0  0.0 0:10.01 watchdog/0
7     root 20  0    0    0    0 S  0.0  0.0 0:05.01 events/0
8     root 20  0    0    0    0 S  0.0  0.0 0:00.00 cgroup
9     root 20  0    0    0    0 S  0.0  0.0 0:00.00 khelper
10    root 20  0    0    0    0 S  0.0  0.0 0:00.00 netns
11    root 20  0    0    0    0 S  0.0  0.0 0:00.00 async/mgr
12    root 20  0    0    0    0 S  0.0  0.0 0:00.00 pm
13    root 20  0    0    0    0 S  0.0  0.0 0:01.70 sync_supers
14    root 20  0    0    0    0 S  0.0  0.0 0:00.63 bdi-default
15    root 20  0    0    0    0 S  0.0  0.0 0:00.00 kintegrityd/0
16    root 20  0    0    0    0 S  0.0  0.0 0:02.52 kblockd/0
17    root 20  0    0    0    0 S  0.0  0.0 0:00.00 kacpid
18    root 20  0    0    0    0 S  0.0  0.0 0:00.00 kacpi_notify
```

我们解释一下命令的输出。top 命令的输出内容是动态的，默认每隔 3 秒刷新一次。命令的输出主要分为两部分：

1. 第一部分是前五行，显示的是整个系统的资源使用状况，我们就是通过这些输出来判断服务器的资源使用状态的；
2. 第二部分从第六行开始，显示的是系统中进程的信息；

我们先来说明第一部分的作用。

- 第一行为任务队列信息。

  | 内 容                         | 说 明                                                        |
  | ----------------------------- | ------------------------------------------------------------ |
  | 12:26:46                      | 系统当前时间                                                 |
  | up 1 day, 13:32               | 系统的运行时间.本机己经运行 1 天 13 小时 32 分钟             |
  | 2 users                       | 当前登录了两个用户                                           |
  | load average: 0.00,0.00，0.00 | 系统在之前 1 分钟、5 分钟、15 分钟的平均负载。如果 CPU 是单核的，则这个数值超过 1 就是高负载：如果 CPU 是四核的，则这个数值超过 4 就是高负载 （这个平均负载完全是依据个人经验来进行判断的，一般认为不应该超过服务器 CPU 的核数） |

- 第二行为进程信息。

  | 内 容           | 说 明                                          |
  | --------------- | ---------------------------------------------- |
  | Tasks: 95 total | 系统中的进程总数                               |
  | 1 running       | 正在运行的进程数                               |
  | 94 sleeping     | 睡眠的进程数                                   |
  | 0 stopped       | 正在停止的进程数                               |
  | 0 zombie        | 僵尸进程数。如果不是 0，则需要手工检查僵尸进程 |

- 第三行为 CPU 信息。

  | 内 容           | 说 明                                                        |
  | --------------- | ------------------------------------------------------------ |
  | Cpu(s): 0.1 %us | 用户模式占用的 CPU 百分比                                    |
  | 0.1%sy          | 系统模式占用的 CPU 百分比                                    |
  | 0.0%ni          | 改变过优先级的用户进程占用的 CPU 百分比                      |
  | 99.7%id         | 空闲 CPU 占用的 CPU 百分比                                   |
  | 0.1%wa          | 等待输入/输出的进程占用的 CPU 百分比                         |
  | 0.0%hi          | 硬中断请求服务占用的 CPU 百分比                              |
  | 0.1%si          | 软中断请求服务占用的 CPU 百分比                              |
  | 0.0%st          | st（steal time）意为虚拟时间百分比，就是当有虚拟机时，虚拟 CPU 等待实际 CPU 的时间百分比 |

- 第四行为物理内存信息。

  | 内 容              | 说 明                                                        |
  | ------------------ | ------------------------------------------------------------ |
  | Mem: 625344k total | 物理内存的总量，单位为KB                                     |
  | 571504k used       | 己经使用的物理内存数量                                       |
  | 53840k&ee          | 空闲的物理内存数量。我们使用的是虚拟机，共分配了 628MB内存，所以只有53MB的空闲内存 |
  | 65800k buffers     | 作为缓冲的内存数量                                           |

- 第五行为交换分区（swap）信息。

  | 内 容               | 说 明                        |
  | ------------------- | ---------------------------- |
  | Swap: 524280k total | 交换分区（虚拟内存）的总大小 |
  | Ok used             | 已经使用的交换分区的大小     |
  | 524280k free        | 空闲交换分区的大小           |
  | 409280k cached      | 作为缓存的交换分区的大小     |


我们通过 top 命令的第一部分就可以判断服务器的健康状态。如果 1 分钟、5 分钟、15 分钟的平均负载高于 1，则证明系统压力较大。如果 CPU 的使用率过高或空闲率过低，则证明系统压力较大。如果物理内存的空闲内存过小，则也证明系统压力较大。

这时，我们就应该判断是什么进程占用了系统资源。如果是不必要的进程，就应该结束这些进程；如果是必需进程，那么我们该増加服务器资源（比如増加虚拟机内存），或者建立集群服务器。

我们还要解释一下缓冲（buffer）和缓存（cache）的区别：

- 缓存（cache）是在读取硬盘中的数据时，把最常用的数据保存在内存的缓存区中，再次读取该数据时，就不去硬盘中读取了，而在缓存中读取。
- 缓冲（buffer）是在向硬盘写入数据时，先把数据放入缓冲区,然后再一起向硬盘写入，把分散的写操作集中进行，减少磁盘碎片和硬盘的反复寻道，从而提高系统性能。

简单来说，缓存（cache）是用来加速数据从硬盘中"读取"的，而缓冲（buffer）是用来加速数据"写入"硬盘的。

再来看 top 命令的第二部分输出，主要是系统进程信息，各个字段的含义如下：

- PID：进程的 ID。
- USER：该进程所属的用户。
- PR：优先级，数值越小优先级越高。
- NI：优先级，数值越小、优先级越高。
- VIRT：该进程使用的虚拟内存的大小，单位为 KB。
- RES：该进程使用的物理内存的大小，单位为 KB。
- SHR：共享内存大小，单位为 KB。
- S：进程状态。
- %CPU：该进程占用 CPU 的百分比。
- %MEM：该进程占用内存的百分比。
- TIME+：该进程共占用的 CPU 时间。
- COMMAND：进程的命令名。


这部分和 ps 命令的输出比较类似，只是如果在终端执行 top 命令，则不能看到所有的进程，而只能看到占比靠前的进程。接下来我们举几个 top 命令常用的实例。

【例 1】如果只想让 top 命令查看某个进程，就可以使用 "-p 选项"。命令如下：

```bash
[root@localhost ~]# top -p 15273
#只查看 PID为 15273的apache进程
top - 14:28:47 up 1 day, 15:34, 3 users, load average: 0.00,0.00,0.00
Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
Cpu(s): 0.0%us, 0.0%sy, 0.0%ni,100.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
Mem: 625344k total, 574124k used, 51220k free, 67024k buffers
Swap: 524280k total, Ok used, 524280k free, 409344k cached
PID     USER PR NI VIRT  RES SHR S %CPU %MEM  TIME+  COMMAND
15273 daemon 20 0  4520 1192 580 S  0.0  0.2 0:00.00   httpd
```

## 7.netstat 显示网络状态和端口占用信息

**1）基本语法** 

`netstat -anp | grep 进程号 （功能描述：查看该进程网络信息） `

`netstat –nlp | grep 端口号 （功能描述：查看网络端口号占用情况`

**2）选项说明**

- -a 显示所有正在监听（listen）和未监听的套接字（socket）

- -n 拒绝显示别名，能显示数字的全部转化成数字 

- -l 仅列出在监听的服务状态 

- -p 表示显示哪个进程在调用

**3）案例实操**

（1）通过进程号查看sshd进程的网络信息

```bash
[root@hadoop101 hadoop-2.7.2]# netstat -anp | grep sshd
tcp 0 0 0.0.0.0:22 0.0.0.0:* LISTEN
951/sshd
tcp 0 0 192.168.202.100:22 192.168.202.1:57741
ESTABLISHED 3380/sshd: root@pts
tcp 0 52 192.168.202.100:22 192.168.202.1:57783
ESTABLISHED 3568/sshd: root@pts
tcp 0 0 192.168.202.100:22 192.168.202.1:57679
ESTABLISHED 3142/sshd: root@pts
tcp6 0 0 :::22 :::* LISTEN
951/sshd
unix 2 [ ] DGRAM 39574 3568/sshd:
root@pts
unix 2 [ ] DGRAM 37452 3142/sshd:
root@pts
unix 2 [ ] DGRAM 48651 3380/sshd:
root@pts
unix 3 [ ] STREAM CONNECTED 21224 951/sshd
```

（2）查看某端口号是否被占用

```bash
[root@hadoop101 桌面]# netstat -nltp | grep 22
tcp 0 0 192.168.122.1:53 0.0.0.0:* LISTEN
1324/dnsmasq
tcp 0 0 0.0.0.0:22 0.0.0.0:* LISTEN
951/sshd
tcp6 0 0 :::22 :::* LISTEN
951/sshd
```

# 11 【实操篇-定时任务 软件安装 克隆虚拟机】

## 1.crontab 系统定时任务

在实际工作中，系统的定时任务一般是需要重复执行的，这就需要使用 crontab 命令来执行循环定时任务。

每个用户都可以实现自己的 crontab 定时任务，只需使用这个用户身份执行“crontab -e”命令即可。当然，这个用户不能写入 /etc/cron.deny 文件。

crontab 命令的基本格式如下：

`[root@localhost ~]# crontab [选项] [file]`

注意，这里的 file 指的是命令文件的名字，表示将 file 作为 crontab 的任务列表文件并载入 crontab，若在命令行中未指定文件名，则此命令将接受标准输入（键盘）上键入的命令，并将它们键入 crontab。

| 选项    | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| -u user | 用来设定某个用户的 crontab 服务，例如 "-u demo" 表示设备 demo 用户的 crontab 服务，此选项一般有 root 用户来运行。 |
| -e      | 编辑某个用户的 crontab 文件内容。如果不指定用户，则表示编辑当前用户的 crontab 文件。 |
| -l      | 显示某用户的 crontab 文件内容，如果不指定用户，则表示显示当前用户的 crontab 文件内容。 |
| -r      | 从 /var/spool/cron 删除某用户的 crontab 文件，如果不指定用户，则默认删除当前用户的 crontab 文件。 |
| -i      | 在删除用户的 crontab 文件时，给确认提示。                    |

其实 crontab 定时任务非常简单，只需执行“crontab -e”命令，然后输入想要定时执行的任务即可。不过，当我们执行“crontab -e”命令时，打开的是一个空文件，而且操作方法和 Vim 是一致的。那么，这个文件的格式才是我们真正需要学习的内容。文件格式如下：

```bash
[root@localhost ！]# crontab -e
#进入 crontab 编辑界面。会打开Vim编辑你的任务
* * * * * 执行的任务
```

这个文件中是通过 5 个“*”来确定命令或任务的执行时间的，这 5 个“*。

| 项目      | 含义                           | 范围                    |
| --------- | ------------------------------ | ----------------------- |
| 第一个"*" | 一小时当中的第几分钟（minute） | 0~59                    |
| 第二个"*" | 一天当中的第几小时（hour）     | 0~23                    |
| 第三个"*" | 一个月当中的第几天（day）      | 1~31                    |
| 第四个"*" | 一年当中的第几个月（month）    | 1~12                    |
| 第五个"*" | 一周当中的星期几（week）       | 0~7（0和7都代表星期日） |

在时间表示中，还有一些特殊符号需要学习。

| 特殊符号  | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| *（星号） | 代表任何时间。比如第一个"*"就代表一小时种每分钟都执行一次的意思。 |
| ,（逗号） | 代表不连续的时间。比如"0 8，12，16***命令"就代表在每天的 8 点 0 分、12 点 0 分、16 点 0 分都执行一次命令。 |
| -（中杠） | 代表连续的时间范围。比如"0 5 ** 1-6命令"，代表在周一到周六的凌晨 5 点 0 分执行命令。 |
| */n       | 代表每隔多久执行一次。比如"*/10****命令"，代表每隔 10 分钟就执行一次命令。 |

当“crontab -e”编辑完成之后，一旦保存退出，那么这个定时任务实际就会写入 /var/spool/cron/ 目录中，每个用户的定时任务用自己的用户名进行区分。而且 crontab 命令只要保存就会生效，只要 crond 服务是启动的。知道了这 5 个时间字段的含义，我们多举几个时间的例子来熟悉一下时间字段。

| 时间              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| 45 22 ***命令     | 在 22 点 45 分执行命令                                       |
| 0 17 ** 1命令     | 在每周一的 17 点 0 分执行命令                                |
| 0 5 1，15**命令   | 在每月 1 日和 15 日的凌晨 5 点 0 分执行命令                  |
| 40 4 ** 1-5命令   | 在每周一到周五的凌晨 4 点 40 分执行命令                      |
| */10 4 ***命令    | 在每天的凌晨 4 点，每隔 10 分钟执行一次命令                  |
| 0 0 1，15 * 1命令 | 在每月 1 日和 15 日，每周一个 0 点 0 分都会执行命令，注意：星期几和几日最好不要同时出现，因为它们定义的都是天，非常容易让管理员混淆 |

现在我们已经对这 5 个时间字段非常熟悉了，可是在“执行的任务”字段中都可以写什么呢？既可以定时执行系统命令，也可以定时执行某个 Shell 脚本，这里举几个实际的例子。

【例 1】让系统每隔 5 分钟就向 /tmp/test 文件中写入一行“11”，验证一下系统定时任务是否会执行。

```bash
[root@localhost ~]# crontab -e
#进入编辑界面
*/5 * * * * /bin/echo "11" >> /tmp/test
```

这个任务在时间工作中没有任何意义，但是可以很简单地验证我们的定时任务是否可以正常执行。如果觉得每隔 5 分钟太长，那就换成“*”，让它每分钟执行一次。而且和 at 命令一样，如果我们定时执行的是系统命令，那么最好使用绝对路径。

【例 2】让系统在每周二的凌晨 5 点 05 分重启一次。

```bash
[root@localhost ~]# crontab -e
5.5 * * 2 /sbin/shutdown -r now
```

如果服务器的负载压力比较大，则建议每周重启一次，让系统状态归零。比如绝大多数游戏服务器每周维护一次，维护时最主要的工作就是重启，让系统状态归零。这时可以让我们的服务器自动来定时执行。

【例 3】在每月 1 日、10 日、15 日的凌晨 3 点 30 分都定时执行日志备份脚本 autobak.sh。

```bash
[root@localhost ~]# crontab -e
30.3 1，10，15 * * /root/sh/autobak.sh
```

这些定时任务保存之后，就可以在指定的时间执行了。我们可以使用命令来查看和删除定时任务，命令如下：

```bash
[root@localhost ~]# crontab -l
#查看root用户的crontab任务
*/5 * * * * /bin/echo "11" >> /tmp/test
5.5 * * 2 /sbin/shutdown -r now
30.3 1，10，15 * * /root/sh/autobak.sh
[root@localhost ~]# crontab -r
#删除root用户所有的定时任务。如果只想删除某个定时任务，则可以执行“crontab -e”命令进入
#编辑模式手工删除
[root@localhost ~]# crontab -l
no crontab for root
#删除后，再查询就没有root用户的定时任务了
```

在书写 crontab 定时任务时，需要注意以下几个事项：

- 6 个选项都不能为空，必须填写。如果不确定，则使用“*”代表任意时间。
- crontab 定时任务的最小有效时间是分钟，最大有效时间是月。像 2018 年某时执行、3 点 30 分 30 秒这样的时间都不能被识别。
- 在定义时间时，日期和星期最好不要在一条定时任务中出现，因为它们都以天为单位，非常容易让管理员混淆。
- 在定时任务中，不管是直接写命令，还是在脚本中写命令，最好都使用绝对路径。有时使用相对路径的命令会报错。

## 2.软件包管理

### 2.1 源码包和二进制包

Linux下的软件包众多，且几乎都是经 GPL 授权、免费开源（无偿公开源代码）的。这意味着如果你具备修改软件源代码的能力，只要你愿意，可以随意修改。

> GPL，全称 General Public License，中文名称“通用性公开许可证”，简单理解 GPL 就是一个保护软件自由的一个协议，经 GPL 协议授权的软件必须开源，请猛击《[开源协议](http://c.biancheng.net/view/2947.html)》了解更多信息。

Linux下的软件包可细分为两种，分别是源码包和二进制包。

#### 2.1.1 源码包

实际上，源码包就是一大堆源代码程序，是由程序员按照特定的格式和语法编写出来的。

我们都知道，计算机只能识别机器语言，也就是二进制语言，所以源码包的安装需要一名“翻译官”将“abcd”翻译成二进制语言，这名“翻译官”通常被称为编译器。

“编译”指的是从源代码到直接被计算机（或虚拟机）执行的目标代码的翻译过程，编译器的功能就是把源代码翻译为二进制代码，让计算机识别并运行。

虽然源码包免费开源，但用户不会编程怎么办？一大堆源代码程序不会使用怎么办？源码包容易安装吗？等等这些都是使用源码包安装方式无法解答的问题。

另外，由于源码包的安装需要把源代码编译为二进制代码，因此安装时间较长。比如，大家应该都在 Windows下安装过 QQ，QQ 功能较多，程序相对较大（有 70 MB左右），但由于其并非是以源码包的形式发布，而是编译后才发布的，因此只需几分钟（经过简单的配置）即可安装成功。但如果我们以源码包安装的方式在 Linux 中安装一个 [MySQL](http://c.biancheng.net/mysql/) 数据库，即便此软件的压缩包仅有 23 MB左右，也需要 30 分钟左右的时间（根据硬件配置不同，略有差异）。

通过对比你会发现，源码包的编译是很费时间的，况且绝多大数用户并不熟悉程序语言，在安装过程中我们只能祈祷程序不要报错，否则初学者很难解决。

为了解决使用源码包安装方式的这些问题，Linux 软件包的安装出现了使用二进制包的安装方式。

#### 2.1.2 二进制包

二进制包，也就是源码包经过成功编译之后产生的包。由于二进制包在发布之前就已经完成了编译的工作，因此用户安装软件的速度较快（同 Windows下安装软件速度相当），且安装过程报错几率大大减小。

二进制包是 Linux 下默认的软件安装包，因此二进制包又被称为默认安装软件包。目前主要有以下 2 大主流的二进制包管理系统：

- RPM 包管理系统：功能强大，安装、升级、査询和卸载非常简单方便，因此很多 Linux 发行版都默认使用此机制作为软件安装的管理方式，例如 Fedora、CentOS、SuSE 等。
- DPKG 包管理系统：由 Debian Linux 所开发的包管理机制，通过 DPKG 包，Debian Linux 就可以进行软件包管理，主要应用在 Debian 和 Ubuntu 中。

> RPM 包管理系统和 DPKG 管理系统的原理和形式大同小异，可以触类旁通。由于本教程使用的是 CentOS 6.x 版本，因此本节主要讲解 RPM 二进制包。

#### 2.1.3 源码包 VS RPM二进制包

源码包一般包含多个文件，为了方便发布，通常会将源码包做打包压缩处理，Linux 中最常用的打包压缩格式为“tar.gz”，因此源码包又被称为 Tarball。

Tarball 是 Linux 系统的一款打包工具，可以对源码包进行打包压缩处理，人们习惯上将最终得到的打包压缩文件称为 Tarball 文件。

源码包需要我们自己去软件官方网站进行下载，包中通常包含以下内容：

- 源代码文件。
- 配置和检测程序（如 configure 或 config 等）。
- 软件安装说明和软件说明（如 INSTALL 或 README）。


总的来说，使用源码包安装软件具有以下几点好处：

- 开源。如果你有足够的能力，则可以修改源代码。
- 可以自由选择所需的功能。
- 因为软件是编译安装的，所以更加适合自己的系统，更加稳定，效率也更高。
- 卸载方便。


但同时，使用源码包安装软件也有几点不足：

- 安装过程步骤较多，尤其是在安装较大的软件集合时（如 LAMP 环境搭建），容易出现拼写错误。
- 编译时间较长，所以安装时间比二进制安装要长。
- 因为软件是编译安装的，所以在安装过程中一旦报错，新手很难解决。


相比源码包，二进制包是在软件发布时已经进行过编译的软件包，所以安装速度比源码包快得多（和 Windows 下软件安装速度相当）。也正是因为已经进行通译，大家无法看到软件的源代码。

使用 RMP 包安装软件具有以下 2 点好处：

1. 包管理系统简单，只通过几个命令就可以实现包的安装、升级、査询和卸载。
2. 安装速度比源码包安装快得多。


与此同时，使用 RMP 包安装软件有如下不足：

- 经过编译，不能在看到源代码。
- 功能选择不如源码包灵活。
- 依赖性。有时我们会发现，在安装软件包 a 时需要先安装 b 和 c，而在安装 b 时需要先安装 d 和 e。这就需要先安装 d 和 e，再安装 b 和 c，最后才能安装 a。比如，我买了一个漂亮的灯具，打算安装在客厅里，可是在安装灯具之前，客厅需要有顶棚，并且顶棚需要刷好油漆。安装软件和装修及其类似，需要有一定的顺序，但是有时依赖性会非常强。

#### 2.1.4 如何选择

通过源码包和 RMP 二进制包的对比，在 Linux 进行软件安装时，我们应该使用哪种软件包呢？

为了更好的区别两种软件包，这里举个例子。假设我们想做一套家具，源码包就像所有的家具完全由自己动手手工打造（手工编译），想要什么样的板材、油漆、颜色和样式都由自己决定（功能自定义，甚至可以修改源代码）。想想就觉得爽，完全不用被黑心的厂商所左右，而且不用担心质量问题（软件更适合自己的系统，效率更高，更加稳定）。但是，所花费的时间大大超过了买一套家具的时间（编译浪费时间），而且自己真的有做木工这个能力吗（需要对源代码非常了解）？就算请别人定制好的家具，再由自己组装，万一哪个部件不匹配（报错很难解决），怎么办？

那么二进制包呢？也是我们需要一套家具，去商场买了一套（安装简单），家具都是现成的，不会有哪个部件不匹配，除非因为自身问题没有量好尺寸而导致放不下（报错很少）。但是我们完全不知道这套家具用的是什么材料、油漆是否合格，而且家具的样式不能随意选择（软件基本不能自定义功能）。

### 2.2 RPM包统一命名规则

RPM 二进制包的命名需遵守统一的命名规则，用户通过名称就可以直接获取这类包的版本、适用平台等信息。

RPM 二进制包命名的一般格式如下：

> 包名-版本号-发布次数-发行商-Linux平台-适合的硬件平台-包扩展名


例如，RPM 包的名称是`httpd-2.2.15-15.el6.centos.1.i686.rpm`，其中：

- httped：软件包名。这里需要注意，httped 是包名，而 httpd-2.2.15-15.el6.centos.1.i686.rpm 通常称为包全名，包名和包全名是不同的，在某些 Linux 命令中，有些命令（如包的安装和升级）使用的是包全名，而有些命令（包的查询和卸载）使用的是包名，一不小心就会弄错。
- 2.2.15：包的版本号，版本号的格式通常为`主版本号.次版本号.修正号`。
- 15：二进制包发布的次数，表示此 RPM 包是第几次编程生成的。
- el*：软件发行商，el6 表示此包是由 Red Hat 公司发布，适合在 RHEL 6.x (Red Hat Enterprise Unux) 和 CentOS 6.x 上使用。
- centos：表示此包适用于 CentOS 系统。
- i686：表示此包使用的硬件平台，目前的 RPM 包支持的平台如表所示：

| 平台名称 | 适用平台信息                                                 |
| -------- | ------------------------------------------------------------ |
| i386     | 386 以上的计算机都可以安装                                   |
| i586     | 686 以上的计算机都可以安装                                   |
| i686     | 奔腾 II 以上的计算机都可以安装，目前所有的 CPU 是奔腾 II 以上的，所以这个软件版本居多 |
| x86_64   | 64 位 CPU 可以安装                                           |
| noarch   | 没有硬件限制                                                 |

- rpm：RPM 包的扩展名，表明这是编译好的二进制包，可以使用 rpm 命令直接安装。此外，还有以 src.rpm 作为扩展名的 RPM 包，这表明是源代码包，需要安装生成源码，然后对其编译并生成 rpm 格式的包，最后才能使用 rpm 命令进行安装。


有人可能会问，Linux 系统不靠扩展名分区文件类型，那为什么包全名中要包含 .rpm 扩展名呢？其实，这里的扩展名是为系统管理员准备的，如果我们不对 RPM 包标注扩展名，管理员很难知道这是一个 RPM 包，当然也就无法正确使用。

### 2.3 RPM包安装、卸载和升级

#### 2.3.1 RPM包默认安装路径

通常情况下，RPM 包采用系统默认的安装路径，所有安装文件会按照类别分散安装到表所示的目录中。

| 安装路径        | 含 义                      |
| --------------- | -------------------------- |
| /etc/           | 配置文件安装目录           |
| /usr/bin/       | 可执行的命令安装目录       |
| /usr/lib/       | 程序所使用的函数库保存位置 |
| /usr/share/doc/ | 基本的软件使用手册保存位置 |
| /usr/share/man/ | 帮助文件保存位置           |


RPM 包的默认安装路径是可以通过命令查询的。

除此之外，RPM 包也支持手动指定安装路径，但此方式并不推荐。因为一旦手动指定安装路径，所有的安装文件会集中安装到指定位置，且系统中用来查询安装路径的命令也无法使用（需要进行手工配置才能被系统识别），得不偿失。

与 RPM 包不同，源码包的安装通常采用手动指定安装路径（习惯安装到 /usr/local/ 中）的方式。既然安装路径不同，同一 apache 程序的源码包和 RPM 包就可以安装到一台 Linux 服务器上（但同一时间只能开启一个，因为它们需要占用同一个 80 端口）。

#### 2.3.2 RPM 包的安装

安装 RPM 的命令格式为：

`[root@localhost ~]# rpm -ivh 包全名`

注意一定是包全名。涉及到包全名的命令，一定要注意路径，可能软件包在光盘中，因此需提前做好设备的挂载工作。

此命令中各选项参数的含义为：

- -i：安装（install）;
- -v：显示更详细的信息（verbose）;
- -h：打印 #，显示安装进度（hash）;


例如，使用此命令安装 apache 软件包，如下所示：

```bash
[root@localhost ~]# rpm -ivh \
/mnt/cdrom/Packages/httpd-2.2.15-15.el6.centos.1.i686.rpm
Preparing...
####################
[100%]
1:httpd
####################
[100%]
```

注意，直到出现两个 100% 才是真正的安装成功，第一个 100% 仅表示完成了安装准备工作。

此命令还可以一次性安装多个软件包，仅需将包全名用空格分开即可，如下所示：

`[root@localhost ~]# rpm -ivh a.rpm b.rpm c.rpm`

如果还有其他安装要求（比如强制安装某软件而不管它是否有依赖性），可以通过以下选项进行调整：

- -nodeps：不检测依赖性安装。软件安装时会检测依赖性，确定所需的底层软件是否安装，如果没有安装则会报错。如果不管依赖性，想强制安装，则可以使用这个选项。注意，这样不检测依赖性安装的软件基本上是不能使用的，所以不建议这样做。
- -replacefiles：替换文件安装。如果要安装软件包，但是包中的部分文件已经存在，那么在正常安装时会报"某个文件已经存在"的错误，从而导致软件无法安装。使用这个选项可以忽略这个报错而覆盖安装。
- -replacepkgs：替换软件包安装。如果软件包已经安装，那么此选项可以把软件包重复安装一遍。
- -force：强制安装。不管是否已经安装，都重新安装。也就是 -replacefiles 和 -replacepkgs 的综合。
- -test：测试安装。不会实际安装，只是检测一下依赖性。
- -prefix：**指定安装路径。为安装软件指定安装路径，而不使用默认安装路径**。

apache 服务安装完成后，可以尝试启动：

```bash
[root@localhost ~]# systemctl start|stop|restart|status 服务名
```

各参数含义：

- start：启动服务；
- stop：停止服务；
- restart：重启服务；
- status: 查看服务状态；

例如：

`[root@localhost ~]# systemctl start httpd #启动apache服务`

服务启动后，可以查看端口号 80 是否出现。命令如下：

```bash
[root@localhost ~]# netstat -tlun | grep 80
tcp 0 0 :::80:::* LISTEN
```

#### 2.3.3 RPM包的升级

使用如下命令即可实现 RPM 包的升级：

`[root@localhost ~]# rpm -Uvh 包全名`

-U（大写）选项的含义是：如果该软件没安装过则直接安装；若没安装则升级至最新版本。

`[root@localhost ~]# rpm -Fvh 包全名`

-F（大写）选项的含义是：如果该软件没有安装，则不会安装，必须安装有较低版本才能升级。

#### 2.4.4 RPM包的卸载

RPM 软件包的卸载要考虑包之间的依赖性。例如，我们先安装的 httpd 软件包，后安装 httpd 的功能模块 mod_ssl 包，那么在卸载时，就必须先卸载 mod_ssl，然后卸载 httpd，否则会报错。

软件包卸载和拆除大楼是一样的，本来先盖的 2 楼，后盖的 3 楼，那么拆楼时一定要先拆除 3 楼。

如果卸载 RPM 软件不考虑依赖性，执行卸载命令会包依赖性错误，例如：

```bash
[root@localhost ~]# rpm -e httpd
error: Failed dependencies:
httpd-mmn = 20051115 is needed by (installed) mod_wsgi-3.2-1.el6.i686
httpd-mmn = 20051115 is needed by (installed) php-5.3.3-3.el6_2.8.i686
httpd-mmn = 20051115 is needed by (installed) mod_ssl-1:2.2.15-15.el6.
centos.1.i686
httpd-mmn = 20051115 is needed by (installed) mod_perl-2.0.4-10.el6.i686
httpd = 2.2.15-15.el6.centos.1 is needed by (installed) httpd-manual-2.2.
15-15.el6.centos.1 .noarch
httpd is needed by (installed) webalizer-2.21_02-3.3.el6.i686
httpd is needed by (installed) mod_ssl-1:2.2.15-15.el6.centos.1.i686
httpd=0:2.2.15-15.el6.centos.1 is needed by(installed)mod_ssl-1:2.2.15-15.el6.centos.1.i686
```

### 2.4 rpm命令查询软件包

rpm 命令还可用来对 RPM 软件包做查询操作，具体包括：

- 查询软件包是否已安装；
- 查询系统中所有已安装的软件包；
- 查看软件包的详细信息；
- 查询软件包的文件列表；
- 查询某系统文件具体属于哪个 RPM 包。

使用 rpm 做查询命令的格式如下：

`[root@localhost ~]# rpm 选项 查询对象`

#### 2.4.1 rpm -q：查询软件包是否安装

用 rpm 查询软件包是否安装的命令格式为：

`[root@localhost ~]# rpm -q 包名`

- -q 表示查询，是 query 的首字母。

例如，查看 Linux 系统中是否安装 apache，rpm 查询命令应写成：

```bash
[root@localhost ~]# rpm -q httpd
httpd-2.2.15-15.el6.centos.1.i686
```

注意这里使用的是包名，而不是包全名。因为已安装的软件包只需给出包名，系统就可以成功识别（使用包全名反而无法识别）。

#### 2.4.2 rpm -qa：查询系统中所有安装的软件包

使用 rpm 查询 Linux 系统中所有已安装软件包的命令为：

```bash
[root@localhost ~]# rpm -qa
libsamplerate-0.1.7-2.1.el6.i686
startup-notification-0.10-2.1.el6.i686
gnome-themes-2.28.1-6.el6.noarch
fontpackages-filesystem-1.41-1.1.el6.noarch
gdm-libs-2.30.4-33.el6_2.i686
gstreamer-0.10.29-1.el6.i686
redhat-lsb-graphics-4.0-3.el6.centos.i686
…省略部分输出…
```

此外，这里还可以使用管道符查找出需要的内容，比如：

```bash
[root@localhost ~]# rpm -qa | grep httpd
httpd-devel-2.2.15-15.el6.centos.1.i686
httpd-tools-2.2.15-15.el6.centos.1.i686
httpd-manual-2.2.15-15.el6.centos.1.noarch
httpd-2.2.15-15.el6.centos.1.i686
```

相比`rpm -q 包名`命令，采用这种方式可以找到含有包名的所有软件包。

#### 2.4.3 rpm -qi：查询软件包的详细信息

通过 rpm 命令可以查询软件包的详细信息，命令格式如下：

`[root@localhost ~]# rpm -qi 包名`

- -i 选项表示查询软件信息，是 information 的首字母。

例如，想查看 apache 包的详细信息，可以使用如下命令：

```bash
[root@localhost ~]# rpm -qi httpd
Name : httpd Relocations:(not relocatable)
#包名
Version : 2.2.15 Vendor:CentOS
#版本和厂商
Release : 15.el6.centos.1 Build Date: 2012年02月14日星期二 06时27分1秒
#发行版本和建立时间
Install Date: 2013年01月07日星期一19时22分43秒
Build Host:
c6b18n2.bsys.dev.centos.org
#安装时间
Group : System Environment/Daemons Source RPM:
httpd-2.2.15-15.el6.centos.1.src.rpm
#组和源RPM包文件名
Size : 2896132 License: ASL 2.0
#软件包大小和许可协议
Signature :RSA/SHA1,2012年02月14日星期二 19时11分00秒，Key ID
0946fca2c105b9de
#数字签名
Packager：CentOS BuildSystem http://bugs.centos.org
URL : http://httpd.apache.org/
#厂商网址
Summary : Apache HTTP Server
#软件包说明
Description:
The Apache HTTP Server is a powerful, efficient, and extensible web server.
#描述
```

#### 2.4.4 rpm -ql：命令查询软件包的文件列表

通过前面的学习我们知道，rpm 软件包通常采用默认路径安装，各安装文件会分门别类安放在适当的目录文件下。使用 rpm 命令可以查询到已安装软件包中包含的所有文件及各自安装路径，命令格式为：

`[root@localhost ~]# rpm -ql 包名`

-l 选项表示列出软件包所有文件的安装目录。

例如，查看 apache 软件包中所有文件以及各自的安装位置，可使用如下命令：

```bash
[root@localhost ~]# rpm -ql httpd
/etc/httpd
/etc/httpd/conf
/etc/httpd/conf.d
/etc/httpd/conf.d/README
/etc/httpd/conf.d/welcome.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/conf/magic
…省略部分输出…
```

同时，rpm 命令还可以查询未安装软件包中包含的所有文件以及打算安装的路径，命令格式如下：

`[root@localhost ~]# rpm -qlp 包全名`

- -p 选项表示查询未安装的软件包信息，是 package 的首字母。

注意，由于软件包还未安装，因此需要使用“绝对路径+包全名”的方式才能确定包。

比如，我们想查看 bing 软件包（未安装，绝对路径为：/mnt/cdrom/Packages/bind-9.8.2-0.10.rc1.el6.i686.rpm）中的所有文件及各自打算安装的位置，可以执行如下命令：

```bash
[root@localhost ~]# rpm -qlp /mnt/cdrom/Packages/bind-9.8.2-0.10.rc1.el6.i686.rpm
/etc/NetworkManager/dispatcher.d/13-named
/etc/logrotate.d/named
/etc/named
/etc/named.conf
/etc/named.iscdlv.key
/etc/named.rfc1912.zones
…省略部分输出…
```

### 2.5 yum是什么

本节介绍一种可自动安装软件包（自动解决包之间依赖关系）的安装方式。

yum，全称“Yellow dog Updater, Modified”，是一个专门为了解决包的依赖关系而存在的软件包管理器。就好像 Windows 系统上可以通过 360 软件管家实现软件的一键安装、升级和卸载，Linux 系统也提供有这样的工具，就是 yum。

![image-20220822200549705](https://i0.hdslb.com/bfs/album/8d1c9c739f0030081e8e017a2c12fe0ded19cb6e.png)

可以这么说，yum 是改进型的 RPM 软件管理器，它很好的解决了 RPM 所面临的软件包依赖问题。yum 在服务器端存有所有的 RPM 包，并将各个包之间的依赖关系记录在文件中，当管理员使用 yum 安装 RPM 包时，yum 会先从服务器端下载包的依赖性文件，通过分析此文件从服务器端一次性下载所有相关的 RPM 包并进行安装。

yum 软件可以用 rpm 命令安装，安装之前可以通过如下命令查看 yum 是否已安装：

```bash
[root@localhost ~]# rpm -qa | grep yum
yum-metadata-parser-1.1.2-16.el6.i686
yum-3.2.29-30.el6.centos.noarch
yum-utils-1.1.30-14.el6.noarch
yum-plugin-fastestmirror-1.1.30-14.el6.noarch
yum-plugin-security-1.1.30-14.el6.noarch
```

可以看到，系统上已经安装了 yum。

使用 rpm 命令安装 yum 的具体方式可查看《[Linux怎么安装yum](https://jingyan.baidu.com/article/e3c78d6483a02a3c4d85f578.html)》一节。

使用 yum 安装软件包之前，需指定好 yum 下载 RPM 包的位置，此位置称为 yum 源。换句话说，yum 源指的就是软件安装包的来源。

使用 yum 安装软件时至少需要一个 yum 源。yum 源既可以使用网络 yum 源，也可以将本地光盘作为 yum 源。接下来就给大家介绍这两种 yum 源的搭建方式。

### 2.6 yum命令（查询、安装、升级和卸载软件包）

#### 2.6.1 yum查询命令

使用 yum 对软件包执行查询操作，常用命令可分为以下几种：

- yum list：查询所有已安装和可安装的软件包。例如：

  ```bash
  [root@localhost yum.repos.d]# yum list
  #查询所有可用软件包列表
  Installed Packages
  #已经安装的软件包
  ConsdeKit.i686 0.4.1-3.el6
  @anaconda-CentOS-201207051201 J386/6.3
  ConsdeKit-libs.i686 0.4.1-3.el6 @anaconda-CentOS-201207051201 J386/6.3
  …省略部分输出…
  Available Packages
  #还可以安装的软件包
  389-ds-base.i686 1.2.10.2-15.el6 c6-media
  389-ds-base-devel.i686 1.2.10.2-15.el6 c6-media
  #软件名 版本 所在位置（光盘）
  …省略部分输出…
  ```

- yum list 包名：查询执行软件包的安装情况。例如：

  ```bash
  [root@localhost yum.repos.d]# yum list samba
  Available Packages samba.i686 3.5.10-125.el6 c6-media
  #查询 samba 软件包的安装情况
  ```

- yum search 关键字：从 yum 源服务器上查找与关键字相关的所有软件包。例如：

  ```bash
  [root@localhost yum.repos.d]# yum search samba
  #搜索服务器上所有和samba相关的软件包
  ======N/S Matched:
  samba =======
  samba-client.i686：Samba client programs
  samba-common.i686：Files used by both Samba servers and clients
  samba-doc.i686: Documentation for the Samba suite
  …省略部分输出…
  Name and summary matches only, use"search all" for everything.
  ```

- yum info 包名：查询执行软件包的详细信息。例如：

  ```bash
  [root@localhost yum.repos.d]# yum info samba
  #查询samba软件包的信息
  Available Packages <-没有安装
  Name : samba <-包名
  Arch : i686 <-适合的硬件平台
  Version : 3.5.10 <―版本
  Release : 125.el6 <—发布版本
  Size : 4.9M <—大小
  Repo : c6-media <-在光盘上
  …省略部分输出…
  ```

#### 2.6.2 yum安装命令

yum 安装软件包的命令基本格式为：

`[root@localhost yum.repos.d]# yum -y install 包名`

其中：

- install：表示安装软件包。
- -y：自动回答 yes。如果不加 -y，那么每个安装的软件都需要手工回答 yes；
  例如使用此 yum 命令安装 gcc：

```bash
[root@localhost yum jepos.d]#yum -y install gcc
#使用yum自动安装gcc
```

gcc 是 C 语言的编译器，鉴于该软件包涉及到的依赖包较多，建议使用 yum 命令安装。

#### 2.6.3 yum 升级命令

使用 yum 升级软件包，需确保 yum 源服务器中软件包的版本比本机安装的软件包版本高。

yum 升级软件包常用命令如下：

- `yum -y update`：升级所有软件包。不过考虑到服务器强调稳定性，因此该命令并不常用。
- `yum -y update 包名`：升级特定的软件包。

#### 2.6.4 yum 卸载命令

使用 yum 卸载软件包时，会同时卸载所有与该包有依赖关系的其他软件包，即便有依赖包属于系统运行必备文件，也会被 yum 无情卸载，带来的直接后果就是使系统崩溃。

除非你能确定卸载此包以及它的所有依赖包不会对系统产生影响，否则不要使用 yum 卸载软件包。

yum 卸载命令的基本格式如下：

```bash
[root@localhost yum.repos.d]# yum remove 包名
#卸载指定的软件包
```

例如，使用 yum 卸载 samba 软件包的命令如下：

```bash
[root@localhost yum.repos.d]# yum remove samba
#卸载samba软件包
```

### 2.7 yum命令补充

- check-update 检查是否有可用的更新 rpm 软件包
- clean 清理 yum 过期的缓存
- deplist 显示 yum 软件包的所有依赖关系

## 3.克隆虚拟机

1）从现有虚拟机(关机状态)克隆出新虚拟机，右键选择管理=>克隆

![image-20220822201743112](https://i0.hdslb.com/bfs/album/81ab29b2c6333361a2e5760c8973006c1c69826f.png)

2）点击下一步

![image-20220822201753788](https://i0.hdslb.com/bfs/album/079c90ecd6966877c51e45af7c6700977be46ff6.png)

3）选择虚拟机中的当前状态

![image-20220822201805481](https://i0.hdslb.com/bfs/album/9107e5367729a6d77f0802b4e5e71bcae9160020.png)

4）选择创建完整克隆

![image-20220822201817328](https://i0.hdslb.com/bfs/album/bb606b1ca024f35ea31865e834e039e980f2d9e4.png)

5）设置虚拟机名称及存储位置

![image-20220822201827451](https://i0.hdslb.com/bfs/album/f6e6905cbb1e2e377264341d25e267176fc5d8bf.png)

6）等等等……等待克隆完成