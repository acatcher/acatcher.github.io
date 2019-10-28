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



### autoencoder 
> Reference[^1] [^2] 

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


