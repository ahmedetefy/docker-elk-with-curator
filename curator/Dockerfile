FROM ubuntu:16.04

RUN apt-get update
RUN apt-get install -y software-properties-common vim
RUN add-apt-repository ppa:jonathonf/python-3.6
RUN apt-get update

RUN apt-get install -y build-essential python3.6 python3.6-dev python3-pip python3.6-venv
RUN apt-get install -y git curl

# update pip
RUN python3.6 -m pip install pip --upgrade
RUN python3.6 -m pip install wheel

RUN mkdir /etc/curator
ADD action_file.yml /etc/curator
ADD config.yml /etc/curator

# Install curator (https://www.elastic.co/guide/en/elasticsearch/client/curator/4.2/installation.html)
RUN pip install elasticsearch-curator>=5.5.4

# download go-cron
RUN curl -L -o /usr/local/bin/go-cron-linux.gz https://github.com/odise/go-cron/releases/download/v0.0.7/go-cron-linux.gz
RUN gunzip /usr/local/bin/go-cron-linux.gz
RUN chmod u+x /usr/local/bin/go-cron-linux

ENV LC_ALL C.UTF-8
ENV LANG C.UTF-8
ENV PATH=/usr/local/bin:$PATH
ENV SCHEDULE "*/5 * * * * *"
ENV COMMAND "curator --config /etc/curator/config.yml  /etc/curator/action_file.yml"
EXPOSE 8080
CMD go-cron-linux -s "$SCHEDULE" -p 8080 -- /bin/bash -c "$COMMAND"