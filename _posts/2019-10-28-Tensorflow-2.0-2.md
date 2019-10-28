---
layout:     post
title:      Tensorflow 2.0 02
subtitle:   AutoEncoder #副标题
date:       2019-10-28
author:     ASeeker
header-img: img/shiyuan2.jpg
catalog: true
tags:
    - tensorflow
    - deep learning
    
---




[^1]: tf2 dataset, https://www.tensorflow.org/api_docs/python/tf/data/Dataset#take
[^2]: autoencoder, https://github.com/aymericdamien/TensorFlow-Examples/blob/master/tensorflow_v2/notebooks/3_NeuralNetworks/autoencoder.ipynb
[^3]: UDI, https://v.youku.com/v_show/id_XMzUyODA5NTM0MA==.html?spm=a2h0k.11417342.soresults.dselectbutton&s=efbfbd3e4cefbfbd03ef




### autoencoder 
> Reference[^1] [^2] [^3]

用tf2实现一下最简单的autoencoder  
这里做下简单的记录，就不讲ae的原理了    

写的比较仓促，如有错误或者侵权，请留言指正/email笔者  

```python
import tensorflow as tf
import numpy as np
```
笔者的💻也只能跑一跑mnist数据集了。。。  

```python
from tensorflow.keras.datasets import mnist
(x_train, t_), (x_test, te_) = mnist.load_data()
```

由于是最简单的 我们就直接将28*28的图片reshape成一维的长度为784的vector 直接塞入到dense网络里  
同时将数据大小控制在0-1之间  

```python
x_train, x_test = x_train.astype(np.float32).reshape([-1, 784]) / 255., x_test.astype(np.float32).reshape([-1, 784]) / 255.
```

一些参数  

```python
batch_size = 32
features = 784
num_hidden1 = 128
num_hidden2 = 64
learning_rate = 1e-3
train_steps = 20000
```

用tf的dataset管理方便很多  
such as batch shuffle……
这里有一个`repeat()`，今天上午学习了一下，如果里面是`None`，那么就会重复无限次，还有一个<a href="#1">下文的</a>`take()`方法，可以配合使用。  
（不得不吐槽一下这些东西不借助梯子都好难查啊

```python
train_data = tf.data.Dataset.from_tensor_slices((x_train))
train_data = train_data.repeat().shuffle(10000).batch(batch_size).prefetch(1)
test_data = tf.data.Dataset.from_tensor_slices(x_test)
test_data = test_data.repeat().batch(batch_size).prefetch(1)
```

这里自己手写一下变量  
注意一下因为要送到`tape`里面记录梯度，用`Variable`包一下  

```python
random_normal = tf.initializers.RandomNormal()

weights = {
    'encoder_h1': tf.Variable(random_normal([features, num_hidden1])),
    'encoder_h2': tf.Variable(random_normal([num_hidden1, num_hidden2])),
    'decoder_h1': tf.Variable(random_normal([num_hidden2, num_hidden1])),
    'decoder_h2': tf.Variable(random_normal([num_hidden1, features]))
}

biases = {
    'encoder_b1': tf.Variable(random_normal([num_hidden1])),
    'encoder_b2': tf.Variable(random_normal([num_hidden2])),
    'decoder_b1': tf.Variable(random_normal([num_hidden1])),
    'decoder_b2': tf.Variable(random_normal([features])),
    
}
```

autoencoder最关键的结构部分了  
其实很简单  
先把高维降到低维，然后再恢复到高维  
一个en，一个de  

loss就采用最简单的和原图像做mse即可，下次再写一下更实用一点的vae的代码    

```python
def encoder(x):
    layer1 = tf.nn.sigmoid(tf.add( tf.matmul(x, weights['encoder_h1']) ,biases['encoder_b1']))
    layer2 = tf.nn.sigmoid(tf.add( tf.matmul(layer1, weights['encoder_h2']) ,biases['encoder_b2'] ))
    
    return layer2

def decoder(x):
    layer1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['decoder_h1']) ,biases['decoder_b1']))
    layer2 = tf.nn.sigmoid(tf.add(tf.matmul(layer1, weights['decoder_h2']) ,biases['decoder_b2']))
    
    return layer2

def mse(recons, origin):
    return tf.reduce_mean(tf.square(recons-origin))

optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)
```

