---
layout:     post
title:      Tensorflow 2.0 05
subtitle:   Variational Autoencoder #副标题
date:       2019-11-06
author:     ASeeker
header-img: img/Weathering1.jpg
catalog: true
tags:
    - tensorflow
    - deep learning
    
---

#### 不管晴天还是雨天，我只是想和你相遇。

新海诚新作，又是一部世界系的作品，价值观和君名相反，中规中矩吧。。。不是特别特别棒的那种 画面和bgm还是很棒的，截屏就可做壁纸    


#### VAE  


> Reference[^1] [^2] [^3] [^4] [^5] [^6]

这个VAE 搞了我好久好久 网上的资料真的是鱼龙混杂 看原始paper看了5、6页实在看不下去了 真是好艰难 加上最近的学习效率。。。 直接自闭了三天 现在终于算是明白一点了  
用tf2实现一下  

[这篇博客](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)对自己的帮助挺大的   

关于普通的AE，得到一个latent的表示，也没有啥用，不能在某种condition下generate出特定的输出。  
VAE做了一点升级，（不过效果还是被现在的GAN吊打  

intuitive的理解在李宏毅老师的[dl](http://speech.ee.ntu.edu.tw/~tlkagk/courses_ML16.html)里讲的是十分精彩了，比如说一张满月的照片🌖和一张弦月🌘的照片，取它们latent representation的中值，并不能很好的表现出我们期望出现的3/4个月亮🌗 那么怎么办呢，我们就要假设latent space是从一个分布中取出来的，这样使得在满月和弦月中间的vector既想train出满月又想train出弦月，取一个折中，就可以生成出一个3/4的月亮了。  

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8ot0ese1pj313b0u0gou.jpg)

那么，我们就想让我们的latent不再生成一个具体的值，而是一个分布，我们从中去sample，得到z。用到了高斯混合的思想。现在的问题是如何设计loss。  

我们可以得到我们想要的`p(z,x)=p(z)*p(x|z)`, 我们可以用NN去优化`p(x\|z)`,那么问题的关键就是要去找x如何映射到z，就是`p(z|x)`的分布。  

由贝叶斯公式，可以知道`p(z|x) =  p(z)*p(x|z)/p(x)`,但是p(x)需要在整个z的空间上进行积分才可以算出来，这样的穷举代价太大了。于是我们假设一个分布`q(z|x)`去逼近`p(z|x)`,hhh这不就是我们NN常做的事情嘛。问题就变成了使得这两个分布最接近，使`KL(q(z|x)||p(z|x))`的值最小。具体的推导我就不再这里写了（懒+不会用md打公式hhh）。最后很神奇的是两项，和我们intutive的想法也很一致，一个是要是`p(z)`和`q(z|x)`的分布接近，一个是要reconstruct的error要小。  

