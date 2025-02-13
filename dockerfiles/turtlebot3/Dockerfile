# Specify the ROS2 distribution as a build argument
ARG ROS_DISTRO=humble

# Use an official ROS base image
FROM osrf/ros:${ROS_DISTRO}-desktop

# Define maintainer
LABEL maintainer="azahir@pa.uc3m.es"

# Set up user with sudo access
WORKDIR /root

# Create a new user 'agent' with sudo privileges and set a password
RUN useradd -m agent && \
    echo "agent:agent" | chpasswd && \
    adduser agent sudo

# Set an environment variable to noninteractive to avoid prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Use bash shell for subsequent commands
SHELL [ "/bin/bash" , "-c" ]

# Remove pre-installed packages to avoid conflicts
RUN apt-get remove -y ros-${ROS_DISTRO}-robot-state-publisher || true
# RUN apt-get remove -y ros-${ROS_DISTRO}-gazebo-ros-pkgs || true

# Switch to the newly created user for better security (avoid using root)
USER agent
WORKDIR /home/agent

# Clone the necessary ROS packages into the workspace
RUN mkdir -p ros2_ws/src 
COPY ros2_ws/src /home/agent/ros2_ws/src

USER root

# Install necessary packages and cleaning up the apt cache to reduce the image size
RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-turtlebot3-msgs \
    ros-${ROS_DISTRO}-gazebo-ros-pkgs

# RUN cd /home/agent/ros2_ws && \
#     rosdep update && \
#     rosdep install --from-paths src --ignore-src -r -y

RUN rm -rf /var/lib/apt/lists/*

USER agent

# Build the workspace
RUN cd ros2_ws && source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build --symlink-install

RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc
RUN echo "source /home/agent/ros2_ws/install/setup.bash" >> ~/.bashrc

RUN mkdir scripts 
COPY scripts/* /home/agent/scripts/

WORKDIR /home/agent/scripts/

# Indicate completion of Dockerfile setup
RUN echo "ALL Done"

