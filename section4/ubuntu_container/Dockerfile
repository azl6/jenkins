FROM ubuntu

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
RUN mkdir -p /home/usuarioUbuntuServer/.ssh/authorized_keys

# Informando que o usuário usuarioUbuntuServer é o dono das pastas /home/usuarioUbuntuServer/** 
RUN chown -R usuarioUbuntuServer:usuarioUbuntuServer /home/usuarioUbuntuServer

# Copiando a chave pública para o contêiner
COPY chavejenkins.pub /home/usuarioUbuntuServer/.ssh/authorized_keys/

# Iniciando o ssh-server
CMD [ "/usr/sbin/sshd", "-D" ]
