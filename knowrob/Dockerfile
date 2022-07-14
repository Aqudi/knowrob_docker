FROM osrf/ros:kinetic-desktop-full as base

ARG APT_CACHER_NG_HOST
ARG APT_CACHER_NG_PORT

# Config apt-cacher-ng proxy
RUN echo "Acquire::http { Proxy \"http://$APT_CACHER_NG_HOST:$APT_CACHER_NG_PORT\"; };" >> /etc/apt/apt.conf.d/01proxy

# Install apt packages
RUN apt-get -y update && \ 
    apt-get -y install git swi-prolog swi-prolog-java libjson-java libjson-glib-dev \
    ros-kinetic-rosjava ros-kinetic-genjava ros-kinetic-rosjava-messages \
    ros-kinetic-rosauth mencoder lame libavcodec-extra \
    ros-kinetic-rosbridge-suite ros-kinetic-rosapi ros-kinetic-rosbridge-library ros-kinetic-rosbridge-server \
    python-rrdtool python-pymongo \
    emacs mongodb-clients openjdk-8-jdk texlive-latex-base imagemagick --fix-missing && \ 
    apt-get -y autoremove  && \ 
    apt-get -y clean && \ 
    rm -rf /var/lib/apt/lists/* && \ 
    rm -rf /tmp/*

RUN cp /opt/ros/kinetic/setup.sh /etc/profile.d/ros_kinetic.sh 

# Create user
RUN useradd -m -d /home/ros -p ros ros && \
    adduser ros sudo && \
    chsh -s /bin/bash ros
ENV HOME /home/ros
WORKDIR /home/ros
USER ros
RUN mkdir /home/ros/src && \
    chown -R ros:ros /home/ros && \
    rosdep update

# Apply ros envs
RUN echo "source /opt/ros/kinetic/setup.bash" >> /home/ros/.bashrc && \
    echo "source /home/ros/.bashrc" >> /home/ros/.bash_profile

# ========================================================
FROM base as main

USER root
WORKDIR /home/ros/src

# Cloning ros packages
RUN echo "Cloning repositories..."
RUN git clone --recursive https://github.com/knowrob/knowrob.git -b kinetic && \
    git clone --recursive https://github.com/knowrob/genowl.git && \
    git clone --recursive https://github.com/knowrob/rosowl.git && \
    git clone --recursive https://github.com/knowrob/rosprolog-java.git && \
    git clone --recursive https://github.com/code-iai/ros-mongodb_log -b onlyPythonSupport


WORKDIR /home/ros

# Build the catkin workspace
# SHELL [ "/bin/bash", "-c" ]
# RUN . /opt/ros/kinetic/setup.bash
# RUN catkin_make

