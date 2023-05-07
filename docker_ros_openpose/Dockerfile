

# <https://hub.docker.com/r/cwaffles/openpose>
FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04


#get deps
RUN apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
python3-dev python3-pip python3-setuptools git g++ wget make libprotobuf-dev protobuf-compiler libopencv-dev \
libgoogle-glog-dev libboost-all-dev libcaffe-cuda-dev libhdf5-dev libatlas-base-dev


#for python api
#RUN pip3 install scikit-build
RUN pip3 install scikit-build numpy opencv-python==4.1.1.26


#replace cmake as old version has CUDA variable bugs
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.0/cmake-3.16.0-Linux-x86_64.tar.gz && \
tar xzf cmake-3.16.0-Linux-x86_64.tar.gz -C /opt && \
rm cmake-3.16.0-Linux-x86_64.tar.gz
ENV PATH="/opt/cmake-3.16.0-Linux-x86_64/bin:${PATH}"


#ADDED PORTION IN FILE :STARTS
# Install ROS Melodic
RUN apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    apt-utils \
    dirmngr \
    gnupg2 \
    lsb-release
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
RUN echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list
RUN apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    ros-melodic-desktop-full

#END OF PORTION


#get openpose
WORKDIR /openpose
RUN git clone -q --depth 1 https://github.com/CMU-Perceptual-Computing-Lab/openpose.git .


#build it
WORKDIR /openpose/build
RUN cmake -DBUILD_PYTHON=ON \
	  -DDOWNLOAD_BODY_25_MODEL=ON \
          -DDOWNLOAD_BODY_MPI_MODEL=OFF \
          -DDOWNLOAD_HAND_MODEL=OFF \
          -DDOWNLOAD_FACE_MODEL=OFF \
	  .. 

#these commands are commenting out a line of code in two CMake files named Cuda.cmake 
#by replacing the line that sets the AMPERE variable with a commented version of the same line.
RUN sed -ie 's/set(AMPERE "80 86")/#&/g'  ../cmake/Cuda.cmake && \
    sed -ie 's/set(AMPERE "80 86")/#&/g'  ../3rdparty/caffe/cmake/Cuda.cmake
RUN make -j`nproc`
RUN make install
WORKDIR /openpose