下面开始训练   
<a name="1">`take()`</a>方法就是就是从dataset取多少批数据   
如果`take(-1)`那么就是去除所有的数据  
那么设置`repeat()`， 这时再`take(num)`， 将num设置为自己想要的次数就可以了。  


```python
for step, batch_x in enumerate(train_data.take(train_steps+1)):
    
    #print(batch_x.shape)
    with tf.GradientTape() as tape:
        reconstruct_x = decoder(encoder(batch_x))
        loss = mse(reconstruct_x, batch_x)
    trainable_v = list(weights.values()) + list(biases.values())
    
    grads = tape.gradient(loss, trainable_v)
    optimizer.apply_gradients(grads_and_vars=zip(grads, trainable_v))
    
    if step % 1000 == 0:
        print('step %d loss: %f' %(step, loss.numpy()))
    
```

每1k步打印一下loss

```
step 0 loss: 0.237771
step 1000 loss: 0.058389
step 2000 loss: 0.044816
step 3000 loss: 0.037741
step 4000 loss: 0.030860
step 5000 loss: 0.029892
step 6000 loss: 0.023699
step 7000 loss: 0.020474
step 8000 loss: 0.019911
step 9000 loss: 0.013075
step 10000 loss: 0.017382
step 11000 loss: 0.016923
step 12000 loss: 0.016155
step 13000 loss: 0.012536
step 14000 loss: 0.011730
step 15000 loss: 0.012818
step 16000 loss: 0.013795
step 17000 loss: 0.013336
step 18000 loss: 0.011487
step 19000 loss: 0.011111
step 20000 loss: 0.010630
```

### 测试

这里测试一下重建的效果  

```python
from matplotlib import pyplot as plt 

n = 4

origin_x = np.empty((28*n, 28*n))
reconstr_x = np.empty((28*n, 28*n))

for i, x in enumerate(test_data.take(n)):
    recon_x = decoder(encoder(x))
    
    for j in range(n):
        origin_x[i*28:(i+1)*28, j*28:(j+1)*28] = x[j].numpy().reshape([28,28])
        reconstr_x[i*28:(i+1)*28, j*28:(j+1)*28] = recon_x[j].numpy().reshape([28,28])



print("Original Images")     
plt.figure(figsize=(n, n))
plt.imshow(origin_x, origin="upper", cmap="gray")
plt.show()

print("Reconstructed Images")
plt.figure(figsize=(n, n))
plt.imshow(reconstr_x, origin="upper", cmap="gray")
plt.show()
```


原始图像

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8e4phbh1wj307506zt8l.jpg)


重建的图像

这是只有2k个step重建的图像  

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8e4rmr0a1j30eu0dut8y.jpg)

当拉到20k时清晰了许多  

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8e4pt34twj307506zgll.jpg)



本篇博客的正文来了。。

#### 写在前面
上周末偶然看了一下`非自然死亡`，没想到超乎想象的好看，一口气看完了10集。好的作品还是需要慢慢咀嚼的，这两天重温了一下第一集，愈发的赞叹这部日剧。以前看到好的电影，也时常想着卖弄一下自己粗浅的见解，写篇认真的影评，但总是由于电影太长了啊认真看一遍好烧脑、自己比较忙啊诸如此类为借口不了了之。所幸的是，这次借助写博客的契机以及UDI一集时长很短的缘由，在此聊表一下对这部作品的敬意。笔者也是第一次写这样长的影评，难免会有些过度解读甚是错误解读，望批评指正～

可以说，越是往细处看，愈是赞不绝口，赞佩演员们的高超演技以及导演的精心设计。

