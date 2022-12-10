# Jenkins

## Iniciando um servidor do Jenkins

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

Será requisitado um password, que poderá ser encontrado nos logs da aplicação:

![image](https://user-images.githubusercontent.com/80921933/206808338-d3a9c377-b59f-4080-9ec5-be1a08584843.png)

Na tela posterior, instalamos os plugins sugeridos.

Depois, criamos um usuário, até chegarmos na página principal.

## Criando um job no Jenkins

Na página principal, podemos clicar em **Create a job**.

Selecionamos a primeira opção para um job simples

![image](https://user-images.githubusercontent.com/80921933/206811942-6e05ba04-e702-49b2-9c97-e386c02a02c6.png)

Na aba de **Build steps**, podemos definir um script simples, que será rodado dentro do contêiner do Jenkins

![image](https://user-images.githubusercontent.com/80921933/206812237-74313eda-6dfa-4fa6-8ad2-84513cbcc0fd.png)

Depois, na tela do job criado, podemos clicar em **Build Now** e o job será executado. As execuções ficarão armazenadas na parte de baixo da tela

![image](https://user-images.githubusercontent.com/80921933/206812389-c184120b-1a90-4f6a-9970-8f4e8e4d32da.png)

Podemos clicar em cada execução para visualizar o output do comando

![image](https://user-images.githubusercontent.com/80921933/206812428-2362d364-2751-4156-8cd1-b69836145df8.png)

# Executando scripts pelo Jenkins

Podemos criar o script localmente e enviar para dentro do contêiner com o comando **docker cp \<FILE> \<CONTAINER>:\<CHOSEN_PATH>**

![image](https://user-images.githubusercontent.com/80921933/206814901-6a8abbd9-e34f-470e-a25b-be00d9545fbe.png)

Depois, alteramos os **Build Steps** para rodar o script que se encontra no diretório **/**

![image](https://user-images.githubusercontent.com/80921933/206815051-7c057907-ddf0-4fb3-82db-ba5648914ccf.png)

O output será o esperado:

![image](https://user-images.githubusercontent.com/80921933/206815247-0a248095-e90c-46f4-b7b3-09cc482c2b6a.png)

# Inserindo parâmetros nos comandos

Na aba **Dashboard > \<PROJECT> > General**, podemos dizer que o projeto é parametrizado, e informar o nome da variável e seu respectivo valor padrão

![image](https://user-images.githubusercontent.com/80921933/206817946-0de526d5-a9f3-491e-91d1-fa5e3e98bbb8.png)

Alterando novamente o código do **Build Steps**

![image](https://user-images.githubusercontent.com/80921933/206818034-d3f2ee19-7f40-4ea6-9a8f-1254db1ffe67.png)

Agora, ao rodar o projeto novamente, somos obrigados a fornecer os parâmetros para rodar o projeto

![image](https://user-images.githubusercontent.com/80921933/206818062-d83f6a97-9239-4c56-9d73-fd7e54c9d656.png)

Podemos criar parâmetros do tipo **list**, **boolean**, etc...





