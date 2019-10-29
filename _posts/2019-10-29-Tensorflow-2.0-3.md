---
layout:     post
title:      Tensorflow 2.0 03
subtitle:   Simple Neural Network & Convolutional Network #副标题
date:       2019-10-29
author:     ASeeker
header-img: img/shiyuan3.jpg
catalog: true
tags:
    - tensorflow
    - deep learning
    
---

#### 有工夫绝望的话，还不如吃点好吃的去睡觉呢。


今天有点划水了。。昨晚写影评写的太晚，今天上午直接就没了。。。好久没逛B站 晚上又堕落的看了好久。。。

所以就只实现一下最简单的nn以及cnn，strengthen一下tf的熟练度     

> Reference[^1] [^2] [^3]

#### Simple Neural Network

```python
import tensorflow as tf
import numpy as np
from tensorflow.keras.datasets import mnist
```

一些模型参数

```python
num_features = 784
num_h1 = 1024
num_h2 = 512
num_classes = 10

learning_rate = 1e-1
batch_size = 256
train_steps = 2000
display_per_step = 100
```
一如既往的选择mnist数据集  
这里由于直接用一个dense的网络，将输入数据拉平到784  

```python
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.astype(np.float32).reshape([-1,num_features]) / 255.
x_test = x_test.astype(np.float32).reshape([-1,num_features]) / 255.

train_data = tf.data.Dataset.from_tensor_slices((x_train, y_train))
train_data = train_data.shuffle(1000).repeat().batch(batch_size).prefetch(1)
```

搭建模型

```python
class NeuralNet(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.dense1 = tf.keras.layers.Dense(units=num_h1, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(units=num_h2, activation=tf.nn.relu)
        self.out = tf.keras.layers.Dense(units=num_classes)
            
    def call(self, inputs, is_train=False):
        outputs = self.dense1(inputs)
        outputs = self.dense2(outputs)
        outputs = self.out(outputs)
        if not is_train:
            outputs = tf.nn.softmax(outputs)
        
        return outputs
        
        
nn = NeuralNet()
optimizer = tf.keras.optimizers.SGD(learning_rate=learning_rate)
```
定义一下acc metric

```python
def acc(y_pred, y_true):
    y_true = tf.cast(y_true, tf.int64)
    acc_bool = tf.equal(tf.argmax(y_pred,axis=-1), y_true)
    acc_rate = tf.reduce_mean(tf.cast(acc_bool, tf.float32))
    
    return acc_rate
```
开始训练

```python
for step, (batch_x, batch_y) in enumerate(train_data.take(train_steps+1)):
    with tf.GradientTape() as tape:
        y_logits = nn(batch_x, True)
        batch_y = tf.cast(batch_y, tf.int32)
        loss = tf.reduce_mean( tf.nn.sparse_softmax_cross_entropy_with_logits(labels=batch_y, logits=y_logits) )
        
    grads = tape.gradient(loss, nn.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, nn.variables))
    
    if step % display_per_step == 0:
        acc_rate = acc(y_logits, batch_y)
        print("step: %i, loss: %f, accuracy: %f" % (step, loss, acc_rate))
    
```

 step: 0, loss: 2.302607, accuracy: 0.070312  
 step: 100, loss: 0.382137, accuracy: 0.886719  
 step: 200, loss: 0.312384, accuracy: 0.890625  
 step: 300, loss: 0.336042, accuracy: 0.898438  
 step: 400, loss: 0.252011, accuracy: 0.925781  
 step: 500, loss: 0.221575, accuracy: 0.937500  
 step: 600, loss: 0.143101, accuracy: 0.964844  
 step: 700, loss: 0.107586, accuracy: 0.968750  
 step: 800, loss: 0.112678, accuracy: 0.968750  
 step: 900, loss: 0.151181, accuracy: 0.953125  
 step: 1000, loss: 0.102401, accuracy: 0.972656  
 step: 1100, loss: 0.134542, accuracy: 0.960938  
 step: 1200, loss: 0.099323, accuracy: 0.980469  
 step: 1300, loss: 0.094068, accuracy: 0.976562  
 step: 1400, loss: 0.066545, accuracy: 0.976562  
 step: 1500, loss: 0.124233, accuracy: 0.968750  
 step: 1600, loss: 0.120372, accuracy: 0.960938  
 step: 1700, loss: 0.119279, accuracy: 0.968750  
 step: 1800, loss: 0.111487, accuracy: 0.976562  
 step: 1900, loss: 0.068802, accuracy: 0.972656  
 step: 2000, loss: 0.082421, accuracy: 0.964844  


