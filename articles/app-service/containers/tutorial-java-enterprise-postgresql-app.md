---
title: Compilar um aplicativo Web Java Enterprise no Serviço de Aplicativo do Azure no Linux | Microsoft Docs
description: Saiba como fazer um aplicativo Java Enterprise funcionar em Wildfly no Serviço de Aplicativo do Azure no Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.openlocfilehash: 40bee31b7880a323a48e92912ee323c43c3a97da
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634738"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Tutorial: Compilar um aplicativo Web Java EE e um aplicativo Web Postgres no Azure

Este tutorial mostrará como criar um aplicativo Web Java Enterprise Edition (EE) no Serviço de Aplicativo do Azure e conectá-lo a um banco de dados Postgres. Quando tiver terminado, você terá um aplicativo [WildFly](http://www.wildfly.org/about/) armazenando dados no [Banco de Dados do Azure para Postgres](https://azure.microsoft.com/services/postgresql/) em execução no [Serviço de Aplicativo do Azure para Linux](app-service-linux-intro.md).

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Implantar um aplicativo Java EE no Azure usando o Maven
> * Criar um banco de dados Postgres no Azure
> * Configurar o servidor WildFly para usar Postgres
> * Atualizar o aplicativo e reimplantar
> * Executar testes de unidade no WildFly

## <a name="prerequisites"></a>Pré-requisitos

1. [Baixe e instale o Git](https://git-scm.com/)
1. [Baixe e instale o Maven 3](https://maven.apache.org/install.html)
1. [Baixe e instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonar e editar o aplicativo de exemplo

Nesta etapa, você clona o aplicativo de exemplo e configura o Modelo de Objeto do Projeto Maven (POM ou pom.xml) para implantação.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue até um diretório de trabalho e clone [o repositório de exemplo](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Atualizar o POM do Maven

Atualize o POM do Maven com o grupo de recursos e o nome desejados de seu Serviço de Aplicativo. Esses valores serão injetados no plug-in do Azure, que está mais adiante no arquivo _pom.xml_. Você não precisa criar o Plano do Serviço de Aplicativo ou a instância com antecedência. O plug-in do Maven criará o grupo de recursos e o Serviço de Aplicativo, se ele ainda não existir.

Substitua os espaços reservados pelos nomes de recurso que você quer:
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```

Role a tela para baixo até a seção `<plugins>` do _pom.xml_ para inspecionar o plug-in do Azure.

## <a name="build-and-deploy-the-application"></a>Compilar e implantar o aplicativo

Agora, usaremos o Maven para compilar nosso aplicativo e implantá-lo no Serviço de Aplicativo.

### <a name="build-the-war-file"></a>Compilar o arquivo .war

O POM neste projeto está configurado para empacotar o aplicativo em um arquivo WAR (Web Archive). Compile o aplicativo usando o Maven:

```bash
mvn clean install -DskipTests
```

Os casos de teste neste aplicativo são projetados execução quando o aplicativo for implantado no WildFly. Vamos ignorar os testes para compilar no local, e você executará os testes após a implantação do aplicativo no Serviço de Aplicativo.

### <a name="deploy-to-app-service"></a>Implantar no Serviço de Aplicativo

Agora que o WAR está pronto, podemos usar o plug-in do Azure para implantar no Serviço de Aplicativo:

```bash
mvn azure-webapp:deploy
```

Após a conclusão da implantação, prossiga para a próxima etapa.

### <a name="create-a-record"></a>Criar um registro

Abra um navegador e navegue até `https://<your_app_name>.azurewebsites.net/`. Parabéns, você implantou um aplicativo Java EE no Serviço de Aplicativo do Azure!

Agora, o aplicativo está usando um banco de dados H2 em memória. Clique em "administrador" na barra de navegação e crie uma nova categoria. Você perderá o registro no banco de dados em memória se reiniciar a instância do Serviço de Aplicativo. Nas etapas a seguir, você corrigirá esse problema com o provisionamento de um banco de dados Postgres no Azure e com a configuração do WildFly para usá-lo.

![Local do botão de administrador](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Provisionar um banco de dados Postgres

Para provisionar um servidor de banco de dados Postgres, abra um terminal e execute o comando a seguir com seus valores desejados para nome do servidor, nome de usuário, senha e local. Use o mesmo grupo de recursos no qual o Serviço de Aplicativo reside. Anote sua senha para uso posterior!

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Navegue até o Portal e pesquise o banco de dados Postgres. Quando a folha estiver ativa, copie os valores de "Nome do servidor" e "Nome de logon do administrador do servidor"; você precisará deles mais tarde.

### <a name="allow-access-to-azure-services"></a>Permitir acesso aos Serviços do Azure

No painel **Segurança de conexão** da folha Banco de Dados do Azure, alterne o botão "Permitir acesso aos serviços do Azure" para a posição **ATIVADO**.

![Permitir acesso aos Serviços do Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Atualizar o aplicativo Java para Postgres

Agora, faremos algumas alterações no aplicativo Java a fim de habilitá-lo para usar nosso banco de dados Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Adicionar credenciais do Postgres ao POM

Em _pom.xml_, substitua os valores de espaço reservado pelo nome do servidor Postgres, pelo nome de logon de administrador e pela senha. Esses valores serão injetados como variáveis de ambiente na instância do Serviço de Aplicativo quando você reimplantar o aplicativo.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Atualizar a API de Transação Java

Em seguida, precisamos editar nossa configuração da JPA (API de Transação Java) para que nosso aplicativo Java se comunique com o Postgres em vez do banco de dados H2 em memória que estávamos usando anteriormente. Abra um editor para _src/main/resources/META-INF/persistence.xml_. Substitua o valor de `<jta-data-source>` por `java:jboss/datasources/postgresDS`. Agora, o JTA XML deve ter esta configuração:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Configurar o servidor de aplicativo do WildFly

Antes de implantar nosso aplicativo reconfigurado, precisamos atualizar o servidor de aplicativo do WildFly com o módulo do Postgres e suas dependências. Para configurar o servidor, precisaremos dos quatro arquivos no diretório `wildfly_config/`:

- **postgresql-42.2.5.jar**: esse arquivo JAR é o driver JDBC para Postgres. Para saber mais, confira o [site oficial](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: esse arquivo XML declara um nome para o módulo do Postgres (org.postgres). Ele também especifica os recursos e as dependências necessárias uso do módulo.
- **jboss_cli_commands.cl**: esse arquivo contém os comandos de configuração que serão executados pela CLI do JBoss. Os comandos adicionam o módulo do Postgres ao servidor de aplicativo do WildFly, fornecem as credenciais, declaram um nome JNDI, definem o tempo limite e muito mais. Se você não estiver familiarizado com a CLI do JBoss, confira a [documentação oficial](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: por fim, esse script de shell será executado sempre que a instância do Serviço de Aplicativo for iniciada. O script executa apenas uma função: canaliza os comandos em `jboss_cli_commands.cli` para a CLI do JBoss.

Recomendamos muito a leitura do conteúdo desses arquivos, especialmente do _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Enviar por FTP os arquivos de configuração

Precisaremos enviar o conteúdo de `wildfly_config/` por FTP para nossa instância do Serviço de Aplicativo. Para obter as credenciais do FTP, clique no botão **Obter Perfil de Publicação** na folha Serviço de Aplicativo no portal do Azure. O nome de usuário e a senha do FTP estarão no documento XML baixado. Para saber mais sobre o perfil de publicação, confira [este documento](https://docs.microsoft.com/azure/app-service/app-service-deployment-credentials).

Usando sua ferramenta de FTP preferida, transfira os quatro arquivos em `wildfly_config/` para `/home/site/deployments/tools/`. (Observe que você não deve transferir o diretório, apenas os arquivos.)


### <a name="finalize-app-service"></a>Finalizar o Serviço de Aplicativo

Na folha Serviço de Aplicativo, navegue até o painel "Configurações de aplicativo". Em "Tempo de execução", defina o campo "Arquivo de inicialização" como `/home/site/deployments/tools/startup_script.sh`. Isso fará com que o script de shell seja executado após a criação da instância do Serviço de Aplicativo, mas antes do servidor do WildFly ser iniciado.

Por fim, reinicie o Serviço de Aplicativo. O botão está no painel "Visão geral".

## <a name="redeploy-the-application"></a>Reimplantar o aplicativo

Em uma janela de terminal, recompile e reimplante o aplicativo.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Parabéns! Agora, seu aplicativo está usando um banco de dados Postgres, e todos os registros criados no aplicativo serão armazenados no Postgres em vez de no banco de dados H3 em memória anterior. Para confirmar isso, crie um registro e reinicie o Serviço de Aplicativo. Os registros ainda estarão lá quando seu aplicativo for reiniciado.

## <a name="clean-up"></a>Limpar

Se não precisar desses recursos para outro tutorial (confira as Próximas etapas), você poderá excluí-los executando o seguinte comando:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um aplicativo Java EE implantado no Serviço de Aplicativo, confira o [Guia do desenvolvedor do Java Enterprise](https://aka.ms/wildfly-quickstart) para saber mais sobre como configurar serviços, solucionar problemas e dimensionar seu aplicativo.