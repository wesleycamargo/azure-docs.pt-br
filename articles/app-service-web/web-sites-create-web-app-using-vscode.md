---
title: Criar um aplicativo Web ASP.NET 5 no Visual Studio Code
description: "Este tutorial mostra como criar um aplicativo Web ASP.NET 5 usando o código do Visual Studio."
services: app-service\web
documentationcenter: .net
author: erikre
manager: wpickett
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4f1bf0f15a373dfe60d923f017a96784424d6563


---
# <a name="create-an-aspnet-5-web-app-in-visual-studio-code"></a>Criar um aplicativo Web ASP.NET 5 no Visual Studio Code
## <a name="overview"></a>Visão geral
Este tutorial mostra como criar um aplicativo Web ASP.NET 5 usando [Código do Visual Studio (Código do VS)](http://code.visualstudio.com//Docs/whyvscode) e implantá-lo no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). 

> [!NOTE]
> Embora este artigo esteja relacionado a aplicativos Web, ele também serve para aplicativos de API e aplicativos móveis. 
> 
> 

O ASP.NET 5 é uma reestruturação significativa do ASP.NET. ASP.NET 5 é uma nova estrutura de código-fonte aberto entre plataformas para criar modernos aplicativos em nuvem da Web usando o .NET. Para obter mais informações, consulte [Introdução ao ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Para obter informações sobre aplicativos Web do Serviço de Aplicativo do Azure, consulte [Visão geral de aplicativos Web](app-service-web-overview.md).

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>Pré-requisitos
* Instale o [Código do VS](http://code.visualstudio.com/Docs/setup).
* Instale o [Node.js](http://nodejs.org) - o Node.js é uma plataforma para a compilação rápida e escalonável de aplicativos de servidor com o uso do JavaScript. O nó é o tempo de execução (Nó) e [npm](http://www.npmjs.com/) é o Gerenciador de Pacotes para os módulos do Nó. Você usará o npm para criar o scaffolding de um aplicativo Web ASP.NET 5 neste tutorial.
* Instalar o Git – você pode instalá-lo de um destes locais: [Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads). Se você for iniciante no Git, escolha [git-scm.com](http://git-scm.com/downloads) e selecione a opção para **Usar o Git no Prompt de Comando do Windows**. Depois de instalar o Git, também será necessário definir o nome de usuário e email do Git, pois eles são necessários posteriormente no tutorial (ao realizar uma confirmação do Código do VS).  

## <a name="install-aspnet-5-and-dnx"></a>Instalar o ASP.NET 5 e DNX
O ASP.NET 5/DNX (o Ambiente de Execução do .NET) é uma pilha enxuta do .NET para criar aplicativos Web e de nuvem modernos e que são executados em OS X, Linux e Windows. Ele foi criado do zero para fornecer uma estrutura de desenvolvimento otimizada para aplicativos que são implantados na nuvem ou então executados localmente. Ele consiste em componentes modulares com sobrecarga mínima, para que você mantenha a flexibilidade durante a construção de suas soluções.

Este tutorial é projetado para começar a criar aplicativos com as versões de desenvolvimento ASP.NET 5 e DNX mais recentes. As instruções a seguir são específicas do Windows. Para obter instruções de instalação mais detalhadas para OS X, Linux e Windows, consulte [Instalação do ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx). 

1. Para instalar o Gerenciador de Versão do .NET (DNVM) no Windows, abra um prompt de comando e execute o comando a seguir.
   
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
   
    Isso baixará o script DNVM e o colocará em seu diretório de perfil do usuário. 
2. **Reinicie o Windows** para concluir a instalação do DNVM. 
   
    Depois de reiniciar o Windows, é possível abrir o prompt de comando para verificar o local do DNVM digitando o seguinte:
   
        where dnvm
   
    O prompt de comando mostrará um caminho semelhante ao seguinte.
   
    ![local dnvm](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)
3. Agora que você tem o DNVM, é preciso usá-lo para baixar o DNX a fim de executar os aplicativos. Execute o seguinte no prompt de comando:
   
        dnvm upgrade
   
    Verifique o DNVM e exiba o tempo de execução ativo digitando o seguinte no prompt de comando:
   
        dnvm list
   
    O prompt de comando mostrará os detalhes do tempo de execução ativo.
   
    ![local dnvm](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)
   
    Se mais de um tempo de execução do DNX estiver listado, você poderá optar por inserir o seguinte (ou uma versão mais recente) no prompt de comando para definir o tempo de execução do DNX ativo. Defina-o como a mesma versão que é usada pelo gerador do ASP.NET 5 quando você criar seu aplicativo Web mais tarde neste tutorial. *Você não precisará alterar o tempo de execução ativo se ele estiver definido como o mais recente disponível.*
   
        dnvm use 1.0.0-update1 –p

> [!NOTE]
> Para obter instruções de instalação mais detalhadas para OS X, Linux e Windows, consulte [Instalação do ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx). 
> 
> 

## <a name="create-the-web-app"></a>Criar o aplicativo Web
Esta seção mostra como criar o scaffolding de um novo aplicativo Web do ASP.NET do aplicativo. Você usará o gerenciador de pacotes do nó (npm) para instalar o [Yeoman](http://yeoman.io/) (ferramenta de scaffolding do aplicativo – o equivalente em código VS à operação do Visual Studio **Arquivo > Novo Projeto**), [Grun](http://gruntjs.com/)t (executor de tarefa JavaScript) e [Bower](http://bower.io/) (gerenciador de pacotes no cliente). 

1. Abra um prompt de comando com direitos de administrador e navegue até o local onde você deseja criar seu projeto ASP.NET. Por exemplo, crie um diretório *vscodeprojects* na raiz da unidade C:\.
2. Digite o seguinte no prompt de comando para instalar as ferramentas de suporte e o Yeoman.
   
        npm install -g yo grunt-cli generator-aspnet bower
   
   > [!NOTE]
   > Você pode receber um aviso sugerindo que sua versão do npm está desatualizada. Esse aviso não deve afetar este tutorial.
   > 
   > 
3. Digite o seguinte no prompt de comando para criar a pasta do projeto e o scaffolding do aplicativo.
   
        yo aspnet
4. Use as teclas de direção para selecionar o tipo **Básico de Aplicativo Web** no menu do gerador do ASP.NET 5 e pressione **&lt;Enter>**.
   
    ![Yoman - gerador do ASP.NET 5](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)
5. Defina o nome de seu novo aplicativo Web ASP.NET como **SampleWebApp**. Como esse nome é usado em todo o tutorial, se você selecionar um nome diferente, precisará substituir cada ocorrência de **SampleWebApp**. Ao pressionar **&lt;Enter>**, o Yeoman criará uma nova pasta chamada **SampleWebApp** e os arquivos necessários para o seu novo aplicativo.
6. No prompt de comando, altere os diretórios para a nova pasta de projeto:
   
        cd SampleWebApp
7. Também no prompt de comando, para instalar os pacotes NuGet necessários para executar o aplicativo, digite o seguinte comando:
   
        dnu restore
8. Abra o código do VS digitando o seguinte no prompt de comando:
   
        code .

## <a name="run-the-web-app-locally"></a>Executar o aplicativo Web localmente
Agora que criou o aplicativo Web e recuperou todos os pacotes do NuGet para o aplicativo, você pode executar o aplicativo Web localmente.

1. Na **Paleta de Comandos** do código do VS, digite o seguinte para mostrar as opções de comando de execução disponíveis:
   
        dnx: Run Command
   
   > [!NOTE]
   > Se o servidor do Omnisharp não estiver sendo executado, ele será inicializado. Digite novamente o comando acima.
   > 
   > 
   
    Em seguida, selecione o seguinte comando para executar seu aplicativo Web:
   
        dnx web - (SampleWebApp)
   
    A janela de comando será exibida se o aplicativo foi iniciado. Se a janela de comando não exibir esta mensagem, verifique o canto inferior esquerdo do Código do VS em busca de erros em seu projeto.
   
   > [!NOTE]
   > Emitir um comando na **Paleta de Comandos** exige um caractere **>** no início da linha de comando. Você pode exibir os detalhes relacionados ao comando **web** no arquivo *project.json* .   
   > Se o comando não for exibido ou não estiver disponível, talvez seja necessário instalar a extensão do C#. Execute `>Extensions: Install Extension` e `ext install c#` para instalar as extensões do C#.
   > 
   > 
2. Abra uma janela de navegador e navegue até a URL a seguir.
   
    **http://localhost:5000**
   
    A página padrão do aplicativo Web será exibida da maneira a seguir.
   
    ![Aplicativo Web local em um navegador](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. Feche o navegador. Na **Janela Comando**, pressione **Ctrl + C** para encerrar o aplicativo e feche a **Janela Comando**. 

## <a name="create-a-web-app-in-the-azure-portal"></a>Criar um aplicativo Web no Portal do Azure
As etapas a seguir o guiarão ao longo da criação de um aplicativo Web no Portal do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO** na parte superior esquerda do Portal.
3. Clique em **Aplicativos Web > Aplicativo Web**.
   
    ![Novo aplicativo Web do Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)
4. Insira um valor para **Nome**, como **SampleWebAppDemo**. Observe que esse nome precisa ser exclusivo, e o portal imporá isso quando você tentar inserir o nome. Portanto, se você selecionar um valor diferente, precisará substituir esse valor para cada ocorrência do **SampleWebAppDemo** que você vê neste tutorial. 
5. Selecione um **Plano de Serviço de Aplicativo** existente ou crie um novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções. Para obter mais informações sobre planos de serviço de aplicativo, consulte o artigo [Visão geral detalhada de planos de serviço de aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
    ![Folha de novo aplicativo Web do Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)
6. Clique em **Criar**.
   
    ![folha de aplicativo Web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Habilitar a publicação de Git para o novo aplicativo Web
O Git é um sistema de controle de versão distribuído que você pode usar para implantar seu aplicativo Web do Serviço de Aplicativo do Azure. Você armazenará o código que você escreve para seu aplicativo Web em um repositório Git local e você implantará seu código no Azure enviando-o por push para um repositório remoto.   

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Procurar**.
3. Clique em **aplicativos Web** para exibir uma lista dos aplicativos Web associados à sua assinatura do Azure.
4. Selecione o aplicativo Web criado neste tutorial.
5. Na folha de seu aplicativo Web, clique em **Configurações** > **Implantação contínua**. 
   
    ![host de aplicativo Web do Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)
6. Clique em **Escolher fonte > Repositório Git local**.
7. Clique em **OK**.
   
    ![Repositório Git Local do Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)
8. Se você não configurou previamente as credenciais de implantação para publicação de um Aplicativo Web ou outro aplicativo de Serviço de Aplicativo, configure-as agora:
   
   * Clique em **Configurações** > **Credenciais de implantação**. A folha **Definir credenciais de implantação** será exibida.
   * Digite um nome de usuário e senha.  Você precisará dessa senha posteriormente ao configurar o Git.
   * Clique em **Salvar**.
9. Na folha de seu aplicativo Web, clique em **Configurações > Propriedades**. A URL do repositório Git remoto na qual você implantará é mostrada em **GIT URL**.
10. Copie o valor de **URL do GIT** para uso posterior no tutorial.
    
    ![URL Git do Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Publicar seu aplicativo Web no serviço de aplicativo do Azure
Nesta seção, você criará um repositório do Git local e enviará esse repositório por push ao Azure para implantar seu aplicativo Web no Azure.

1. No código do VS, selecione a opção **Git** na barra de navegação à esquerda.
   
    ![Ícone do Git no Código do VS](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. Selecione **Inicializar repositório git** para assegurar que o espaço de trabalho está sob controle do código-fonte git. 
   
    ![Inicializar Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. Abra a Janela Comando e altere os diretórios para o diretório do seu aplicativo Web. Digite o seguinte comando:
   
        git config core.autocrlf false
   
    Esse comando previne um problema de texto envolvendo as terminações CRLF e LF.
4. No Código do VS, adicione uma mensagem de confirmação e clique no ícone de verificação **Confirmar Tudo** .
   
    ![Confirmar Tudo do Git](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)
5. Após a conclusão do processamento do Git, você verá que não há nenhum arquivo listado na janela do Git em **Alterações**. 
   
    ![Sem alterações do Git](./media/web-sites-create-web-app-using-vscode/no-changes.png)
6. Retorne à Janela do Comando em que o prompt de comando aponta para o diretório em que o aplicativo Web está localizado.
7. Crie uma referência remota para enviar atualizações para seu aplicativo Web usando a URL do Git (terminando em ".git") que você copiou anteriormente.
   
        git remote add azure [URL for remote repository]
8. Configure o Git para salvar suas credenciais localmente para que elas possam ser acrescentadas automaticamente aos seus comandos por push gerados no VS Code.
   
        git config credential.helper store
9. Envie as alterações por push ao Azure usando o comando a seguir. Depois desse push inicial no Azure, você poderá executar todos os comandos por push no VS Code. 
   
        git push -u azure master
   
    Será solicitada a senha que você criou anteriormente no Azure. **Observação: a senha não será visível.**
   
    A saída deste comando termina com uma mensagem de que a implantação foi bem-sucedida.
   
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master

> [!NOTE]
> Se fizer alterações ao seu aplicativo, você poderá publicá-las de novo diretamente no Código do VS usando a funcionalidade interna do Git selecionando a opção **Confirmar Tudo** seguida da opção **Enviar por Push**. Você encontrará a opção **Envio por Push** disponível no menu suspenso ao lado de **Confirmar Tudo** e dos botões **Atualizar**.
> 
> 

Se precisa colaborar em um projeto, você deve considerar envios por push ao GitHub entre os envios por push ao Azure.

## <a name="run-the-app-in-azure"></a>Executar o aplicativo no Azure
Agora que você implantou seu aplicativo Web, vamos executar o aplicativo enquanto ele está hospedado no Azure. 

Isso pode ser feito de duas maneiras:

* Abra um navegador e digite o nome do aplicativo Web da maneira a seguir.   
  
        http://SampleWebAppDemo.azurewebsites.net
* No Portal do Azure, localize a folha de seu aplicativo Web e clique em **Procurar** para exibi-lo 
* no navegador padrão.

![Aplicativo Web do Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>Resumo
Neste tutorial, você aprendeu a criar um aplicativo Web no código do VS e implantá-lo no Azure. Para saber mais sobre o Código do VS, confira o artigo [Por que o Visual Studio Code?](https://code.visualstudio.com/Docs/) Para obter informações sobre os aplicativos Web do Serviço de Aplicativo, consulte [Visão geral de Aplicativos Web](app-service-web-overview.md). 




<!--HONumber=Nov16_HO3-->


