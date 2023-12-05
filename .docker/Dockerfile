ARG ROS_DISTRO=noetic

FROM ros:$ROS_DISTRO-ros-base as base

ARG ROS_DISTRO

ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive
ENV TERM xterm-256color

RUN apt-get update && apt-get install -y --no-install-recommends\
    ssh \
    git \
    curl \
    wget \
    build-essential \
    cmake \
    python3-pip \
    python3-flake8 \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pip3 install --no-cache-dir --upgrade pip\
    argcomplete \
    flake8-blind-except \
    flake8-builtins \
    flake8-comprehensions \
    flake8-deprecated \
    flake8-return \
    flake8-length \
    flake8-todo \
    flake8-quotes \
    black \
    mypy \
    prospector \
    pylint \
    numpy \
    opencv-python \
    pyquaternion \
    python-dateutil \
    matplotlib \
    pyyaml \
    pytransform3d \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends\
    pkg-config \
    python3-catkin-tools \
    python3-rosdep \
    python3-rosinstall-generator \
    python3-vcstool \
    python3-pykdl \
    ros-$ROS_DISTRO-rqt \
    ros-$ROS_DISTRO-rqt-common-plugins \
    ros-$ROS_DISTRO-rqt-robot-plugins \
    ros-$ROS_DISTRO-rqt-joint-trajectory-controller \   
    ros-$ROS_DISTRO-roslint \
    ros-$ROS_DISTRO-rqt-gui \
    ros-$ROS_DISTRO-rqt-gui-py \
    ros-$ROS_DISTRO-rqt-py-common \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /
RUN mkdir -p dev_ws/src
WORKDIR /dev_ws/src


# FROM base as ka

# RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.4/libk4a1.4_1.4.1_amd64.deb > /tmp/libk4a1.4_1.4.1_amd64.deb
# RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.4-dev/libk4a1.4-dev_1.4.1_amd64.deb > /tmp/libk4a1.4-dev_1.4.1_amd64.deb
# RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.1/libk4abt1.1_1.1.2_amd64.deb > /tmp/libk4abt1.1_1.1.2_amd64.deb
# RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.1-dev/libk4abt1.1-dev_1.1.2_amd64.deb > /tmp/libk4abt1.1-dev_1.1.2_amd64.deb
# RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/k/k4a-tools/k4a-tools_1.4.1_amd64.deb > /tmp/k4a-tools_1.4.1_amd64.deb
# RUN echo 'libk4a1.4 libk4a1.4/accepted-eula-hash string 0f5d5c5de396e4fee4c0753a21fee0c1ed726cf0316204edda484f08cb266d76' | debconf-set-selections
# RUN echo 'libk4abt1.1	libk4abt1.1/accepted-eula-hash	string	03a13b63730639eeb6626d24fd45cf25131ee8e8e0df3f1b63f552269b176e38' | debconf-set-selections
# RUN dpkg -i /tmp/libk4a1.4_1.4.1_amd64.deb
# RUN dpkg -i /tmp/libk4a1.4-dev_1.4.1_amd64.deb
# RUN dpkg -i /tmp/libk4abt1.1_1.1.2_amd64.deb
# RUN dpkg -i /tmp/libk4abt1.1-dev_1.1.2_amd64.deb
# RUN apt-get update
# RUN apt-get install -y libsoundio1
# RUN dpkg -i /tmp/k4a-tools_1.4.1_amd64.deb
# # fix k4abtConfig.cmake Error: Azure Kinect SDK Version numbers contain exactly 3 components
# RUN sed -i 's/find_dependency(k4a 1.4 REQUIRED)/find_dependency(k4a 1.4.1 REQUIRED)/' /usr/lib/cmake/k4abt/k4abtConfig.cmake

# RUN pip3 install --no-cache-dir --upgrade pyk4a

# RUN apt-get update && apt-get install -y --no-install-recommends\
#     ros-$ROS_DISTRO-image-geometry \
#     ros-$ROS_DISTRO-compressed-image-transport \
#     ros-$ROS_DISTRO-camera-info-manager \
#     ros-$ROS_DISTRO-xacro \
#     ros-$ROS_DISTRO-rgbd-launch \
#     ros-$ROS_DISTRO-joint-state-publisher \
#     ros-$ROS_DISTRO-robot-state-publisher \
#     && apt-get clean && rm -rf /var/lib/apt/lists/*

# WORKDIR /dev_ws/src
# RUN git clone https://github.com/microsoft/Azure_Kinect_ROS_Driver.git



## for Astra
FROM base as astra
WORKDIR /
RUN apt-get update && apt-get install -y --no-install-recommends\
    libgoogle-glog-dev \
    libusb-1.0-0-dev \
    libeigen3-dev \   
    libgflags-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends\
    libgflags-dev \
    ros-$ROS_DISTRO-image-geometry \
    ros-$ROS_DISTRO-camera-info-manager \
    ros-$ROS_DISTRO-image-transport \
    ros-$ROS_DISTRO-image-publisher \
    libgoogle-glog-dev \
    libusb-1.0-0-dev \
    libeigen3-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN ["/bin/bash", "-c", "git clone https://github.com/libuvc/libuvc.git &&\
    cd libuvc &&\
    mkdir build && cd build &&\
    cmake .. && make -j4 &&\
    sudo make install &&\
    sudo ldconfig"]

WORKDIR /dev_ws/src
RUN ["/bin/bash", "-c", "git clone https://github.com/orbbec/ros_astra_camera.git"]



## for TurtleBot
FROM astra as tbot
WORKDIR /
RUN apt-get update && apt-get install -y --no-install-recommends\
    ros-$ROS_DISTRO-rviz \
    ros-$ROS_DISTRO-xacro \
    ros-$ROS_DISTRO-rosbash \
    ros-$ROS_DISTRO-rqt-action \
    ros-$ROS_DISTRO-rqt-console \
    ros-$ROS_DISTRO-rqt-graph \
    ros-$ROS_DISTRO-rqt-topic \
    ros-$ROS_DISTRO-rqt-tf-tree \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends\
    ros-$ROS_DISTRO-joy \
    ros-$ROS_DISTRO-openni2-launch \
    ros-$ROS_DISTRO-gmapping \
    ros-$ROS_DISTRO-slam-gmapping \
    ros-$ROS_DISTRO-move-base \
    ros-$ROS_DISTRO-amcl \
    ros-$ROS_DISTRO-navigation \
    ros-$ROS_DISTRO-map-server \
    && apt-get clean && rm -rf /var/lib/apt/lists/*


# WORKDIR /dev_ws/src
# git repos/packages installed in the workspace outside the docker


# ## for Lidar
# git repos/packages installed in the workspace outside the docker


FROM tbot as open3d

RUN pip3 install --no-cache-dir --upgrade open3d

FROM open3d as build

COPY . /dev_ws/src/

WORKDIR /dev_ws

# RUN rosdep init
RUN apt-get update
RUN rosdep update
RUN rosdep install --from-paths src --ignore-src -r -y
RUN apt-get clean && rm -rf /var/lib/apt/lists/*


RUN ["/bin/bash", "-c", "source /opt/ros/$ROS_DISTRO/setup.bash &&\
    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release &&\
    catkin build \
    "]

FROM build as bash

RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/setup.bash /dev_ws/setup.bash && chmod 777 /dev_ws/setup.bash"]
RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/entrypoint.bash / && chmod 777 /entrypoint.bash"]
ENTRYPOINT ["bash", "/entrypoint.bash" ]

CMD ["bash"]
