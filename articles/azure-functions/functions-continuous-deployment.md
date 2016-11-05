---
title: Implantação contínua para Azure Functions | Microsoft Docs
description: Use recursos de implantação contínua do Serviço de Aplicativo do Azure para publicar seu Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga

---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions
O Azure Functions torna fácil configurar a implantação contínua para seu aplicativo de função. As funções aproveitam a integração do Serviço de Aplicativo do Azure com BitBucket, Dropbox, GitHub e VSTS (Visual Studio Team Services) para habilitar um fluxo de trabalho de implantação contínua em que o Azure obtém as atualizações para o código de funções quando elas são publicadas em um desses serviços. Se você for iniciante no Azure Functions, comece pela [Visão geral do Azure Functions](functions-overview.md).

A implantação contínua é uma ótima opção para projetos nos quais várias contribuições frequentes são integradas. Ele também permite manter o controle do código-fonte no código de funções. As seguintes fontes de implantação têm suporte atualmente:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://bitbucket.org/)
* [Repositório local Git](../app-service-web/app-service-deploy-local-git.md)
* Repositório externo Git
* [GitHub]
* Repositório externo do Mercurial
* [OneDrive](https://onedrive.live.com/)
* Visual Studio Team Services

As implantações são configuradas para cada aplicativo de função. Após a implantação contínua ser habilitada, o acesso ao código de função no portal é definido como *somente leitura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implantação contínua
Você deve ter a fonte de implantação configurada e o código de funções na fonte de implantação antes de configurar a implantação contínua. Em uma implantação de aplicativo de função específica, cada função reside em um subdiretório nomeado, em que o nome do diretório é o nome da função. Essa estrutura de pastas é, essencialmente, o código do site. 

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="setting-up-continuous-deployment"></a>Configurar a implantação contínua
Use o procedimento a seguir para configurar a implantação contínua para um aplicativo de função existente:

1. No aplicativo de função no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Configurar integração contínua** > **Instalação**.
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment.png)
   
    ![Configurar a implantação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
    Você também pode obter a folha Implantações do guia de início rápido Funções clicando em **Iniciar do controle de origem**.
2. Na folha Implantações, clique em **Escolher fonte**, preencha as informações da origem de implantação escolhida e clique em **OK**.
   
    ![Escolher a fonte de implantação](./media/functions-continuous-deployment/choose-deployment-source.png)

Após a implantação contínua ser configurada, todos os arquivos de alterações na fonte de implantação são copiados para o aplicativo de função e uma implantação completa do site é disparada. O site é reimplantado quando os arquivos da origem são atualizados.

## <a name="deployment-options"></a>Opções de implantação
A seguir estão alguns cenários comuns de implantação:

* ### <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

Aplicativos de Função ainda não dão suporte a slots de implantação. No entanto, você ainda pode gerenciar implantações de produção e preparo separadas usando a integração contínua.

O processo para configurar e trabalhar com uma implantação de preparo geralmente tem esta aparência:

1. Crie dois aplicativos de função em sua assinatura, um para o código de produção e outro para preparo. 
2. Crie uma fonte de implantação, se você ainda não tiver uma. Usaremos o [GitHub].
3. Para o aplicativo de função de produção, conclua as etapas acima em **Configurar a implantação contínua** e defina a ramificação de implantação para a ramificação mestra do repositório GitHub.
   
    ![Escolher a ramificação de implantação](./media/functions-continuous-deployment/choose-deployment-branch.png)
4. Repita essa etapa para o aplicativo de função de preparo, mas, dessa vez, escolha a ramificação de preparo no repositório GitHub. Se sua fonte de implantação não der suporte à ramificação, use uma pasta diferente.
5. Faça atualizações no código na ramificação de preparo ou na pasta e verifique se as alterações são refletidas na implantação de preparo.
6. Depois de testar, mescle alterações da ramificação de preparo na ramificação mestre. Isso disparará a implantação para o aplicativo de função de produção. Se sua fonte de implantação não der suporte a ramificações, substitua os arquivos na pasta de produção pelos arquivos da pasta de preparo.

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua
Quando tem funções existentes que criou e manteve no portal, você precisa baixar os arquivos de código de função existentes usando o FTP ou o repositório Git local antes para poder configurar a implantação contínua conforme descrito acima. Você pode fazer isso nas configurações do Serviço de Aplicativo para seu aplicativo de função. Depois que os arquivos forem baixados, você poderá carregá-los na fonte de implantação contínua escolhida.

> [!NOTE]
> Depois de configurar a integração contínua, você não poderá mais editar os arquivos de origem no portal de Funções.
> 
> 

#### <a name="how-to:-configure-deployment-credentials"></a>Como: configurar credenciais de implantação
Para poder baixar arquivos do aplicativo de função, você deve configurar suas credenciais para acessar o site, o que pode ser feito por meio do portal. As credenciais são definidas no nível do aplicativo de Função.

1. No seu aplicativo de funções no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Ir para configurações do Serviço de Aplicativo** > **Credenciais de implantação**.
   
    ![Definir credenciais de implantação local](./media/functions-continuous-deployment/setup-deployment-credentials.png)
2. Digite um nome de usuário e senha e clique em **Salvar**. Agora você pode usar essas credenciais para acessar seu aplicativo de função do FTP ou do repositório Git interno.

#### <a name="how-to:-download-files-using-ftp"></a>Como: baixar arquivos usando FTP
1. No aplicativo de função no [Portal do Azure Functions](https://functions.azure.com/signin), clique em **Configurações do aplicativo de funções** > **Ir para configurações do Serviço de Aplicativo** > **Propriedades** e copie os valores para **Usuário de FTP/Implantação**, **Nome do Host FTP** e **Nome de Host FTPS**.  
   **Usuário de FTP/implantação** deve ser inserido conforme exibido no portal, incluindo o nome do aplicativo, a fim de fornecer o contexto adequado para o servidor FTP.
   
    ![Obter as informações de implantação](./media/functions-continuous-deployment/get-deployment-credentials.png)
2. No cliente de FTP, use as informações de conexão coletadas para se conectar ao aplicativo e baixar os arquivos de origem para suas funções.

#### <a name="how-to:-download-files-using-the-local-git-repository"></a>Como: baixar arquivos usando o repositório Git local
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



<!--HONumber=Oct16_HO2-->


