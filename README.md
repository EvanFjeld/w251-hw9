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
```git clone https://github.com/EvanFjeld/w251-hw9.git```

## Install nccl
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb
dpkg -i cuda-keyring_1.0-1_all.deb
apt-get update
```

## Launch Juypter Notebook
```jupyter-notebook```
run the notebooks

## Launch Tensorboard
```
pip install tensorflow
tensorboard --logdir=/workspace/w251-hw9/logs
```
