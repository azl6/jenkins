FROM ubuntu

USER root

# Criando pasta necessária (ainda não sei o porquê)
RUN mkdir -p /var/run/sshd 

# Instalando o libs básicas e o ssh server
RUN apt update && \
    apt install -y lsb-core && \
    apt install -y openssh-server

# Criando usuário usuarioUbuntuServer com a senha 123
RUN useradd usuarioUbuntuServer && \
    echo "usuarioUbuntuServer:123" | chpasswd

# Criando diretórios necessários do usuário usuarioUbuntuServer
RUN mkdir -p /home/usuarioUbuntuServer/

# Informando que o usuário usuarioUbuntuServer é o dono das pastas /home/usuarioUbuntuServer/** 
RUN chown -R usuarioUbuntuServer:usuarioUbuntuServer /home/usuarioUbuntuServer

# Copiando a chave pública para o contêiner
#COPY remote-ki.pub /home/usuarioUbuntuServer/.ssh/authorized_keys/remote-ki.pub

RUN apt-get update && \
    apt-get install && \
    ca-certificates && \
    curl && \
    gnupg && \
    lsb-release

RUN mkdir -p /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

RUN echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

RUN apt-get update && \
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Iniciando o ssh-server
CMD [ "/usr/sbin/sshd", "-D" ]
