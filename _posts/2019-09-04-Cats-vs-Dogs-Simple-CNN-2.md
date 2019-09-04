---
layout:     post
title:      Deep Learning with Python 02
subtitle:   cats vs dogs simple cnn 2 #副标题
date:       2019-09-01
author:     ASeeker
header-img: img/flipped1.jpeg
catalog: true
tags:
    - keras
    - cnn
    
---

Some of us get dipped in flat, some in satin, some in gloss. But every once in a while, you find someone who's iridescent. And when you do, nothing will ever compare.  

解决overfitting的一些简单方法


```python
import keras
```

    Using TensorFlow backend.


各个路径


```python
import os

origin_dir = '/Users/You/Desktop/dogs-vs-cats/train'
base_dir = '/Users/You/Desktop/mine'
train_dir = os.path.join(base_dir, 'train')
val_dir = os.path.join(base_dir, 'val')
test_dir = os.path.join(base_dir, 'test')
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
val_cats_dir = os.path.join(val_dir, 'cats')
val_dogs_dir = os.path.join(val_dir, 'dogs')
test_cats_dir = os.path.join(test_dir, 'cats')
test_dogs_dir = os.path.join(test_dir, 'dogs')
```

### 数据增强
导入ImageDataGenerator


```python
from keras.preprocessing.image import ImageDataGenerator
```

变换生成一些新的数据  
基本上可以从参数名看出各种变换的含义


```python
datagen = ImageDataGenerator(
    rotation_range=40,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,
    fill_mode='nearest')
```


```python
from keras.preprocessing import image
```


```python
fnames = [ os.path.join(train_cats_dir, fname) for fname in os.listdir(train_cats_dir)]
```


```python
img_path = fnames[4]
img = image.load_img(img_path, target_size=(150,150))
x = image.img_to_array(img)
x = x.reshape((1,) + x.shape)
```

来看看效果吧


```python
%matplotlib inline
import matplotlib.pyplot as plt

i = 0
for batch in datagen.flow(x, batch_size=1):
    plt.figure(i)
    imgplot = plt.imshow(image.array_to_img(batch[0]))
    i += 1
    if i % 4 == 0:
        break
        
plt.show()
```


![](https://tva1.sinaimg.cn/large/006y8mN6gy1g6nxifhctcj3079070wes.jpg)



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g6nxirq9wyj3079070q36.jpg)



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g6nxjeinz0j3079070glu.jpg)



