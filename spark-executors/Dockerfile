FROM hellodk/spark-py:kfw-sp-hdp-3-2-0-v2

USER root

RUN mkdir /opt/spark/logs && chown 185:185 /opt/spark/logs

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
  procps && \
  apt-get clean && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*

RUN pip3 install pyarrow pandas numpy boto3 tqdm s3fs keras==2.2.4 tensorflow==1.14.0 h5py==2.10.0

COPY entrypoint.sh /opt/entrypoint.sh
RUN chmod 755 /opt/entrypoint.sh
ARG spark_uid=185
USER ${spark_uid}

#CMD ["./opt/spark/sbin/start-mesos-shuffle-service.sh"]
