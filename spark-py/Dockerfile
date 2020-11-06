FROM ubuntu:18.04
#FROM openjdk:8-jdk-slim

ARG SPARK_VER=2.4.7

WORKDIR /root

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
    apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini

# Install a base time build environment
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
	apt-utils \
	build-essential \
	curl \
	git \
	libpng-dev \
	libfreetype6-dev \
        bash \
	pkg-config \
	python3 \
	python3-dev \
	python3-distutils \
	software-properties-common \
	sudo \
	tar \
	unzip \
	wget \
    python3-pip \
	vim && \
    apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

# Get the latest pip3
# RUN wget -q https://bootstrap.pypa.io/get-pip.py && python3 get-pip.py && rm -f get-pip.py

RUN pip3 --no-cache-dir install pyspark findspark

# Install Spark dependencies
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
	openjdk-8-jre \
	scala && \
	apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

# Install Spark
RUN wget -q -O spark.tgz http://mirror.olnevhost.net/pub/apache/spark/spark-${SPARK_VER}/spark-${SPARK_VER}-bin-hadoop2.7.tgz && \
	mkdir -p /usr/local && tar -xf spark.tgz -C /usr/local && \
	mv /usr/local/spark*${SPARK_VER}* /usr/local/spark && \
    pip3 install --upgrade pyspark

# Clean up
RUN rm -rf /root/* && chmod 755 /root

# Required spark configuration for local user access
ENV SPARK_HOME=/usr/local/spark
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=python3
ENV PYTHONPATH=/usr/local/spark/python:/usr/local/spark/python/lib/py4j-0.10.7-src.zip
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/spark/bin

EXPOSE 4040 4041 8080 8888 

USER root

COPY aws-java-sdk-1.7.4.jar $SPARK_HOME/jars/
COPY hadoop-aws-2.7.3.jar $SPARK_HOME/jars/
COPY jets3t-0.9.4.jar $SPARK_HOME/jars/
COPY java-xmlbuilder-1.3.jar $SPARK_HOME/jars/

RUN cp $SPARK_HOME/kubernetes/dockerfiles/spark/entrypoint.sh /opt/

# Use bash instead of sh
SHELL ["/bin/bash", "-c"]

ENTRYPOINT [ "/opt/entrypoint.sh" ]

# Specify the User that the actual main process will run as
ARG spark_uid=185
USER ${spark_uid}
