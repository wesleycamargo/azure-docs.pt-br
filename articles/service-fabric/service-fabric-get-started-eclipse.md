---
title: "Introdução ao plug-in Eclipse para o Azure Service Fabric | Microsoft Docs"
description: "Introdução ao plug-in Eclipse para o Azure Service Fabric."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Introdução ao plug-in Eclipse para desenvolvimento de aplicativos Java do Service Fabric
O Eclipse é um dos IDEs mais usados para desenvolvedores de Java. Neste artigo, discutiremos como você pode definir o seu ambiente de desenvolvimento do Eclipse para trabalhar com o Service Fabric. Este artigo o ajudará a instalar o plug-in, criar aplicativos do Service Fabric e implantar seu aplicativo do Service Fabric em um cluster do Service Fabric local ou remoto.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Instalar ou atualizar o plug-in do Service Fabric no Eclipse Neon
O Service Fabric fornece um plug-in para o **Eclipse IDE para desenvolvedores Java** que pode simplificar o processo de criação e implantação dos serviços Java.

1. Verifique se você tem o Eclipse **Neon** mais recente e a versão mais recente do Buildship (1.0.17 ou posterior) instalados. Você pode verificar as versões dos componentes instalados escolhendo ``Help => Installation Details``. Você pode atualizar o Buildship usando as instruções [aqui][buildship-update]. Para verificar se o Eclipse Neon está na versão mais recente e atualizá-la, vá para ``Help => Check for Updates``.

2. Para instalar o plug-in do Service Fabric, escolha ``Help => Install New Software...``.
  1. Na caixa de texto "Trabalhar com", digite: ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Clique em Adicionar.

  ![Plug-in do Eclipse Neon para o Service Fabric][sf-eclipse-plugin-install]

  3. Escolha o plug-in do Service Fabric e clique em Avançar.
  4. Continue com a instalação e aceite o contrato de licença do usuário final.

Se você já tiver o plug-in Eclipse do Service Fabric instalado, verifique se está usando a versão mais recente. Você pode verificar se ele pode ser atualizado ainda mais para o seguinte - ``Help => Installation Details``. Em seguida, procure o Service Fabric na lista de plug-ins instalados e clique em atualizar. Se houver qualquer atualização pendente, ela será buscada e instalada.