![](https://tva1.sinaimg.cn/large/006y8mN6gy1g6nxjw6zaaj3079070glw.jpg)

好可爱的小猫😸


```python
from keras import models
from keras import layers
```


```python
model = models.Sequential()
model.add(layers.Conv2D(32, (3,3), activation='relu', input_shape=(150,150,3)))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(64, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Flatten())
model.add(layers.Dropout(0.5))
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
```

这里加了一个dropout


```python
from keras import optimizers
```


```python
model.compile(loss='binary_crossentropy',
             optimizer=optimizers.RMSprop(lr=1e-4),
             metrics=['acc'])
```


```python
train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=40,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,)

test_datagen = ImageDataGenerator(rescale=1./255)

train_generator = train_datagen.flow_from_directory(train_dir,
                                                   target_size=(150,150),
                                                   batch_size=20,
                                                   class_mode='binary')

val_generator = test_datagen.flow_from_directory(val_dir,
                                                 target_size=(150,150),
                                                 batch_size=20,
                                                 class_mode='binary')
```

    Found 2000 images belonging to 2 classes.
    Found 1000 images belonging to 2 classes.


这里注意test上面只要rescale就好了


```python
history = model.fit_generator(train_generator,
                             steps_per_epoch=100,
                             epochs=100,
                             validation_data=val_generator,
                             validation_steps=50)
```

    Epoch 1/100
    100/100 [==============================] - 146s 1s/step - loss: 0.6965 - acc: 0.5070 - val_loss: 0.6840 - val_acc: 0.6000
    Epoch 2/100
    100/100 [==============================] - 154s 2s/step - loss: 0.6855 - acc: 0.5450 - val_loss: 0.6700 - val_acc: 0.5970
    Epoch 3/100
    100/100 [==============================] - 152s 2s/step - loss: 0.6737 - acc: 0.5785 - val_loss: 0.6552 - val_acc: 0.6020
    Epoch 4/100
    100/100 [==============================] - 156s 2s/step - loss: 0.6576 - acc: 0.5985 - val_loss: 0.6656 - val_acc: 0.5850
    Epoch 5/100
    100/100 [==============================] - 151s 2s/step - loss: 0.6481 - acc: 0.6165 - val_loss: 0.6372 - val_acc: 0.6220
    Epoch 6/100
    100/100 [==============================] - 159s 2s/step - loss: 0.6332 - acc: 0.6315 - val_loss: 0.6047 - val_acc: 0.6790
    Epoch 7/100
    100/100 [==============================] - 158s 2s/step - loss: 0.6099 - acc: 0.6685 - val_loss: 0.5843 - val_acc: 0.6750
    Epoch 8/100
    100/100 [==============================] - 157s 2s/step - loss: 0.6063 - acc: 0.6635 - val_loss: 0.6172 - val_acc: 0.6440
    Epoch 9/100
    100/100 [==============================] - 148s 1s/step - loss: 0.6125 - acc: 0.6490 - val_loss: 0.6018 - val_acc: 0.6630
    Epoch 10/100
    100/100 [==============================] - 150s 1s/step - loss: 0.5974 - acc: 0.6785 - val_loss: 0.5650 - val_acc: 0.6990
    Epoch 11/100
    100/100 [==============================] - 143s 1s/step - loss: 0.5941 - acc: 0.6790 - val_loss: 0.5940 - val_acc: 0.6600
    Epoch 12/100
    100/100 [==============================] - 143s 1s/step - loss: 0.5947 - acc: 0.6830 - val_loss: 0.5564 - val_acc: 0.7140
    Epoch 13/100
    100/100 [==============================] - 144s 1s/step - loss: 0.5824 - acc: 0.6920 - val_loss: 0.5477 - val_acc: 0.7030
    Epoch 14/100
    100/100 [==============================] - 144s 1s/step - loss: 0.5623 - acc: 0.7015 - val_loss: 0.5763 - val_acc: 0.6900
    Epoch 15/100
    100/100 [==============================] - 144s 1s/step - loss: 0.5711 - acc: 0.7065 - val_loss: 0.5722 - val_acc: 0.6860
    Epoch 16/100
    100/100 [==============================] - 144s 1s/step - loss: 0.5657 - acc: 0.7045 - val_loss: 0.5415 - val_acc: 0.7240
    Epoch 17/100
    100/100 [==============================] - 143s 1s/step - loss: 0.5623 - acc: 0.7060 - val_loss: 0.5336 - val_acc: 0.7210
    Epoch 18/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5666 - acc: 0.7045 - val_loss: 0.5383 - val_acc: 0.7200
    Epoch 19/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5408 - acc: 0.7220 - val_loss: 0.5422 - val_acc: 0.7160
    Epoch 20/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5546 - acc: 0.7060 - val_loss: 0.5565 - val_acc: 0.7130
    Epoch 21/100
    100/100 [==============================] - 143s 1s/step - loss: 0.5518 - acc: 0.7045 - val_loss: 0.5279 - val_acc: 0.7270
    Epoch 22/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5416 - acc: 0.7285 - val_loss: 0.5132 - val_acc: 0.7410
    Epoch 23/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5346 - acc: 0.7345 - val_loss: 0.5414 - val_acc: 0.7190
    Epoch 24/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5487 - acc: 0.7230 - val_loss: 0.5282 - val_acc: 0.7270
    Epoch 25/100
    100/100 [==============================] - 143s 1s/step - loss: 0.5268 - acc: 0.7350 - val_loss: 0.4970 - val_acc: 0.7520
    Epoch 26/100
    100/100 [==============================] - 150s 2s/step - loss: 0.5226 - acc: 0.7400 - val_loss: 0.4974 - val_acc: 0.7510
    Epoch 27/100
    100/100 [==============================] - 158s 2s/step - loss: 0.5278 - acc: 0.7255 - val_loss: 0.5386 - val_acc: 0.7210
    Epoch 28/100
    100/100 [==============================] - 157s 2s/step - loss: 0.5150 - acc: 0.7490 - val_loss: 0.5232 - val_acc: 0.7450
    Epoch 29/100
    100/100 [==============================] - 157s 2s/step - loss: 0.5301 - acc: 0.7400 - val_loss: 0.5141 - val_acc: 0.7450
    Epoch 30/100
    100/100 [==============================] - 154s 2s/step - loss: 0.5031 - acc: 0.7525 - val_loss: 0.4865 - val_acc: 0.7590
    Epoch 31/100
    100/100 [==============================] - 146s 1s/step - loss: 0.5167 - acc: 0.7370 - val_loss: 0.4805 - val_acc: 0.7780
    Epoch 32/100
    100/100 [==============================] - 144s 1s/step - loss: 0.5202 - acc: 0.7305 - val_loss: 0.4863 - val_acc: 0.7560
    Epoch 33/100
    100/100 [==============================] - 162s 2s/step - loss: 0.5198 - acc: 0.7355 - val_loss: 0.5094 - val_acc: 0.7470
    Epoch 34/100
    100/100 [==============================] - 146s 1s/step - loss: 0.5108 - acc: 0.7555 - val_loss: 0.5836 - val_acc: 0.7120
    Epoch 35/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5076 - acc: 0.7555 - val_loss: 0.4924 - val_acc: 0.7600
    Epoch 36/100
    100/100 [==============================] - 142s 1s/step - loss: 0.5022 - acc: 0.7455 - val_loss: 0.5013 - val_acc: 0.7530
    Epoch 37/100
    100/100 [==============================] - 145s 1s/step - loss: 0.5042 - acc: 0.7510 - val_loss: 0.5189 - val_acc: 0.7440
    Epoch 38/100
    100/100 [==============================] - 148s 1s/step - loss: 0.4836 - acc: 0.7600 - val_loss: 0.5507 - val_acc: 0.7100
    Epoch 39/100
    100/100 [==============================] - 145s 1s/step - loss: 0.5007 - acc: 0.7600 - val_loss: 0.5516 - val_acc: 0.7110
    Epoch 40/100
    100/100 [==============================] - 149s 1s/step - loss: 0.4939 - acc: 0.7665 - val_loss: 0.5484 - val_acc: 0.7450
    Epoch 41/100
    100/100 [==============================] - 154s 2s/step - loss: 0.4935 - acc: 0.7655 - val_loss: 0.5024 - val_acc: 0.7570
    Epoch 42/100
    100/100 [==============================] - 153s 2s/step - loss: 0.4899 - acc: 0.7590 - val_loss: 0.4803 - val_acc: 0.7870
    Epoch 43/100
    100/100 [==============================] - 154s 2s/step - loss: 0.4910 - acc: 0.7610 - val_loss: 0.5111 - val_acc: 0.7550
    Epoch 44/100
    100/100 [==============================] - 151s 2s/step - loss: 0.4862 - acc: 0.7455 - val_loss: 0.4563 - val_acc: 0.7850
    Epoch 45/100
    100/100 [==============================] - 155s 2s/step - loss: 0.4787 - acc: 0.7705 - val_loss: 0.4751 - val_acc: 0.7790
    Epoch 46/100
    100/100 [==============================] - 155s 2s/step - loss: 0.4931 - acc: 0.7645 - val_loss: 0.4804 - val_acc: 0.7730
    Epoch 47/100
    100/100 [==============================] - 152s 2s/step - loss: 0.4910 - acc: 0.7600 - val_loss: 0.4700 - val_acc: 0.7830
    Epoch 48/100
    100/100 [==============================] - 152s 2s/step - loss: 0.4659 - acc: 0.7805 - val_loss: 0.4947 - val_acc: 0.7580
    Epoch 49/100
    100/100 [==============================] - 153s 2s/step - loss: 0.4730 - acc: 0.7785 - val_loss: 0.4889 - val_acc: 0.7820
    Epoch 50/100
    100/100 [==============================] - 149s 1s/step - loss: 0.4818 - acc: 0.7730 - val_loss: 0.4700 - val_acc: 0.7780
    Epoch 51/100
    100/100 [==============================] - 151s 2s/step - loss: 0.4715 - acc: 0.7715 - val_loss: 0.4816 - val_acc: 0.7650
    Epoch 52/100
    100/100 [==============================] - 142s 1s/step - loss: 0.4842 - acc: 0.7665 - val_loss: 0.4560 - val_acc: 0.7760
    Epoch 53/100
    100/100 [==============================] - 147s 1s/step - loss: 0.4757 - acc: 0.7680 - val_loss: 0.4558 - val_acc: 0.7850
    Epoch 54/100
    100/100 [==============================] - 151s 2s/step - loss: 0.4667 - acc: 0.7775 - val_loss: 0.4785 - val_acc: 0.7720
    Epoch 55/100
    100/100 [==============================] - 151s 2s/step - loss: 0.4729 - acc: 0.7735 - val_loss: 0.4469 - val_acc: 0.7900
    Epoch 56/100
    100/100 [==============================] - 150s 1s/step - loss: 0.4641 - acc: 0.7735 - val_loss: 0.4548 - val_acc: 0.7880
    Epoch 57/100
    100/100 [==============================] - 150s 1s/step - loss: 0.4633 - acc: 0.7810 - val_loss: 0.4615 - val_acc: 0.7840
    Epoch 58/100
    100/100 [==============================] - 145s 1s/step - loss: 0.4749 - acc: 0.7755 - val_loss: 0.4551 - val_acc: 0.7970
    Epoch 59/100
    100/100 [==============================] - 151s 2s/step - loss: 0.4586 - acc: 0.7835 - val_loss: 0.4572 - val_acc: 0.7850
    Epoch 60/100
    100/100 [==============================] - 145s 1s/step - loss: 0.4563 - acc: 0.7860 - val_loss: 0.4316 - val_acc: 0.8040
    Epoch 61/100
    100/100 [==============================] - 150s 1s/step - loss: 0.4621 - acc: 0.7815 - val_loss: 0.4691 - val_acc: 0.7820
    Epoch 62/100
    100/100 [==============================] - 158s 2s/step - loss: 0.4487 - acc: 0.7925 - val_loss: 0.4801 - val_acc: 0.7640
    Epoch 63/100
    100/100 [==============================] - 149s 1s/step - loss: 0.4550 - acc: 0.7845 - val_loss: 0.4860 - val_acc: 0.7660
    Epoch 64/100
    100/100 [==============================] - 150s 2s/step - loss: 0.4564 - acc: 0.7790 - val_loss: 0.4586 - val_acc: 0.7900
    Epoch 65/100
    100/100 [==============================] - 146s 1s/step - loss: 0.4479 - acc: 0.7910 - val_loss: 0.4556 - val_acc: 0.7820
    Epoch 66/100
    100/100 [==============================] - 150s 1s/step - loss: 0.4581 - acc: 0.7705 - val_loss: 0.4366 - val_acc: 0.8050
    Epoch 67/100
    100/100 [==============================] - 147s 1s/step - loss: 0.4518 - acc: 0.7965 - val_loss: 0.4302 - val_acc: 0.8150
    Epoch 68/100
    100/100 [==============================] - 146s 1s/step - loss: 0.4473 - acc: 0.7900 - val_loss: 0.4829 - val_acc: 0.7790
    Epoch 69/100
    100/100 [==============================] - 148s 1s/step - loss: 0.4458 - acc: 0.7975 - val_loss: 0.4238 - val_acc: 0.8170
    Epoch 70/100
    100/100 [==============================] - 159s 2s/step - loss: 0.4493 - acc: 0.7900 - val_loss: 0.4383 - val_acc: 0.7980
    Epoch 71/100
    100/100 [==============================] - 147s 1s/step - loss: 0.4352 - acc: 0.8000 - val_loss: 0.4585 - val_acc: 0.7910
    Epoch 72/100
    100/100 [==============================] - 156s 2s/step - loss: 0.4369 - acc: 0.8020 - val_loss: 0.4281 - val_acc: 0.8040
    Epoch 73/100
    100/100 [==============================] - 145s 1s/step - loss: 0.4475 - acc: 0.7900 - val_loss: 0.5091 - val_acc: 0.7810
    Epoch 74/100
    100/100 [==============================] - 148s 1s/step - loss: 0.4379 - acc: 0.7865 - val_loss: 0.4565 - val_acc: 0.7800
    Epoch 75/100
    100/100 [==============================] - 147s 1s/step - loss: 0.4315 - acc: 0.8045 - val_loss: 0.4503 - val_acc: 0.8050
    Epoch 76/100
    100/100 [==============================] - 154s 2s/step - loss: 0.4386 - acc: 0.7895 - val_loss: 0.4379 - val_acc: 0.8060
    Epoch 77/100
    100/100 [==============================] - 146s 1s/step - loss: 0.4454 - acc: 0.7925 - val_loss: 0.4516 - val_acc: 0.7960
    Epoch 78/100
    100/100 [==============================] - 152s 2s/step - loss: 0.4459 - acc: 0.7940 - val_loss: 0.4661 - val_acc: 0.7850
    Epoch 79/100
    100/100 [==============================] - 144s 1s/step - loss: 0.4452 - acc: 0.7935 - val_loss: 0.4203 - val_acc: 0.8180
    Epoch 80/100
    100/100 [==============================] - 148s 1s/step - loss: 0.4318 - acc: 0.8025 - val_loss: 0.4556 - val_acc: 0.7950
    Epoch 81/100
    100/100 [==============================] - 152s 2s/step - loss: 0.4171 - acc: 0.8105 - val_loss: 0.4178 - val_acc: 0.8160
    Epoch 82/100
    100/100 [==============================] - 156s 2s/step - loss: 0.4327 - acc: 0.7940 - val_loss: 0.4878 - val_acc: 0.7740
    Epoch 83/100
    100/100 [==============================] - 145s 1s/step - loss: 0.4220 - acc: 0.8000 - val_loss: 0.4192 - val_acc: 0.8190
    Epoch 84/100
    100/100 [==============================] - 156s 2s/step - loss: 0.4392 - acc: 0.7925 - val_loss: 0.4264 - val_acc: 0.8020
    Epoch 85/100
    100/100 [==============================] - 144s 1s/step - loss: 0.4215 - acc: 0.8110 - val_loss: 0.4329 - val_acc: 0.7970
    Epoch 86/100
    100/100 [==============================] - 144s 1s/step - loss: 0.4196 - acc: 0.8005 - val_loss: 0.4153 - val_acc: 0.8230
    Epoch 87/100
    100/100 [==============================] - 145s 1s/step - loss: 0.4144 - acc: 0.8035 - val_loss: 0.4202 - val_acc: 0.8160
    Epoch 88/100
    100/100 [==============================] - 147s 1s/step - loss: 0.4194 - acc: 0.8065 - val_loss: 0.4216 - val_acc: 0.8180
    Epoch 89/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4108 - acc: 0.8090 - val_loss: 0.4340 - val_acc: 0.8060
    Epoch 90/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4323 - acc: 0.7885 - val_loss: 0.4167 - val_acc: 0.8130
    Epoch 91/100
    100/100 [==============================] - 144s 1s/step - loss: 0.4114 - acc: 0.8155 - val_loss: 0.4445 - val_acc: 0.8080
    Epoch 92/100
    100/100 [==============================] - 144s 1s/step - loss: 0.4213 - acc: 0.8080 - val_loss: 0.4108 - val_acc: 0.8240
    Epoch 93/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4059 - acc: 0.8150 - val_loss: 0.4118 - val_acc: 0.8250
    Epoch 94/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4150 - acc: 0.8150 - val_loss: 0.4159 - val_acc: 0.8200
    Epoch 95/100
    100/100 [==============================] - 142s 1s/step - loss: 0.4189 - acc: 0.8030 - val_loss: 0.4894 - val_acc: 0.7870
    Epoch 96/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4264 - acc: 0.8090 - val_loss: 0.4253 - val_acc: 0.8160
    Epoch 97/100
    100/100 [==============================] - 142s 1s/step - loss: 0.4046 - acc: 0.8165 - val_loss: 0.4245 - val_acc: 0.8070
    Epoch 98/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4103 - acc: 0.8020 - val_loss: 0.4478 - val_acc: 0.8010
    Epoch 99/100
    100/100 [==============================] - 143s 1s/step - loss: 0.4063 - acc: 0.8115 - val_loss: 0.4164 - val_acc: 0.8250
    Epoch 100/100
    100/100 [==============================] - 142s 1s/step - loss: 0.4012 - acc: 0.8290 - val_loss: 0.4538 - val_acc: 0.8050



```python
model.save('cats_and_dogs_small_2.h5')
```

从训练情况可以发现，数据增强后，overfitting得到了一定的解决  
val的acc也随着train的acc上涨到了80%  
这里原书上感觉有一个错误，虽然说train可以随机生成若干的图像，`batch_size`和`steps_per_epochs`、`validation_steps`配合还是要等于目录里的样本数；从另一个角度来看val是就那么多张的，所以我认为还是等于20.

这两天效率着实有点低。。🙃不能再拖了。。趁着还没到明天，赶紧先更新一下。。  
flipped很好看hhh😀
