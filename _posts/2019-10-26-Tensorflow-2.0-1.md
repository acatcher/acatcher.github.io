---
layout:     post
title:      Tensorflow 2.0 01
subtitle:   Install #副标题
date:       2019-10-26
author:     ASeeker
header-img: img/shiyuan1.jpg
catalog: true
tags:
    - tensorflow
    
---


哇。。。不能再拖博客了。。。感觉写博客还是很好的 记录了自己的每一天 又‘迫使’自己每天学习   

非自然死亡好好看啊！！🤩 昨晚到今天中午一口气就看完了  

最近在虚拟机上写tf实在是太卡了 本来电脑就没显卡（多么希望自己有块N卡）还是折腾了一下自己的电脑 安装了一下tf2  
这里做一个简单的小结


### anaconda 
命令行的安装包小了200M  
去thu的镜像上下载/梯子

```
bash Anaconda3-2019.10-MacOSX-x86_64.sh  
export PATH="/Users/You/anaconda3/bin:$PATH"  
vim ~/.bash_profile
source ~/.bash_profile
```

### 创建虚拟环境  
方便管理

```
conda create --name tf2.0 python=3.7   
conda activate tf2.0               
```

### tf2
pip install tensorflow==2.0.0
然后由于thu上好像还没tf2 不然应该可以-i https://mirrors.tuna.tsinghua.edu.cn/help/pypi/   

直接下载的速度。。10k/s。。。  

实在受不了了，由于笔者只有chrome的插件，这里笔者用了手动下载安装  
将命令行里的Downloading：后面的一串balabala的网址粘贴下来 嗯 借助梯子把包载下来  
tensorflow-2.0.0-cp37-cp37m-macosx_10_11_x86_64.whl
numpy-1.17.3-cp37-cp37m-macosx_10_9_x86_64.whl等等

然后直接pip install 这些包 就好了  

### jupyter
笔者（暂时）喜欢在jupyter里写 这里再在jupyter里配一下conda的环境
tf2.0是笔者的环境

```
conda install ipykernel    
source activate tf2.0  
python -m ipykernel install --user --name tf2.0 --display-name "tf2.0"
```

心情不好的时候就去多吃点东西吧🤪    
🍗🍔🍟🌭🍖🌮🍩🍦🍪🍭  


一个平凡却又特殊的研究所里折射出社会中种种不可避免的问题  
幸运的是  
这个世界里还有许多像三澄这样温情的灵魂   
永不言弃   
感化人心   

（最后不得不说的是 石原里美实在是太漂亮了hhh