# 本文为中文版Ambertools19版安装记录

**由于国内已知的CSDN的教程过于老旧和bug太多所以记录一下自己的安装经验**

---

*此教程仅在Ubuntu 18.04上测试安装Ambertools19通过。暂时（2020/03/03）只能确保串行版本安装，并行版本安装需要在串行版本安装结束后安装。*

***备注：***
***1.以下以`$`开头的命令指的是在命令行中输入的命令。没有以`$`开头的指的是直接到上文指定的位置中输入或更改对应的部分即可。以`$$`开头的值得是系统的输出，暂时以我的输出为例。没有特殊声明`$`不需要输入***
***2.此处以全新安装的Ubuntu 18.04为操作系统，bash为默认shell，以nano为文本编辑器。不同发行版和版本的Linux可能略有差别，可以提issus。***
***3.目前仅在x86_64平台上经过了测试，暂不确定Arm平台是否相同。***

---

1. 在amber官网下载Ambertools。[官网下载页面](http://ambermd.org/GetAmber.php) 进入下载页面后填写必要的信息后下载即可。得到的是一个.tar.gz的压缩包

2. 在ubuntu中，解压此压缩包到一个你熟悉的目录中。在amber的PDF教程中建议先解压到  /home/myname  下。具体的来说，如果你的用户是ubuntu，你就解压到  /home/ubuntu  下。（如何查看现在的用户是哪个？打开terminal，输入`$ whoami`查看返回即可。
```
$ pwd
$$ ubuntu
```
解压后的目录结构应该是
```
$ pwd
$$ /home/ubuntu
$ ls
$$ Desktop Download amber18 ... 
```
有其他的文件夹也属于正常，不需要删除。要保证有`amber`这个文件夹就好。

3.进行系统升级和安装必要的软件
```
$ apt update && apt upgrade -y
$ apt -y install software-properties-common
$ add-apt-repository universe
$ apt -y install zliblg-dev g++ tcsh make gcc gfortran flex bison patch bc xorg-dev libbz2-dev wget
```
由于输出过多此处不展示输出结果。如果有error，可以试一下`apt install --fix-missing`

4.在上一步安装成功后，进入amber目录
```
$ cd amber18
$ export AMBERHOME=/home/ubuntu/amber18
```
注意，上述的amber指的都是解压目录，不再是压缩包，以后不再赘述。
此时应针对刚才设置的变量进行检查
```
$ echo $AMBERHOME
$$ /home/ubuntu/amber18
```
**注：此处的`$`AMBERHOME中的`$`是必须要输入的。后文中除了在一行开头处输入的`$`不需要输入外，其余均需要。**
~~返回值正确而且能够`cd`到正确目录的话，对于bash的配置文件进行编辑。
`$ nano /home/ubuntu/.bashrc`
在出现的编辑文件中，在最后一行，填上如下内容
`export AMBERHOME=/home/ubuntu/amber18`
这样就不需要每次启动都输入变量值了。~~
5. 在amber目录中进行编译安装的准备。
```
$ pwd
$$ /home/ubuntu/amber18
$ ./configure --help
```
此处会输出所有的命令选项。可以选择不输入`./configure --help`这行命令。
由于Ubuntu中C语言使用的是gnu编译器（此处可能有误，还望大佬指正。下面按照这个说法继续写了）。所以接下来的编译过程中用到的都是**gnu**。
```
$ ./configure gnu
```
此时会自动检测各类变量的配置是否正确以及是否有更新的版本。此处建议更新，但不挂梯子可能更新失败。通常情况下由于amber需要的是python 2.7 而Ubuntu 18.04中并没有默认安装python 2.7。所以此处会提示你安装miniconda。输入y按回车安装miniconda。
在安装结束后，amber目录下会出现两个新的文件，amber.sh和amber.csh。使用这两个文件对于shell中的变量进行修改。
```
$ source /home/ubuntu/amber18/amber.sh
```
6. 此处建议先重启在进行下一步操作……重启过后还是要`$ echo $AMBERHOME`看一下输出是否还是一开始的设置。一般不会有问题，而此处你会发现命令行最左端出现了(base)的字样，证明miniconda安装成功。没有的话问题也不大，输入下面的进行必要的检测。
```
$ ls -lah $AMBERHOME/bin
$$ ...
... amber.conda -> ../miniconda/bin/conda
... amber.pip -> ../miniconda/bin/pip
... amber.python -> ../miniconda/bin/python
...
```
存在上述三条正确的软连接就可以了，其他的不要进行改动。没有这三条软连接的使用`ln -s`进行创建对应的软连接过来。

接下来就是编译了。 
```
$ cd $AMBERHOME
$ make install
```
此时稍等一会，由于不同的处理器的原因，编译所需时间也略有不同。
在编译结束后，需要进行一下检测。
但在运行检测前，需要先检查一下变量是否正确
```
$ nano $AMBERHOME/config.h
```
输入`Ctrl + W`之后输入`pythonpath`后按回车，寻找到以`PATHONPATH=`开头的那行。
如果那行是这样的`PYTHONPATH=$(AMBER_PREFIX)/lib/python2.7/site-packages`则不需要改动，
如果是`PYTHONPATH=$(AMBER_PREFIX)/lib/python3.7/site-packages`的则需要把`python3.7`改成`python2.7` 。
之后输入`Ctrl+X`保存并关闭文件
7. 为啥上一步要改呢，因为不改的话，直接运行`make test`会报错，无法正常运行。
然后在amber刚解压的位置中，输入`$ make test`此时会再滚屏一阵子，时长还是根据不同的处理器有一定差别。
只要随后输出没有报错就是安装成功了。

