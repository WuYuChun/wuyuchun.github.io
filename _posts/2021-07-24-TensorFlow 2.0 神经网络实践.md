# TensorFlow 2.0 神经网络实践

[^]: Hands-On Neural Networks with Tensorflow 2.0



https://github.com/PacktPublishing/Hands-On-Neural-Networks-with-Tensorflow-2.0



Tensorflow 是一个数值计算库，可以随意使用它提供的数学运算操作，充分发挥硬件的的全部计算能力，即使是用于与机器学习无关的任务。

使用python的虚拟环境



TensorFlow 1.x的环境设置

```
pipenv --python 3.7
pipenv shell
pip install tensorflow==1.15
```



Tensorflow 2.0的环境设置

```
pipenv --python 3.7
pipenv shell
pip install tensorflow==2.0
```



```
tennsorboard --logdir log/matmul
```



tensorflow 1.x 和 tensorflow 2.0的架构设计上的区别



**tf.data 和 tf.estimator**



tensorflow 数据集：tdfs

```
pip install tensorflow-datasets
```

