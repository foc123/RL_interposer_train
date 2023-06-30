# RL_interposer_train

软件版本如下：
ngspice-39
python 3.9.16
anaconda   2022.05-Linux-x86
conda rllib

Reference:https://docs.ray.io/en/latest/rllib/index.html

Intallation commands:

$ conda create -n rllib python=3.9
$ conda activate rllib
$ pip install "ray[rllib]" tensorflow torch
$ pip install "gym[atari]" "gym[accept-rom-license]" atari_pyReference:https://docs.ray.io/en/latest/rllib/index.html

Intallation commands:

$ conda create -n rllib python=3.9
$ conda activate rllib
$ pip install "ray[rllib]" tensorflow torch
$ pip install "gym[atari]" "gym[accept-rom-license]" atari_py








chip config file: duo.conf
CHIP       ： 定义chip名称、大小、功率、电压
metal layer： 定义PDN网络的疏密程度、线宽

gzip.ptrace：  定义chip内部模块功率
duo.flp    ： 定义chip内部模块的预设位置


interposer config file : int1.conf
INTERPOSER  ：定义中介层尺寸、输入电压
CHIPLET	    ：定义多个chiplet位置以及子电路文件

bin/diegen duo.conf   生成名为duo的chiplet电路网表
bin/intgen int1.conf  生成包含chiplet的整个系统网表
interposer1_ac.sp     用于ac分析


在interposer1_ac.sp中进行以下操作：
1.  在*-- pkg instances 上方添加 
.include 'int_param_dcap.txt'
.inc 'full-interposer-dcap.inc'

full-interposer-dcap.inc 代表interposer中添加decap的位置
int_param_dcap.txt	代表添加的decap的容值，进行训练时主要修改的是这个文件


2.  在 .include 'pcb.subckt' 下方添加

ivdd 0 nd_pkg_pad 0 ac 1.0
.ac dec 100 10meg 10g
.save vm(nd_pkg_pad)

.control
set filetype=ascii
run
wrdata test.txt vm(nd_pkg_pad)
quit
.endc
.end

nd_pkg_pad是添加电流源的端口，可以换成其他节点名，如port1、port2


将interposer1_ac.sp中external power source下面的内容全部注释掉