测试集上的acc

```python
test_pred = nn(x_test, False)
print('test acc: %f' %(acc(test_pred, y_test)))

```

  test acc: 0.972100

看一下实际的效果  

```python
import matplotlib.pyplot as plt

n_tests = 4
to_test_img = x_test[:n_tests]
pred_test = nn.predict(to_test_img)

for i in range(n_tests):
    plt.imshow(to_test_img[i].reshape([28,28]), cmap='gray')
    plt.show()
    print('pred label: %i' %(tf.argmax(pred_test[i]).numpy()))

```


![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fgs6tlnsj306z06w742.jpg)


`pred label: 7`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fgsls80cj306z06wa9u.jpg)


`pred label: 2`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fgsvs87qj306z06w3ya.jpg)


`pred label: 1`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fgt8vt7yj306z06wa9u.jpg)


`pred label: 0`

这里还有一个raw的写法，所有的变量都自己定义 然后`tf.matmul`、 `tf.add`手动计算   
大部分代码都是一样的，就没有再写一遍了 不过有一个cross_entropy的手动计算，还是有必要看一下的  
里面有一个`tf.clip_by_value`函数   
tf.clip_by_value(A, min, max)：输入一个张量A，把A中的每一个元素的值都压缩在min和max之间。小于min的让它等于min，大于max的元素的值等于max    
这是避免log函数接近零时会出现负无穷的情况 而tf封装好的loss已经帮我们处理过了  

```python
def cross_entropy(y_pred, y_true):
    # Encode label to a one hot vector.
    y_true = tf.one_hot(y_true, depth=num_classes)
    # Clip prediction values to avoid log(0) error.
    y_pred = tf.clip_by_value(y_pred, 1e-9, 1.)
    # Compute cross-entropy.
    return tf.reduce_mean(-tf.reduce_sum(y_true * tf.math.log(y_pred)))
```




bug总结
写着部分代码的时候笔者遇到了两个bug，一个是
`acc_rate = tf.reduce_mean(tf.cast(acc_bool, tf.float32))`
这行code时一开始cast成了tf.int32,没注意到是reduce_mean，导致了acc算出来都是0
还有一个注意mnist导入的这个label的数据类型是unit8，送到loss里面的时候直接报错了
`labels=tf.cast(batch_y, tf.int64)`   
对了，这里再提一下tf的loss函数真的是太多了。。。。有机会一定要总结一下  
这个`tf.nn.sparse_softmax_cross_entropy_with_logits`函数的官方doc这样描述的，labels必须要int类型   
A common use case is to have logits of shape [batch_size, num_classes] and have labels of shape [batch_size], but higher dimensions are supported, in which case the dim-th dimension is assumed to be of size num_classes. logits must have the dtype of float16, float32, or float64, and labels must have the dtype of int32 or int64.  


#### Simple Convolutional Network


常规操作  

```python
import tensorflow as tf
import numpy as np
from tensorflow.keras.datasets import mnist

batch_size = 32
learning_rate = 1e-3
train_steps = 2000
display_step = 100

num_classes = 10

conv1_filters = 32
conv2_filters = 64
dense1_units = 1024
dropout1_rate = 0.5

```

这部分要注意一下送到cnn要reshape成4d  
gray图像就加一个channel  

```python
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.astype(np.float32).reshape([-1,28,28,1]) / 255.
x_test = x_test.astype(np.float32) / 255.

train_data = tf.data.Dataset.from_tensor_slices((x_train, y_train))
train_data = train_data.repeat().shuffle(10000).batch(batch_size).prefetch(1)
```

这部分搭建一下网络  
参数变large了，添加一个dropout防止overfitting  so这是就要引入`is_training`这个参数了，如果是test的时候就不用dropout了  

```python
class ConvNet(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.conv1 = tf.keras.layers.Conv2D(filters=conv1_filters,
                                            kernel_size=5,
                                            padding='same',
                                            activation=tf.nn.relu)
        self.pool1 = tf.keras.layers.MaxPool2D(pool_size=2,
                                               strides=2)
        self.conv2 = tf.keras.layers.Conv2D(filters=conv2_filters,
                                            kernel_size=3,
                                            padding='same',
                                            activation=tf.nn.relu)
        self.pool2 = tf.keras.layers.MaxPooling2D(pool_size=2,
                                                  strides=2)
        
        self.flatten = tf.keras.layers.Flatten()
        
        self.dense1 = tf.keras.layers.Dense(units=dense1_units,
                                            activation=tf.nn.relu)
        
        self.dropout1 = tf.keras.layers.Dropout(rate=dropout1_rate)
        
        self.out = tf.keras.layers.Dense(units=num_classes)
    
    
    def call(self, inputs, is_training=False):
        x = self.conv1(inputs)
        x = self.pool1(x)
        x = self.conv2(x)
        x = self.pool2(x)
        x = self.flatten(x)
        x = self.dense1(x)
        x = self.dropout1(x, is_training)
        x = self.out(x)
        
        if not is_training:
            x = tf.nn.softmax(x)
            
        return x
 

cn = ConvNet()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)

def acc(y_pred, y_true):
    y_true = tf.cast(y_true, tf.int64)
    acc_bool = tf.equal(tf.argmax(y_pred, axis=-1), y_true)
    acc_rate = tf.reduce_mean(tf.cast(acc_bool, tf.float32))
    
    return acc_rate
```
开始训练

