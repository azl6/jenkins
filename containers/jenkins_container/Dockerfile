FROM jenkins/jenkins

USER root

RUN apt update && apt install -y nano

RUN curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall

RUN mkdir -p /home/jenkins/bin/ && chown -R 1000:1000 /home/jenkins 

RUN apt update && apt install -y maven
     
USER jenkins