不过这里我们也看到了一个问题，那你不就是让所有的z都要是`N(0,I)`的分布了吗？大家的z就都很接近了。。而我感觉不同类型的z应该分的很远，而loss里有`mean**2`这一项。我在训练的时候就碰到了这个问题（不过也有可能是其他问题。。。自己也还是个萌新👶🏻🐣），output都长得差不多，也看不出来东西，后面把kl loss的weight降了，才train出来。[知乎上随便查了一下](https://zhuanlan.zhihu.com/p/52676826)，确实存在一个叫编码坍塌的问题，不过这篇文章自己真的深究不下去了。。vae已经把自己折腾的半死  

下面上代码  


```python
import tensorflow as tf
import numpy as np
from tensorflow.keras import layers
from matplotlib import pyplot as plt 

from tensorflow.keras.datasets import fashion_mnist

(x_train, y_train), (x_test, y_test) = fashion_mnist.load_data()
x_train = x_train.astype(np.float32).reshape([-1,784]) / 255.

train_steps = 10000
display_step = 1000

learning_rate = 1e-3
batch_size = 128

dense1_units = 256
z_dims = 20
img_dims = 784

train_data = tf.data.Dataset.from_tensor_slices(x_train)
train_data = train_data.repeat().shuffle(10000).batch(batch_size).prefetch(1)
```

一个encoder 一个decoder  
z是sample出来的，实现的时候用了一个简单的trick，直接看代码就好了  

```python
class VAE(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.dense0 = layers.Dense(units=dense1_units, activation=tf.nn.relu)
        self.dense1 = layers.Dense(units=dense1_units, activation=tf.nn.relu)
        self.dense21 = layers.Dense(units=z_dims)
        self.dense22 = layers.Dense(units=z_dims)
        
        self.dense3 = layers.Dense(units=dense1_units, activation=tf.nn.relu)
        self.dense4 = layers.Dense(units=dense1_units, activation=tf.nn.relu)
        self.out = layers.Dense(units=img_dims)
        
        
    def encoder(self, inputs):
        outputs = self.dense0(inputs)
        outputs = self.dense1(outputs)
        mean = self.dense21(outputs)
        var = self.dense22(outputs)
        
        return mean, var
        
        
    def decoder(self, inputs):
        outputs = self.dense3(inputs)
        outputs = self.dense4(outputs)
        outputs = self.out(outputs)
        
        return outputs
        
        
    def sample(self, mean, var):
        eps = tf.random.normal(mean.shape)
        var = (tf.exp(var)**0.5) * eps
        z = mean+var
    
        return z
        
        
    def call(self, inputs):
        mean, var = self.encoder(inputs)
        z_sample = self.sample(mean, var)
        outputs = self.decoder(z_sample)
        
        return outputs, mean, var
               
vae = VAE()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)

def draw_img(ori_img, recon_img):
    n = 4

    origin_x = np.empty((28*n, 28*n))
    reconstr_x = np.empty((28*n, 28*n))

    ori_img = (ori_img * 255.).numpy().astype(np.uint8).reshape([-1, 28, 28])
    #recon_img = recon_img.reshape([-1, 28, 28])
    
    for i in range(n):
        for j in range(n):
            origin_x[i*28:(i+1)*28, j*28:(j+1)*28] = ori_img[i*4+j,:,:]
            reconstr_x[i*28:(i+1)*28, j*28:(j+1)*28] = recon_img[i*4+j,:,:]

    print("Original Images")     
    plt.figure(figsize=(n, n))
    plt.imshow(origin_x, origin="upper", cmap="gray")
    plt.show()

    print("Reconstructed Images")
    plt.figure(figsize=(n, n))
    plt.imshow(reconstr_x, origin="upper", cmap="gray")
    plt.show()
```

train了好久  最后把`klloss`的weight无奈的改到了0.1。。。。最后的效果很一般  

```python
for step, batch_x in enumerate(train_data.take(train_steps+1)):
    with tf.GradientTape() as tape:
        x_recon, mean, var = vae(batch_x)
        reconloss = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(labels=batch_x, logits=x_recon))
        klloss = 0.1 * tf.reduce_mean(tf.exp(var) - (var + 1) + mean**2)
        loss = reconloss + 1. * klloss
    grads = tape.gradient(loss, vae.trainable_variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, vae.trainable_variables))

    if step % display_step == 0:
        print('step %i: loss %f, reloss %f, klloss %f' %(step, loss.numpy(), reconloss.numpy(), klloss.numpy()))
        
    
        img_recon = (tf.reshape(tf.nn.sigmoid(x_recon), [-1,28,28]).numpy() * 255.).astype(np.uint8)

        draw_img(batch_x, img_recon)

        
        
    
    
```

   step 0: loss 0.714608, reloss 0.693045, klloss 0.021563  
  Original Images



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8osu5m42vj307506zq31.jpg)


   Reconstructed Images



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8osuebi1pj307506zmxb.jpg)


 step 1000: loss 0.385244, reloss 0.347702, klloss 0.037542



 step 2000: loss 0.370257, reloss 0.328157, klloss 0.042100



 step 3000: loss 0.365704, reloss 0.324734, klloss 0.040970



 step 4000: loss 0.373777, reloss 0.330440, klloss 0.043337



 step 5000: loss 0.375169, reloss 0.333341, klloss 0.041828


 step 6000: loss 0.374485, reloss 0.332791, klloss 0.041694



 step 7000: loss 0.353559, reloss 0.310435, klloss 0.043124



 step 8000: loss 0.370547, reloss 0.325082, klloss 0.045465



 step 9000: loss 0.372162, reloss 0.328155, klloss 0.044007



   step 10000: loss 0.370595, reloss 0.326418, klloss 0.044176  
   Original Images



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8osulcoqxj307506zaa4.jpg)


 Reconstructed Images



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8osur257qj307506z74e.jpg)





vae的效果一般，布吉岛是不是自己的实现哪里有点问题  

vae真的是太难train了！😭😭


----

[^1]: https://jaan.io/what-is-variational-autoencoder-vae-tutorial/
[^2]: https://zhuanlan.zhihu.com/p/52676826
[^3]: https://zhuanlan.zhihu.com/p/55557709
[^4]: https://zhuanlan.zhihu.com/p/33194849
[^5]: https://github.com/altosaar/variational-autoencoder/blob/master/train_variational_autoencoder_tensorflow.py
[^6]: http://speech.ee.ntu.edu.tw/~tlkagk/courses_ML16.html






