---
title: "Compilação contínua e integração para seu aplicativo Java Linux do Azure Service Fabric usando o Jenkins | Microsoft Docs"
description: "Compilação contínua e integração para seu aplicativo Java Linux do Azure Service Fabric usando o Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Criar e implantar seu aplicativo Java Linux usando o Jenkins
Jenkins é uma ferramenta popular para implantação e integração contínua. Neste documento, percorreremos a criação e a implantação de seu aplicativo do Service Fabric usando o Jenkins.

## <a name="general-prerequisites"></a>Pré-requisitos gerais
1. Ter o Git instalado localmente. Você pode instalar a versão apropriada do Git [aqui](https://git-scm.com/downloads) com base no seu sistema operacional. Se você for novo no Git, poderá seguir as etapas mencionadas na [documentação](https://git-scm.com/docs) para se familiarizar com ele.
2. Ter o plug-in Jenkins do Service Fabric à mão. Baixe o plug-in Jenkins do Service Fabric [aqui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configurando o Jenkins em um cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
1. Ter um cluster Linux do Service Fabric pronto. O cluster do Service Fabric criado no portal do Azure já tem o Docker instalado. Se você estiver executando o cluster localmente, verifique se o Docker está instalado ou não usando o comando ``docker info`` e, se ele não estiver instalado, instale-a adequadamente usando os seguintes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Ter o aplicativo de contêiner do Service Fabric implantado no cluster usando as seguintes etapas:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Isso instala o contêiner Jenkins no cluster e pode ser monitorado usando o Service Fabric Explorer.

### <a name="steps"></a>Etapas
1. Vá para a URL ``http://PublicIPorFQDN:8081`` no seu navegador. Ela fornece o caminho da senha de administrador inicial necessária para o logon. Você pode continuar a usar o Jenkins como usuário administrador ou pode criar e alterar o usuário depois de fazer logon com a conta do administrador inicial.

  > [!NOTE]
  > Você precisa especificar a porta 8081 como porta de ponto de extremidade do aplicativo durante a criação do cluster
  >

2. Obter a ID de instância do contêiner usando ``docker ps -a``.
3. Faça logon SSH para o contêiner e cole o caminho exibido no portal do Jenkins. Por exemplo, se o portal mostra o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, você pode:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configurar o GitHub para funcionar com o Jenkins usando as etapas mencionadas no [link](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Usar as instruções fornecidas no GitHub para gerar uma chave SSH e adicionar a chave SSH à conta do GitHub que está hospedando o repositório.
    * Executar os comandos mencionados no link anterior no shell Jenkins Docker (e não em seu host)
    * Para fazer logon no shell Jenkins do host, use o seguinte comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configurando Jenkins fora de um cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
Você precisa ter o Docker instalado. Os comandos abaixo podem ser usados para instalar o Docker do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Agora, quando você executa ``docker info`` no terminal, deve ver a saída executada pelo serviço Docker.

### <a name="steps"></a>Etapas
  1. Baixar a imagem de contêiner Jenkins do Service Fabric:``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Executar a imagem de contêiner:``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Obter a ID de instância de imagem de contêiner. Você pode listar todos os contêineres do Docker com o comando ``docker ps –a``
  4. Fazer logon no portal do Jenkins usando as seguintes etapas:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se a id do contêiner é 2d24a73b5964, use 2d 24.
    * Esta senha é necessária para fazer logon no painel do Jenkins do portal, que é ``http://<HOST-IP>:8080``
    * Depois de fazer logon pela primeira vez, você pode criar sua própria conta de usuário e usá-la futuramente ou pode continuar a usar a conta de administrador. Quando você cria um usuário, precisa continuar com ela.
  5. Configurar o GitHub para funcionar com o Jenkins usando as etapas mencionadas no [link](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Use as instruções fornecidas no GitHub para gerar uma chave SSH e adicione a chave SSH à conta GitHub que está (estaria) hospedando o repositório.
      * Executar os comandos mencionados no link anterior no shell Jenkins Docker (e não em seu host)
      * Para fazer logon no shell Jenkins do host, use os seguintes comandos:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Verifique se o cluster/máquina onde a imagem de contêiner do Jenkins está hospedada tem um IP voltado para o público, de modo que as notificações do GitHub sejam recebidas pela instância Jenkins.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Instalar o plug-in Jenkins do Service Fabric do portal

1. Acesse ``http://PublicIPorFQDN:8081``
2. No painel do Jenkins, selecione **Gerenciar Jenkins** -> **Gerenciar plug-ins** -> **Avançado**.
Aqui, você pode carregar um plug-in. Selecione a opção **Escolher arquivo** e selecione o arquivo serviceFabric.hpi que você baixou na etapa de pré-requisitos. Quando você seleciona o carregamento, o Jenkins instala o plug-in automaticamente. Se solicitado, permita a reinicialização.

## <a name="creating-and-configuring-a-jenkins-job"></a>Criando e configurando um trabalho do Jenkins

1. Criar um **novo item** do painel
2. Insira um nome de item, por exemplo, **MyJob**, selecione o projeto de estilo livre e clique em OK
3. Em seguida, vá para a página do trabalho e clique em **Configurar** -
  * Na seção geral, em **Projeto Github**, especifique a URL do projeto do GitHub, que hospeda o aplicativo Java do Service Fabric que você deseja integrar ao fluxo CI/CD do Jenkins (por exemplo, ``https://github.com/sayantancs/SFJenkins``).
  * Na seção **Gerenciamento de Código-Fonte**, selecione **Git**. Especifique a URL do repositório que hospeda o aplicativo Java do Service Fabric que você deseja integrar ao fluxo CI/CD do Jenkins (por exemplo, ``https://github.com/sayantancs/SFJenkins.git``). Você também pode especificar aqui quais ramificações devem ser criadas, por exemplo, ***/mestre**.
4. Configure seu *GitHub* (que está hospedando o repositório) para que ele seja capaz de se comunicar com o Jenkins usando as seguintes etapas:
  1. Vá para sua página do repositório GitHub. Vá para **Configurações** -> **Integrações e Serviços**.
  2. Selecione **Adicionar Serviço**, digite Jenkins, selecione o **plug-in Jenkins-Github**.
  3. Insira a URL do webhook Jenkins (por padrão, ele deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em adicionar/atualizar serviço.
  4. Um evento de teste é enviado para a instância do Jenkins. Você verá uma marca de seleção verde ao lado do webhook no GitHub e seu projeto será criado!
  5. Na seção **Criar Disparadores**, selecione a opção de compilação desejada: neste caso, planejamos disparar uma compilação sempre que houver um envio por push para o repositório. Assim, a opção correspondente seria **gatilho hook GitHub para sondagem de GITScm** (anteriormente era 'Criar quando uma alteração é enviada ao GitHub')
  6. Na **seção Criar**, na lista suspensa **Adicionar etapa de compilação**, selecione a opção **Invocar Gradle Script**. No widget surgido, especifique o caminho para **Script da build raiz** para seu aplicativo. Ele pega o build.gradle do caminho especificado e trabalha de acordo com isso. Se você criar um projeto chamado ``MyActor``(usando o plug-in Eclipse ou gerador Yeoman), o script da build raiz deverá conter - ``${WORKSPACE}/MyActor``. Por exemplo, esta seção fica mais ou menos assim:

    ![Ação Compilar do Jenkins no Service Fabric][build-step]
  7. No menu suspenso **Ações Pós-Compilação**, selecione **Implantar Projeto do Service Fabric**. Aqui, você precisa fornecer detalhes do cluster no qual o aplicativo do Service Fabric compilado no Jenkins seria implantado e detalhes adicionais de aplicativo usados para implantar o aplicativo. A seguinte captura de tela pode ser usada como referência:

    ![Ação Compilar do Jenkins no Service Fabric][post-build-step]

 > [!NOTE]
 > Aqui, o cluster pode ser o mesmo que hospeda o aplicativo de contêiner Jenkins caso você esteja usando o Service Fabric para implantar a imagem de contêiner Jenkins.
 >

### <a name="end-to-end-scenario"></a>Cenário de ponta a ponta
Agora, seu GitHub e Jenkins estão configurados e você pode fazer algumas alterações de exemplo no seu projeto ``MyActor`` no repositório de exemplo https://github.com/sayantancs/SFJenkins e enviar suas alterações por push para a ramificação ``master`` remota (ou outra configurada para trabalhar com ele). Isso dispararia o trabalho do Jenkins ``MyJob`` configurado. O que ele faria seria basicamente buscar as alterações do GitHub, compilá-las e implantar o aplicativo no ponto de extremidade do cluster especificado nas ações pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

