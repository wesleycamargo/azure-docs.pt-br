---
title: Criar um espaço de desenvolvimento Kubernetes na nuvem usando o .NET Core e o VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: fdaba2afecd453794dbee3b47ef5202700d005d2
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919852"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Introdução ao Azure Dev Spaces com .NET Core

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Agora você está pronto para criar um espaço de desenvolvimento baseado em Kubernetes no Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu espaço de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Seu computador local pode executar Windows, Mac ou Linux. Para o Linux, há suporte para as seguintes distribuições: Ubuntu (18.04, 16.04 e 14.04), Debian 8 e 9, RHEL 7, Fedora 26 +, CentOS 7, openSUSE 42.2 e SLES 12.

Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Se você já tiver a CLI do Azure instalada, verifique se está usando a versão 2.0.43 ou superior.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Você pode começar a desenvolver o código enquanto aguarda a criação do cluster.

## <a name="create-a-web-app-running-in-a-container"></a>Criar um aplicativo Web em execução em um contêiner

Nesta seção, você criará um aplicativo Web ASP.NET Core e o executará em um contêiner no Kubernetes.

### <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core
Se você tiver [.NET Core](https://www.microsoft.com/net) instalado, você pode criar rapidamente um aplicativo Web do ASP.NET Core em uma pasta chamada `webfrontend`.
    
```cmd
dotnet new mvc --name webfrontend
```

Ou, **baixar o código de exemplo do GitHub** navegando até https://github.com/Azure/dev-spaces e selecionando **Clonar ou baixar** para baixar o repositório do GitHub para seu ambiente local. O código para este guia está em `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Atualizar um arquivo de conteúdo
O Azure Dev Spaces não serve apenas para executar o código em Kubernetes; ele também serve para permitir que você veja as alterações de código entrarem em vigor de forma rápida e iterativa em um ambiente Kubernetes na nuvem.

1. Localize o arquivo `./Views/Home/Index.cshtml` e edite o HTML. Por exemplo, altere a linha 70 que lê `<h2>Application uses</h2>` para algo como: `<h2>Hello k8s in Azure!</h2>`
1. Salve o arquivo. Em seguida, na janela do Terminal, você verá uma mensagem informando que um arquivo no contêiner em execução foi atualizado.
1. Volte para o navegador e atualize a página. Você verá a página da Web exibir o HTML atualizado.

O que aconteceu? Edições em arquivos de conteúdo, como HTML e CSS, não exigem a recompilação em um aplicativo Web do .NET Core e, portanto um comando `azds up` ativo sincroniza automaticamente os arquivos de conteúdo modificados no contêiner em execução no Azure. Assim, você pode ver suas edições de conteúdo imediatamente.

### <a name="update-a-code-file"></a>Atualizar um arquivo de código
A atualização dos arquivos de código requer um pouco mais de trabalho, pois um aplicativo .NET Core precisa recompilar e produzir os binários do aplicativo atualizado.

1. Na janela do terminal, pressione `Ctrl+C` (para parar `azds up`).
1. Abra o arquivo de código chamado `Controllers/HomeController.cs` e edite a mensagem exibida na página Sobre: `ViewData["Message"] = "Your application description page.";`
1. Salve o arquivo.
1. Execute `azds up` na janela do terminal. 

Esse comando recompila a imagem de contêiner e reimplanta o gráfico de Helm. Para ver as alterações de código entrarem em vigor no aplicativo em execução, vá ao menu Sobre no aplicativo Web.


Mas existe um *método ainda mais rápido* para desenvolver código, que você irá explorar na próxima seção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contêiner no Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração AZDS
1. Para abrir o modo de exibição Depuração, clique no ícone Depuração na **Barra de Atividades** do lado do VS Code.
1. Selecione **Inicialização do .NET Core (AZDS)** como a configuração de depuração ativa.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Se você não vir comandos do Azure Dev Spaces na Paleta de Comandos, verifique se instalou a extensão do VS Code para o Azure Dev Spaces. Verifique se o espaço de trabalho que você abriu no VS Code é a pasta que contém azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Depurar o contêiner no Kubernetes
Pressione **F5** para depurar seu código no Kubernetes.

Assim como acontece com o comando `up`, o código está sincronizado com o espaço de desenvolvimento e um contêiner é criado e implantado no Kubernetes. Desta vez, é claro, o depurador está anexado ao contêiner remoto.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Defina um ponto de interrupção em um arquivo de código do lado do servidor, por exemplo, dentro da função `Index()` no arquivo de origem `Controllers/HomeController.cs`. A atualização da página do navegador gera o ponto de interrupção.

Você tem acesso completo às informações de depuração exatamente como teria se o código fosse executado localmente, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção etc.

### <a name="edit-code-and-refresh"></a>Editar código e atualizar
Com o depurador ativo, edite o código. Por exemplo, modifique a mensagem da página Sobre em `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Salve o arquivo e, no **painel Ações de depuração**, clique no botão **Atualizar**. 

![](media/get-started-netcore/debug-action-refresh.png)

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o que geralmente levará um tempo considerável, o Azure Dev Spaces recompilará incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

Atualize o aplicativo Web no navegador e vá para a página Sobre. Sua mensagem personalizada deverá aparecer na interface de usuário.

**Agora você tem um método para iterar em código rapidamente e depurar diretamente no Kubernetes!** Em seguida, você verá como você pode criar e chamar um segundo contêiner.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe](team-development-netcore.md)
