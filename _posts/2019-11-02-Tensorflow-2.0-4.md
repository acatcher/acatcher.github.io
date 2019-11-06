---
layout:     post
title:      Tensorflow 2.0 04
subtitle:   RNN In One #副标题
date:       2019-11-02
author:     ASeeker
header-img: img/BetterDays1.jpg
catalog: true
tags:
    - tensorflow
    - deep learning
    
---

#### 你往前走，我一定在你后面。

很棒的一部电影，反映了不少中国的现状，周冬雨和千玺的演技也很不错，虽然还有些缺憾，心目中今年的最佳华语影片👑

RNN系列  
顺带复习一下RNN  

> Reference[^1] [^2] [^3] [^4] [^5] [^6] [^7] [^8] [^9] [^10]

#### RNN --> LSTM

这里直接写LSTM，最简单的RNN会出现vanish的问题  
代码中也直接就用`LSTM`了，还有一种`LSTMCell`,需要自己每次for循环timestep  

之前是在ng和李宏毅的视频里看的RNN，不过总感觉视频讲的有点浅，在[这篇博客](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)里面讲的很具体。这些dalao的博客写的真是太好了，希望自己以后也可以写出这样有价值的文章分享给他人😊。   

很简单的解释了一下rnn的不足  
 
>
If we are trying to predict the last word in `“the clouds are in the sky,”` we don’t need any further context – it’s pretty obvious the next word is going to be sky. In such cases, where the gap between the relevant information and the place that it’s needed is small, RNNs can learn to use the past information.
    
>Consider trying to predict the last word in the text `“I grew up in France… I speak fluent French.”` Recent information suggests that the next word is probably the name of a language, but if we want to narrow down which language, we need the context of France, from further back. It’s entirely possible for the gap between the relevant information and the point where it is needed to become very large.  

对于LSTM的一个intuitive的理解

>The key to LSTMs is the cell state, the horizontal line running through the top of the diagram.  
The cell state is kind of like a conveyor belt. It runs straight down the entire chain, with only some minor linear interactions. It’s very easy for information to just flow along it unchanged.

最后还有一些LSTM的variations


