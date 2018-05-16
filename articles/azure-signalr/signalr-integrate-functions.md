---
title: Tutorial para integrar funções do Azure ao Serviço Azure SignalR | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure Functions com o Serviço Azure SignalR
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Tutorial: Integrar o Azure Functions ao Serviço Azure SignalR

Este tutorial baseia-se no aplicativo de sala de chat criado em tutoriais anteriores. Se você não tiver completado [Criar uma sala de chat com SignalR Service](signalr-quickstart-dotnet-core.md) e [Autenticação do Serviço Azure SignalR](./signalr-authenticate-oauth.md), conclua esses exercícios primeiro. 

Um cenário comum com aplicativos em tempo real é nas atualizações de conteúdo originadas de um servidor que serão publicadas para clientes da web. O [Azure Functions](../azure-functions/functions-overview.md) é um ótimo candidato para gerar essas atualizações de conteúdo. Um importante benefício de usar as funções do Azure é que você pode executar seu código sob demanda sem se preocupar com a arquitetura de um aplicativo inteiro ou a infraestrutura para executá-lo. Você só paga pelo tempo em que o código for executado.  

Normalmente, esse cenário representaria um problema ao tentar usar o SignalR porque o SignalR tenta manter uma conexão entre o cliente e o servidor para enviar atualizações de conteúdo. Como o código só é executado sob demanda, uma conexão não pode ser mantida. No entanto, o Serviço Azure SignalR pode dar suporte a esse cenário, pois ele gerencia conexões para você em tempo de execução.

Neste tutorial, você usará o Azure Functions para gerar mensagens usando uma função de temporizador no início de cada minuto. A função publicará as mensagens para todos os clientes da sala de chat criada nos tutoriais anteriores. Para obter mais informações sobre funções de temporizador, consulte [Função de Temporizador](../azure-functions/functions-create-scheduled-function.md).

Você pode usar qualquer editor de código para concluir as etapas deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente nas plataformas Windows, macOS e Linux.

