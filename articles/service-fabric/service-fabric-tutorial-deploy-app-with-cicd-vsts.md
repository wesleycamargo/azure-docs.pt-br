---
title: "Implantar um aplicativo do Azure Service Fabric com integração contínua (Team Services) | Microsoft Docs"
description: "Saiba como configurar a integração e a implantação contínua para um aplicativo do Service Fabric usando o Visual Studio Team Services.  Implantar um aplicativo em um cluster do Service Fabric no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 2fb7ab906208a58c0b5cd3af8b53188fbab94029
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Implantar um aplicativo com CI/CD em um cluster do Service Fabric
Este tutorial é a terceira parte de uma série e descreve como configurar a integração e a implantação contínua para um aplicativo do Service Fabric do Azure usando o Visual Studio Team Services.  É necessário um aplicativo existente do Service Fabric e o aplicativo criado em [Criar um aplicativo .NET](service-fabric-tutorial-create-dotnet-app.md) é usado como exemplo.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar uma definição de build no Team Services
> * Criar uma definição de versão no Team Services
> * Implantar e atualizar automaticamente um aplicativo

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Configurar CI/CD usando o Visual Studio Team Services
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)
- Crie um cluster do Service Fabric do Windows no Azure; você pode [seguir este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md) como exemplo
- Crie uma [conta do Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação
Se você não tiver criado o aplicativo de exemplo Votação na [parte um esta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação
Agora que você [criou um aplicativo](service-fabric-tutorial-create-dotnet-app.md) e [implantou o aplicativo no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), você está pronto para configurar a integração contínua.  Primeiro, prepare um perfil de publicação em seu aplicativo para ser usado pelo processo de implantação que é executado no Team Services.  O perfil de publicação deve ser configurado para direcionar-se ao cluster que você criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicativo do Service Fabric existente.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no aplicativo e selecione **Publicar...**.

Escolha um perfil de destino em seu projeto de aplicativo a fim de usar para o fluxo de trabalho de integração contínua, por exemplo, Nuvem.  Especifique o ponto de extremidade de conexão do cluster.  Marque a caixa de seleção **Atualizar o Aplicativo** para que seu aplicativo seja atualizado a cada implantação no Team Services.  Clique no hiperlink **Salvar** para salvar as configurações em seu perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.  

![Perfil de envio por push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Team Services
Compartilhe os arquivos de origem do seu aplicativo em um projeto de equipe no Team Services, para que você possa gerar builds.  

Crie um novo repositório Git local para seu projeto selecionando **Adicionar ao controle do código-fonte** -> **Git** na barra de status no canto inferior direito do Visual Studio. 

Na exibição **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Enviar por Push para o Visual Studio Team Services**.

![Enviar por push o repositório Git][push-git-repo]

Verifique seu email e selecione sua conta na lista suspensa **Domínio do Team Services**. Digite o nome do seu repositório e selecione **Publicar Repositório**.

![Enviar por push o repositório Git][publish-code]

A publicação do repositório cria um novo projeto de equipe em sua conta com o mesmo nome do repositório local. Para criar o repositório em um projeto de equipe existente, clique em **Avançado** ao lado do nome do **Repositório** e selecione um projeto de equipe. Você pode exibir seu código na Web selecionando **Vê-lo na Web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurar a entrega contínua com VSTS
Uma definição de build do Team Services descreve um fluxo de trabalho composto por um conjunto de etapas de compilação que são executadas sequencialmente. Crie uma definição de build que produza um pacote de aplicativos do Service Fabric e outros artefatos, para implantar em um cluster do Service Fabric. Saiba mais sobre as [definições de build do Team Services](https://www.visualstudio.com/docs/build/define/create). 

Uma definição de versão do Team Services descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usadas juntas, a definição de build e a definição de versão executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre as [definições de versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)do Team Services.

### <a name="create-a-build-definition"></a>Criar a definição de build
Abra um navegador da web e navegue até seu novo projeto de equipe em: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Selecione a guia **Build e versão** e então **Compilações** e **+ Nova definição**.  Em **selecionar um modelo de**, selecione o **aplicativo do Azure Service Fabric** modelo e clique em **aplicar**. 

![Escolher o modelo de build][select-build-template] 

Em **Tarefas**, digite "VS2017 Hospedado" como a **Fila do agente**. 

![Selecionar tarefas][save-and-queue]

Em **Gatilhos**, habilite a integração contínua definindo o **Status do gatilho**.  Selecione **Salvar e enfileirar** para iniciar uma compilação manualmente.  

![Selecionar gatilhos][save-and-queue2]

A compilação também é disparada durante o push ou o check-in. Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster. 

### <a name="create-a-release-definition"></a>Criar uma definição de versão  

Selecione o **Build e versão** guia, em seguida, **versões**, em seguida, **+ nova definição**.  Em **Selecionar um modelo**, selecione o modelo **Implantação do Azure Service Fabric** na lista e **Aplicar**.  

![Escolher modelo de versão][select-release-template]

Selecione **Tarefas**->**Ambiente 1** e **+ Novo** para adicionar uma nova conexão do cluster.

![Adicionar conexão do cluster][add-cluster-connection]

No modo de exibição **Adicionar nova Conexão do Service Fabric**, selecione a autenticação **Baseada em Certificado** ou **Azure Active Directory**.  Especifique o nome de conexão de "mysftestcluster" e um ponto de extremidade do cluster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (ou o ponto de extremidade do cluster em que você está implantando). 

Para autenticação baseada em certificado, adicione a **Impressão digital do certificado de servidor** do certificado do servidor usado para criar o cluster.  Em **Certificado do cliente**, adicione a codificação de base 64 do arquivo de certificado do cliente. Veja no pop-up de ajuda do campo informações sobre como obter essa representação de codificação de base 64 do certificado. Essa também é a **Senha** para o arquivo do certificado.  Se você não tiver um certificado do cliente ou do servidor separado, poderá usar o certificado de cluster ou do servidor. 

Para as credenciais do Azure Active Directory, adicione a **Impressão digital do certificado de servidor** do certificado do servidor usado para criar o cluster e as credenciais que deseja usar para se conectar ao cluster nos campos **Nome de Usuário** e **Senha**. 

Clique em **Adicionar** para salvar a conexão do cluster.

Em seguida, adicione um artefato de compilação ao pipeline para que a definição da versão possa encontrar a saída da compilação. Selecione **Pipeline** e **Artefatos**->**+Adicionar**.  Em **Fonte (Definição de compilação)**, selecione a definição de compilação criada anteriormente.  Clique em **Adicionar** para salvar o artefato de compilação.

![Adicionar artefato][add-artifact]

Habilite um gatilho de implantação contínua para que uma versão seja criada automaticamente quando a compilação for concluída. Clique no ícone de raio no artefato, habilite o gatilho e clique em **Salvar** para salvar a definição de versão.

![Habilitar gatilho][enable-trigger]

Selecione **+ Versão** -> **Criar Versão** -> **Criar** para criar manualmente uma versão.  Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e enviar alterações por push, disparar uma versão
Para verificar se o pipeline de integração contínua está funcionando ao fazer check-in de algumas alterações de código no Team Services.    

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local, selecionando o ícone de alterações pendentes (![Pendente][pending]) na barra de status na parte inferior direita.

Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

![Confirmar tudo][changes]

Selecione o ícone de barra de status de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Team Services/TFS.

![Enviar alterações por push][push]

Enviar por push as alterações ao Team Services dispara um build automaticamente.  Quando a definição de build é concluída com êxito, uma versão é criada automaticamente e inicia a atualização do aplicativo no cluster.

Para verificar o progresso do build, alterne para a guia **Builds** no **Team Explorer** no Visual Studio.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster.

Verifique se a implantação foi bem-sucedida e o aplicativo está em execução no cluster.  Abra um navegador da Web e navegue até [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe a versão do aplicativo, neste exemplo é "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Atualizar o aplicativo
Fazer alterações de código no aplicativo.  Salve e confirme as alterações, seguindo as etapas anteriores.

Depois que a atualização do aplicativo se inicia, você pode observar o progresso da atualização no Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

A atualização do aplicativo pode levar vários minutos. Quando a atualização estiver concluída, o aplicativo estará executado a próxima versão.  Neste exemplo, "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar a definição de build
> * Criar uma definição de versão
> * Implantar e atualizar automaticamente um aplicativo

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
