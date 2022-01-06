ARG JDK_VERSION="8"
FROM openjdk:${JDK_VERSION}-slim-buster

ARG POLYNOTE_VERSION="0.4.4"
ARG SCALA_VERSION="2.11"

WORKDIR /opt
ENV UID 1000
ENV NB_USER polly
ENV POLYNOTE_SCALA_VERSION ${SCALA_VERSION}

RUN apt-get update -q -y \
    && apt-get install -y curl python3 python3-dev python3-pip build-essential \
    && curl -sL https://github.com/polynote/polynote/releases/download/$POLYNOTE_VERSION/polynote-dist.tar.gz \
        | tar xz -C /opt/ \
    && pip3 install -r ./polynote/requirements.txt \
    && adduser --disabled-password --gecos "Default user" --uid ${UID} ${NB_USER} \
    && chown -R ${NB_USER}:${NB_USER} /opt/polynote

USER ${NB_USER}

EXPOSE 8192

ENTRYPOINT ["./polynote/polynote.py"]