#### 开场
影片一开始，三澄和东海林日常调侃，三澄大早上就吃着天妇罗盖浇饭，大清早吃这么油腻的东西确实让人很是受不了，但是对于三澄来说，一日之计在于晨，这其实与三澄的‘吃疗法’（笔者暂时这么称呼）密不可分，后面会再说，新的一天用天妇罗盖饭带来好的心情与干劲。而东海林所谓的‘异性交流会’也会为下面某一集（不记得具体哪一集了）的情节直接埋下伏笔。`7k`这一UDI工作者调侃自己工作环境的名词也第一次出现。个人感觉什么花粉症的‘不为大众所知’，也暗示了她们自己内心的压抑着感受，不经意流露了出来。这么短短的几句欢快的聊天快节奏的输出了大量信息，可见真的很用心啊～久部作为一个新人第一次来到了UDI，表现的很稚嫩。中堂的开场则以躺在解剖台上与大家见面，如果没有看后面的情节确实感到很奇怪，这个我们后面再说吧，给人的印象就是一怪人。两位检查技师（东海林和坂本）也表现出了对中堂的排斥，都想远离中堂，这也进一步塑造了中堂的形象，迫不及待想知道中堂的背景，可谓吊足了胃口。  

主要人物的开场完了，下面就是社会环境的铺垫。  

第一例非自然死亡案件的家属前来委托调查，交谈之间所长讲了一下日本的现状，其实就是巧妙的穿插了故事的背景，日本的解剖率在发达国家中最低，`UDI`就此成立。于是乎，第一集要干啥有了，整个影片的bg也建立了，一点也不拖泥带水。

久部年轻与稚嫩的形象在第一次的解剖表现的淋漓尽致，为死者不解剖感到纳闷，然后又直接吐了，解剖完之后又不洗浴，不顾吉言相劝‘臭味不管怎么都是遮不住的’，就喷了点啥？（这部分与后面吃饭别人闻到味道有呼应）医学知识的缺失又显现出他的不专业，原来是7k并且薪资很低的工作只有他一个人来应聘，hhh其实久部也不是真心想来的，自己在杂志社还有一份工作，他过来兼职也只是为了搜集素材。  

这里有一段很简短的对话  
`-法医学是为死者而存在的科学吧 我还是考虑成为治疗生者的临床医生`
`-法医学是为未来而存在的工作`  
吹爆这里的演技，三澄先是出乎意料的转向了久部，然后有点无奈的转过身来说是为了未来，很多很多人都会忽视这些法医工作者的存在，人都已经不在了，他们的工作有多大的价值呢？三澄知道自己去反驳没有任何意义，只是又是一个不了解非自然死亡的人，没有经历的人是不会理解这当中的意义，她只是淡淡的回了这么一句。因为三澄自己的身世，她用自己默默的努力在这个领域前行，这么多年来，纵使别人的冷嘲热讽，因为她内心深处坚信自己的工作是为了更美好的未来而做的，只有靠着自己一步步证明给世人看，年少的悲惨命运，没有使她走向黑暗堕落，反是尽自己一切所能理解帮助感化众生。（再加上石原的buff加成，这个形象太让人喜欢了。

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8e9uins2oj31c00u0wjh.jpg)


#### 剧情第一次加速   
与死者一同工作过的由果也突然死亡。三澄便要去调查小由果的尸体，这时候年轻久部的作用也体现出来了，引导观众的思维走向连环投毒杀人案。

这里有三个细节我想讲一下。(供台上摆着饼干的就算了。。)  
一个是小由果母亲的演技，和三澄一行讲述时，小由果的母亲一直在微笑，讲着讲着说小由果是个很好的孩子，最后还是没能忍住哭了出来，感觉特别真实也很是让人痛心。  
另一个是三澄去小由果家里穿着的是一件黑色正装，上了UDI的车后，随即换上了UDI的白大褂，我想这也是不经意间流露的工作的尽职和热爱吧。  
还有一个是三澄吐槽要是在美国就好了，美国没有火葬，可以继续解剖，hhh这就与这部片后面中堂女朋友被杀的证据直接关联起来了，第一集就有啦。   


晚上，三澄去找男朋友啦。三澄一路小跑着去男朋友那，看到了前面是红灯还在跑，过完马路后继续跑着去，hhh自行体会。男朋友呢，看书、伸懒腰、打瞌睡，显然已经等了很久很久啦。吃饭间，在亲近的人身边，三澄还是会说一说心里话，吐槽一下UDI 7k的生活，可见还是很辛苦的，不过三澄还是在坚持做下去，也言到比大学自由多了，UDI也给自己的法医工作带来了更多可能，不受约束的尽自己所能。hhh经常来这家店竟然最开始是男朋友喜欢，而男朋友自己却不知道，两年前的夏天，那么久了…




