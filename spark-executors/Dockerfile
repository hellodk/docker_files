FROM hellodk/spark-py:kfw-sp-hdp-3-2-0-v2

USER root

RUN mkdir /opt/spark/logs


ARG spark_uid=185
USER ${spark_uid}

CMD ["sh /opt/spark/sbin/start-mesos-shuffle-service.sh"]
