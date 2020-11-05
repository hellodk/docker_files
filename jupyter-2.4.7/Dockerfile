FROM hellodk/spark:v1

ARG NB_USER=jovyan
ENV NB_PREFIX /

WORKDIR /root

# Install a base time build environment
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
  curl \
  git \
  wget \
  python3-pip \
  vim && \
    apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

# Get the latest pip3
#RUN wget -q https://bootstrap.pypa.io/get-pip.py && python3 get-pip.py && rm -f get-pip.py
RUN pip3 install --no-cache-dir jupyter matplotlib jupyterlab numpy pandas pyspark findspark awscli

RUN jupyter notebook --generate-config
RUN sed -i -E 's,^#\s*c.NotebookApp.terminado_settings.*,c.NotebookApp.terminado_settings = {"shell_command" : ["\/bin\/bash"]},g' ~/.jupyter/jupyter_notebook_config.py

# Setup password authentication so we don't have to remember tokens (password: "jupyter")
RUN echo "{ \"NotebookApp\": { \"password\": \"sha1:ad16e87de314:a02efac10ccd7ead24e845f438b2b87fe8bc2d0f\" } }" >> ~/.jupyter/jupyter_notebook_config.json
RUN mkdir -p ~/.ipython/profile_default
RUN echo "c.TerminalInteractiveShell.editing_mode = 'vi'" >> ~/.ipython/profile_default/ipython_config.py

EXPOSE 4040 4041 8080 8888 

COPY python /opt/spark/python
COPY conf /opt/spark/python

ARG TF_SERVING_VERSION=0.0.0
ARG TF_SERVING_VERSION=0.0.0
USER root

ENV DEBIAN_FRONTEND noninteractive
ENV NB_USER $NB_USER
ENV NB_UID 1000
ENV HOME /home/$NB_USER
ENV NB_PREFIX /
ENV PATH $HOME/.local/bin:$PATH

# Required spark configuration for local user access
ENV SPARK_HOME=/opt/spark
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=python3
ENV PYTHONPATH=/opt/spark/python:/opt/spark/python/lib/py4j-0.10.7-src.zip
#ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
#ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/spark/bin

COPY aws-java-sdk-1.11.30.jar $SPARK_HOME/jars/
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
CMD ["sh","-c", "jupyter notebook --notebook-dir=/home/${NB_USER} --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]
