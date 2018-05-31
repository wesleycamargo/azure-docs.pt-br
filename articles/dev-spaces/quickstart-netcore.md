---
title: Criar um ambiente de desenvolvimento Kubernetes na nuvem | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 279b7a8c20717668c0ff4be541e9168e2d8706fd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361569"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-net-core-and-vs-code"></a>Tutorial: criar um ambiente de desenvolvimento Kubernetes com o Azure Dev Spaces (.NET Core e VS Code)


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Agora você está pronto para criar um ambiente de desenvolvimento baseado em Kubernetes no Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu ambiente de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Se você já tiver a CLI do Azure instalada, verifique se está usando a versão 2.0.32 ou superior.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Você pode começar a desenvolver o código enquanto aguarda a criação do cluster.

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core
Se tiver o [.NET Core](https://www.microsoft.com/net) instalado, você pode criar rapidamente um aplicativo Web do ASP.NET Core em uma pasta chamada `webfrontend`.

```cmd
   dotnet new mvc --name webfrontend
```

Ou **baixar o código de exemplo do GitHub** navegando até https://github.com/Azure/dev-spaces e selecionando **Clonar ou Baixar** para baixar o repositório do GitHub em seu ambiente local. O código para este guia está em `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Atualizar um arquivo de conteúdo
O Azure Dev Spaces não serve apenas para executar o código em Kubernetes; ele também serve para permitir que você veja as alterações de código entrarem em vigor de forma rápida e iterativa em um ambiente Kubernetes na nuvem.

1. Localize o arquivo `./Views/Home/Index.cshtml` e edite o HTML. Por exemplo, altere a linha 70 que lê `<h2>Application uses</h2>` para algo como: `<h2>Hello k8s in Azure!</h2>`
1. Salve o arquivo. Em seguida, na janela do Terminal, você verá uma mensagem informando que um arquivo no contêiner em execução foi atualizado.
1. Volte para o navegador e atualize a página. Você verá a página da Web exibir o HTML atualizado.

O que aconteceu? Edições em arquivos de conteúdo, como HTML e CSS, não exigem a recompilação em um aplicativo Web do .NET Core e, portanto um comando `azds up` ativo sincroniza automaticamente os arquivos de conteúdo modificados no contêiner em execução no Azure. Assim, você pode ver suas edições de conteúdo imediatamente.

## <a name="update-a-code-file"></a>Atualizar um arquivo de código
A atualização dos arquivos de código requer um pouco mais de trabalho, pois um aplicativo .NET Core precisa recompilar e produzir os binários do aplicativo atualizado.

1. Na janela do terminal, pressione `Ctrl+C` (para parar `azds up`).
1. Abra o arquivo de código chamado `Controllers/HomeController.cs` e edite a mensagem exibida na página Sobre: `ViewData["Message"] = "Your application description page.";`
1. Salve o arquivo.
1. Execute `azds up` na janela do terminal. 

Esse comando recria a imagem de contêiner e reimplanta o gráfico Helm. Para ver as alterações de código entrando em vigor no aplicativo em execução, vá ao menu Sobre no aplicativo Web.


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

Assim como acontece com o comando `up`, o código está sincronizado com o ambiente de desenvolvimento e um contêiner é criado e implantado no Kubernetes. Desta vez, é claro, o depurador está anexado ao contêiner remoto.

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

**Agora você tem um método para iterar em código rapidamente e depurar diretamente no Kubernetes!**

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Trabalhando com vários contêineres e desenvolvimento em equipe](get-started-netcore.md#call-a-service-running-in-a-separate-container)