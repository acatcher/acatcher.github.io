---
layout:     post
title:      Deep Learning with Python 01
subtitle:   cats vs dogs simple cnn #副标题
date:       2019-09-01
author:     ASeeker
header-img: img/name1.jpeg
catalog: true
tags:
    - keras
    - cnn
    
---

## 君の名は

> Reference[^1]

[^1]: 《python 深度学习》

自从上学期期末和夏令营开始，很久没有更新博客了  
这中间发生了很多的事，有开心的，有难受的，有很多想说的  
不过  
过去的就过去吧  
继续写每天的博客吧 :-）   
毕竟  
这是一件让我感到快乐幸福的事情  

## cats vs dogs simple cnn classifier 


```python
import os
import shutil
```

### 创建路径，放入train、test、validation数据


```python
origin_dir = '/Users/You/Desktop/dogs-vs-cats/train'
```


```python
base_dir = '/Users/You/Desktop/mine'
```


```python
os.mkdir(base_dir)
train_dir = os.path.join(base_dir, 'train')
val_dir = os.path.join(base_dir, 'val')
test_dir = os.path.join(base_dir, 'test')
os.mkdir(train_dir)
os.mkdir(val_dir)
os.mkdir(test_dir)
```


```python
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
val_cats_dir = os.path.join(val_dir, 'cats')
val_dogs_dir = os.path.join(val_dir, 'dogs')
test_cats_dir = os.path.join(test_dir, 'cats')
test_dogs_dir = os.path.join(test_dir, 'dogs')
os.mkdir(train_cats_dir)
os.mkdir(train_dogs_dir)
os.mkdir(val_cats_dir)
os.mkdir(val_dogs_dir)
os.mkdir(test_cats_dir)
os.mkdir(test_dogs_dir)
```


```python
fnames = ['cat.{}.jpg'.format(i) for i in range(1000)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(train_cats_dir, fname)
    shutil.copy(src_img, dest_img)
```


```python
fnames = ['cat.{}.jpg'.format(i) for i in range(1000,1500)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(val_cats_dir, fname)
    shutil.copy(src_img, dest_img)

fnames = ['cat.{}.jpg'.format(i) for i in range(1500,2000)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(test_cats_dir, fname)
    shutil.copy(src_img, dest_img)

fnames = ['dog.{}.jpg'.format(i) for i in range(1000)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(train_dogs_dir, fname)
    shutil.copy(src_img, dest_img)
    
fnames = ['dog.{}.jpg'.format(i) for i in range(1000,1500)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(val_dogs_dir, fname)
    shutil.copy(src_img, dest_img)

```


```python

fnames = ['dog.{}.jpg'.format(i) for i in range(1500,2000)]
for fname in fnames:
    src_img = os.path.join(origin_dir, fname)
    dest_img = os.path.join(test_dogs_dir, fname)
    shutil.copy(src_img, dest_img)
```

check


```python
print(len(os.listdir(train_cats_dir)))
print(len(os.listdir(train_dogs_dir)))
print(len(os.listdir(val_cats_dir)))
print(len(os.listdir(val_dogs_dir)))
print(len(os.listdir(test_cats_dir)))
print(len(os.listdir(test_dogs_dir)))
```

    1000
    1000
    500
    501
    500
    500


### 搭建一个简单的cnn模型


```python
from keras import layers
from keras import models
```

    Using TensorFlow backend.



```python
model = models.Sequential()
```


```python
model.add(layers.Conv2D(32,(3,3), activation='relu', input_shape=(150,150,3)))
```


```python
model.add(layers.MaxPool2D((2,2)))
```


```python
model.add(layers.Conv2D(64, (3,3), activation='relu'))
model.add(layers.MaxPool2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPool2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPool2D((2,2)))
```


```python
model.add(layers.Flatten())
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
```