```python
for step, (batch_x, batch_y) in enumerate(train_data.take(train_steps+1)):
    with tf.GradientTape() as tape:
        y_pred = cn(batch_x, is_training=True)
        batch_y = tf.cast(batch_y, tf.int32)
        loss = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(logits=y_pred, labels=batch_y))
    grads = tape.gradient(loss, cn.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, cn.variables))
    
    if step % display_step == 0:
        
        print("step %d loss: %f  acc: %f" %(step, loss, acc(y_pred, batch_y)))
```

step 0 loss: 2.336060  acc: 0.062500  
step 100 loss: 0.187474  acc: 0.906250  
step 200 loss: 0.226720  acc: 0.968750  
step 300 loss: 0.088488  acc: 1.000000  
step 400 loss: 0.062447  acc: 0.968750  
step 500 loss: 0.162202  acc: 0.937500  
step 600 loss: 0.115830  acc: 0.968750  
step 700 loss: 0.043087  acc: 1.000000  
step 800 loss: 0.238977  acc: 0.937500  
step 900 loss: 0.008179  acc: 1.000000  
step 1000 loss: 0.007713  acc: 1.000000  
step 1100 loss: 0.085170  acc: 0.968750  
step 1200 loss: 0.065700  acc: 0.968750  
step 1300 loss: 0.056616  acc: 0.968750  
step 1400 loss: 0.018644  acc: 1.000000  
step 1500 loss: 0.029943  acc: 0.968750  
step 1600 loss: 0.103587  acc: 0.968750  
step 1700 loss: 0.005319  acc: 1.000000  
step 1800 loss: 0.004890  acc: 1.000000  
step 1900 loss: 0.000941  acc: 1.000000  
step 2000 loss: 0.057181  acc: 0.968750  

可见这个网络还是很棒很棒的～ 不过笔者很是怀疑是不是自己的coding出了问题，居然有那么多100%以及相同的acc，比如0.968750。后来看了一下，自己的batch设置的很小，0.968750的acc正好就是31个判断正确，1个error，就不是什么问题了，只是因为大部分都会正确预测。笔者又试了一下，牺牲下自己的电脑，把batch开到了128，acc相同的情况好了一点。   

和之前一样测试与一下，test送到网络前也不能忘了reshape    
在测试集上的acc也好高  

```python
x_test = x_test.reshape([-1,28,28,1])
pred_test = cn(x_test, False)
acc_test = acc(pred_test, y_test)
print('test acc: %f' %(acc_test))
```

 test acc: 0.986300

```python
import matplotlib.pyplot as plt

n_images = 4

to_show_img = x_test[5:5+n_images]
y_test_pred = cn.predict(to_show_img)

for i in range(n_images):
    display_img = to_show_img[i]
    plt.imshow(display_img.reshape([28,28]), cmap='gray')
    plt.show()
    print('pred label: %d' %(tf.argmax(y_test_pred[i])))
    
    
    


```


![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fhk6jmnnj306z06w742.jpg)

`pred label: 1`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fhkt7bipj306z06wa9u.jpg)


`pred label: 4`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fhl0osy9j306z06wa9u.jpg)


`pred label: 9`



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g8fhl75esbj306z06wa9u.jpg)


`pred label: 5`   
这个5写的是真丑。。。


两段很水的代码。。。。今天着实有点混。。。。也没有去运动🏸️   




-------
[^1]: sparse\_softmax\_cross\_entropy\_with\_logits, https://www.tensorflow.org/api_docs/python/tf/nn/sparse_softmax_cross_entropy_with_logits


[^2]: clip\_by\_value, https://blog.csdn.net/uestc_c2_403/article/details/72190248

[^3]: origin code, https://github.com/aymericdamien/TensorFlow-Examples



