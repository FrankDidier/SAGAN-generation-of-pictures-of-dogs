# Self-Attention-GAN-Tensorflow-Deep Learning Final project
Simple Tensorflow implementation of ["Self-Attention Generative Adversarial Networks" (SAGAN)](https://arxiv.org/pdf/1805.08318.pdf)


## Requirements
* pip install -r requirements.txt

## Related works
* [BigGAN-Tensorflow](https://github.com/taki0112/BigGAN-Tensorflow)

## Summary
### Framework
![framework](./assests/framework.PNG)

### Code
```python
def attention(self, x, ch):
  f = conv(x, ch // 8, kernel=1, stride=1, sn=self.sn, scope='f_conv') # [bs, h, w, c']
  g = conv(x, ch // 8, kernel=1, stride=1, sn=self.sn, scope='g_conv') # [bs, h, w, c']
  h = conv(x, ch, kernel=1, stride=1, sn=self.sn, scope='h_conv') # [bs, h, w, c]

  # N = h * w
  s = tf.matmul(hw_flatten(g), hw_flatten(f), transpose_b=True) # # [bs, N, N]

  beta = tf.nn.softmax(s)  # attention map

  o = tf.matmul(beta, hw_flatten(h)) # [bs, N, C]
  gamma = tf.get_variable("gamma", [1], initializer=tf.constant_initializer(0.0))

  o = tf.reshape(o, shape=x.shape) # [bs, h, w, C]
  x = gamma * o + x

  return x
```

### Code2 (Google Brain)
```python
def attention_2(self, x, ch):
    batch_size, height, width, num_channels = x.get_shape().as_list()
    f = conv(x, ch // 8, kernel=1, stride=1, sn=self.sn, scope='f_conv')  # [bs, h, w, c']
    f = max_pooling(f)

    g = conv(x, ch // 8, kernel=1, stride=1, sn=self.sn, scope='g_conv')  # [bs, h, w, c']

    h = conv(x, ch // 2, kernel=1, stride=1, sn=self.sn, scope='h_conv')  # [bs, h, w, c]
    h = max_pooling(h)

    # N = h * w
    s = tf.matmul(hw_flatten(g), hw_flatten(f), transpose_b=True)  # # [bs, N, N]

    beta = tf.nn.softmax(s)  # attention map

    o = tf.matmul(beta, hw_flatten(h))  # [bs, N, C]
    gamma = tf.get_variable("gamma", [1], initializer=tf.constant_initializer(0.0))

    o = tf.reshape(o, shape=[batch_size, height, width, num_channels // 2])  # [bs, h, w, C]
    o = conv(o, ch, kernel=1, stride=1, sn=self.sn, scope='attn_conv')
    x = gamma * o + x

    return x
```
## Usage
### dataset
Create a new folder and name it dataset and then follow below:

* For `Our dataset`, put images like this:

```
├── dataset
   └── OUR_DATASET_NAME
       ├── xxx.jpg (name, format doesn't matter)
       ├── yyy.png
       └── ...
```

### train
* python main.py --phase train --dataset OUR_DATASET_NAME --gan_type hinge

### test
* python main.py --phase test --dataset OUR_DATASET_NAME --gan_type hinge

## Results
### ImageNet Example
<div align="">
   <img src="./assests/result_.png" width="420">
</div>

## Reference:
## Main Author
Junho Kim

## Contributors
Isingizwe Didier Frank, Bobak & Bastien
