# IMTransformer
transform images to data which can be used to train AI models

### About the convert_to_mnist_format.py 

Core method of reading image is : (pip install imageio)

```python
image=imageio.imread(image_path+filename)
```
if the image use 24bit instead of 8bit, then you can try to read one image by the above code, and `image.shape` to find that dimension, always can be averaged using `numpy.average(image,2)`, the parameter `2` stands for the last dimension of the image array.


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

