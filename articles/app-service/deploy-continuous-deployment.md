---
title: Implantação contínua – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como habilitar a implantação contínua no Serviço de Aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765543"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua no Serviço de Aplicativo do Azure
Este artigo mostra como configurar a implantação contínua para o [Serviço de Aplicativo do Azure](overview.md). O Serviço de Aplicativo habilita a implantação contínua do BitBucket, do GitHub e do [Azure DevOps Services](https://www.visualstudio.com/team-services/) extraindo as atualizações mais recentes do seu repositório existente em um desses serviços.

Para saber como configurar a implantação contínua manualmente de um repositório de nuvem não listado pelo Portal do Azure (como o [GitLab](https://gitlab.com/)), consulte [Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps) (Configurar a implantação contínua usando etapas manuais).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publicar o repositório preparado em um dos serviços com suporte. Para saber mais sobre como publicar seu projeto nesses serviços, confira [Criar um repositório (GitHub)], [Criar um repositório (BitBucket)] e [Introdução às soluções do Azure DevOps Services].

## <a name="deploy-continuously-from-github"></a>Implantar continuamente do GitHub

Para habilitar a implantação contínua com o GitHub, navegue até sua página de aplicativos do Serviço de Aplicativo no [portal do Azure](https://portal.azure.com).

No menu esquerdo, clique em **Centro de Implantação** > **GitHub** > **Autorizar**. Siga as solicitações de autorização. 

![](media/app-service-continuous-deployment/github-choose-source.png)

É necessário autorizar com o GitHub apenas uma vez. Se você já tiver autorização, basta clicar em **Continuar**. É possível alterar a conta do GitHub autorizada clicando em **Alterar conta**.

![](media/app-service-continuous-deployment/github-continue.png)

Na página **Provedor de build**, escolha o provedor de build e clique em > **Continuar**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opção 1: usar o servidor de build Kudu do Serviço de Aplicativo

Na página **Configurar**, selecione a organização, o repositório e o branch dos quais você deseja implantar continuamente. Ao terminar, clique em **Continuar**.

Para implantar de um repositório em uma organização do GitHub, navegue para o GitHub e vá para **as configurações** > **aplicativos** > **autorizado a aplicativos de OAuth**. Em seguida, clique em "Serviço de aplicativo do Azure".

![Configurações > aplicativos > aplicativos de OAuth autorizados > serviço de aplicativo do Azure](media/app-service-continuous-deployment/github-settings-navigation.png)

Na próxima página, conceda acesso de serviço de aplicativo aos repositórios da sua organização ao clicar no botão de "Concessão" no lado direito.

![Clique em "Concessão" para conceder acesso de serviço de aplicativo para os repositórios da organização](media/app-service-continuous-deployment/grant-access.png)

Sua organização agora deve mostrar, na lista de "Organização" na **configurar** página do Centro de implantação.

### <a name="option-2-use-azure-pipelines-preview"></a>Opção 2: usar o Azure Pipelines (versão prévia)

> [!NOTE]
> Para o Serviço de Aplicativo criar os Azure Pipelines necessários em sua organização do Azure DevOps Services, sua conta do Azure deve ter a função de **Proprietário** em sua assinatura do Azure.
>

Na página **Configurar**, na seção **Código**, selecione a organização, o repositório e o branch dos quais você deseja implantar continuamente. Ao terminar, clique em **Continuar**.

Na página **Configurar**, na seção **Build**, configure uma nova organização do Azure DevOps Services ou especifique uma organização existente. Ao terminar, clique em **Continuar**.

> [!NOTE]
> Se você quiser usar uma organização do Azure DevOps Services existente que não está listada, você precisará [vincular a organização do Azure DevOps Services à sua assinatura do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Na página **Teste**, escolha se deseja habilitar testes de carga. Em seguida, clique em **Continuar**.

Dependendo do [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do Serviço de Aplicativo, talvez você possa ver uma página **Implantar no preparo**. Escolha se deseja [habilitar slots de implantação](deploy-staging-slots.md) e clique em **Continuar**.

### <a name="finish-configuration"></a>Concluir configuração

Na página **Resumo**, verifique as opções e clique em **Concluir**.

Quando a configuração for concluída, novas confirmações no repositório selecionado serão implantadas continuamente em seu aplicativo do Serviço de Aplicativo.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Implantar continuamente do BitBucket

Para habilitar a implantação contínua com o BitBucket, navegue até sua página de aplicativos do Serviço de Aplicativo no [portal do Azure](https://portal.azure.com).

No menu esquerdo, clique em **Centro de Implantação** > **BitBucket** > **Autorizar**. Siga as solicitações de autorização. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

É necessário autorizar com o BitBucket apenas uma vez. Se você já tiver autorização, basta clicar em **Continuar**. É possível alterar a conta do BitBucket autorizada clicando em **Alterar conta**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Na página **Configurar**, selecione o repositório e o branch dos quais você deseja implantar continuamente. Ao terminar, clique em **Continuar**.

Na página **Resumo**, verifique as opções e clique em **Concluir**.

Quando a configuração for concluída, novas confirmações no repositório selecionado serão implantadas continuamente em seu aplicativo do Serviço de Aplicativo.

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Implantar continuamente do Azure Repos (DevOps Services)

Para habilitar a implantação contínua com o [Azure Repos](https://docs.microsoft.com/azure/devops/repos/index), navegue até sua página de aplicativos do Serviço de Aplicativo no [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Central de Implantação** > **Azure Repos** > **Continuar**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Na página **Provedor de build**, escolha o provedor de build e clique em > **Continuar**.

> [!NOTE]
> Se você quiser usar uma organização do Azure DevOps Services existente que não está listada, você precisará [vincular a organização do Azure DevOps Services à sua assinatura do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>Opção 1: usar o servidor de build Kudu do Serviço de Aplicativo

Na página **Configurar**, selecione a organização do Azure DevOps Services, o projeto, o repositório e o branch dos quais você deseja implantar continuamente. Ao terminar, clique em **Continuar**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Opção 2: usar a entrega contínua do Azure DevOps Services

> [!NOTE]
> Para o Serviço de Aplicativo criar os Azure Pipelines necessários em sua organização do Azure DevOps Services, sua conta do Azure deve ter a função de **Proprietário** em sua assinatura do Azure.
>

Na página **Configurar**, na seção **Código**, selecione a organização do Azure DevOps Services, o projeto, o repositório e o branch dos quais você deseja implantar continuamente. Ao terminar, clique em **Continuar**.

Na página **Configurar**, na seção **Build**, especifique a estrutura de linguagem que o Azure DevOps Services deve usar para executar as tarefas de build para seu repositório selecionado. Ao terminar, clique em **Continuar**.

Na página **Teste**, escolha se deseja habilitar testes de carga. Em seguida, clique em **Continuar**.

Dependendo do [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do Serviço de Aplicativo, talvez você possa ver uma página **Implantar no preparo**. Escolha se deseja [habilitar slots de implantação](deploy-staging-slots.md) e clique em **Continuar**. 

### <a name="finish-configuration"></a>Concluir configuração

Na página **Resumo**, verifique as opções e clique em **Concluir**.

Quando a configuração for concluída, novas confirmações no repositório selecionado serão implantadas continuamente em seu aplicativo do Serviço de Aplicativo.

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Para desabilitar a implantação contínua, navegue até a página de aplicativo do Serviço de Aplicativo no [Portal do Azure](https://portal.azure.com).

No menu esquerdo, clique em **Centro de Implantação** > **GitHub** ou **Azure DevOps Services** ou **BitBucket** > **Desconectar**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Como investigar problemas comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Como usar o PowerShell para o Azure]
* [Documentação do Git]
* [Kudu do Projeto](https://github.com/projectkudu/kudu/wiki)
* [Usar o Azure para gerar automaticamente um pipeline de CI/CD para implantar um aplicativo ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Como usar o PowerShell para o Azure]: /powershell/azureps-cmdlets-docs
[Documentação do Git]: https://git-scm.com/documentation

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Introdução às soluções do Azure DevOps Services]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