```python
model.summary()
```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    conv2d_1 (Conv2D)            (None, 148, 148, 32)      896       
    _________________________________________________________________
    max_pooling2d_1 (MaxPooling2 (None, 74, 74, 32)        0         
    _________________________________________________________________
    conv2d_2 (Conv2D)            (None, 72, 72, 64)        18496     
    _________________________________________________________________
    max_pooling2d_2 (MaxPooling2 (None, 36, 36, 64)        0         
    _________________________________________________________________
    conv2d_3 (Conv2D)            (None, 34, 34, 128)       73856     
    _________________________________________________________________
    max_pooling2d_3 (MaxPooling2 (None, 17, 17, 128)       0         
    _________________________________________________________________
    conv2d_4 (Conv2D)            (None, 15, 15, 128)       147584    
    _________________________________________________________________
    max_pooling2d_4 (MaxPooling2 (None, 7, 7, 128)         0         
    _________________________________________________________________
    flatten_1 (Flatten)          (None, 6272)              0         
    _________________________________________________________________
    dense_1 (Dense)              (None, 512)               3211776   
    _________________________________________________________________
    dense_2 (Dense)              (None, 1)                 513       
    =================================================================
    Total params: 3,453,121
    Trainable params: 3,453,121
    Non-trainable params: 0
    _________________________________________________________________



```python
from keras import optimizers
```


```python
model.compile(loss='binary_crossentropy',
              optimizer=optimizers.RMSprop(lr=1e-4),
              metrics=['acc'])
```

### python 生成器


```python
def test_generator():
    i = 0
    while True:
        i += 1
        yield i

for i in test_generator():
    print(i)
    if i > 4:
        break
```

    0
    1
    2
    3
    4
    5


### 输入图像转成张量 
rgb范围0～1 图片大小统一为150，150


```python
from keras.preprocessing.image import ImageDataGenerator
```


```python
train_datagen = ImageDataGenerator(rescale=1/255.)
```


```python
test_datagen = ImageDataGenerator(rescale=1/255.)
```


```python
train_generator = train_datagen.flow_from_directory(train_dir,
                                                   target_size=(150,150),
                                                   batch_size=20,
                                                   class_mode='binary')
```

    Found 2000 images belonging to 2 classes.



```python
val_generator = test_datagen.flow_from_directory(val_dir,
                                                target_size=(150,150),
                                                batch_size=20,
                                                class_mode='binary')
```

    Found 1000 images belonging to 2 classes.



```python
for a,b in train_generator:
    print(a.shape)
    print(b.shape)
    print(type(a))
    break
```

    (20, 150, 150, 3)
    (20,)
    <type 'numpy.ndarray'>


### 开始训练 使用生成器
用`fit_generator` 这里要注意一下`steps_per_epoch`  
mbp炼丹好慢啊，多想有一块显卡🤩


```python
history = model.fit_generator(train_generator,
                             steps_per_epoch=100,
                             epochs=30,
                             validation_data=val_generator,
                             validation_steps=50)
```

    Epoch 1/30
    100/100 [==============================] - 144s 1s/step - loss: 0.6900 - acc: 0.5405 - val_loss: 0.6774 - val_acc: 0.5100
    Epoch 2/30
    100/100 [==============================] - 142s 1s/step - loss: 0.6529 - acc: 0.6250 - val_loss: 0.6497 - val_acc: 0.6270
    Epoch 3/30
    100/100 [==============================] - 143s 1s/step - loss: 0.6023 - acc: 0.6780 - val_loss: 0.6120 - val_acc: 0.6680
    Epoch 4/30
    100/100 [==============================] - 141s 1s/step - loss: 0.5597 - acc: 0.7080 - val_loss: 0.6024 - val_acc: 0.6710
    Epoch 5/30
    100/100 [==============================] - 142s 1s/step - loss: 0.5384 - acc: 0.7315 - val_loss: 0.5927 - val_acc: 0.6780
    Epoch 6/30
    100/100 [==============================] - 141s 1s/step - loss: 0.5092 - acc: 0.7555 - val_loss: 0.6572 - val_acc: 0.6670
    Epoch 7/30
    100/100 [==============================] - 142s 1s/step - loss: 0.4843 - acc: 0.7655 - val_loss: 0.6140 - val_acc: 0.6860
    Epoch 8/30
    100/100 [==============================] - 141s 1s/step - loss: 0.4577 - acc: 0.7860 - val_loss: 0.6488 - val_acc: 0.6590
    Epoch 9/30
    100/100 [==============================] - 142s 1s/step - loss: 0.4360 - acc: 0.7950 - val_loss: 0.5635 - val_acc: 0.7150
    Epoch 10/30
    100/100 [==============================] - 142s 1s/step - loss: 0.4128 - acc: 0.8025 - val_loss: 0.5634 - val_acc: 0.7070
    Epoch 11/30
    100/100 [==============================] - 142s 1s/step - loss: 0.3848 - acc: 0.8300 - val_loss: 0.5777 - val_acc: 0.7060
    Epoch 12/30
    100/100 [==============================] - 142s 1s/step - loss: 0.3633 - acc: 0.8395 - val_loss: 0.5907 - val_acc: 0.7060
    Epoch 13/30
    100/100 [==============================] - 141s 1s/step - loss: 0.3402 - acc: 0.8570 - val_loss: 0.6282 - val_acc: 0.7070
    Epoch 14/30
    100/100 [==============================] - 142s 1s/step - loss: 0.3215 - acc: 0.8640 - val_loss: 0.5797 - val_acc: 0.7010
    Epoch 15/30
    100/100 [==============================] - 142s 1s/step - loss: 0.2878 - acc: 0.8760 - val_loss: 0.6882 - val_acc: 0.6910
    Epoch 16/30
    100/100 [==============================] - 143s 1s/step - loss: 0.2687 - acc: 0.8950 - val_loss: 0.6435 - val_acc: 0.7120
    Epoch 17/30
    100/100 [==============================] - 144s 1s/step - loss: 0.2547 - acc: 0.8960 - val_loss: 0.6800 - val_acc: 0.7100
    Epoch 18/30
    100/100 [==============================] - 141s 1s/step - loss: 0.2282 - acc: 0.9110 - val_loss: 0.7673 - val_acc: 0.6960
    Epoch 19/30
    100/100 [==============================] - 142s 1s/step - loss: 0.2074 - acc: 0.9180 - val_loss: 0.6590 - val_acc: 0.7230
    Epoch 20/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1901 - acc: 0.9255 - val_loss: 0.6846 - val_acc: 0.7210
    Epoch 21/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1607 - acc: 0.9430 - val_loss: 0.7559 - val_acc: 0.7110
    Epoch 22/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1452 - acc: 0.9465 - val_loss: 0.8126 - val_acc: 0.7060
    Epoch 23/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1333 - acc: 0.9590 - val_loss: 0.7738 - val_acc: 0.7200
    Epoch 24/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1188 - acc: 0.9610 - val_loss: 0.8173 - val_acc: 0.7240
    Epoch 25/30
    100/100 [==============================] - 140s 1s/step - loss: 0.1009 - acc: 0.9670 - val_loss: 0.8727 - val_acc: 0.7070
    Epoch 26/30
    100/100 [==============================] - 141s 1s/step - loss: 0.0849 - acc: 0.9755 - val_loss: 0.8614 - val_acc: 0.7170
    Epoch 27/30
    100/100 [==============================] - 141s 1s/step - loss: 0.0754 - acc: 0.9735 - val_loss: 0.9892 - val_acc: 0.7060
    Epoch 28/30
    100/100 [==============================] - 140s 1s/step - loss: 0.0630 - acc: 0.9820 - val_loss: 0.9978 - val_acc: 0.7230
    Epoch 29/30
    100/100 [==============================] - 140s 1s/step - loss: 0.0533 - acc: 0.9875 - val_loss: 0.9771 - val_acc: 0.7330
    Epoch 30/30
    100/100 [==============================] - 140s 1s/step - loss: 0.0548 - acc: 0.9830 - val_loss: 1.1568 - val_acc: 0.7180



```python
model.save('cats_and_dogs_small_1.h5')
```

### 保存模型 绘图判断overfitting


```python
import matplotlib.pyplot as plt
```


```python
acc = history.history['acc']
```


```python
val_acc = history.history['val_acc']
loss = history.history['loss']
val_loss = history.history['val_loss']
```


```python
epochs = range(1, len(acc)+1)
```


```python
plt.plot(epochs, acc, 'bo', label='Train acc')
plt.plot(epochs, val_acc, 'b', label='Val acc')
plt.title("Train and Val acc")
plt.legend()
plt.figure()
```




    <matplotlib.figure.Figure at 0x10053f190>




![](https://tva1.sinaimg.cn/large/006y8mN6ly1g6l9fl1smtj30af07cwec.jpg)



    <matplotlib.figure.Figure at 0x10053f190>



```python
plt.plot(epochs, loss, 'bo', label='Train loss')
plt.plot(epochs, val_loss, 'b', label='Val loss')
plt.title("Train and Val loss")
plt.legend()
plt.show()
```


![](https://tva1.sinaimg.cn/large/006y8mN6ly1g6l9gdhozij30af07c745.jpg)


val 的acc在70%就上不去了，overfitting
