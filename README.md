# Jenkins

Para rodar o Jenkins, usamos a imagem **jenkins/jenkins** do DockerHub.

Basta criar um **docker-compose** simples com um bind-mount (caso o Jenkins não consiga escrever na pasta local do bind-mount, rodar **chown 1000:1000 \<pasta>**):

```yaml
version: '3.8'

services:
  jenkins:
    container_name: 'jenkins'
    image: 'jenkins/jenkins'
    ports:
      - 8080:8080
    volumes:
      - $PWD/jenkinsbindmount:/var/jenkins_home
```
