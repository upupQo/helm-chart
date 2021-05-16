FROM openjdk:8
RUN wget "https://archive.apache.org/dist/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.6.tgz" \
    && tar -xzvf spark-2.3.1-bin-hadoop2.6.tgz \
    # https://stackoverflow.com/questions/36388465/how-to-set-bash-aliases-for-docker-containers-in-dockerfile
    && echo "alias ll='ls -al' " >> ~/.bashrc
# 起一个前台进程bash，这样docker run的时候可以不必传了
CMD /bin/bash