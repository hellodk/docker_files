ARG BASE_IMAGE=jupyter/pyspark-notebook
FROM $BASE_IMAGE
ARG TF_SERVING_VERSION=0.0.0
ARG TF_SERVING_VERSION=0.0.0
ARG NB_USER=jovyan

# TODO: User should be refactored instead of hard coded jovyan

USER root

ENV DEBIAN_FRONTEND noninteractive

ENV NB_USER $NB_USER

ENV NB_UID 1000
ENV HOME /home/$NB_USER
ENV NB_PREFIX /
ENV PATH $HOME/.local/bin:$PATH

RUN apt-get update -y
# RUN wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk/1.7.4/aws-java-sdk-1.7.4.jar -P $SPARK_HOME/jars/
# RUN wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/2.7.3/hadoop-aws-2.7.3.jar -P $SPARK_HOME/jars/

COPY aws-java-sdk-1.7.4.jar $SPARK_HOME/jars/
COPY hadoop-aws-2.7.3.jar $SPARK_HOME/jars/
COPY jets3t-0.9.4.jar $SPARK_HOME/jars/
COPY java-xmlbuilder-1.3.jar $SPARK_HOME/jars/

# Use bash instead of sh
SHELL ["/bin/bash", "-c"]

# Create NB_USER user with UID=1000 and in the 'users' group
# but allow for non-initial launches of the notebook to have
# $HOME provided by the contents of a PV
#RUN useradd -M -s /bin/bash -N -u $NB_UID $NB_USER
RUN chown -R ${NB_USER}:users /usr/local/bin
RUN mkdir -p $HOME
RUN chown -R ${NB_USER}:users ${HOME}

# Configure container startup
EXPOSE 8888
USER jovyan
ENTRYPOINT ["tini", "--"]
CMD ["sh","-c", "jupyter notebook --notebook-dir=/home/${NB_USER} --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]
