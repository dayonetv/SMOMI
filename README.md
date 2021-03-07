# Описание архитектуры нейронной сети
В данной лабораторной работе мы обучали сверточную нейронную сеть для решения задачи классификации изображений дикой природы Oregon wildlife. Данный датасет Oregon_wildlife включает в себя около 12.000 картинок дикой природы, которые распределены на 20 различных категорий. Для обучения нашей нейронной сети мы использовали 50 эпох, т.е. весь наш датасет пройдет через нейронную сеть 50 раз. Каждую эпоху мы раздели на части (батчи) размером 512 объектов в каждом батче. 
*  Размерность входного изображения (224x224x3): 
```
inputs = tf.keras.Input(shape=(RESIZE_TO, RESIZE_TO, 3))
```
***
* Сверточные слои добавлены к нашей нейронной сети с использованием следующей функции Conv2D:  
```
x = tf.keras.layers.Conv2D(filters=8, kernel_size=3)(inputs)
```
Всего в одном сверточном слое у нас 8 фильтров, каждый фильтр будет применяться к изображению. Размерность матрицы ядра (фильтра) состовляет 3x3.
Благодаря ядру мы постепенно “скользим” по нашему изображению  и выполняем операцию свертки, а именно, поэлементно умножаем элементы нашего изображения на соответствующие элементы матрицы ядра и затем все эти произведения суммируем между собой. Далее с определенным шагом (по умолчанию шаг = 1) мы переходим к следующей части нашего изображения. Таким образом, наше ядро будет перемещаться по всему изображению с определенным шагом. Соответственно на выходе мы получаем некоторую выходную матрицу с элементами, которые получились в результате свертки. На выходе уменьшиться размерность (она станет 222x222x8, так как в картинку размером 224x224 поместятся только 222x222 окон свертки размером 3x3). Формула изменения размера изображения:
```
[(W-K+2P)/S]+1
W - размер входного изображения
K - размер матрицы ядра
P - если есть padding, то 1, если нету, то 0
S - шаг 
```
*** 
* После получения свернутого изображения производится операция подвыборки (Max-Pooling):  
```
x = tf.keras.layers.MaxPool2D()(x)
```
Данная операция позволяет сгруппировать пиксели изображения и уплотнить их, т.е. уменьшить размер матрицы признаков (на выходе получим тензор размеров 111x111x8). Она сгруппирует пиксели в участке определенного размера (по умолчанию 2x2) и выберет элемент с наибольшим значением из 4 возможных значений, а далее перейдет к следующему участку с определенным шагом (по умолчанию 2), поэтому мы сожмем изображение и подчеркнем некоторые признаки.
***
* Flatten слой, позволяющий преобразовать наш многомерный тензор в одномерный (1D). На выходе получим одномерный массив размеров 111x111x8=98568
```
x = tf.keras.layers.Flatten()(x)
```
***
* Полносвязный Dense слой
```
outputs = tf.keras.layers.Dense(NUM_CLASSES, activation=tf.keras.activations.softmax)(x)
```
Здесь мы и определяем к какому классу (категории) из 20 возможных будет относится поданное на вход изображение, а именно функция активации softmax даст определенную вероятность того, что поданное на вход изображение относится к определенному классу (из 20 возможных).
***
### После обучения данной нейронной сети получили следующие графики:
* График точности для сверточной нейронной сети с одним сверточных слоем:
![accuracy 512_batch_size](https://user-images.githubusercontent.com/59259102/110211637-342a8100-7ea0-11eb-92d8-e1555b53729e.jpg)  
* График функции потерь для сверточной нейронной сети с одним сверточных слоем:
![loss 512_batch_size](https://user-images.githubusercontent.com/59259102/110211665-50c6b900-7ea0-11eb-851d-3d5912691f13.jpg)
* Анализ полученных результатов
***
----
## Создать и обучить сверточную нейронную сеть произвольной архитектуры с количеством сверточных слоев >3.
* Для решения этой задачи мы изменили количесвто сверточных слоев, а именно, увеличили количество слоев Conv2D и увеличили количество слоев подвыборки MaxPool2D. Всего получили 5 сверточных слоев и 4 слоя подвыборки.
 * Причем у первых двух сверточных слоев изменили такие параметры как размер матрицы ядра (стал 5x5), а также добавили padding (padding = 'same') для того, чтобы входное изображение дополнялось крайними нулевыми значениями, и таким образом, когда ядро будет проходить по нашему изображению на выходе мы получим матрицу таких же размеров что и на входе (размерность не будет уменьшаться). 
 * У последнего сверточного слоя увеличили шаг: по умолчанию шаг = 1, стал равен 2 (strides = 2). Таким образом увеличив шаг с которым наша матрица ядра будет проходить по изображению по вертикали и по горизонтали, мы уменьшим размерность нашего изображения в 2 раза на выходе.
 * Все слои подвыборки MaxPool2D остались в значениях по умолчанию (размер ядра пулинга = 2x2, шаг пулинга = 2, padding отсутствует)