> [!NOTE]
> Caso a instalação ou atualização do plug-in Eclipse do Service Fabric demore muito tempo, é porque o Eclipse tenta buscar metadados de todas as novas alterações feitas a todos os update-sites registrados em sua instância do Eclipse. Portanto, para torná-lo mais rápido, você pode usar este truque: vá para `Available Software Sites` e desmarque tudo o que não aponta para o local do plug-in do Service Fabric `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Criar aplicativo do Service Fabric usando o Eclipse

1. Vá para ``File => New => Other``. Selecione ``Service Fabric Project``. Clique em ``Next``.

    ![Novo Projeto do Service Fabric Página 1][create-application/p1]

2. Dê um nome ao seu projeto. Clique em ``Next``.

    ![Novo Projeto do Service Fabric Página 2][create-application/p2]

3. Selecione o Modelo de Serviço disponível no conjunto de modelos (Ator, Sem Estado, Contêiner ou Executável por Convidado). Clique em ``Next``.

    ![Novo Projeto do Service Fabric Página 3][create-application/p3]

4. Insira o nome do serviço e/ou os detalhes de serviço relevantes nessa página e clique em ``Finish``.

    ![Novo Projeto do Service Fabric Página 4][create-application/p4]

5. Ao criar seu primeiro projeto do Service Fabric, ele perguntará se vai querer definir a perspectiva do Service Fabric. Selecione ``yes`` para continuar.

    ![Novo Projeto do Service Fabric Página 5][create-application/p5]

6. Após a criação bem-sucedida, o projeto terá a seguinte aparência:

    ![Novo Projeto do Service Fabric Página 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Criar e implantar o aplicativo do Service Fabric usando o Eclipse

* Clique com botão direito no aplicativo do Service Fabric criado anteriormente. Selecione a opção ``Service Fabric`` no menu de contexto. Isso abre um submenu com várias opções. que se pareceriam com:

    ![Menu de atalho do Service Fabric][publish/RightClick]

  Quando você clicar em opções para compilação, recompilação e limpeza, ele executará as ações pretendidas.
  - ``Build Application`` criará o aplicativo sem limpar
  - ``Rebuild Application`` fará uma compilação limpa do aplicativo
  - ``Clean Application`` removerá os artefatos internos do aplicativo


* Você pode optar por implantar, remover a implantação e publicar seu aplicativo nesse menu.
  - ``Deploy Application`` implantará no cluster local
  - ``Publish Application...`` solicitará qual dos perfis de publicação, ``Local.json`` ou ``Cloud.json``, você deseja escolher. Esses arquivos JSON são usados para armazenar informações (como pontos de extremidade de conexão e informações de segurança) necessárias para se conectar a um cluster local ou de nuvem (Azure).

  ![Menu de atalho do Service Fabric][publish/Publish]

* Há um modo alternativo pelo qual você pode implantar seu aplicativo do Service Fabric usando configurações de execução do Eclipse.

  1. Escolha ``Run => Run Configurations``. Selecione a configuração de execução ``ServiceFabricDeployer`` em ``Grade Project``.
  2. na guia ``Arguments`` no painel à direita, especifique **local** ou **nuvem** como o ``publishProfile``. A configuração padrão é **local**. Para implantar em um cluster remoto/na nuvem, selecione **nuvem**.
  3. Preencha as informações corretas nos perfis de publicação editando `Local.json` ou `Cloud.json` conforme apropriado, com detalhes do ponto de extremidade e credenciais de segurança, se houver.
  4. Verifique se ``Working Directory`` no painel direito em ``Grade Project`` aponta para o aplicativo que você deseja implantar. Se não, basta clicar no botão ``Workspace...`` e selecionar o aplicativo desejado.
  5. Clique em **Aplicar** e **Executar**.

Seu aplicativo será compilado e implantado em poucos instantes. Você pode monitorar o status no Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Adicionar novo serviço do Service Fabric ao aplicativo do Service Fabric

É possível adicionar um novo serviço do Service Fabric a um aplicativo do Service Fabric existente usando as seguintes etapas:

1. Clique com o botão direito do mouse no projeto ao qual você deseja adicionar um serviço para abrir o menu de contexto e selecione a opção ‘Service Fabric’. Isso abre um submenu com várias opções.

    ![Adicionar Serviço do Service Fabric Página 1][add-service/p1]

2. Selecione a opção `Add ServiceFabric Service` e ela o orientará pelo próximo conjunto de etapas para adicionar um serviço ao projeto.
3. Selecione o modelo de serviço que você deseja adicionar ao projeto e clique em 'Avançar'.

    ![Adicionar Serviço do Service Fabric Página 2][add-service/p2]

4. Insira o nome do serviço (e outros detalhes necessários) e clique no botão "Adicionar serviço" abaixo.  

    ![Adicionar Serviço do Service Fabric Página 3][add-service/p3]

5. Depois que o serviço é adicionado com êxito, a estrutura de todo o projeto fica semelhante ao seguinte:

    ![Adicionar Serviço do Service Fabric Página 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Atualizar seu aplicativo Java do Service Fabric

Vamos supor que você criou o projeto ``App1`` usando o plug-in Eclipse do Service Fabric e o implantou usando o plug-in para criar um aplicativo chamado ``fabric:/App1Application`` do tipo de aplicativo ``App1AppicationType`` e versão 1.0. Agora você deseja atualizar seu aplicativo sem desativá-lo.

Faça a alteração em seu aplicativo e recompile o serviço modificado.  Atualize o arquivo de manifesto do serviço modificado (``ServiceManifest.xml``) com as versões atualizadas para o serviço (e Código, Configuração ou Dados, conforme apropriado). Modifique também o manifesto do aplicativo (``ApplicationManifest.xml``) com o número da versão atualizada do aplicativo e o serviço modificado.  

Para atualizar seu aplicativo usando o Eclipse, você pode criar uma configuração de execução duplicada e usá-la para atualizar seu aplicativo como e quando for necessário, usando as seguintes etapas:
1. Escolha ``Run => Run Configurations``. Clique na seta pequena à esquerda de ``Grade Project`` no painel esquerdo.
2. Clique com o botão direito do mouse em ``ServiceFabricDeployer`` e selecione ``Duplicate``. Forneça um novo nome para essa configuração, por exemplo, ``ServiceFabricUpgrader``.
3. No painel direito, em ``Arguments``, altere ``-Pconfig='deploy'`` para ``-Pconfig=upgrade`` e clique em ``Apply``.
4. Agora, você criou e salvou uma configuração de execução para atualizar seu aplicativo, que você pode ``Run`` quando desejar. Ela se encarregará de obter a versão do tipo de aplicativo mais recente atualizada do arquivo de manifesto do aplicativo.

Agora você pode monitorar a atualização de aplicativo usando o Service Fabric Explorer. Em alguns minutos, o aplicativo seria atualizado.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

