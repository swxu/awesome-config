# YOLO Training Conclusion on Custom Dataset and FAQs

> [YOLO official website](https://pjreddie.com/darknet/yolo/)

## Prepare dataset
You can google how to prepare your custom dataset. There are lots of tutorials available. I recommend [this one](https://timebutt.github.io/static/how-to-train-yolov2-to-detect-custom-objects/). 

My custom dataset has ~15k images and 3 classes. I just replace image files in VOC dataset ```/VOCdevkit``` with my custom images and run official python script to generate data YOLO needs. You can click the official website link above to find the script. It's easy to learn how to use it.

## Compile
We need to modify ```Makefile``` before we ```make``` it.
```
GPU=1 # If you have a GPU, it will speed up the training process greatly.
CUDNN=1 # Using cudnn
OPENCV=0 # Using camera
OPENMP=0
DEBUG=0
...
```
Then, just ```make``` the source code.

## Train
There are three files needing to be modified before training on custom dataset.
- First, you just need to write class names in your dataset to ```data/voc.names```. Of course, you can change the file name.
```
class A
class B
class C
...
```
- Second, you need to modify ```cfg/voc.names```. Just change it based on your custom dataset and file paths.
```
classes= 3
train  = <path to train file>/train.txt
valid  = <path to test file>/test.txt
names = data/voc.names
backup = backup
```
- Before strating step 3, I recommend you to read [this article](https://stackoverflow.com/questions/50390836/understanding-darknets-yolo-cfg-config-files) to understand what parameters mean.
- Third, the key step here. We need to modify yolo network configuration to meet our requirements for custom dataset. Read configuration below **carefully**. Here we give two examples, ```yolov2-tiny-voc.cfg``` and ```yolov3-voc.cfg```, you can configure the network you like.
```
// yolov2-tiny-voc.cfg
[net]
# Testing
# batch=1
# subdivisions=1

# Training
batch=64 # you need to comment testing cfg and uncomment training cfg
subdivisions=2

width=416
height=416
channels=3
...

# you can change it for your own settings
learning_rate=0.001
max_batches = 40200
policy=steps
steps=-1,100,20000,30000
scales=.1,10,.1,.1
...
...
[convolutional]
size=1
stride=1
pad=1
filters=40 # filters = num * (classes + coords + 1) = 5 * (3 + 4 + 1)
activation=linear

[region]
anchors = 1.08,1.19,  3.42,4.41,  6.63,11.38,  9.42,5.11,  16.62,10.52
bias_match=1
classes=3 # you may need to change this
coords=4 # compute filters above
num=5 # compute filters above
softmax=1
jitter=.2
rescore=1

object_scale=5
noobject_scale=1
class_scale=1
coord_scale=1

absolute=1
thresh = .6
random=1
```
```
// yolov3-voc.cfg
[net]
# Testing
# batch=1
# subdivisions=1

# Training
batch=64 # you need to comment testing cfg and uncomment training cfg
subdivisions=2

width=416
height=416
channels=3
...

# you can change it for your own settings
learning_rate=0.001
max_batches = 40200
policy=steps
steps=-1,100,20000,30000
scales=.1,10,.1,.1
...
...
[convolutional]
size=1
stride=1
pad=1
filters=24 # filters = 3 * (classes + 5)
activation=linear

[yolo]
mask = 6,7,8
anchors = 10,13,  16,30,  33,23,  30,61,  62,45,  59,119,  116,90,  156,198,  373,326
classes=3 # you need to modify
num=9
jitter=.3
ignore_thresh = .5
truth_thresh = 1
random=1
...
...
[convolutional]
size=1
stride=1
pad=1
filters=24 # filters = 3 * (classes + 5)
activation=linear

[yolo]
mask = 3,4,5
anchors = 10,13,  16,30,  33,23,  30,61,  62,45,  59,119,  116,90,  156,198,  373,326
classes=3 # you need to modify
num=9
jitter=.3
ignore_thresh = .5
truth_thresh = 1
random=1
...
...
[convolutional]
size=1
stride=1
pad=1
filters=24 # filters = 3 * (classes + 5)
activation=linear

[yolo]
mask = 0,1,2
anchors = 10,13,  16,30,  33,23,  30,61,  62,45,  59,119,  116,90,  156,198,  373,326
classes=3 # you need to modify
num=9
jitter=.3
ignore_thresh = .5
truth_thresh = 1
random=1
```
- Now, we can run the training command. Here we train from scratch, you also can download pre-trained weights from YOLO official website above.
```shell
./darknet detector train cfg/voc.data cfg/yolov3-voc.cfg
```
- In addition, you can read [this article](https://timebutt.github.io/static/understanding-yolov2-training-output/) to understand outputs of YOLO.
# Test
It's easy to test model trainde well. Just run command here.
```shell
./darknet detector test cfg/voc.data cfg/yolov3-voc.cfg backup/yolov3-voc_final.weights <path to testing image>
```
If you get **no bounding box** in ```predictions.jpg```, you firstly need to check your ```cfg/yolov3-voc.cfg``` file. In fact, I forgot to uncomment testing configuration in ```.cfg``` file. And just modify it, you will get correct prediction results. If nothing changes, you need to think about whether your custom dataset correctly generated.

# FAQs
### ```Dimension mismatch``` when loading network model
Obviously, your network filters is incorrect. Go back to ```.cfg``` file and modify ```filters```.
### ```CUDA Out of Memory``` when loading data
You need to modify ```cfg/yolov3-voc.cfg```. Increase ```subdivisions```, or decrease ```batch```.
### Appearence of lots of ```nan``` when training.
First, check your data path and data whether is correct. If that is not ok, you should focus on ```learning_rate```. Generally, you should decrease your ```learning_rate``` in ```.cfg``` file.
### Training output ```obj=0.000000```
It means that your network does not detect any object. Definately, it has some problems. You could tune ```learning_rate``` and ```batch``` in ```.cfg``` file. Generally, you should decrease the ```learning_rate``` or decrease the ```batch```.
### No bounding boxes when testing images
I also encountered the problem. Actually, it is a careless problem. Just go back to the ```.cfg``` file and uncomment testing configuration and comment training configuration.