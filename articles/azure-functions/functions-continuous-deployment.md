---
title: Implantação contínua do Azure Functions | Microsoft Docs
description: Use os recursos de implantação contínua do serviço de aplicativo do Azure para publicar suas funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943891"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions
O Azure Functions torna mais fácil de implantar seu aplicativo de funções usando a integração contínua. Functions integra-se com repositórios de código principal e fontes de implantação. Essa integração permite que um fluxo de trabalho em que o código de função atualizações feito por meio de uma implantação de gatilho esses serviços do Azure. Se você for novo no Azure Functions, comece com o [visão geral do Azure Functions](functions-overview.md).

Implantação contínua é uma ótima opção para projetos em que você estiver integrando vários e frequentes contribuições. Ele também permite manter o controle do código-fonte em seu código de função. O Azure Functions dá suporte aos seguintes fontes de implantação:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositório externo (Git ou Mercurial)
* [Repositório local Git](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

As implantações são configuradas para cada aplicativo de função. Após a implantação contínua ser habilitada, o acesso ao código de função no portal é definido como *somente leitura*.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para implantação contínua

Antes de configurar a implantação contínua, você deve ter a fonte de implantação configurada e o código de função na fonte de implantação. Em uma implantação de aplicativo de função, cada função está em um subdiretório nomeado, onde o nome do diretório é o nome da função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Para ser capaz de implantar do Azure DevOps, primeiro você deve vincular sua organização do Azure DevOps com sua assinatura do Azure. Para obter mais informações, confira [Configurar cobrança para sua organização do Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua
Utilize o procedimento a seguir para configurar a implantação contínua para um aplicativo de função existente. Essas etapas demonstram a integração com um repositório GitHub, porém etapas semelhantes se aplicam para serviços do Azure DevOps ou outros serviços de implantação.

1. Em seu aplicativo de função na [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **opções de implantação**. 
   
    ![Seleções para abrir as opções de implantação](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Sobre o **implantações** folha, selecione **instalação**.
 
    ![Folha implantações](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sobre o **fonte de implantação** folha, selecione **Escolher fonte**. Preencha as informações para sua fonte de implantação escolhida e, em seguida, selecione **Okey**.
   
    ![Escolher uma fonte de implantação](./media/functions-continuous-deployment/choose-deployment-source.png)

Depois de configurar a implantação contínua, todas as alterações de arquivo na fonte de implantação são copiadas para o aplicativo de funções e uma implantação completa do site é disparada. O site é reimplantado quando os arquivos da origem são atualizados.

## <a name="deployment-scenarios"></a>Cenários de implantação

Cenários comuns de implantação incluem a criação de uma implantação de preparo e mover as funções existentes para implantação contínua.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

Aplicativos de função ainda não dão suporte a slots de implantação. Mas você ainda pode gerenciar implantações de produção e preparo separadas usando a integração contínua.

O processo para configurar e trabalhar com uma implantação de preparo geralmente tem esta aparência:

1. Crie dois aplicativos de funções em sua assinatura: um para o código de produção e outro para preparação. 

1. Crie uma fonte de implantação, se você ainda não tiver uma. Este exemplo usa [GitHub].

1. Para o aplicativo de funções de produção, conclua as etapas acima em [Configurar a implantação contínua](#set-up-continuous-deployment) e defina a ramificação de implantação para a ramificação mestra do repositório GitHub.
   
    ![Seleções para escolher uma ramificação de implantação](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Repita a etapa 3 para o aplicativo de função de preparo, mas escolha a ramificação de preparo em vez disso, no seu repositório GitHub. Se sua fonte de implantação não der suporte à ramificação, use uma pasta diferente.
    
1. Fazer atualizações em seu código na ramificação de preparo ou pasta e, em seguida, verifique se a implantação de preparo reflete essas alterações.

1. Depois de testar, mescle alterações da ramificação de preparo na ramificação mestre. Essa mesclagem disparará a implantação para o aplicativo de função de produção. Se sua fonte de implantação não der suporte a ramificações, substitua os arquivos na pasta de produção pelos arquivos da pasta de preparo.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua
Quando você tem funções existentes que você criou e manteve no portal, você precisa baixar os arquivos de código de função por meio de FTP ou repositório Git local antes de você pode configurar a implantação contínua conforme descrito anteriormente. Você pode fazer isso nas configurações do serviço de aplicativo do Azure para seu aplicativo de funções. Depois de baixar os arquivos, você pode carregá-los à sua fonte de implantação contínua escolhida.

> [!NOTE]
> Depois de configurar a integração contínua, você não pode editar seus arquivos de origem no portal do Functions.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>configurar credenciais de implantação
Antes de você pode baixar arquivos de seu aplicativo de funções usando o FTP ou um repositório Git local, você deve configurar suas credenciais para acessar o site. As credenciais são definidas no nível do aplicativo de função. Utilize as etapas a seguir para definir as credenciais de implantação no portal do Azure:

1. Em seu aplicativo de função na [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **credenciais de implantação**.
   
1. Insira um nome de usuário e senha e, em seguida, selecione **salvar**. 

   ![Seleções para definir as credenciais de implantação local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Agora você pode usar essas credenciais para acessar seu aplicativo de função do FTP ou do repositório Git interno.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Baixar arquivos usando FTP

1. Em seu aplicativo de função na [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **propriedades**. Em seguida, copie os valores para **usuário FTP/implantação**, **nome do Host FTP**, e **nome de Host FTPS**.  

   O **Usuário de FTP/Implantação** deve ser inserido conforme exibido no portal, incluindo o nome do aplicativo, a fim de fornecer o contexto adequado para o servidor FTP.
   
   ![Seleções para obter as informações da implantação](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. De seu cliente FTP, use as informações de conexão coletadas para se conectar ao seu aplicativo e baixar os arquivos de origem para suas funções.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Baixar arquivos usando um repositório Git local

1. Em seu aplicativo de função na [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **opções de implantação**. 
   
    ![Seleções para abrir as opções de implantação](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Em seguida, na **implantações** folha, selecione **instalação**.
 
    ![Folha implantações](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sobre o **fonte de implantação** folha, selecione **repositório Git Local** > **Okey**.

1. Na **recursos da plataforma**, selecione **propriedades** e observe o valor da URL do Git. 
   
    ![Seleções para obter a URL do Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Clone o repositório em seu computador local usando um prompt de comando de reconhecimento do Git ou sua ferramenta Git favorita. O comando Git clone é semelhante ao seguinte:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Busque arquivos de seu aplicativo de função para o clone no computador local, como no seguinte exemplo:
   
        git pull origin master
   
    Se solicitado, forneça as [credenciais de implantação configuradas](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
