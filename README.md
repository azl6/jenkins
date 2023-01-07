**Tutoriais iniciantes** <br>
[Iniciando um servidor do Jenkins](#iniciando-um-servidor-do-jenkins) <br>
[Criando um job no Jenkins](#criando-um-job-no-jenkins) <br>
[Executando scripts pelo Jenkins](#executando-scripts-pelo-jenkins) <br>

**Secrets, variáveis normais e de ambiente** <br>
[Inserindo parâmetros nos comandos](#inserindo-parâmetros-nos-comandos) <br>
[Gerenciando secrets pelo Jenkins](#gerenciando-secrets-pelo-jenkins) <br>
[Jenkins-defined environment variables](#jenkins-defined-environment-variables) <br>
[User-defined environment variables](#user-defined-environment-variables) <br>

**Cron jobs** <br>
[Cron jobs com o Jenkins](#cron-jobs-com-o-jenkins) <br>

**Trigger de jobs** <br>
[Utilizando ngrok e webhooks para dar trigger na pipeline monobranch](#utilizando-ngrok-e-webhooks-para-dar-trigger-na-pipeline-monobranch) <br>
[Utilizando ngrok e webhooks para dar trigger na pipeline multibranch](#utilizando-ngrok-e-webhooks-para-dar-trigger-na-pipeline-multibranch) <br>
[Trigger de jobs sem parâmetros a partir de fontes externas](#trigger-de-jobs-sem-parâmetros-a-partir-de-fontes-externas) <br>
[Trigger de jobs com parâmetros a partir de fontes externas](#trigger-de-jobs-com-parâmetros-a-partir-de-fontes-externas) <br>

**Integrando o Jenkins com outros serviços** <br>
[Integrando o Jenkins com o AWS SES](#integrando-o-jenkins-com-o-aws-ses) <br>
[Integrando o Jenkins ao Gmail](#integrando-o-jenkins-ao-gmail) <br>
[Enviando e-mail em caso de falha em jobs](#enviando-e-mail-em-caso-de-falha-em-jobs) <br>

**DSL** <br>
[Estrutura de um script DSL](#estrutura-de-um-script-dsl) <br>
[Exemplo de script DSL](#exemplo-de-script-dsl) <br>

**Pipelines** <br>
[Pipeline simples](#pipeline-simples) <br>
[Comandos na pipeline](#comandos-na-pipeline) <br>
[Criando pipeline](#criando-pipeline) <br>

**Docker e Jenkins** <br>
[Rodando o Docker no Jenkins](#rodando-o-docker-no-jenkins) <br>

**Conta e roles** <br>
[Permitindo que usuários criem uma conta](#permitindo-que-usuários-criem-uma-conta) <br>
[Instalando o plugin Role-based Authorization Strategy e o utilizando roles](#instalando-o-plugin-role-based-authorization-strategy-e-o-utilizando-roles) <br>
[Criando uma role e assignando-a a um usuário](#criando-uma-role-e-assignando-a-a-um-usuário) <br>
[Criando uma project role](#criando-uma-project-role) <br>

**Plugins** <br>
[Instalando o plugin de SSH](#instalando-o-plugin-de-ssh) <br>
[Instalando os plugins necessários para integrar o Jenkins com o Maven e Git](#instalando-os-plugins-necessários-para-integrar-o-jenkins-com-o-maven-e-git) <br>
[Instalando o plugin de DSL do Jenkins](#instalando-o-plugin-de-dsl-do-jenkins) <br>
[Instalando plugin do Jenkins Pipeline](#instalando-plugin-do-jenkins-pipeline) <br>

**Utilitários** <br>
[Fazendo backup de um banco MySQL pro S3 pelo Jenkins](#fazendo-backup-de-um-banco-mysql-pro-s3-pelo-jenkins) <br>
[Informações sobre a utilização de um contêiner do Maven para gerar jar](#informações-sobre-a-utilização-de-um-contêiner-do-maven-para-gerar-jar) <br>
[Como clonar um projeto do Github a partir do Jenkins](#como-clonar-um-projeto-do-github-a-partir-do-jenkins) <br>
[Configurando o Maven no Jenkins](#configurando-o-maven-no-jenkins) <br>
[Documentando o último artifact jar buildado com sucesso](#documentando-o-último-artifact-jar-buildado-com-sucesso) <br>

# Iniciando um servidor do Jenkins

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

# Criando um job no Jenkins

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

# Configurando o Maven no Jenkins

Em **Dashboard > Manage Jenkins > Global Tool Configuration**, clicamos em **Add Maven** e selecionamos a versão desejada

![image](https://user-images.githubusercontent.com/80921933/208012867-13c731b4-5871-4f3d-a3ef-b813be022130.png)

Na sessão de **Build steps** do job, selecionamos a opção **Invoke top-level Maven targets** e selecionamos o objeto do Maven criado anteriormente, além de definir os comandos executados

Essa "pipeline" terá a seguinte sequência:

1. Clonar o repositório
2. Gerar o jar com o mvn -DskipTests clean package
3. Rodar os testes com mvn test
4. Executar o código com o java -jar \<CAMINHO_JAR_GERADO>

Clonando o repositório...

![image](https://user-images.githubusercontent.com/80921933/208015884-77e3762b-8d40-4e49-8107-7bf19b7cab28.png)

Gerando o jar...

![image](https://user-images.githubusercontent.com/80921933/208013107-71db59da-fbbc-436d-9610-3c2900f8492f.png)

Rodando os testes...

![image](https://user-images.githubusercontent.com/80921933/208015518-ebc733e9-0750-44f5-8ba9-a8d1bd603326.png)

Executando o código a partir do jar...

![image](https://user-images.githubusercontent.com/80921933/208015731-411b6572-dc91-4219-9e1b-8ecd7a704a00.png)

# Documentando o último artifact jar buildado com sucesso

Nas configurações do job, vamos em **Post-build Actions**

Selecionamos a opção **Archive the artifact**

Preenchemos as informações para que o Jenkins possa identificar o arquivo a ser documentado, nesse caso, o jar da pasta target

![image](https://user-images.githubusercontent.com/80921933/208016512-e354d1c4-744d-4f78-b9f7-e905a822ea9d.png)

Vale lembrar que podemos passar o caminho relativo (target/*.jar) porque o job possui uma workspace, e executará tudo a partir dela.

![image](https://user-images.githubusercontent.com/80921933/208016695-0775f553-2d8b-44c4-93ab-64485ff4ac90.png)

Agora, a tela pricipal do job mostrará o último artifact buildado com sucesso

![image](https://user-images.githubusercontent.com/80921933/208016787-53bd21fa-43e3-4a43-b11f-34eddbe62613.png)

# Instalando o plugin de DSL do Jenkins

![image](https://user-images.githubusercontent.com/80921933/208022063-e83866ce-d80a-4523-a64c-8fff62ab603e.png)

# Estrutura de um script DSL

- Script DSL que cria um job chamado **job_dsl_created**

    ```groovy
    job('job_dsl_created'){

    }
    ```
- Definindo o description de um job (texto que aparece abaixo do título)
    
    ```groovy
    job('job_dsl_created'){
    
      description('This is my first job description!')
      
    }
    ```
    
- Definindo os parâmetros de um job

    ```groovy
    job('job_dsl_created'){
    
      parameters {
          stringParam('Planet', defaultValue = 'world', description = 'This is the world')
          booleanParam('FLAG', true)
          choiceParam('OPTION', ['option 1 (default)', 'option 2', option 3])
      }
      
    }
    ```
    
- Definindo o Source Code Management (SCM)

    ```groovy
    job('job_dsl_created'){

      git('https://github.com/jenkins-docs/simple-java-maven-app', 'master')

    }
    ```

- Definindo triggers, como cron expressions

    ```groovy
    job('job_dsl_created'){

      cron('* * * * *')

    }
    ```

- Definindo build-steps para uma linha

    ```groovy
    job('job_dsl_created'){

      shell("echo 'Hello world!'")

    }
    ```
    
- Definindo build-steps para mais de uma linha

    ```groovy
    job('job_dsl_created'){

      shell("""
            echo 'Linha 1'
            echo 'Linha 2'
            echo 'Linha 3'
            """)

    }
    ```

- Definindo Mailing para Post-build actions

    ```groovy
    job('job_dsl_created'){

      publishers{
          mailer('me@example.com', true, true)
          archiveArtifacts('<PATH_TO_FILE_TO_ARCHIVE>')
      }

    }
    ```
    
# Exemplo de script DSL

```groovy
job('maven_dsl'){
  
  description('Esse job foi criado a partir de um script DSL, a fim de replicar o job de integração com o Maven, feito manualmente')
  
  scm{
    git('https://github.com/jenkins-docs/simple-java-maven-app', 'master', {node -> node / 'extensions' << ''})
  }
  
  steps {
    
    maven {
      mavenInstallation('maven-jenkins')
      goals('-B -DskipTests clean package')
    }
    
    shell("""
      echo "Executing jar..."
      java -jar /var/jenkins_home/workspace/maven-job/target/my-app-1.0-SNAPSHOT.jar
      """)
  }
  
  publishers {
  	archiveArtifacts('target/*.jar')
  }
}
```

# Instalando plugin do Jenkins Pipeline

![image](https://user-images.githubusercontent.com/80921933/208271956-99c852b2-91ee-4ef5-b51b-54d71f96c539.png)

# Pipeline simples


```groovy
pipeline {
    agent any 
    stages {
        stage('Build') { 
            steps {
                sh 'echo "Building..."' 
            }
        }
        stage('Test') { 
            steps {
                sh 'echo "Testing..."' 
            }
        }
        stage('Deploy') { 
            steps {
                sh 'echo "Deploying..."' 
            }
        }
    }
}
```

Com o arquivo acima, podemos criar um **Pipeline project**

![image](https://user-images.githubusercontent.com/80921933/208272092-f2f10e0e-c9b8-4733-b974-a45cb24f5f77.png)

Na etapa **Pipeline**, basta colarmos o nosso script

![image](https://user-images.githubusercontent.com/80921933/208272128-f08450b9-791b-45ca-ae7e-31523bb914e3.png)

Ao rodá-lo, vemos que deu tudo certo

![image](https://user-images.githubusercontent.com/80921933/208272151-f6960c44-9973-4965-a000-f4386cd064ab.png)

# Comandos na pipeline

- **Retry** - Serve para re-tentarmos executar o comando n vezes. Caso não rode adequadamente em nenhuma tentativa, o Jenkins cancelará o job. 

  ```groovy
  pipeline {
      agent any 
      stages {
          stage('Build') { 
              steps {
                  retry(3){
                    sh 'I am not gonna work' 
                  }
              }
          }

      }
  }
  ```
  
- **Timeout** - Serve para cancelarmos execuções que tomarem mais tempo que o esperado. No script abaixo, a execução do script **sleep 5** demorará mais que os 3 segundos definidos no timeout, portanto, o Jenkins cancelará a execução.

  ```groovy
  pipeline {
      agent any 
      stages {
          stage('Build') { 
              steps {
                  retry(3){
                    sh 'echo hello' 
                  }
                  
                  timeout(time: 3, unit: 'SECONDS'){
                    sh 'sleep 5'
                  }
              }
          }

      }
  }
  ```
  
  - **Environment** - Serve para definirmos variáveis que podem ser referênciadas nos steps

  ```groovy
  pipeline {
      agent any
      
      environment {
        NAME='ALEX'
        FAMILYNAME='RODRIGUES'
      }
      stages {
          stage('Build') { 
              steps {
                sh 'echo "Hello, $NAME $FAMILYNAME!"'
              }
          }

      }
  }
  ```
  
    - **Credentials** - Gerenciamento de credenciais. Basta criar a credencial no menu do Jenkins, e referênciá-la no objeto **environment** com o método **credentials**

  ```groovy
  pipeline {
      agent any
      
      environment {
        secret=credentials('TEST')
      }
      stages {
          stage('Build') { 
              steps {
                sh 'echo "My secret is $TEST"'
              }
          }

      }
  }
  ```
  
    - **Post-actions** - Ações executadas após a pipeline

  ```groovy
  pipeline {
      agent any
      
      stages {
          stage('Build') { 
              steps {
                sh 'echo "This is a simple step"'
              }
          }

      }
      post{
        always{
          sh 'echo "I will always be executed"'
        }
        success{
          sh 'echo "I will only be executed on success"'
        }
        failure{
          sh 'echo "I will only be executed on failure"'
        }
        unstable{
          sh 'echo "I will only be executed if this is unstable"'
        }
      }
  }
  ```
  
# Informações sobre a utilização de um contêiner do Maven para gerar jar

Todos os downloads feitos pelos comandos do Maven são armazenados em **~/.m2**. Isso significa que um bind-mount nessa localidade é uma boa estratégia, já que não precisaremos re-baixar tudo que o Maven necessita toda vez que rodarmos comandos.

# Criando pipeline

- Instalar Docker no contêiner do Jenkins
- Ser capaz de utilizar um contêiner alpine do Maven para realizar o package e test de uma aplicação Java/Maven
- Automatizar o processo de geração de jar com shell script
- Automatizar o processo de build de imagem com shell script
  
Lembrar:

Usar a env $BUILD_TAG do Jenkins

Quase finalizado... Basta criar o script do deploy e configurar o plugin de ssh no jenkins para rodar um script de deploy em um server ubuntu contêinerizado;;;

Verificar pq o comando do Dockerfile do ubuntu quebrou

# Rodando o Docker no Jenkins

Use o seguinte Dockerfile:

```
FROM jenkins/jenkins

USER root

RUN apt update && apt install -y nano

RUN curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall

RUN mkdir -p /home/jenkins/bin/ && chown -R 1000:1000 /home/jenkins 

RUN apt update && apt install -y maven
     
USER jenkins
```

Faça os seguintes bind-mounts

```
volumes:
  - /home/azl6/Projects/jenkins/section4/jenkinsbindmount:/var/jenkins_home
  - /var/run/docker.sock:/var/run/docker.sock
```

Além disso, dar permissão de rwx no arquivo **/var/run/docker.sock**, caso contrário, o Jenkins não conseguirá rodar comandos do Docker, pois terá permission denied.

```
sudo chmod a+rwx /var/run/docker.sock
```

# Utilizando ngrok e webhooks para dar trigger na pipeline monobranch

Primeiro, instalamos o ngrok para expor o nosso servidor local de Jenkins para a web. Após ter tudo instalado, basta executar

```
ngrok http <PORTA_JENKINS>
```

Depois, criamos um projeto no Jenkins.

Na aba **General**, definimos o repositório onde o trigger rodará

![image](https://user-images.githubusercontent.com/80921933/211126542-e9875b89-bd72-4efd-9575-0ef8299b2d4c.png)

Agora, na aba de **Build Triggers**, selecionamos a opção **GitHub hook trigger for GITScm polling**

![image](https://user-images.githubusercontent.com/80921933/211126609-ea39acca-4f06-4c97-b01b-ecbe64386fe1.png)

Finalize as configurações como desejar (se o projeto for uma Pipeline, realize tais configurações, etc...)

Agora, salvamos o projeto, e vamos para o repositório no Github.

Clicamos no botão **Settings**

![image](https://user-images.githubusercontent.com/80921933/211126683-ec55f9c6-4d18-48a2-83fa-95e084f44d3b.png)

Depois, no canto esquerdo, selecionamos a opção **Webhooks**

![image](https://user-images.githubusercontent.com/80921933/211126698-66557ad1-c3aa-42df-8138-7b487d345a54.png)

Clicamos em **Add Webhook**

![image](https://user-images.githubusercontent.com/80921933/211126718-a6355b2e-8132-417e-b4e1-faa814518c65.png)

No form a seguir, preenchemos as seguintes informações:

**Payload URL**: \<JENKINS_SOCKET>/github-webhook/ (se o Jenkins estiver exposto via ngrok, seguir o mesmo princípio) <BR>
**Content type**: application/json

Ao salvar, precisamos dar 1 build manualmente, pois somente aí o hook funcionará.

Pronto!
  
# Utilizando ngrok e webhooks para dar trigger na pipeline multibranch

Ao criar um projeto no Jenkins, selecionar **Multibranch pipeline**

ADICIONAR IMAGEM
  
Nas configurações, em **Branch sources**, adicionamos nosso repositório 

![image](https://user-images.githubusercontent.com/80921933/211130691-72fc248a-8318-4a52-b577-ee9324667473.png)

Assumindo que o Jenkinsfile estará na raiz do diretório, o restante das configurações deve estar correto. Damos **Apply** e **Save**.

Ao atualizar a página do projeto, verificamos que foi criado uma pipeline para cada branch

![image](https://user-images.githubusercontent.com/80921933/211130893-1dced654-7fba-422d-b740-14f27e66e629.png)

Para os próximos passos, precisaremos do seguinte plugin:

![image](https://user-images.githubusercontent.com/80921933/211130969-eaa9c851-35fc-492f-8e08-51abcbc30649.png)

Após instalar o plugin, nas configurações do projeto de Multibranch pipeline, teremos a seguinte opção desbloqueada

![image](https://user-images.githubusercontent.com/80921933/211131216-f235b0ad-307f-4379-9778-5ed370224fc2.png)

Selecionamos a opção **Scan by webhook** e definimos um token (podemos escolher o texto dele)

![image](https://user-images.githubusercontent.com/80921933/211131355-7a85357e-e402-4c83-aa57-249965177325.png)

Resumidamente, utilizaremos a URL informada abaixo do token (JENKINS_URL/multibranch-webhook-trigger/invoke?token=TOKEN), e se o TOKEN bater com o informado, a pipeline terá sido "triggerada", em sua respectiva branch.

  
