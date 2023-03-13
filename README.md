# w251-hw9

Create 2 ec2 g5.xlarge instances with NVIDEA GPU_Optimized AMI image and 400gb of storage. Link them to my security groups and private key and then ssh into them through my cli.

## Download imagenet file to aws and extract the files to the appropriate test and val folders
```
mkdir data
cd data
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_img_train.tar
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_img_val.tar
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_devkit_t12.tar.gz

mkdir train && mv ILSVRC2012_img_train.tar train/ && cd train
tar -xvf ILSVRC2012_img_train.tar && rm -f ILSVRC2012_img_train.tar
find . -name "*.tar" | while read NAME ; do mkdir -p "${NAME%.tar}"; tar -xvf "${NAME}" -C "${NAME%.tar}"; rm -f "${NAME}"; done
cd ..
```

### Extract the training dataset
```
mkdir train && mv ILSVRC2012_img_train.tar train/ && cd train
tar -xvf ILSVRC2012_img_train.tar && rm -f ILSVRC2012_img_train.tar
find . -name "*.tar" | while read NAME ; do mkdir -p "${NAME%.tar}"; tar -xvf "${NAME}" -C "${NAME%.tar}"; rm -f "${NAME}"; done
cd ..
```

### Extract the validation dataset
```
mkdir val && mv ILSVRC2012_img_val.tar val/ && cd val && tar -xvf ILSVRC2012_img_val.tar
wget -qO- https://raw.githubusercontent.com/soumith/imagenetloader.torch/master/valprep.sh | bash
```

## Launch docker image
Run docker image built for pytorce and mount it to the data folder where the cinic data is stored
```
docker run --rm -v /home/ubuntu/data:/data --net=host --gpus=all -ti -p 1234:1234 -p 6006:6006 nvcr.io/nvidia/pytorch:23.02-py3 bash
```

## Clone this repo
```
git clone https://github.com/EvanFjeld/w251-hw9.git
```

## Launch Juypter Notebook
```
jupyter-notebook
```
run the notebooks

## Launch Tensorboard
```
pip install --upgrade pip
pip install tensorflow
pip install tensorboard
pip install tensorflow-gpu
tensorboard --logdir=/workspace/w251-hw9/logs --host=0.0.0.0 --port=6006
```

# Notebooks
```imagenet_train_pytorch_ho9_singlevm.ipynb``` is the notework that trains the model on imagenet on a signle VM. ```imagenet_train_pytorch_ho9_0.ipynb``` is the master notebook to train on multiple VMs with RANK=0 while ```imagenet_train_pytorch_ho9_1.ipynb``` is the worker notebook with RANK=0.

For training, all notebooks us a g5.2xlarge instence on AWS with a NVIDIA GPU-Optimized AMI 22.06.0 image and 200 GBs or storage. They all train on a cuda GPU using autocast, a batch size of 1024 to use the GPU. I was not able to consistently use the GPUs at greather than 95% but got relatively close. 

In order to process the images in ImageNet 2012, I resize every image to 256x256 and then center crop at 224x224. I had some issues with the sizes of images and this was the most effective way I got it to work. 

If you open the TensorBoard following the instructions above, you can see the performance trends. There are screenshots of both the single VM TensorBoard as well as the multiple VM TensorBoard in their 

# Performance

## Single VM Training
In the folder titled 'Single VM Performance' you can see a few files with the GPU utilization as well as a screenshot of the tensorflow dashboard. To train two epochs, it took between 6.2 and 6.5 minutes a batch with a final accuracy for the top 1 predictions of 46.854% and an accuracy of the top 5 predictions of 72.586%. 

CPU times: user 27min 5s, sys: 4min 23s, total: 31min 28s
Wall time: 4h 42min 47s

You can see in the Tensorboard images that the model begins to overfit during each epoch but then takes a big jump in performance. Turning the learning rate and momentum would likely lead to better performance. 

## Two VM Training
In ithe folder title 'Double VM Performance', you can see a few images of the GPU usage and the model performance. To train the model two epochs on two VMs with multiple GPUs took just less than half the time at xxxx with a with a final accuracy for the top 1 predictions of 51.552% and an accuracy of the top 5 predictions of 77.380%. Clearly, the time savings of multiple GPUs was worth it. 

It's not quite half the time because of the additional overhead required to coordinate the work between the VMs and communicate between themm. 

CPU times: user 19min 17s, sys: 13min 39s, total: 32min 57s
Wall time: 1h 16min 49s