#### 关于BPTT  
以前没有仔细推导过bptt，只是从直观上去了解，这里看了下[这篇博客](http://www.wildml.com/2015/10/recurrent-neural-networks-tutorial-part-3-backpropagation-through-time-and-vanishing-gradients/),解释的还挺不错的。BPTT其实就是对每个timestep做sum。也从公式推导上对vanish问题有了进一步的了解。这个[github](上面)有更详细的推导。        


这边前面都是与前面一样的就不多说了，这里还是用mnist，每一行看作一个timestep      

```python
import tensorflow as tf
import numpy as np
from tensorflow.keras.datasets import mnist
```


```python
batch_size = 32
train_steps = 1000
display_step = 100

lstm_units = 32
num_classes = 10

learning_rate = 1e-3

```


```python
(x_train, y_train), _ = mnist.load_data()

x_train = x_train.astype(np.float32).reshape([-1,28,28]) / 255.

train_data = tf.data.Dataset.from_tensor_slices((x_train,y_train))
train_data = train_data.repeat().shuffle(10000).batch(batch_size).prefetch(1)
```

很方便，直接给一个LSTM层就可以了  

```python
class Lstm(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.lstm1 = tf.keras.layers.LSTM(lstm_units)
        self.out = tf.keras.layers.Dense(num_classes)
    
    def call(self, inputs, is_training=False):
        outputs = self.lstm1(inputs)
        outputs = self.out(outputs)
        if not is_training:
            outputs = tf.nn.softmax(outputs)
        
        return outputs

lstm = Lstm()
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
        y_pred = lstm(batch_x, is_training=True)
        batch_y = tf.cast(batch_y, tf.int32)
        loss = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(logits=y_pred, labels=batch_y))
    
    grads = tape.gradient(loss, lstm.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, lstm.variables))
    
    if step % display_step == 0:
        print('step %d: loss %f  acc %f' %(step, loss, acc(y_pred, batch_y)))



```

step 0: loss 2.307400  acc 0.093750  
step 100: loss 1.696196  acc 0.406250  
step 200: loss 1.099894  acc 0.625000  
step 300: loss 1.079787  acc 0.593750  
step 400: loss 0.856504  acc 0.718750  
step 500: loss 0.447243  acc 0.875000  
step 600: loss 0.541385  acc 0.812500  
step 700: loss 0.699608  acc 0.781250  
step 800: loss 0.417174  acc 0.875000  
step 900: loss 0.431013  acc 0.812500  
step 1000: loss 0.365609  acc 0.875000  


测试一下 lstm用在mnist上的效果还是很一般的  

```python
x_test,y_test = _

x_test = x_test.astype(np.float32) / 255.

y_test_pred = lstm(x_test, is_training=False)

print('test acc: %f' %(acc(y_test_pred, y_test)))
```

   test acc: 0.860400


关于tf实现的LSTM的输出是1、2、3个，可以参考[这篇博客](https://huhuhang.com/post/machine-learning/lstm-return-sequences-state)


#### Dynamic Rnn

一开始不知道啥是dynamic，后来一查是为了解决timestep缺失或者数据集长短不一的情况的   
大体步骤是先将每条数据padding成max的timestep  
然后缺失的timestep给特定的mask值  
在搭建模型的时候加入一个`Masking`层  
后面就又都是一样的了  

具体可以看[这儿](https://www.tensorflow.org/guide/keras/masking_and_padding)




```python
import tensorflow as tf
import numpy as np

batch_size = 32

lstm_units = 32
classes_num = 2

train_steps = 2000
display_step = 200
learning_rate = 1e-3

masking_value = -1

seq_min_length = 5
seq_max_length = 20
max_value = 10000

```

训练数据的生成   
假设连续的数据 label为0   
不连续的label为1  
这里dataset的写法使用了from_generator

```python
def toy_data_generator():
    
    while True:
        
        seq_length = np.random.randint(seq_min_length, seq_max_length)
        rand_start = np.random.randint(0, max_value-seq_length)
    
        if np.random.random() > 0.5:
            seq = np.arange(rand_start, rand_start+seq_length)
            seq = seq / max_value
            
            seq = np.pad(seq, mode='constant', pad_width=(0, seq_max_length-seq_length), constant_values=masking_value)
            
            label = 0
        else:
            seq = np.random.randint(0, max_value, size=seq_length)
            seq = seq / max_value
            seq = np.pad(seq, mode='constant', pad_width=(0, seq_max_length-seq_length), constant_values=masking_value)
            label = 1
        
        yield np.array(seq, dtype=np.float32), np.array(label, dtype=np.int64)

train_data = tf.data.Dataset.from_generator(toy_data_generator, output_types=(tf.float32, tf.int64))
train_data = train_data.repeat().shuffle(1000).batch(batch_size).prefetch(1)
```


```python
class DynamicRNN(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.masking1 = tf.keras.layers.Masking(mask_value=masking_value)
        self.lstm1 = tf.keras.layers.LSTM(units=lstm_units)
        self.out = tf.keras.layers.Dense(classes_num)
        
    def call(self, inputs, is_training=False):
        # A RNN Layer expects a 3-dim input (batch_size, seq_len, num_features).
        outputs = tf.expand_dims(inputs, -1)
        outputs = self.masking1(outputs)
        outputs = self.lstm1(outputs)
        outputs = self.out(outputs)
        
        if not is_training:
            outputs = tf.nn.softmax(outputs)
            
        return outputs
```

训练  

```python
dyrnn = DynamicRNN()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)

def acc(y_pred, y_true):
    return tf.reduce_mean( tf.cast( tf.equal( tf.argmax(y_pred, axis=-1), y_true)  ,tf.float32 )  )

for step, (batch_x, batch_y) in enumerate(train_data.take(train_steps+1)):
    with tf.GradientTape() as tape:
        
        y_pred = dyrnn(batch_x, is_training=True)
        loss = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(logits=y_pred, labels=batch_y))
    grads = tape.gradient(loss, dyrnn.variables)
    optimizer.apply_gradients(zip(grads, dyrnn.variables))

    if step % display_step == 0:
        print('step %d  loss: %f  acc: %f' % (step, loss, acc(y_pred, batch_y)))
    
```

step 0  loss: 0.720475  acc: 0.437500   
step 200  loss: 0.578635  acc: 0.750000  
step 400  loss: 0.463771  acc: 0.750000  
step 600  loss: 0.461265  acc: 0.750000  
step 800  loss: 0.454481  acc: 0.875000  
step 1000  loss: 0.279865  acc: 0.875000  
step 1200  loss: 0.221204  acc: 0.937500  
step 1400  loss: 0.135849  acc: 0.937500  
step 1600  loss: 0.138664  acc: 1.000000  
step 1800  loss: 0.234895  acc: 0.906250  
step 2000  loss: 0.180870  acc: 1.000000  


#### Bidirectional Rnn

最后登场的是我们的双向RNN🌟  
tf（keras）里面也帮我们实现的很好了，调一个`Bidirectional`就好了，如果你想要两个方向上的网络长得一样，就直接传一个就好了，tf会自己复制一下；（想不一样就传两个好了  [doc见此](https://www.tensorflow.org/api_docs/python/tf/keras/layers/Bidirectional)


```python
import tensorflow as tf
import numpy as np

batch_size = 32
learning_rate = 1e-3
train_steps = 2000
display_step = 200

classes_num = 10
lstm1_units = 32

from tensorflow.keras.datasets import mnist
(x_train, y_train), (x_test, y_test) = mnist.load_data()

x_train, x_test = x_train.astype(np.float32) / 255., x_test.astype(np.float32) / 255.

y_train, y_test = y_train.astype(np.int64), y_test.astype(np.int64)

train_data = tf.data.Dataset.from_tensor_slices((x_train, y_train))
train_data = train_data.repeat().shuffle(10000).batch(batch_size).prefetch(1)
```

反向的那个记得要`go_backwards`  

```python
class BiLSTM(tf.keras.Model):
    def __init__(self):
        super().__init__()
        lstm_fw1 = tf.keras.layers.LSTM(units=lstm1_units)
        lstm_bw1 = tf.keras.layers.LSTM(units=lstm1_units, go_backwards=True)
        self.bilstm1 = tf.keras.layers.Bidirectional(lstm_fw1, backward_layer=lstm_bw1)
        self.out = tf.keras.layers.Dense(units=classes_num)
        
    def call(self, inputs, is_training=False):
        outputs = self.bilstm1(inputs)
        outputs = self.out(outputs)
        
        if not is_training:
            outputs = tf.nn.softmax(outputs)
            
        return outputs    


biLstm = BiLSTM()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)


def acc(y_pred, y_true):
    
    return tf.reduce_mean(tf.cast(tf.equal(tf.argmax(y_pred, axis=-1) ,y_true) ,tf.float32))
    

for step, (batch_x, batch_y) in enumerate(train_data.take(train_steps+1)):
    with tf.GradientTape() as tape:
        y_pred = biLstm(batch_x)
        loss = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(logits=y_pred, labels=batch_y))
    grads = tape.gradient(loss, biLstm.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, biLstm.variables))
    
    if step % display_step == 0:
        print('step %d loss: %f  acc: %f'%(step, loss, acc(y_pred, batch_y)))
    
```

step 0 loss: 2.303555  acc: 0.062500  
step 200 loss: 1.889396  acc: 0.593750  
step 400 loss: 1.818122  acc: 0.687500  
step 600 loss: 1.729275  acc: 0.750000  
step 800 loss: 1.540786  acc: 0.968750  
step 1000 loss: 1.793411  acc: 0.687500  
step 1200 loss: 1.596297  acc: 0.875000  
step 1400 loss: 1.683933  acc: 0.781250  
step 1600 loss: 1.580734  acc: 0.875000  
step 1800 loss: 1.685784  acc: 0.750000  
step 2000 loss: 1.616963  acc: 0.875000  


bi的效果确实要好了一点  

```python
y_test_pred = biLstm(x_test)
print('test acc %f' %(acc(y_test_pred, y_test)))
```

   test acc 0.895500







#### 还有一些

[这里](https://www.tensorflow.org/guide/keras/rnn)有一个rnn tf的官方guide  

[tf.tile()函数](https://blog.csdn.net/xwd18280820053/article/details/72867818)  

[np.pad()函数](https://docs.scipy.org/doc/numpy/reference/generated/numpy.pad.html)


好了，关于RNN系列有了一个比较系统的学习。  
（不过这篇博客实在是拖了太久了，，，真的不应该

最近看到了关于豫章书院的新闻。。一开始自己义愤填膺，看了很多文章、up主的视频，有各种各样的声音。后来冷静下来想想，这种事情对于政府来说确实很难处理。相信我们的国家会越来越好的。

----

[^1]: http://colah.github.io/posts/2015-08-Understanding-LSTMs/

[^2]: http://www.wildml.com/2015/10/recurrent-neural-networks-tutorial-part-3-backpropagation-through-time-and-vanishing-gradients/

[^3]: https://github.com/go2carter/nn-learn/blob/master/grad-deriv-tex/rnn-grad-deriv.pdf

[^4]: https://www.tensorflow.org/guide/keras/rnn

[^5]: https://www.tensorflow.org/guide/keras/masking_and_padding

[^6]: https://huhuhang.com/post/machine-learning/lstm-return-sequences-state

[^7]: https://www.tensorflow.org/api_docs/python/tf/keras/layers/Bidirectional

[^8]: https://blog.csdn.net/xwd18280820053/article/details/72867818

[^9]: https://docs.scipy.org/doc/numpy/reference/generated/numpy.pad.html

[^10]: https://github.com/aymericdamien/TensorFlow-Examples/tree/master/tensorflow_v2/notebooks/3_NeuralNetworks