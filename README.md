# w251-hw9

Create 2 ec2 g5.xlarge instances with NVIDEA GPU_Optimized AMI image and 400gb of storage. Link them to my security groups and private key and then ssh into them through my cli.

Download the cinic.zip file to aws:
```
mkdir data
cd data
curl -L https://w251hw05.s3.us-west-1.amazonaws.com/cinic.zip cinic.zip -o cinic.zip
unzip cinic.zip
```

Run docker image built for pytorce and mount it to the data folder where the cinic data is stored
```docker run --rm -v /data:/data --net=host --gpus=all -ti nvcr.io/nvidia/pytorch:23.02-py3 bash```