O código para este tutorial está disponível para download no [repositório AzureSignalR-samples do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Aplicativo de chat com mensagens do servidor](./media/signalr-integrate-functions/signalr-functions-complete.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova função de Temporizador com o Azure Functions usando a CLI do Azure.
> * Configurar a função de temporizador para implantação do repositório Git local.
> * Conectar o temporizador ao Serviço SignalR para enviar atualizações a cada minuto

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>pré-requisitos

Para concluir o tutorial, você deve ter os pré-requisitos a seguir:

* [Git](https://git-scm.com/)
* [SDK do .NET Core](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Faça o download ou clone o repositório [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) do github.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve criar um aplicativo de função para definir o ambiente de execução para suas funções. O aplicativo de funções também permite agrupar várias funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos. Para obter mais informações, consulte [Criar sua primeira função usando a CLI do Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md).

Nesta seção, você usará o Azure Cloud Shell para criar um novo aplicativo de função do Azure configurado para a implantação de um repositório Git local. 

Ao criar os recursos do aplicativo de funções, você deve criá-los no mesmo grupo de recursos usado nos tutoriais anteriores. Essa abordagem facilita o gerenciamento de todos os recursos de tutorial.

Copie o script abaixo em seu editor de texto e substitua os valores dos parâmetros de variáveis pelos valores dos seus recursos. Copie e cole o script atualizado no Azure Cloud Shell e pressione **Enter** para criar uma conta de armazenamento e o aplicativo de funções.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parâmetro | DESCRIÇÃO |
| -------------------- | --------------- |
| ResourceGroupName | Este nome de grupo de recursos foi sugerido nos tutoriais anteriores. É recomendável manter todos os recursos de tutorial agrupados juntos. Use o mesmo nome de grupo de recursos que é usado na seção anterior. | 
| location | Atualize essa variável para o mesmo local que você usou para criar o grupo de recursos nos tutoriais anteriores. | 
| functionappName | Atualize essa variável com um nome exclusivo para o novo aplicativo de funções. Por exemplo, signalrfunctionapp22665120. | 
| storageAccountName | Insira um nome para que a nova conta de armazenamento mantenha as configurações e o código do aplicativo de funções. | 



## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

Nesta seção, você configurará o aplicativo de funções com uma configuração de aplicativo que contenha a cadeia de conexão para o recurso do Serviço Azure SignalR. O código de função usará essa configuração para se conectar e publicar mensagens na sala de chat. Você também configurará o aplicativo de funções para a implantação de um repositório Git local.

Copie o script abaixo e substitua os valores dos parâmetros. Cole o script atualizado no Azure Cloud Shell e aperte **Enter**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parâmetro | DESCRIÇÃO |
| -------------------- | --------------- |
| ResourceGroupName | Este nome de grupo de recursos foi sugerido nos tutoriais anteriores. É recomendável manter todos os recursos de tutorial agrupados juntos. Use o mesmo nome de grupo de recursos que é usado na seção anterior. | 
| functionappName | Atualize essa variável com um nome exclusivo para o novo aplicativo de funções. Por exemplo, signalrfunctionapp22665120. | 
| connstring | Insira a cadeia de conexão para seu recurso do Serviço SignalR. Você pode recuperar essa cadeia de conexão de sua página de recursos do Serviço SignalR no Portal do Azure clicando em **Chaves** em **CONFIGURAÇÕES**. | 



Anote a URL de implantação do Git retornada do último comando. Você usará essa URL para implantar o código de função.


## <a name="the-timer-function"></a>A função de temporizador

O exemplo de função de temporizador está localizado no diretório */samples/Timer* do download, ou clone do repositório github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). O código de função de temporizador está localizado em *TimerFunction.cs*. Esse código usa a cadeia de conexão armazenada com a chave de configuração padrão (*Azure:SignalR:ConnectionString*) para criar um proxy para o hub. Como o código de função está em execução no servidor, não há nenhum motivo para exigir autenticação como cliente comum. O código é confiável e pode usar a cadeia de conexão. Usando esse proxy do hub, o código de função pode chamar qualquer um dos métodos que você definiu em seu hub. O código chama o método `BroadcastMessage` para publicar uma mensagem contendo a hora atual quando o gatilho acionou.

O gatilho para o código de função é um *timerTrigger*, definido nas associações em *TimerFunction/function.json*. Ele inclui uma [expressão CRON](https://wikipedia.org/wiki/Cron#CRON_expression) para definir o gatilho do temporizador para acionar no início de cada minuto.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Criando a função de temporizador

Use a [interface de linha de comando (CLI) .NET Core](https://docs.microsoft.com/dotnet/core/tools/) nas etapas a seguir para criar a função e prepará-la para implantação:

1. Navegue até o subdiretório */samples/Timer* do download, ou clone do repositório github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Restaure os pacotes do NuGet usando o seguinte comando:

        dotnet restore

3. Crie o aplicativo de função de *Temporizador* usando o seguinte comando:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Criar e implantar o repositório Git local

1. Em um Git Shell, navegue até o subdiretório de compilação, */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Inicialize o diretório como um novo repositório Git usando o seguinte comando:

        git init

3. Adicione uma nova confirmação para todos os arquivos no diretório de compilação.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Adicione um ponto de extremidade remoto para a URL de implantação do Git anotada durante a configuração do seu aplicativo de funções:

        git remote add Azure <enter your Git deployment URL>

5. Implantar o aplicativo de funções

        git push Azure master

   Quando o código for implantado, o temporizador começará a disparar imediatamente a cada minuto para executar seu código.

## <a name="test-the-chat-app"></a>Testar o aplicativo de chat

Navegue até o aplicativo de chat, a função de Temporizador Tque você acabou de criar agora relatará a hora no início de cada minuto.

![Aplicativo de chat com mensagens do servidor](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar a testar o aplicativo, você pode manter os recursos que você criou.

Caso contrário, se você não for mais usar o aplicativo de exemplo, exclua os recursos do Azure para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
> 
> 

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *SignalRTestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...**, depois em **Excluir grupo de recursos**.

   
![Excluir](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.
   
Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a integrar o Azure Functions para enviar atualizações para clientes com base em gatilhos do Azure Functions. Para saber mais sobre como usar o Servidor do Azure SignalR, continue para os exemplos de CLI do Azure para o Serviço SignalR.

> [!div class="nextstepaction"]
> [Exemplos de CLI do Azure SignalR](./signalr-cli-samples.md)