#### 剧情二次加速  
写的有点累了，，，，第一次写影评还真。。。质量可能不断下降了（本来就没啥质量hhh） 
三澄一行到死者家里采集可能的毒物，遇上了死者的女朋友马场。  
面对三澄的询问，马场表现得很冷淡 `-身体不好也不会和我说的 他工作很忙`，这其实在后面父母对于儿子的描述以及马场负责着公司的一个大项目中就有了解释。     
顺水推舟，导演娴熟的运用技巧，带观众的视线从连环投毒案升级到马场连环投毒，久部警官上线了hhh，种种质疑马场，而马场表现的很无所谓，这段的演技也很精彩。  
`-你又不在场证明吗？-ha` 马场上下打量着久部，微笑着回答。 

这里就有一个对比，一开始久部是避开死者的血迹走的，但是马场毫无顾忌的踩在了丈夫的血迹上，并且说要在这里睡觉。两个人对于死者的态度截然不同，也为后面马场的清白做了伏笔，试想如果真的是马场蓄意谋杀了丈夫，肯定是表现出很忌讳，不愿意站在他的血迹之上的，而马场把地毯上的那滩血迹甚至有点当作了丈夫的尚存的气息。


#### 剧情再度加速  
仿佛真相就要浮出了水面，一切都显得合情合理。 （第三者 聚酯研究 毒物 乙二醇 饼干）  
影片的这波操作，等下再来个大反转。当然呢这也是个常用的手段。

继续表现出久部的不专业，不知道乙二醇，什么乙儿？  

有两个三澄的画面刻画的很好，在死者公司时，三澄拉住久部让他不要多管闲事，不要乱说话，指责久部不应该乱猜疑，真的是很心地善良啊。饼干未检测出毒素时，三澄说是好，可又不好，一方面不希望马场真的是那么坏坏的人，另一方面这么多天了竟然还没解决掉真的是烦死了啊。所长这时已经开始放弃，要求这周内整理好材料，交给家属，但三澄仍然没有放弃，不行，一定要查的水落石出，为未来工作！ 

#### 逆转
实在木有办法了，向有着3k解剖经验的中堂哥求救去。但中堂表现的不屑一顾，有什么的不会的自己baidu啊！真的是高冷哎，这么漂亮的石原你都拒绝，太不像话了吧。人家中堂两耳不闻窗外案，一心只顾红金鱼。hhh开个玩笑   

很用心啊，久部回来时，三澄称呼他‘警官’hhh  

一baidu，果然查出东西来了    

mars 火星？ ha 久部真的是太不专业了  

影片到这里已经给出了死亡的初步答案   

也引发了大家去思考一个社会问题：舆论声讨的力量   
感觉这个问题还是比较深刻的 对于国家的发展也是有很大参考价值的，如果在一个国民素质很低下的国度，生产力水平也很低下，人们很大程度上追逐私利，一旦出现了某个社会问题，损及了大家的利益，加上一些煽风点火，网络暴力便会成为‘燎原之势’。这样的例子应该还是很多的。我对于这个问题的了解还不是很深刻，在此就不贻笑大方了。  

第二次再看时，发现记者居然也登场了！-您打算怎么负责 将会出现更多的死者  
哼 怪不得他是个坏蛋   


不得不提一下死者的父母。冲破重重记者的包围，面对三澄，-给您添麻烦了 又是深深鞠躬致歉，讲述了他们对儿子的教育，-不能感冒就不去上课 要成为一个坚强的人，是我们的教育有问题吗？死者的父母给人的印象是教育层次、文化素质比较高，几番医院不接受解剖，但仍没有放弃，最后在殡仪馆的介绍下来到了UDI；初次与所长见面时，母亲有些伤心，言辞激动，父亲立即紧握住母亲的手，安慰让其镇静下来，向所长讲述儿子去世的情况；以及对三澄的鞠躬，感觉要是普通人。。。。都怪你查出来了是病毒。。。   

马场也坚守在丈夫身边，面对巨大的舆论压力，并没有逃避啊撇清关系啊。  

