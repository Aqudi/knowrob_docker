FROM osrf/ros:kinetic-desktop-full as base

ARG APT_CACHER_NG_HOST
ARG APT_CACHER_NG_PORT

# Config apt-cacher-ng proxy
RUN echo "Acquire::http { Proxy \"http://$APT_CACHER_NG_HOST:$APT_CACHER_NG_PORT\"; };" >> /etc/apt/apt.conf.d/01proxy

# Install apt packages
RUN apt-get -y update && \ 
    apt-get -y install zip tmux curl git swi-prolog swi-prolog-java libjson-java libjson-glib-dev \
    ros-kinetic-rosjava ros-kinetic-genjava ros-kinetic-rosjava-messages \
    ros-kinetic-rosauth mencoder lame libavcodec-extra \
    ros-kinetic-rosbridge-suite ros-kinetic-rosapi ros-kinetic-rosbridge-library ros-kinetic-rosbridge-server \
    python-rrdtool python-pymongo \
    emacs mongodb-clients openjdk-8-jdk texlive-latex-base imagemagick --fix-missing && \ 
    apt-get -y autoremove  && \ 
    apt-get -y clean && \ 
    rm -rf /var/lib/apt/lists/* && \ 
    rm -rf /tmp/*

# Install pip2.7
RUN curl -L https://bootstrap.pypa.io/pip/2.7/get-pip.py | python -
RUN pip install rdflib

RUN cp /opt/ros/kinetic/setup.sh /etc/profile.d/ros_kinetic.sh 

# Create user
RUN useradd -m -d /home/ros -p ros ros && \
    adduser ros sudo && \
    chsh -s /bin/bash ros
ENV HOME /home/ros
WORKDIR /home/ros

# Switch user
USER ros
RUN mkdir /home/ros/src && \
    chown -R ros:ros /home/ros && \
    rosdep update

# Apply ros envs
ENV PATH=/home/ros/devel/bin:/opt/ros/kinetic/bin:.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
ENV ROS_PACKAGE_PATH=/home/ros/src:/opt/ros/kinetic/share:/opt/ros/kinetic/stacks
ENV CMAKE_PREFIX_PATH=/home/ros/kinetic/catkin_ws/devel:/opt/ros/kinetic
ENV PKG_CONFIG_PATH=/home/ros/devel/lib/pkgconfig:/opt/ros/kinetic/lib/pkgconfig
ENV ROS_MASTER_URI=http://localhost:11311
ENV ROS_MAVEN_DEPLOYMENT_REPOSITORY=/home/ros/devel/share/maven
ENV ROS_MAVEN_PATH=/home/ros/devel/share/maven:/opt/ros/kinetic/share/maven
ENV ROS_WORKSPACE=/home/ros
ENV ROS_IP=127.0.0.1
ENV SWI_HOME_DIR=/usr/lib/swi-prolog
ENV PYTHONPATH=/home/ros/devel/lib/python2.7/dist-packages:/opt/ros/kinetic/lib/python2.7/dist-packages
ENV LD_LIBRARY_PATH=/home/ros/devel/lib:/opt/ros/kinetic/lib:/usr/lib/jvm/default-java/jre/lib/amd64:/usr/lib/jvm/default-java/jre/lib/amd64/server:/opt/ros/kinetic/lib/python2.7/dist-packages

RUN echo "source /opt/ros/kinetic/setup.bash" >> /home/ros/.bashrc && \
    echo "source /home/ros/.bashrc" >> /home/ros/.bash_profile

# ========================================================
FROM base as main

USER root
WORKDIR /home/ros/src

# Cloning ros packages
RUN echo "Cloning repositories..."
RUN git clone --recursive https://github.com/Aqudi/knowrob.git -b kinetic && \
    git clone --recursive https://github.com/knowrob/genowl.git && \
    git clone --recursive https://github.com/knowrob/rosowl.git && \
    git clone --recursive https://github.com/knowrob/rosprolog-java.git && \
    git clone --recursive https://github.com/code-iai/ros-mongodb_log -b onlyPythonSupport && \
    git clone --recursive https://github.com/code-iai/iai_common_msgs.git && \
    git clone --recursive https://github.com/knowrob/ease_ontology.git && \
    git clone --recursive https://github.com/ease-crc/dul.git

WORKDIR /home/ros/src/genowl
RUN python setup.py install

# Install prolog packages
WORKDIR /home/temp
RUN curl -LO https://github.com/mndrix/list_util/archive/v0.13.0.zip && \
    unzip v0.13.0.zip && \
    cd list_util-0.13.0 && \
    swipl -g "pack_install(., [interactive(false)])." -t halt

WORKDIR /home/ros
RUN  /bin/bash -c /opt/ros/kinetic/bin/catkin_make
RUN echo "source /home/ros/devel/setup.bash" >> /home/ros/.bashrc
