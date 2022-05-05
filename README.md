# IMTransformer
Transform IMages to data which can be used to train AI models. Thanks to https://github.com/Arlen0615/Convert-own-data-to-MNIST-format.

> But len(train_data.classes) still return 10, maybe diving into the source code of `torchvision.datasets.MNIST` should be necessary.
### Check the format of MNIST dataset
```python


train_data = torchvision.datasets.MNIST(root = "mnist",
                           train = True,
                           transform = torchvision.transforms.ToTensor(),
                           download = True)
 

test_data = torchvision.datasets.MNIST(root = "mnist",
                           train = False,
                           transform = torchvision.transforms.ToTensor(),
                           download = True)
 
print(train_data)
print("size=", len(train_data))
print("")
print(test_data)
print("size=", len(test_data))

```

### About the convert_to_mnist_format.py 

Before runing the script, you should make the directories look like this:

```
├─Origin-NonMnistFormat
│  ├─class-label-name0
|  |   ├─xx.png
|  |   ├─xx.png
│  ├─class-label-name1
|  |   ├─xx.jpg
|  |   ├─xx.png
│  ├─class-label-name2
       ├─xx.png
       ├─xx.jpg
```

Output is like this in a new created floder called `convert_MNIST` after you run the script and `gzip *ubyte`:

```
t10k-images-idx3-ubyte
t10k-images-idx3-ubyte.gz
t10k-labels-idx1-ubyte
t10k-labels-idx1-ubyte.gz
train-images-idx3-ubyte
train-images-idx3-ubyte.gz
train-labels-idx1-ubyte
train-labels-idx1-ubyte.gz
```


Core method of reading image is : (pip install imageio)

```python
image=imageio.imread(image_path+filename)
```
if the image use 24bit instead of 8bit, then you can try to read one image by the above code, and `image.shape` to find that dimension, always can be averaged using `numpy.average(image,2)`, the parameter `2` stands for the last dimension of the image array.


### How to manage these files:

If you want to use the least code to train the already existing models on your own data, follow this:

```python
test_data = torchvision.datasets.MNIST("./data",
                                       download=True,
                                       train=False,
                                       transform=torchvision.transforms.ToTensor())
```
As you know, the code above creates a dataset called "MNIST", but "MNIST" does not have to be MNIST. We still use the method `torchvision.datasets.MNIST`, `download=True`, and the folder "./data" should `contain` these contents:

```
├─MNIST
│  └─raw

```
`Put your new created MNIST-format files into the raw directory`, the settting `download=True` will automatically create something about "processed" and "training.pt","test.pt" in it. Also if you just put \*.gz into "raw", it will uncompress them. So well finished , keep going with your model.

### Have a test

Since you have finished the last step, try the following codes to see if the data compressed by the uppper method can be correctly reshown, if the image can be reshown, then we can use it safely.
```
import torchvision
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt

test_data = torchvision.datasets.MNIST("./data",
                                       download=True,
                                       train=False,
                                       transform=torchvision.transforms.ToTensor())
def imshow(tensor, title=None):
    image = tensor.cpu().clone()  # we clone the tensor to not do changes on it
    image = image.squeeze(0)  # remove the fake batch dimension
    image = unloader(image)
    plt.imshow(image)
    if title is not None:
        plt.title(title)
    plt.pause(0.001)  # pause a bit so that plots are updated
    
img,label=test_data[0]
unloader = torchvision.transforms.ToPILImage()
imshow(img,label)
```

### Recrusion read file :

```python
os.listdir(direct_parent_dir_of_images)
```

this will generate a list of all images, then you can change all the images by iterating it. 

### If you want to transfor your 3 channels' images to 1 channel, then follw this code:

```python
import torch
import torchvision
import torchvision.transforms as transforms
from PIL import Image

for filename in image_path:

    image = Image.open(path+filename)

    input_transform = transforms.Compose([
       transforms.Grayscale(1), #这一句就是转为单通道灰度图像
       transforms.ToTensor(),
    ])
    image_tensor = input_transform(image)
    torchvision.utils.save_image(image_tensor,target_path)
```

