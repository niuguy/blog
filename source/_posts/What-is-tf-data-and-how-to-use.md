---
title: What is tf.data and how to use
date: 2018-09-26 20:30:31
categories: 'tensorflow'
---

Tf.data is a high level API provided by tensorflow, it performs as a pipeline for complex input and output. The core data structure of tf.data is Dataset which represents a potentially large set of elements. 

Here is the defination of Dataset given by tensorflow.org

> A `Dataset` can be used to represent an input pipeline as a collection of elements (nested structures of tensors) and a "logical plan" of transformations that act on those elements.

To summarize, the dataset is a data pipeline, and we can do some preprocessing on it. The core problem of a pipeline is how the data be imported and consumed,  the following part will explain that as well as some useful APIs in preprocessing data.

### 1. Data input

Dataset can be built from several sources including csv file, numpy array and tensors.

#### From CSV

Tf.data provides a convenient API  make_csv_dataset to read records from one or more csv files.

Suppose the csv file is 

```csv
a,b,c,d
how,are,you,mate
I,am,fine,thanks
```

We can build a dataset from the above csv in the following way

```python
dataset = tf.contrib.data.make_csv_dataset(CSV_PATH, batch_size=2)
```

Here batch_size represents how many records would be aquired in a batch

We can use Iterator to see what contains in this dataset

```python
batch = dataset.make_one_shot_iterator().get_next()
print(batch['a'])
```

The result is 

```
tf.Tensor(['how' 'I'], shape=(2,), dtype=string)
```

make_csv_dataset defaultly takes the first row as header, if there are no header in the csv file like this

```csv
how,are,you,mate
I,am,fine,thanks
```

We can set `header=False`and `column_names=['a','b','c''d']`

```
dataset2 = tf.contrib.data.make_csv_dataset(CSV_PATH, batch_size=2, header=False,column_names=['a','b','c''d'])
```

Dataset2 should have the same value with dataset1

####  From Tensor slices

We can also create a dataset from tensors, the related API is  tf.data.Dataset.from_tensor_slices()

```python
dataset2 = tf.data.Dataset.from_tensor_slices(tf.random_uniform([10, 5]))
```

Actually the input of this API is not necessarily tensors,  numpy arrays are also adaptable .

```
dataset3 = tf.data.Dataset.from_tensor_slices(np.random.sample((10, 5)))
```

### 2. Data consuming

The only way to retrieve the data is Iterator(),  Iterator enables us to loop over all the dataset and get back the data we want. There are basically two kinds of Iterator which are [`make_one_shot_iterator`](https://www.tensorflow.org/api_docs/python/tf/data/Dataset#make_one_shot_iterator) and [`make_initializable_iterator`](https://www.tensorflow.org/api_docs/python/tf/data/Dataset#make_initializable_iterator).

#### make_one_shot_iterator

The examples can be find in the first part when we show how to import csv files

#### make_initializable_iterator

Compared to one shot iterator, initializable iterator allows data to be changed after dataset has already been built.Note that this cannot work in eager_execution model. Here is the example

```python
# using a placeholder
x = tf.placeholder(tf.float32, shape=[None,2])
dataset = tf.data.Dataset.from_tensor_slices(x)
data = np.random.sample((100,2))
iter = dataset.make_initializable_iterator() # create the iterator
el = iter.get_next()
with tf.Session() as sess:
    # feed the placeholder with data
    sess.run(iter.initializer, feed_dict={ x: data }) 
    print(sess.run(el)) # output [ 0.11342909, 0.81430183]
```



### 3. Data proprocessing

Tf.data provides several tools for data preprocessing such as batch and shuffle

#### Batch

dataset.batch(BATCH_SIZE)  given the BATCH_SIZE, this API will make the output in a batch way, and output BATCH_SIZE size of data at one time.

```python
# BATCHING
tf.enable_eager_execution()
BATCH_SIZE = 2
x = np.array([1,2,3,4])
# make a dataset from a numpy array
dataset = tf.data.Dataset.from_tensor_slices(x).batch(BATCH_SIZE)
iter = dataset.make_one_shot_iterator()
iter.get_next()
```

Output

```python
<tf.Tensor: id=102, shape=(2,), dtype=int64, numpy=array([1, 2])>
```



 #### Shuffle

When preparing the training data, one important step is shuffling the data to mitigate overfitting, tf.data offers convenient API to do that.

```python
BATCH_SIZE = 2
x = np.array([1,2,3,4])
# make a dataset from a numpy array
dataset = tf.data.Dataset.from_tensor_slices(x).shuffle(buffer_size = 10).batch(BATCH_SIZE)
iter = dataset.make_one_shot_iterator()
iter.get_next()
```

Output

```python
<tf.Tensor: id=115, shape=(2,), dtype=int64, numpy=array([2, 3])>
```



