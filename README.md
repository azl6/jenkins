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

To-do list para essa task:

- Criar 3 contêineres: Jenkins, Server Ubuntu e MySQL contêiner
- Criar uma tabela de exemplo pra fazer o dump
- Realizar o dump com o **mysqldump** manualmente e o upload para o S3 manualmente
- Automatizar a tarefa com shell script
- Criar um job no Jenkins, que permita que o contêiner do Jenkins execute um script no servidor Ubuntu via plugin SSH, onde tal script realizará o dump do banco escolhido, e realizará o upload de tal para o S3

**IMPORTANTE**: Para a comunicação com o S3, será necessário a utilização do **Access Key ID** e **Secret Access Key** da AWS. No script, inserir ENVS que referenciam tais variáveis. O seguinte link representa bem a ideia: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html

**Comando dump:**

 A flag -h representa o host. Esse comando foi executado do Server Ubuntu.
 
![image](https://user-images.githubusercontent.com/80921933/206938544-f1be1674-2888-4374-b7f7-2453d83f69ec.png)

Fazer o upload do dump para o S3 manualmente com o seguinte comando

![image](https://user-images.githubusercontent.com/80921933/206956336-cdb5d33f-c042-4963-b946-6e3fa28d6038.png)

O resultado final deve ficar assim:

![image](https://user-images.githubusercontent.com/80921933/206960397-31888462-6f10-4aae-aae9-a4255964b9c5.png)

Resultado: **DEU CERTO!**

![image](https://user-images.githubusercontent.com/80921933/207519462-a83b7f37-75fd-4aec-b186-1f37fbefc3e5.png)

O README contém todas as informações para a execução deste projeto.

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

# Permitindo que usuários criem uma conta

Em **Dashboard > Manage Jenkins > Configure Global Security**, marcamos a opção **Allow users to sign up**

![image](https://user-images.githubusercontent.com/80921933/207936441-ad7f94b1-8d04-474e-8bac-d7afd6e6c024.png)

Agora, ao acessar a página de login, teremos acesso ao botão de cadastrar uma nova conta

![image](https://user-images.githubusercontent.com/80921933/207936534-487a0699-d43b-42d5-ab37-2100c4c7b687.png)

Todas as contas criadas terão permissões máximas, e normalmente não é isso que queremos.

# Instalando o plugin Role-based Authorization Strategy e o utilizando roles

Instalamos o plugin **Role-based Authorization Strategy** e reiniciamos o Jenkins

Depois, em **Configure Global Security**, selecionamos a opção **Role-based strategy**

![image](https://user-images.githubusercontent.com/80921933/207938085-ccbde430-3fe8-4fc2-be76-b30f93d523d7.png)

Uma nova opção aparecerá no menu: **Manage and assign roles**

Em **Manage users**

![image](https://user-images.githubusercontent.com/80921933/207939290-df2524fa-39de-41bf-9ddb-d5690069f063.png)

Poderemos ver todos os usuários na base de dados do Jenkins

![image](https://user-images.githubusercontent.com/80921933/207939372-7e237fbc-e1c1-4b55-959c-c80f0264200a.png)

Criei um novo usuário chamado **alex**

![image](https://user-images.githubusercontent.com/80921933/207939527-552f164e-0441-415e-a3a7-ca7f73821507.png)

Ao tentar logar com o usuário **alex**, recebemos uma mensagem de erro. Isso acontece porque ainda não configuramos roles para ele.

![image](https://user-images.githubusercontent.com/80921933/207939829-8d2adf41-d777-4c14-96c3-fa234ee59eff.png)

# Criando uma role e assignando-a a um usuário

Clicar no seguinte botão

![image](https://user-images.githubusercontent.com/80921933/207940932-cba96a66-1fcc-43c8-bcea-225d294f08bb.png)

Clicamos em **Manage roles**

![image](https://user-images.githubusercontent.com/80921933/207941720-5bcd74a1-1bf9-4e5f-80e7-14cacaea154e.png)

Nomeamos nossa role e clicamos em **Add**

![image](https://user-images.githubusercontent.com/80921933/207941186-19701c3e-15cb-42e3-93d0-099176a1868d.png)

Agora, basta atribuir as permissões desejadas para a role criada

![image](https://user-images.githubusercontent.com/80921933/207941301-6ed67bca-0753-43ec-b119-b9e382bb9eb7.png)

Depois, em **Assign Roles**

![image](https://user-images.githubusercontent.com/80921933/207941695-39b1a6c2-6475-41d8-80bf-25d74628547f.png)

Inserimos o usuário criado (alex) na tabelinha com o botão **Add**

![image](https://user-images.githubusercontent.com/80921933/207942005-274d2246-77f3-4536-8337-1ed0655b0047.png)

Agora, basta selecionar o checkbox da role a qual você gostaria de associar o usuário, e salvar.

![image](https://user-images.githubusercontent.com/80921933/207945492-fa55bd64-f3ce-45e5-8c8c-cb11b6c7a209.png)

Associando a role **read-only** criada ao usuário **alex**, vemos que o login agora funciona adequadamente. Para outras roles, basta dar as permissões desejadas.

# Criando uma project role

Podemos informar que um usuário X terá acesso a todos os jobs que começam com **ansible-**, por exemplo. Essas são as Project Roles.

Para mais detalhes sobre elas, consultar aula 74.

# Jenkins-defined environment variables

Algumas env existem no Jenkins por padrão.

![image](https://user-images.githubusercontent.com/80921933/207947124-92e96c24-d2e1-430f-830d-449d82374e02.png)

Podemos consultar todas as envs disponíveis em: https://wiki.jenkins.io/display/JENKINS/Building+a+software+project

# User-defined environment variables

Em **Dashboard > Manage Jenkins > Configure System**, podemos selecionar a opção **Environment variables** e definir nossas próprias variáveis de ambiente personalizadas, e posteriormente referenciá-las em scripts.

![image](https://user-images.githubusercontent.com/80921933/207948217-c7266bbe-624b-45c1-aa19-650643398a29.png)

# Cron jobs com o Jenkins

Podemos agendar um job para rodar em horários determinados utilizando cron expressions.

Para tal,

1. Selecionamos um job

2. Clicamos em **Configure**

    ![image](https://user-images.githubusercontent.com/80921933/207949317-5ca9d2e3-4f3f-4dff-81c0-1cff16c65f7a.png)
  
3. Em **Build triggers**, definimos um cron expression e salvamos.

    ![image](https://user-images.githubusercontent.com/80921933/207949541-6133d2c4-8d3a-4d90-a1b8-7b0d6e67b562.png)

Pronto! Seu job está agendado.

# Trigger de jobs sem parâmetros a partir de fontes externas

Na tela do job escolhido, copiamos a url do botão **Build Now**

![Screenshot from 2022-12-15 17-15-48](https://user-images.githubusercontent.com/80921933/207958413-75d558e9-e927-4ebe-96a8-0aa7643b8a9c.png)

Agora, rodamos o comando (o -u alex:123 referencia um usuário criado com as permissões necessárias, e o localhost:8080 representa onde o Jenkins está hospedado. O resto é padrão. Adaptar conforme a necessidade.)

```
crumb=$(curl -u "alex:123" -s 'http://localhost:8080/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
```

Receberemos como resposta um **Jenkins-Crumb**

![image](https://user-images.githubusercontent.com/80921933/207960515-ccdf5451-b324-4918-b42f-61f89fcc0904.png)

Depois, basta executar o comando (adaptando para a sua realidade)

```
curl -u "alex:123" -H "$crumb" -X POST http://localhost:8080/job/remote-host-simple-task/build?delay=0sec
```

E o job será rodado.

**Importante**: Caso a prática retorne um 403, basta seguir o seguinte tutorial:

1. Install the Strict Crumb Issue Plugin as per normal process.
2. Manage Jenkins > Configure Global Security > CSRF Protection. Change it to Strict Crumb Issuer. Click Advanced. Uncheck the Check the session ID box. Save.

# Trigger de jobs com parâmetros a partir de fontes externas

Seguimos os mesmos passos anteriores, porém, na hora de executar o segundo curl, **substituimos o "build?" por "buildWithParameters?"**. Depois do **?**, passarmos as variáveis no seguinte formato:

\<CURL> \<URL>/buildWithParameters?VARIAVEL1=oi&VARIAVEL2=ola...

Em caso de dúvidas, consultar a aula 82.

# Integrando o Jenkins com o AWS SES

Depois de verificar um e-mail, vamos para a opção **SMTP settings**, e copiamos a variável **SMTP endpoint**

![image](https://user-images.githubusercontent.com/80921933/207968323-cb2544f0-fb07-4084-bbf2-23617c891081.png)

Em **Dashboard > Manage Jenkins > Configure System**, colamos o **SMTP endpoint** copiado na opção com o header **Email notification**

![image](https://user-images.githubusercontent.com/80921933/207968780-3dcb12df-4d8c-4f58-bfa1-52e95113707d.png)

De volta a AWS, clicamos em **Create SMTP credentials**

Escolhemos um nome para as credenciais, e baixamos o .csv fornecido pela AWS

![Screenshot from 2022-12-15 18-18-27](https://user-images.githubusercontent.com/80921933/207969251-23ca8a79-48dd-4bcb-87fc-002d5ca49e43.png)

De volta ao Jenkins, ao clicar em **Advanced**, preenchemos o User Name e Password gerados pela AWS

![image](https://user-images.githubusercontent.com/80921933/207969837-6a89b23d-3da7-4544-8d7e-df5e780dd2fc.png)

Para o campo **SMTP Port**, buscamos na AWS as portas disponibilizadas pelo servidor SMTP

![image](https://user-images.githubusercontent.com/80921933/207970139-15293d3d-d032-4db8-a510-b2d11e0d4741.png)

Para o campo **Reply-to address**, preenchemos nosso e-mail convencional

Ao clicar em **Test configuration**, devemos ver a seguinte mensagem

![image](https://user-images.githubusercontent.com/80921933/207970693-dbbff9a7-ff00-4be7-839b-d4d246339764.png)

# Integrando o Jenkins ao Gmail

Basta alterar algumas informações do tutorial anterior, de forma que fique da seguinte maneira

![image](https://user-images.githubusercontent.com/80921933/207974777-315004c0-f1fa-4a5b-ae7f-fe5a729e6e15.png)

O servidor SMTP do google é o smtp.google.com <br>
O **user name** e **password** referenciam as credenciais de sua própria conta <br>
A porta do servidor via SSL é 465 <br>

Caso tenha algum problema, consultar páginas acerca do **Gmail with less-secure applications**

# Enviando e-mail em caso de falha em jobs

Nas configurações do job, em **Post-build Actions**, podemos selecionar a opção **Email notification**, e receber e-mails sempre que a build quebrar.

![image](https://user-images.githubusercontent.com/80921933/207975904-f31a42a6-377c-4496-ae83-993810850dd7.png)

# Instalando os plugins necessários para integrar o Jenkins com o Maven e Git

Devemos instalar os plugins **Maven integration**, **Git plugin** e **Git client** (caso já não estejam instalados)

# Como clonar um projeto do Github a partir do Jenkins

Link para o repositório clonado nesse exemplo: https://github.com/jenkins-docs/simple-java-maven-app

Nas configurações de um job, em **Source code management**, inserimos o link para o repositório do Github

![image](https://user-images.githubusercontent.com/80921933/207978377-10cf0e74-0aa8-4206-b565-6a49306c452c.png)

Agora, se rodarmos o job, rodará normalmente.

O projeto clonado ficará armazenado no **workspace** com o nome do job criado. Nesse exemplo, o job se chamava **maven-job**, portanto, o projeto estará armazenado em **/var/jenkins_home/workspace/maven-job**

![image](https://user-images.githubusercontent.com/80921933/207978929-8dc0c5af-5252-42f5-9f1e-2a8b758848d8.png)














