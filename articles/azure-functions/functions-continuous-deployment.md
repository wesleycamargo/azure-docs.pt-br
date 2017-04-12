---
title: "Implantação contínua do Azure Functions | Microsoft Docs"
description: "Use recursos de implantação contínua do Serviço de Aplicativo do Azure para publicar seu Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: dbcee4d57448c6f25de24d5995b85849d6d82f77
ms.lasthandoff: 04/04/2017


---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions
O Azure Functions torna fácil configurar a implantação contínua para seu aplicativo de função. O Functions usa a integração do Serviço de Aplicativo do Azure com BitBucket, Dropbox, GitHub e VSTS (Visual Studio Team Services) para habilitar um fluxo de trabalho de implantação contínua em que o Azure obtém as atualizações para o código de funções quando elas são publicadas em um desses serviços. Se você for iniciante no Azure Functions, comece pela [Visão geral do Azure Functions](functions-overview.md).

A implantação contínua é uma ótima opção para projetos nos quais várias contribuições frequentes são integradas. Ele também permite manter o controle do código-fonte no código de funções. As seguintes fontes de implantação têm suporte atualmente:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* [Repositório local Git](../app-service-web/app-service-deploy-local-git.md)
* Repositório externo Git
* [GitHub]
* Repositório externo do Mercurial
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

As implantações são configuradas para cada aplicativo de função. Após a implantação contínua ser habilitada, o acesso ao código de função no portal é definido como *somente leitura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implantação contínua

Você deve ter a fonte de implantação configurada e o código de funções na fonte de implantação antes de configurar a implantação contínua. Em uma implantação de aplicativo de funções específica, cada função reside em um subdiretório nomeado, em que o nome do diretório é o nome da função. Essa estrutura de pastas é, essencialmente, o código do site. 

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua
Use o procedimento a seguir para configurar a implantação contínua para um aplicativo de função existente:

1. No aplicativo de função no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Configurar integração contínua** > **Instalação**.
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment.png)
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
    Você também pode obter a folha Implantações do guia de início rápido Funções clicando em **Iniciar do controle de origem**.
2. Na folha **Origem da implantação**, clique em **Escolher fonte**, preencha as informações da origem de implantação escolhida e clique em **OK**.
   
    ![Escolher a fonte de implantação](./media/functions-continuous-deployment/choose-deployment-source.png)

Após a implantação contínua ser configurada, todos os arquivos de alterações na fonte de implantação são copiados para o aplicativo de função e uma implantação completa do site é disparada. O site é reimplantado quando os arquivos da origem são atualizados.

## <a name="deployment-options"></a>Opções de implantação

A seguir estão alguns cenários comuns de implantação:

- [Criar uma implantação de preparo](#staging)
- [Mover as funções existentes para implantação contínua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

Aplicativos de Função ainda não dão suporte a slots de implantação. No entanto, você ainda pode gerenciar implantações de produção e preparo separadas usando a integração contínua.

O processo para configurar e trabalhar com uma implantação de preparo geralmente tem esta aparência:

1. Crie dois aplicativos de função em sua assinatura, um para o código de produção e outro para preparo. 

2. Crie uma fonte de implantação, se você ainda não tiver uma. Este exemplo usa [GitHub].

3. Para o aplicativo de funções de produção, conclua as etapas acima em **Configurar a implantação contínua** e defina a ramificação de implantação para a ramificação mestra do repositório GitHub.
   
    ![Escolher a ramificação de implantação](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita essa etapa para o aplicativo de funções de preparo, mas escolha a ramificação de preparo em vez do repositório GitHub. Se sua fonte de implantação não der suporte à ramificação, use uma pasta diferente.

5. Faça atualizações no código na ramificação de preparo ou na pasta e verifique se as alterações são refletidas na implantação de preparo.

6. Depois de testar, mescle alterações da ramificação de preparo na ramificação mestre. Isso disparará a implantação para o aplicativo de função de produção. Se sua fonte de implantação não der suporte a ramificações, substitua os arquivos na pasta de produção pelos arquivos da pasta de preparo.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua
Quando houver funções existentes que você criou e manteve no portal, será preciso baixar os arquivos de código de função existentes usando o FTP ou o repositório Git local para poder configurar a implantação contínua conforme descrito acima. Você pode fazer isso nas configurações do Serviço de Aplicativo para seu aplicativo de função. Depois que os arquivos forem baixados, você poderá carregá-los na fonte de implantação contínua escolhida.

> [!NOTE]
> Depois de configurar a integração contínua, você não poderá mais editar os arquivos de origem no portal de Funções.

- [Como configurar credenciais de implantação](#credentials)
- [Como baixar arquivos usando FTP](#downftp)
- [Como baixar arquivos usando o repositório Git local](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Como: configurar credenciais de implantação
Para poder baixar arquivos do aplicativo de funções com o FTP ou repositório Git local, você deve configurar suas credenciais para acessar o site, o que pode ser feito por meio do portal. As credenciais são definidas no nível do aplicativo de Função.

1. No seu aplicativo de funções no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Ir para configurações do Serviço de Aplicativo** > **Credenciais de implantação**.
   
    ![Definir credenciais de implantação local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Digite um nome de usuário e senha e clique em **Salvar**. Agora você pode usar essas credenciais para acessar seu aplicativo de função do FTP ou do repositório Git interno.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Como: baixar arquivos usando FTP

1. No aplicativo de função no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Ir para configurações do Serviço de Aplicativo** > **Propriedades** e copie os valores para **Usuário de FTP/Implantação**, **Nome do Host FTP** e **Nome de Host FTPS**.  

    O **Usuário de FTP/Implantação** deve ser inserido conforme exibido no portal, incluindo o nome do aplicativo, a fim de fornecer o contexto adequado para o servidor FTP.
   
    ![Obter as informações de implantação](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. No cliente de FTP, use as informações de conexão coletadas para se conectar ao aplicativo e baixar os arquivos de origem para suas funções.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-the-local-git-repository"></a>Como: baixar arquivos usando o repositório Git local

1. No aplicativo de função no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Configurar integração contínua** > **Instalação**.

2. Na folha Implantações, clique em **Escolher fonte** e **Repositório Git Local** e clique em **OK**.

3. Clique em **Ir para configurações de Serviço de Aplicativo** > **Propriedades** e anote o valor da URL do Git. 
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clone o repositório em seu computador local usando uma linha de comando com reconhecimento do Git ou sua ferramenta de Git favorita. O comando Git clone é semelhante ao seguinte:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Busque arquivos de seu aplicativo de função para o clone no computador local, como no seguinte exemplo:
   
        git pull origin master
   
    Caso solicitado, forneça o nome de usuário e a senha para a implantação do aplicativo de função.  

[GitHub]: https://github.com/

