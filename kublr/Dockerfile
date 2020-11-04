FROM kublr/pyspark-notebook:spark-2.4.0-hadoop-2.6

ARG NB_USER=jovyan
ENV NB_PREFIX /

CMD ["sh","-c", "jupyter notebook --notebook-dir=/home/${NB_USER} --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='' --Notebook
App.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]