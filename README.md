# w251-hw9

Create 2 ec2 g5.xlarge instances with NVIDEA GPU_Optimized AMI image and 400gb of storage. Link them to my security groups and private key and then ssh into them through my cli.

## Download imagenet file to aws and extract the files to the appropriate test and val folders
```
mkdir data
cd data
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_img_train.tar
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_img_val.tar
wget https://w251hw05.s3.us-west-1.amazonaws.com/ILSVRC2012_devkit_t12.tar.gz

mkdir train
mkdir val
tar -xvf ILSVRC2012_img_train.tar -C train
tar -xvf ILSVRC2012_img_val.tar -C val

rm ILSVRC2012_img_train.tar
rm ILSVRC2012_img_val.tar
```

## Launch docker image
Run docker image built for pytorce and mount it to the data folder where the cinic data is stored
```docker run --rm -v /home/ubuntu/data:/data --net=host --gpus=all -ti nvcr.io/nvidia/pytorch:23.02-py3 bash```

## Clone this repo
```get clone ```

## Launch Juypter Notebook
```jupyter-notebook```
