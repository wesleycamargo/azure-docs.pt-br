---
title: "Implantar o Serviço de Aplicativo do Azure com o Plug-in Jenkins | Microsoft Docs"
description: "Saiba como usar o plug-in Jenkins do Serviço de Aplicativo do Azure para implantar um aplicativo Web do Java no Azure no Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: bd4e629e522fb9acea5601be8eac7c70ae61d042
ms.contentlocale: pt-br
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Implantar para o Serviço de Aplicativo do Azure com o Plug-in Jenkins 
Para implantar um aplicativo Web do Java no Azure, você pode usar a CLI do Azure no [Pipeline do Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) ou você pode fazer uso do [Plug-in Jenkins do Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-service). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar Jenkins para implantar para o Serviço de Aplicativo do Azure via FTP 
> * Configurar o Jenkins para implantar o Serviço de Aplicativo do Azure em Linux por meio do Docker 

## <a name="create-and-configure-jenkins-instance"></a>Criar e configurar uma instância do Jenkins
Se você ainda não tiver um mestre do Jenkins, comece com o [Modelo de Solução](install-jenkins-solution-template.md), que inclui o JDK8 e os plug-ins necessários a seguir:

* [Plug-in de cliente Git do Jenkins](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-server) v.0.1

Você pode usar o plug-in do Serviço de Aplicativo para implantar o aplicativo Web em todas as linguagens (por exemplo, C#, PHP, Java e node.js, etc.) com suporte pelo Serviço de Aplicativo do Azure. Neste tutorial, estamos usando o aplicativo Java de exemplo, [Aplicativo Web Java Simples para o Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar o repositório para sua própria conta do GitHub, clique no botão **Bifurcação** no canto superior direito.  

O Java JDK e o Maven são necessários para compilar o projeto Java. Verifique se você instalou os componentes no mestre do Jenkins ou no agente de VM, caso você use um deles para a integração contínua. 

Para instalar, faça logon na instância do Jenkins usando o SSH e execute os seguintes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para implantar o Serviço de Aplicativo no Linux, você também precisa instalar o Docker no mestre do Jenkins ou no agente de VM usado para o build. Consulte este artigo para instalar o Docker: https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adicione uma entidade de serviço do Azure na credencial do Jenkins

Uma entidade de serviço do Azure é necessária para implantar no Azure. 

<ol>
<li>Use a [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou o [Portal do Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal) para criar uma entidade de serviço do Azure</li>
<li>No painel do Jenkins, clique em **Credenciais -> Sistema ->**. Clique em **Credenciais globais (irrestrito)**.</li>
<li>Clique em **Adicionar credenciais** para adicionar uma entidade de serviço do Microsoft Azure, preenchendo a ID da assinatura, a ID do cliente, o segredo do cliente e o Ponto de Extremidade do Token OAuth 2.0. Forneça uma ID, **mySp**, para uso na próxima etapa.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Plug-in do Serviço de Aplicativo do Azure

O plug-in do Serviço de Aplicativo do Azure v1.0 dá suporte à implantação contínua para o aplicativo Web do Azure por meio de:

* Git e FTP
* Docker para Aplicativo Web no Linux

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Configurar o Jenkins para implantar o aplicativo Web via FTP usando o painel do Jenkins

Para implantar seu projeto de aplicativo Web do Azure, você pode carregar seus artefatos de build (por exemplo, o arquivo .war em Java) usando o Git ou FTP.

Antes de configurar o trabalho em Jenkins, você precisa de um plano do Serviço de Aplicativo do Azure e de um aplicativo Web para executar o aplicativo Java.


1. Crie um plano do Serviço de Aplicativo do Azure com o tipo de preço **GRÁTIS** usando a CLI de comando [az appservice plan create](/cli/azure/appservice/plan#create). O plano do serviço de aplicativo define os recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do serviço de aplicativo compartilham esses recursos, permitindo que você economize hospedando vários aplicativos.
2. Crie um aplicativo Web. Você pode usar o [Portal do Azure](/azure/app-service/web-sites-configure) ou use o seguinte comando da CLI do Azure:
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. Verifique se que você definiu a configuração de tempo de execução do Java que seu aplicativo precisa. O comando da CLI do Azure a seguir configura o aplicativo Web para ser executado em um JDK 8 Java recente e [Apache Tomcat](http://tomcat.apache.org/) 8.0.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Configurar o trabalho do Jenkins


1. Criar um novo projeto de **estilo livre** no painel do Jenkins
2. Configure o **Gerenciamento de Código-fonte** para usar o seu fork local de [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample), fornecendo a **URL do Repositório**. Por exemplo: http://github.com/&lt;yourID>/aplicativowebjavadeexemplo.
3. Adicione uma etapa de build para compilar o projeto usando o Maven. Faça isso adicionando um **Executar shell**. Neste exemplo, precisamos de uma etapa adicional para renomear o arquivo *.war na pasta de destino para ROOT.war.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. Adicione uma ação pós-build selecionando **Publicar um Aplicativo Web do Azure**.
5. Forneça "mySp", a entidade de serviço do Azure armazenada na etapa anterior.
6. Em **Configuração do Aplicativo**, escolha o aplicativo Web e o grupo de recursos em sua assinatura. O plug-in detecta automaticamente se o aplicativo Web é do Windows ou do Linux. Para um aplicativo Web baseado em Windows, a opção "Publicar Arquivos" é apresentada.
7. Preencha os arquivos que você deseja implantar (por exemplo, um pacote war se você estiver usando o Java.) O Diretório de Origem e o Diretório de Destino são opcionais. Os parâmetros permitem que você especifique as pastas de origem e de destino ao carregar arquivos. O aplicativo Web Java no Azure é executado em um servidor Tomcat. Desse modo, você carrega o pacote war para a pasta webapps. Neste exemplo, defina **Diretório de Origem** como "destino" e forneça "webapps" como **Diretório de Destino**.
8. Se você deseja implantar em um slot que não seja de produção, você também pode definir o Nome do **Slot**.
9. Salve o projeto e compile-o. Seu aplicativo Web será implantado no Azure quando o build for concluído.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Implantar o aplicativo Web via FTP usando o pipeline do Jenkins

O plug-in está preparado para o pipeline. Você pode se referir a uma amostra no repositório GitHub.

1. Na interface do usuário da Web do GitHub, abra o arquivo **Jenkinsfile_ftp_plugin**. Clique no ícone de lápis para editar esse arquivo para atualizar o grupo de recursos e o nome do seu aplicativo Web nas linhas 11 e 12, respectivamente.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Altere a linha 14 para atualizar a ID de credencial na sua instância do Jenkins.    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Crie um pipeline do Jenkins

1. Abra o Jenkins em um navegador da Web, clique em **Novo Item**.
2. Dê um nome para o trabalho e selecione **Pipeline**. Clique em **OK**.
3. Clique na próxima guia **Pipeline**.
4. Para **Definição**, selecione **Script de pipeline do SCM**.
5. Para **SCM**, selecione **Git**. Insira a URL do GitHub em seu repositório bifurcado: https:&lt;seu repositório bifurcado>.git
6. Atualize o **Caminho de Script** para "Jenkinsfile_ftp_plugin"
7. Clique em **Salvar** e execute o trabalho.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Configurar o Jenkins para implantar o Aplicativo Web no Linux por meio do Docker

Além de Git/FTP, o aplicativo Web no Linux dá suporte à implantação usando o Docker. Para implantar usando o Docker, você precisa fornecer um Dockerfile que empacote seu aplicativo Web com tempo de execução do serviço em uma imagem do Docker. O plug-in então compila a imagem, envia-a por push a um registro de Docker e a implanta em seu aplicativo Web.

O aplicativo Web no Linux também dá suporte a modos tradicionais, como Git e FTP, mas somente para linguagens internas (.NET Core, Node.js, PHP e Ruby). Para outras linguagens, você precisa empacotar o código do aplicativo e tempo de execução do serviço juntos em uma imagem do Docker e usar o Docker para implantar.

Antes de configurar o trabalho no Jenkins, você precisa de um Serviço de Aplicativo do Azure no Linux. Um registro de contêiner também é necessário para armazenar e gerenciar suas imagens privadas de contêiner Docker. Você pode usar o DockerHub; estamos usando o Registro de Contêiner do Azure para este exemplo.

* Você pode seguir as etapas [aqui](../app-service/containers/quickstart-nodejs.md) para criar um aplicativo Web no Linux 
* O Registro de Contêiner do Azure é um serviço gerenciado de [registro do Docker] (https://docs.docker.com/registry/) com base no software livre Docker Registry 2.0. Siga as etapas [aqui] (/azure/container-registry/container-registry-get-started-azure-cli) para mais diretrizes sobre como fazer isso. Você também pode usar o DockerHub.

### <a name="to-deploy-using-docker"></a>Para implantar usando o docker:

1. Crie um novo projeto de estilo livre no painel do Jenkins.
2. Configure o **Gerenciamento de Código-fonte** para usar o seu fork local de [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample), fornecendo a **URL do Repositório**. Por exemplo: http://github.com/&lt;yourid>/aplicativowebjavadeexemplo.
Adicione uma etapa de build para compilar o projeto usando o Maven. Faça isso adicionando um **Executar shell** e adicione a seguinte linha no **Comando**:    
```bash
mvn clean package
```

3. Adicione uma ação pós-build selecionando **Publicar um Aplicativo Web do Azure**.
4. Forneça **mySp**, a entidade de serviço do Azure armazenada na etapa anterior como Credenciais do Azure.
5. Em **Configuração do Aplicativo**, escolha o aplicativo Web do Linux e o grupo de recursos em sua assinatura.
6. Escolha Publicar por meio do Docker.
7. Preencha o caminho de **Dockerfile**. Você pode manter o "/Dockerfile" padrão para a **URL de Registro do Docker**, forneça-a no formato https://&lt;myRegistry>.azurecr.io se você usar o Registro de Contêiner do Azure. Deixe-a em branco se você usar o DockerHub.
8. Para **Credenciais de registro**, adicione a credencial para o Registro de Contêiner do Azure. Você pode obter a ID de usuário e senha executando os comandos a seguir na CLI do Azure. O primeiro comando habilita a conta administrador.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. O nome da imagem do docker e a marca na guia **Avançado** são opcionais. Por padrão, o nome da imagem é obtido do nome da imagem que você configurou no Portal do Azure (na configuração do Contêiner do Docker). A marca é gerada de $BUILD_NUMBER. Verifique se você especificou o nome da imagem em um Portal do Azure ou forneça um valor para **Imagem do Docker** na guia **Avançado**. Neste exemplo, forneça "&lt;yourRegistry>.azurecr.io/calculator" para a **Imagem do Docker** e deixe a **Marca da Imagem do Docker** em branco.
10. Observe que a implantação falhará se você usar uma configuração de imagem do Docker interna. Verifique se que você alterou a configuração do Docker para usar a imagem personalizada na configuração Contêiner do Docker no Portal do Azure. Para a imagem interna, use a abordagem de upload de arquivo para implantar.
11. Semelhante à abordagem de upload de arquivo, você pode escolher um slot diferente que não seja de produção.
12. Salve e compile o projeto. Você verá que sua imagem de contêiner é enviada por push para o Registro e o aplicativo Web é implantado.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Implante o aplicativo Web no Linux por meio do Docker usando o pipeline do Jenkins

1. Na interface do usuário da Web do GitHub, abra o arquivo **Jenkinsfile_container_plugin**. Clique no ícone de lápis para editar esse arquivo para atualizar o grupo de recursos e o nome do seu aplicativo Web nas linhas 11 e 12, respectivamente.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Altere a linha 13 para o servidor de registro de contêiner    
```java
def registryServer = '<registryURL>'
```    

3. Altere a linha 16 para atualizar a ID de credencial na instância do Jenkins    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Criar pipeline do Jenkins    

1. Abra o Jenkins em um navegador da Web, clique em **Novo Item**.
2. Dê um nome para o trabalho e selecione **Pipeline**. Clique em **OK**.
3. Clique na próxima guia **Pipeline**.
4. Para **Definição**, selecione **Script de pipeline do SCM**.
5. Para **SCM**, selecione **Git**.
6. Insira a URL do GitHub em seu repositório bifurcado: https:&lt;seu repositório bifurcado>.git</li>
7, Atualize o **Caminho de Script** para "Jenkinsfile_container_plugin"
8. Clique em **Salvar** e execute o trabalho.

## <a name="verify-your-web-app"></a>Verifique seu aplicativo Web

1. Para verificar se o arquivo WAR foi implantado com êxito em seu aplicativo Web, Abra um navegador da Web.
2. Acesse http://&lt;nome_do-aplicativo>.azurewebsites.net/api/calculator/ping Você verá:    
     Bem-vindo ao aplicativo Web Java!!! Ele está atualizado!
   Domingo, 17 de junho de 2017, 16:39:10 UTC
3. Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x > e &lt;y > por qualquer número) para obter a soma de x e y        
    ![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Para o Serviço de Aplicativo no Linux

* Para verificar, na CLI do Azure, execute:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Você obterá o seguinte resultado:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Você verá a mensagem: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x > e &lt;y > por qualquer número) para obter a soma de x e y
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você pode usar o plug-in do Serviço de Aplicativo do Azure para implantar no Azure.

Você aprendeu como:

> [!div class="checklist"]
> * Configurar Jenkins para implantar o Serviço de Aplicativo do Azure via FTP 
> * Configurar o Jenkins para implantar o Serviço de Aplicativo do Azure em Linux por meio do Docker 
