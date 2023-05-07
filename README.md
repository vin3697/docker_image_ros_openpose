# docker_image_ros_openpose

#Credits
1. https://cmu-perceptual-computing-lab.github.io/openpose
2. https://janbkk10.medium.com/build-to-openpose-docker-on-ssh-server-5603874834e9
3. https://github.com/CMU-Perceptual-Computing-Lab/openpose/issues/1753
4. https://github.com/ravijo/ros_openpose.git

I have created an docker image where I have Openpose running with ROS software.
Host machine OS: Ubuntu 18.04 CUDA 10.1 and CuDnn 7.6 with Nvidia Graphic card.

The image has only Openpose and ROS installation. You can refer to my Docker Hub account where I have added the ROS driver and ROS Openpose wrapper where I am still working on it.


If you want to use the docker imgae please do the below steps,
After pulling the image and running with the command below:

docker run --gpus all -e DISPLAY=$DISPLAY --env NVIDIA_VISIBLE_DEVICES=all --env NVIDIA_DRIVER_CAPABILITIES=all --env DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v /dev:/dev --net=host --privileged --device=/dev/video0:/dev/video0 -it vin8/openpose_vin:v2.0 /bin/bash

The explanation of this command I have uploaded to Docker Hub,
https://hub.docker.com/r/vin8/openpose_vin

cd /openpose/build
rm -r *
cmake ..

#instead of cmake .. you can use below command

cmake -DBUILD_PYTHON=ON \
	  -DDOWNLOAD_BODY_25_MODEL=ON \
          -DDOWNLOAD_BODY_MPI_MODEL=OFF \
          -DDOWNLOAD_HAND_MODEL=OFF \
          -DDOWNLOAD_FACE_MODEL=OFF \
	  .. 
	  

sed -ie 's/set(AMPERE "80 86")/#&/g' ../cmake/Cuda.cmake
sed -ie 's/set(AMPERE "80 86")/#&/g' ../3rdparty/caffe/cmake/Cuda.cmake
make -j`nproc`
sudo make install

#BUILD PYTHON OPENPOSE
$ cd /openpose/build/python/openpose
$ make install

If you use pyopenpose (python scripts instead of cpp)
#SETUP setup env. FOR PYOPENPOSE AND IMPORT CHECK
$ cd /openpose/build/python/openpose
$ cp ./pyopenpose.cpython-36m-x86_64-linux-gnu.so /usr/local/lib/python3.6/dist-packages
$ cd /usr/local/lib/python3.6/dist-packages
$ ln -s pyopenpose.cpython-36m-x86_64-linux-gnu.so pyopenpose
$ export LD_LIBRARY_PATH=/openpose/build/python/openpose
$ python3
>>> import pyopenpose as op


/openpose# ./build/examples/openpose/openpose.bin
Starting OpenPose demo...
Configuring OpenPose...
Starting thread(s)...
Auto-detecting camera index... Detected and opened camera 0.

