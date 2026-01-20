ARG ROS_DISTRO=humble
FROM ros:${ROS_DISTRO}-ros-base AS base

ARG ROS_DISTRO

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive \
    TERM=xterm-256color


RUN apt-get update && apt-get install -y --no-install-recommends\
    wget \
    git \
    ssh \
    terminator \
    iputils-ping \
    openssh-server \
    iproute2 \
    python3-pip \
    nano \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends\
    ros-$ROS_DISTRO-rqt \
    ros-$ROS_DISTRO-rqt-common-plugins \
    ros-$ROS_DISTRO-rqt-robot-dashboard \
    ros-$ROS_DISTRO-rqt-robot-steering \
    ros-$ROS_DISTRO-rqt-tf-tree \
    ros-$ROS_DISTRO-turtlesim \
    ros-$ROS_DISTRO-rviz2 \
    libgl1-mesa-dri \
    libgl1-mesa-glx \
    mesa-utils \
    ros-$ROS_DISTRO-rmw-fastrtps-cpp \
    && apt-get clean && rm -rf /var/lib/apt/lists/*


WORKDIR /
RUN mkdir -p dev_ws/src

COPY . /dev_ws/src/

WORKDIR /dev_ws

# Install ROS package dependencies (rosdep)
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-rosdep \
    python3-colcon-common-extensions \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN rosdep init || true
RUN rosdep update
RUN ["/bin/bash", "-c", "source /opt/ros/$ROS_DISTRO/setup.bash && \
    rosdep install --from-paths src --ignore-src -r -y"]

# Build

RUN ["/bin/bash", "-c", "source /opt/ros/$ROS_DISTRO/setup.bash && \
     colcon build --merge-install --symlink-install --cmake-args '-DCMAKE_BUILD_TYPE=Release'"]


RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/setup.bash /dev_ws/setup.bash && chmod 777 /dev_ws/setup.bash"]
RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/entrypoint.bash / && chmod 777 /entrypoint.bash"]

ENTRYPOINT ["bash", "/entrypoint.bash"]

CMD ["bash"]