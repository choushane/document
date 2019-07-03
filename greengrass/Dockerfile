FROM ubuntu:16.04
ENV QNAP_QPKG=Greengrass
ARG greengrass_release
ENV GREENGRASS_RELEASE=${greengrass_release}
RUN apt-get -y update && useradd ggc_user && groupadd ggc_group && apt-get -y install supervisor sqlite3 wget curl libc6-dev build-essential python2.7 libpython2.7 ocl-icd-opencl-dev lsb-release sudo cpio vim && wget https://bootstrap.pypa.io/get-pip.py && python get-pip.py && rm get-pip.py && apt-get -y install --reinstall procps && curl -sL https://deb.nodesource.com/setup_10.x | bash - && apt-get -y install -y nodejs && rm -rf /var/lib/apt/lists/* && pip install opencv-python
COPY supervisord.conf /etc/supervisor/supervisord.conf 
COPY sysctl.conf /etc/sysctl.conf

RUN mkdir -p /dest/Source/ /dest/Result
RUN mkdir /local_src/
RUN chmod 0775 /local_src/
RUN chmod -R 0775 /dest

RUN echo '# AWS Greengrass' >> /etc/sysctl.conf
RUN echo 'fs.protected_hardlinks = 1' >> /etc/sysctl.conf
RUN echo 'fs.protected_symlinks = 1' >> /etc/sysctl.conf

# Copy/Untar Greengrass RC inside Docker Image
COPY $greengrass_release /
COPY l_openvino_toolkit_p_2018.5.455.tgz /
RUN tar xzf /$GREENGRASS_RELEASE -C /
RUN rm /$GREENGRASS_RELEASE
RUN apt-get -y remove curl
#ENTRYPOINT ["supervisord","-c","/etc/supervisor/supervisord.conf"]