死者的父母以及马场小姐也是受害者啊，我们的社会为什么没有人去同情他们呢？那么多人在讨伐一个已经死去的人？一定全部责怪这个死者吗？为什么不能多一些宽容？这些都值得我们去深思。   

所长送礼的镜头，希望院长美言几句不能停了补助，反映了一定的政治问题。   

解锁关键情节，红色的金鱼。虽然这个月没有找到，但是中堂还是给了钱，中堂医生真的很看重很看重女友的事情啊。到底什么是红色的金鱼？一直吸引着观众。最近东央医大有很多死人埋下伏笔。

名场面。暖心的三澄带着豆馅面包来了，不要和我抢！敲黑板记笔记：心情不好就要吃！  
这也说明了为何三澄每天一大早就要吃天妇罗盖浇饭，开始辛苦的工作前吃点好吃的吧～    
这一小段的马场与三澄的对话以及父母对儿子的教育，也说通了一些前面马场的表现，马场工作很忙，死者身体有什么异样也不会和她说，怕打扰到她。  


 

#### 再次逆转

中堂拿出了证据，再度展现其性格高冷还有经验上的优势。中堂表示很无奈，一群猪队友。。。而自己已经调查的很清楚了。没说出来，写在了纸上面，还准备了一封伪造的解剖公文书。可见早就一清二楚事实的真相了。。


这次就当没有发生 刚送了礼   

那处大家都说三澄什么打扮，穿了一件漂亮的裙子，其实是为后文准备的，今天要去见男朋友的父母了。而三澄却急着要去拿尸体，这里又有一个名场面，三澄二话不说套上了牛仔裤坐摩托车去追火化。     

三澄与中堂第一次的价值观交锋，用伪造公文还是家属许可（其实很想看这段是如何说下来的hhhh


院长这波演技很不错，面对所长的质问，斩钉截铁的说不，像极了狡猾推脱的老狐狸政客。所以呢这又怎么样，三澄送来了证据，坐了下来，哑口无言。  
三澄的主角光环是真的强，首先对没有敲门就直接进来表示抱歉，也不忘让院长恢复死者家属的名誉。嗯，优秀的品德集于一身。

三澄解剖，和男朋友父母见面迟到啦。。。 
lemon…   




分手了，三澄一个人迷失在街头   
![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8ebmb5cnlj31c00u0diu.jpg)




#### 第一集的最后
好了，第一个小故事就这样结束了  
`lemon`不停的回旋在耳畔  
镜头回到了新一天的早晨    

早上，我们的三澄继续吃着天妇罗盖浇饭  
![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8ebkv6x2bj31c00u0tc0.jpg)

中堂医生依旧从解剖台上醒来  
![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8ebkx359oj31c00u0jzi.jpg)


而久部才开始成长，认识到了解剖是很重要的事情   
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8eeixcjlrj31c00u07b1.jpg)

突然，又被晴天霹雳：三澄的真名是雨宫，这背后还有耸人听闻的自杀事件…  
悬念就此而来  

好棒的结尾啊  


一段精彩的对话
-你运气真好 在这烂透的社会 还真是稀奇烂没烂透 -不是自己决定的吗 -等你一个人能把证据找齐了 再来说大话 -敌人是谁？-udi   
引发对价值观思考 也是两个人价值观的碰撞   

一处细微的演技
久部听到了不可思议的消息后，对着电话那头 -不好意思再说一遍，然后换了一下耳朵听手机以再次确认，换右手认真打字搜索


#### 写在最后  

自己第一次写这么长的影评，感到很失败。。。感觉写成了故事情节。。。自己的文笔又很烂。。后面也写的越来越仓促。。自己看了看觉得很幼稚。。。以后多多努力吧。也很感谢能看到最后的朋友～     
短短的一集，主要人物悉数登场，形象成功塑造耐人寻味，情节跌宕起伏，前后伏笔不断，就算是配角也演技精心，既有悬疑、爱情，又折射出不少的政治现象、社会问题。与其说是影片节奏很紧凑，我想不如可以说是真的很细致，很是用心。很感谢制片者和演员们作出的奉献，为我们的时代带来了一部好的作品。      

最后的最后！石原里美*** （自行揣测省略内容hhh  






  




