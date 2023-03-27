# DPU-Xilinx-log
# 2023-03-27

## Environment Setup
> 1.Clone the Vitis AI repository 
```shell
[Host]$ git clone https://github.com/Xilinx/Vitis-AI 
[Host]$ cd Vitis-AI
```
> 2.Install Docker
>> Prerequisites
``` shell
sudo apt install gnome-terminal
```
>>Set up Docker’s package repository
>>>1.Update the apt package index and install packages to allow apt to use a repository over HTTPS
``` shell
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg
```
>>>2.Add Docker’s official GPG key:
``` shell
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
>>>3.Use the following command to set up the repository:
``` shell
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
>>Install Docker Engine
>>>1.Update the apt package index:
``` shell
sudo apt-get update
```
>>>2.Install Docker Engine, containerd, and Docker Compose.
``` shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
>>>3.Verify that the Docker Engine installation is successful by running the hello-world image:
``` shell
sudo docker run hello-world
```
>>Install the package with apt as follows:
``` shell
sudo apt-get update
sudo apt-get install ./docker-desktop-<version>-<arch>.deb
```


> 3. Ensure your Linux user is in the group docker
>>1.Create the docker group.
``` shell
sudo groupadd docker
```
>>2.Add your user to the docker group.
``` shell
sudo usermod -aG docker $USER
```
>>3.Log out and log back in so that your group membership is re-evaluated.
--
If you’re running Linux in a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.

``` shell
newgrp docker
```
>>4.Verify that you can run docker commands without sudo.
``` shell
docker run hello-world
```

> 4.Download the latest Vitis AI Docker
``` shell
docker pull xilinx/vitis-ai-<pytorch/tensorflow/tensorflow2>-cpu:latest
```


## Model Deployment
>Downloading float model from model zoo
>Take tensorflow resnet50 for exampl
``` shell
cd Vitis-AI
wget https://www.xilinx.com/bin/public/openDownload?filename=tf_resnetv1_50_imagenet_224_224_6.97G_3.0.zip -O tf_resnetv1_50_imagenet_224_224_6.97G_.0.zip
unzip tf_resnetv1_50_imagenet_224_224_6.97G_3.0.zip
```
>Quantizing the model
>>Prepare dataset. For tf_resnet50 model, download the calibration images from 
Imagenet_calib.tar.gz, and copy into Vitis-AI folder (Full dataset is from ImageNet).
>>Launch the docker image
``` shell
./docker_run.sh xilinx/vitis-ai-tensorflow-cpu:latest
```
>> Quantize the model.
>>Set CALIB_BATCH_SIZE in the tf_resnetv1_50_imagenet_224_224_6.97G_3.0/
code/quantize/config.ini to 5. Then run
``` shell
conda activate vitis-ai-tensorflow
tar -xzvf Imagenet_calib.tar.gz -C tf_resnetv1_50_imagenet_224_224_6.97G_3.0/data
cd tf_resnetv1_50_imagenet_224_224_6.97G_3.0/code/quantize
bash quantize.sh
```
>>>After running quantize.sh, the quantized model are available in
tf_resnetv1_50_imagenet_224_224_6.97G_3.0/quantized

## Compiling the model

