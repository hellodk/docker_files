FROM ubuntu:18.04

ARG SPARK_VER=2.4.7

WORKDIR /root

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
    apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

# Install a base time build environment
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
	apt-utils \
	build-essential \
	curl \
	git \
	libpng-dev \
	libfreetype6-dev \
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

# Install python3 infrastructure
COPY requirements.txt .
RUN pip3 --no-cache-dir install -r requirements.txt && rm -f requirements.txt

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

RUN jupyter notebook --generate-config
RUN sed -i -E 's,^#\s*c.NotebookApp.terminado_settings.*,c.NotebookApp.terminado_settings = {"shell_command" : ["\/bin\/bash"]},g' ~/.jupyter/jupyter_notebook_config.py

# Setup password authentication so we don't have to remember tokens (password: "jupyter")
RUN echo "{ \"NotebookApp\": { \"password\": \"sha1:ad16e87de314:a02efac10ccd7ead24e845f438b2b87fe8bc2d0f\" } }" >> ~/.jupyter/jupyter_notebook_config.json
RUN mkdir -p ~/.ipython/profile_default
RUN echo "c.TerminalInteractiveShell.editing_mode = 'vi'" >> ~/.ipython/profile_default/ipython_config.py

# Required spark configuration for local user access
ENV SPARK_HOME=/usr/local/spark
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=python3
ENV PYTHONPATH=/usr/local/spark/python:/usr/local/spark/python/lib/py4j-0.10.7-src.zip
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/spark/bin

EXPOSE 4040 4041 8080 8888 

ARG TF_SERVING_VERSION=0.0.0
ARG TF_SERVING_VERSION=0.0.0
ARG NB_USER=jovyan
USER root

ENV DEBIAN_FRONTEND noninteractive

ENV NB_USER $NB_USER

ENV NB_UID 1000
ENV HOME /home/$NB_USER
ENV NB_PREFIX /
ENV PATH $HOME/.local/bin:$PATH

COPY aws-java-sdk-1.7.4.jar $SPARK_HOME/jars/
COPY hadoop-aws-2.7.3.jar $SPARK_HOME/jars/
COPY jets3t-0.9.4.jar $SPARK_HOME/jars/
COPY java-xmlbuilder-1.3.jar $SPARK_HOME/jars/
# Use bash instead of sh
SHELL ["/bin/bash", "-c"]

# Create NB_USER user with UID=1000 and in the 'users' group
# but allow for non-initial launches of the notebook to have
# $HOME provided by the contents of a PV
RUN useradd -M -s /bin/bash -N -u $NB_UID $NB_USER
RUN chown -R ${NB_USER}:users /usr/local/bin
RUN mkdir -p $HOME
RUN chown -R ${NB_USER}:users ${HOME}

EXPOSE 8888

USER jovyan

# ENTRYPOINT ["/usr/local/bin/jupyter"]
# ENTRYPOINT ["tini", "--"]

ENV NB_PREFIX /
# CMD ["notebook", "--no-browser", "--allow-root", "--ip=0.0.0.0", "--port=8888"]
CMD ["sh","-c", "jupyter notebook --notebook-dir=/home/${NB_USER} --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]
