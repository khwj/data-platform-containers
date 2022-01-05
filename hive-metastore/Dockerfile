FROM openjdk:8-jdk-slim-buster

ARG HADOOP_VERSION=3.3.1
ARG HIVE_VERSION=3.0.0
ARG POSTGRES_CONNECTOR_VERISON=42.3.1

RUN apt-get update && \
    apt-get install -y curl --no-install-recommends && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV HADOOP_HOME="/opt/hadoop"
ENV HIVE_METASTORE_HOME="/opt/hive-metastore"
ENV HADOOP_CLASSPATH="${HADOOP_HOME}/share/hadoop/tools/lib/*:${HIVE_METASTORE_HOME}/lib"
ENV PATH="${HADOOP_HOME}/bin:${HIVE_METASTORE_HOME}/lib/:${HADOOP_HOME}/share/hadoop/tools/lib/:/opt/postgresql-jdbc.jar:${PATH}"

# Download and extract the Hadoop package
RUN curl -s https://archive.apache.org/dist/hadoop/core/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz \
        | tar xz -C /opt/  \
        && ln -s ${HADOOP_HOME}-$HADOOP_VERSION ${HADOOP_HOME} \
        && rm -r ${HADOOP_HOME}/share/doc

# Add S3a jars to the Hadoop classpath
RUN cp ${HADOOP_HOME}/share/hadoop/tools/lib/hadoop-aws* ${HADOOP_HOME}/share/hadoop/common/lib/ && \
    cp ${HADOOP_HOME}/share/hadoop/tools/lib/aws-java-sdk* ${HADOOP_HOME}/share/hadoop/common/lib/

# Download and install the standalone metastore
RUN curl -s http://apache.uvigo.es/hive/hive-standalone-metastore-${HIVE_VERSION}/hive-standalone-metastore-${HIVE_VERSION}-bin.tar.gz \
        | tar xz -C /opt/ \
        && ln -s /opt/apache-hive-metastore-${HIVE_VERSION}-bin ${HIVE_METASTORE_HOME}

# Add jars to the Hive Metastore classpath
RUN cp ${HADOOP_HOME}/share/hadoop/tools/lib/hadoop-aws* ${HIVE_METASTORE_HOME}/lib/ && \
    cp ${HADOOP_HOME}/share/hadoop/tools/lib/aws-java-sdk* ${HIVE_METASTORE_HOME}/lib/

# Postgres connector for Hive Metastore
RUN curl -s https://jdbc.postgresql.org/download/postgresql-${POSTGRES_CONNECTOR_VERISON}.jar -o postgresql-jdbc.jar  \
  && cp postgresql-jdbc.jar ${HADOOP_HOME}/share/hadoop/common/lib/ \
  && mv postgresql-jdbc.jar ${HIVE_METASTORE_HOME}/lib/ \
  && chmod -R 775 ${HADOOP_HOME}/share/hadoop/common/lib/* \
  && chmod -R 775 ${HIVE_METASTORE_HOME}/lib/*

EXPOSE 9083

WORKDIR ${HIVE_METASTORE_HOME}

CMD ["exec", "/opt/hive-metastore/bin/start-metastore"]