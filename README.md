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

**IMPORTANTE**: Parâmetros passados do Jenkins para um script/comando devem **sempre** utilizar o **$**

![image](https://user-images.githubusercontent.com/80921933/206959478-5aa46cb3-e57c-433c-9bc3-f1fdc92a1c19.png)


# Instalando o plugin de SSH

**IMPORTANTE**: Na geração de chaves para essa etapa, devemos usar o comando ssh-keygen -t ecdsa -m PEM -f id_rsa3, caso contrário, teremos o erro de "Can't connect..." na hora de testar a conexão via SSH

Em **Dashboard > Manage Jenkins > Plugin Manager > Available Plugins**, podemos buscar pelo plugin SSH

![image](https://user-images.githubusercontent.com/80921933/206933597-a55646db-55c1-43d2-a410-293a54382edf.png)

Depois, vamos para **Dashboard > Manage Jenkins > Credentials > System > Global credentials (unrestricted)**

![image](https://user-images.githubusercontent.com/80921933/206935588-f83890b4-581d-4742-b1d9-9ac28e86f4cf.png)

Ao clicar em **Add credentials**, devemos preencher os campos **Username** e **Private key**

- O campo **Username** se refere ao usuário criado no servidor remoto
- O campo **Private key** se refere ao retorno do comando **cat \<PRIVATE_KEY>**

Depois, clicamos em 

Vamos para **Dashboard > Manage Jenkins > Configure System**, buscamos por **SSH remote hosts**, e clicamos em **Add**

![image](https://user-images.githubusercontent.com/80921933/206935420-5fe2a575-b7c5-437f-8345-920ea47aaa39.png)

Devemos preencher os seguintes campos:

- Hostname: Se refere ao DNS do servidor ao qual desejamos nos conectar
- Port: 22
- Credential: A credencial criada no passo anterior

![image](https://user-images.githubusercontent.com/80921933/206936895-745400ff-64cc-462c-b262-dba3b183c90c.png)

Para executar scripts no host remoto conectado via ssh, selecionamos a seguinte opção

![image](https://user-images.githubusercontent.com/80921933/206937220-39589b91-6fb6-4653-ba4f-5919e10d86e8.png)

Basta selecionarmos o host criado e colocar o script normalmente na caixa de texto. Tudo será rodado no host remoto, e quaisquer arquivos também serão criados lá.

![image](https://user-images.githubusercontent.com/80921933/206937307-39de0d88-b31c-40f7-912a-92e275d73305.png)

# Fazendo backup de um banco MySQL pro S3 pelo Jenkins

- Criar 3 contêineres: Jenkins, Server Ubuntu e MySQL contêiner
- Criar uma tabela de exemplo pra fazer o dump

**Comando dump:**

 A flag -h representa o host. Esse comando foi executado do Server Ubuntu.
 
![image](https://user-images.githubusercontent.com/80921933/206938544-f1be1674-2888-4374-b7f7-2453d83f69ec.png)

- Fazer o upload do dump para o S3 manualmente com o seguinte comando

![image](https://user-images.githubusercontent.com/80921933/206956336-cdb5d33f-c042-4963-b946-6e3fa28d6038.png)

- Automatizar o backup com shell script

O resultado final deve ficar assim:

![image](https://user-images.githubusercontent.com/80921933/206960397-31888462-6f10-4aae-aae9-a4255964b9c5.png)

Resultado: **DEU CERTO!**

![image](https://user-images.githubusercontent.com/80921933/207519462-a83b7f37-75fd-4aec-b186-1f37fbefc3e5.png)

Basta seguir as informações fornecidas no README.

# Gerenciando secrets pelo Jenkins

Em **Manage Credentials**

![image](https://user-images.githubusercontent.com/80921933/206957608-44fbc93c-a04e-4097-9f6f-dcfe85446190.png)

Clicamos em **System**

![image](https://user-images.githubusercontent.com/80921933/206957720-acb6e58c-ba84-44b0-ac16-588122e975ba.png)

Depois, em **Global Credentials**

![image](https://user-images.githubusercontent.com/80921933/206957762-4bc235dc-d225-4e1a-a181-6fc6ae7bbb1d.png)

Depois de clicar em **Add Credentials**, selecionamos **Secret text** do menu **Kind**

Depois, basta preencher as informações

- **ID** se refere a key da secret
- **Secret** se refere ao value da secret

![image](https://user-images.githubusercontent.com/80921933/206958037-2b881d68-e5c6-476d-ba03-2ed9dd184278.png)

Para utilizar a secret criada, basta que, na sessão **Build Environment**, selecionemos as seguintes opções

![image](https://user-images.githubusercontent.com/80921933/206958727-9e247c39-cd1e-472b-b727-7df5bcba56db.png)

Depois, selecionamos a secret criada e a associamos com um value que será usado no script

![image](https://user-images.githubusercontent.com/80921933/206958874-78bf8477-16d6-4094-b5e8-de7ca09852c9.png)

