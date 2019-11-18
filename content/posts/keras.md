---
title: "Getting started with Keras"
date: 2019-11-17T14:43:58-08:00
draft: true
---

First, let's install [keras](https//:keras.io).

```bash
$ pip install keras.
```

Lenet is a model designed by LeCun et al in 1998, used to recognize handwritten digits. Let's write a function that builds one.

```python
import keras

def create_lenet() -> keras.Sequential:
    model = keras.Sequential()
    model.add(layers.Conv2D(filters=6, kernel_size=(3, 3), activation='relu', input_shape=(28 , 28, 1)))
    model.add(layers.AveragePooling2D())
    model.add(layers.Conv2D(filters=16, kernel_size=(3, 3), activation='relu'))
    model.add(layers.AveragePooling2D())
    model.add(layers.Flatten())
    model.add(layers.Dense(units=120, activation='relu'))
    model.add(layers.Dense(units=84, activation='relu'))
    model.add(layers.Dense(units=10, activation = 'softmax'))
    return model
```

Now that we have our model, let's train it against the MNIST dataset.

```python
from keras.datasets import mnist
from keras.utils import np_utils

(X_train, y_train), (X_test, y_test) = mnist.load_data()
X_train /= 255
X_test /= 255

Y_train = np_utils.to_categorical(y_train, n_classes)
Y_test = np_utils.to_categorical(y_train, n_classes)

model = create_lenet()
model.compile(loss='categorical_crossentropy', metrics=['accuracy'], optimizer='sgd')
history = model.fit(X_train, Y_train, batch_size=32, epochs=20, validation_data=(X_test, Y_test))

model.save('lenet.h5')
```
