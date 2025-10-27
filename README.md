# go

#importing the required libraries
from tensorflow.keras.datasets import mnist
from tensorflow keras.models import Sequentfial
from tensorflow keras.layers import Conv2D
from tensorflow.keras.layers import MaxPool2D
from tensorflow.keras.layers import Flatten
from tensorflow.keras.layers import Dropout
from tensorflow keras.layers import Dense
#loading data
(X_train.y_train) , (X_test,y_test)=mnist.load_data()
#reshaping data
X _train=X_train.reshape((X_train.shape[0]. X_train.shape[1]. X_train.shape[2]. 1))
X_test =X _test.reshape((X_test.shape[0].X_test.shape[1].X_test.shape[2].1))
#checking the shape after reshaping
print(X_train.shape)
print(X_test.shape)
#normalizing the pixel values
X tran=X_train/255
X_test=X_test/255
Downloading data from https://storage.googleapis.com/tensorflou/tf-keras-datasets/mnist.npz
11493376/11490434 [ - @s us/step
11501568/11498434 | - 0s dus/step
(60009, 28, 28, 1)
(1009, 28, 28, 1)
#defining model
model=Sequential()
#adding convolution layer
modeladd(Conv2D(32.(3.3).activation=Telu'’.input_shape=(28.28.1)))
#adding pooling layer
model.add(MaxPool2D(2.2))
#adding fully connected layer
model.add(Flatten())
model.add(Dense(100.activation="relu’))
#adding output layer
modeladd(Dense(10.activation="softmax"))
#compiling the model
model.compile(loss='sparse_categorical _crossentropy'.optimizer="adam'.metrics=['accuracy])
#fitting the model
model fit(X_1 railLy_train.epochs=10)
Epoch 1/10 1875/1875
Epoch 2/10 1875/1875 [=:
Epoch 3/10 1875/1875 Epoch 4/10 1875/1875
Epoch 5/10 1875/1875 Epoch 6/10 1875/1875
Epoch 7/10 1875/1875 Epoch 8/10 1875/1875 Epoch 9/10 1875/1875
Epoch 16/10 1875/1875
#evaluting the model
model evaliate(X_testy_test)
313/313
[0.048568155616521835, ©.9854999780654907]
27 14ms/step - loss: ©.8464 - accuracy: ©.7492
25 13ms/step - loss: ©,3448 - accuracy: @.8985
18: 10ms/step - loss: ©.2882 - accuracy: ©.9149
oms/step - loss: 0.2433 - accuracy: 0.9281
- 18
- 18
1@ms/step - loss: @.2081 - accuracy: €.9383
18 lems/step - loss: 0.1841 - accuracy: 0.9442
18 1ems/step - loss: 0.1670 - accuracy: .9502
18 oms/step - loss: @.1532 - accuracy: @.9546
17 9ms/step - loss: ©.1426 - accuracy: 0.9578
18 lems/step - loss: 0.1329 - accuracy: .9500
] - 2s 7ms/step - loss: 0.0486 - accuracy: ©.9855