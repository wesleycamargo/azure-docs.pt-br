---
title: Criar um aplicativo Web ASP.NET com o Cache Redis | Microsoft Docs
description: Neste início rápido, você aprenderá a criar um aplicativo Web ASP.NET com o Cache Redis
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: baaa53b04f608e2cb3546fcac6a6eb4eda4d3c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640747"
---
# <a name="quickstart-create-a-aspnet-web-app-with-redis-cache"></a>Início Rápido: Criar um aplicativo Web ASP.NET com o Cache Redis


## <a name="introduction"></a>Introdução

Este início rápido mostra como criar e implantar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio 2017. O aplicativo de exemplo se conecta a um Cache Redis do Azure para armazenar e recuperar dados do cache. Ao concluir o início rápido, você terá um aplicativo Web em execução, hospedado no Azure, que lê e grava em um Cache Redis do Azure.

![Teste simples concluído no Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Para concluir o início rápido, é necessário ter os seguintes pré-requisitos:

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    * Desenvolvimento Web e ASP.NET
    * Desenvolvimento do Azure

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Abra o Visual Studio e clique em **Arquivo**, **Novo**, **Projeto**.

![Criar projeto](./media/cache-web-app-howto/cache-create-project.png)

Na caixa de diálogo Novo Projeto, execute as seguintes etapas:

1. Expanda o nó **Visual C#** da lista **Modelos**
1. Selecione **Nuvem**
1. Clique em **Aplicativo Web ASP.NET**
1. Verifique se o **.NET Framework 4.5.2** ou superior está selecionado
1. Dê um nome ao projeto na caixa de texto **Nome**; para este exemplo, usamos **ContosoTeamStats**
1. Clique em **OK**.

Você verá a tela Novo Aplicativo Web ASP.NET:

![Selecionar modelo de projeto](./media/cache-web-app-howto/cache-select-template.png)

Selecione **MVC** como o tipo de projeto.

Certifique-se de que a opção **Sem Autenticação** esteja especificada para as configurações **Autenticação**. Dependendo de sua versão do Visual Studio, o padrão pode ser definido para algo diferente. Para alterá-lo, clique em **Alterar Autenticação**e selecione **Sem Autenticação**.

Clique em **OK** para criar o projeto.

## <a name="create-a-cache"></a>Criar um cache

Em seguida, crie o cache para o aplicativo.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Crie um arquivo no computador chamado *CacheSecrets.config* e coloque-o em um local em que ele não fará o check-in com o código-fonte do aplicativo de exemplo. Para este início rápido, o arquivo *CacheSecrets.config* está localizado aqui, em *C:\AppSecrets\CacheSecrets.config*.

Edite o arquivo *CacheSecrets.config* e adicione o seguinte conteúdo:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Substitua `<cache-name>` pelo nome do host do cache.

Substitua `<access-key>` pela chave primária do cache.

> [!TIP]
> A chave de acesso secundária é usada durante a rotação de chave como uma chave alternativa enquanto você regenera a chave de acesso primária.
>

Salve o arquivo.

## <a name="update-the-mvc-application"></a>Atualizar o aplicativo MVC

Nesta seção, você atualiza o aplicativo para dar suporte a uma nova exibição que exibirá um teste simples em um Cache Redis do Azure.

* [Atualizar o arquivo web.config com uma configuração de aplicativo para o cache](#Update-the-webconfig-file-with-an-app-setting-for-the-cache)
* [Configurar o aplicativo para usar o cliente StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Atualizar o HomeController e o Layout](#update-the-homecontroller-and-layout)
* [Adicionar uma nova exibição RedisCache](#add-a-new-rediscache-view)

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Atualizar o arquivo web.config com uma configuração de aplicativo para o cache

Quando você executa o aplicativo localmente, as informações em *CacheSecrets.config* são usadas para se conectar à instância do Cache Redis do Azure. Posteriormente, você implantará esse aplicativo no Azure. Nesse momento, você definirá uma configuração de aplicativo no Azure que será usada pelo aplicativo para recuperar as informações de conexão do cache em vez desse arquivo. Como *CacheSecrets.config* não está implantado no Azure com o aplicativo, você apenas o usará durante o teste do aplicativo localmente. Você deseja manter essas informações com o máximo de segurança possível para evitar o acesso mal-intencionado aos dados do cache.

No **Gerenciador de Soluções**, clique duas vezes no arquivo *web.config* para abri-lo.

![Web.config](./media/cache-web-app-howto/cache-web-config.png)

No arquivo *web.config*, encontre o elemento `<appSetting>` e adicione o atributo `file` a seguir. Se você usou um nome de arquivo ou local diferente, substitua esses valores pelos mostrados no exemplo.

* Antes: `<appSettings>`
* Após: ` <appSettings file="C:\AppSecrets\CacheSecrets.config">`

O tempo de execução do ASP.NET mescla o conteúdo do arquivo externo com a marcação no elemento `<appSettings>` . O tempo de execução ignorará o atributo de arquivo se o arquivo especificado não puder ser encontrado. Seus segredos (a cadeia de conexão do cache) não são incluídos como parte do código-fonte do aplicativo. Quando você implantar o aplicativo Web no Azure, o arquivo *CacheSecrests.config* não será implantado.

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar o aplicativo para usar StackExchange.Redis

Para configurar o aplicativo para que ele use o pacote NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) do Visual Studio, clique em **Ferramentas > Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.

Execute o comando a seguir na janela `Package Manager Console`:

```powershell
Install-Package StackExchange.Redis
```

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis. Se você preferir usar uma versão de nome forte da biblioteca de cliente `StackExchange.Redis`, instale o pacote `StackExchange.Redis.StrongName`.

### <a name="update-the-homecontroller-and-layout"></a>Atualizar o HomeController e o Layout

No **Gerenciador de Soluções**, expanda a pasta **Controladores** e abra o arquivo *HomeController.cs*.

Adicione as duas instruções `using` a seguir na parte superior do arquivo para dar suporte às configurações de aplicativo e de cliente de cache.

```csharp
using System.Configuration;
using StackExchange.Redis;
```

Adicione o método a seguir à classe `HomeController` para dar suporte a uma nova ação `RedisCache` que executa alguns comandos no novo cache.

```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Redis Cache on ASP.NET.";

        var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        // Connection refers to a property that returns a ConnectionMultiplexer
        // as shown in the previous example.
        IDatabase cache = lazyConnection.Value.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demostrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

        lazyConnection.Value.Dispose();

        return View();
    }
```

No **Gerenciador de Soluções**, expanda a pasta **Exibições**>**Compartilhado** e abra o arquivo *_Layout.cshtml*.

Substitua:

```csharp
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

Por:

```csharp
@Html.ActionLink("Azure Redis Cache Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

### <a name="add-a-new-rediscache-view"></a>Adicionar uma nova exibição RedisCache

No **Gerenciador de Soluções**, expanda a pasta **Exibições** e, em seguida, clique com o botão direito do mouse na pasta **Início**. Escolha **Adicionar** > **Exibição...**

Na caixa de diálogo Adicionar Exibição, insira **RedisCache** para o Nome de Exibição e clique em **Adicionar**.

Substitua o código no arquivo *RedisCache.cshtml* pelo seguinte código:

```csharp
@{
    ViewBag.Title = "Azure Redis Cache Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Por padrão, o projeto está configurado para hospedar o aplicativo localmente no [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) para teste e depuração.

No Visual Studio, no menu, clique em **Depurar** > **Iniciar Depuração** para criar e iniciar o aplicativo localmente para teste e depuração.

No navegador, clique em **Teste do Cache Redis do Azure** na barra de navegação.

No exemplo abaixo, você pode ver que a chave `Message` já tinha um valor armazenado em cache, que foi definido por meio do Console do Redis no portal. O aplicativo atualizou esse valor armazenado em cache. O aplicativo também executou os comandos `PING` e `CLIENT LIST`.

![Teste simples concluído localmente](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publicar e executar no Azure

Depois de testar com êxito o aplicativo localmente, você implantará o aplicativo no Azure e o executará na nuvem.

### <a name="publish-the-app-to-azure"></a>Publicar o aplicativo no Azure

No Visual Studio, clique com o botão direito do mouse no nó do projeto no Gerenciador de Soluções e escolha **Publicar**.

![Publicar](./media/cache-web-app-howto/cache-publish-app.png)

Clique em **Serviço de Aplicativo do Microsoft Azure**, escolha **Criar Novo** e clique em **Publicar**.

![Publicar no serviço de aplicativo](./media/cache-web-app-howto/cache-publish-to-app-service.png)

Na caixa de diálogo **Criar Serviço de Aplicativo**, faça as seguintes alterações:

| Configuração | Valor recomendado | DESCRIÇÃO |
| ------- | :---------------: | ----------- |
| **Nome do aplicativo** | Usar padrão | O nome do aplicativo será o nome do host para o aplicativo quando ele for implantado no Azure. O nome pode ter um sufixo de carimbo de data/hora adicionado, se necessário, para torná-lo exclusivo. |
| **Assinatura** | Escolha a sua assinatura do Azure | Esta assinatura será cobrada por todos os encargos de hospedagem relacionados. Caso tenha várias assinaturas do Azure, verifique se a assinatura desejada está selecionada.|
| **Grupo de recursos** | Use o mesmo grupo de recursos no qual o cache foi criado. Por exemplo, *TestResourceGroup*. | O grupo de recursos ajuda você a gerenciar todos os recursos como um grupo. Posteriormente, quando você desejar excluir o aplicativo, poderá simplesmente excluir o grupo. |
| **Plano do Serviço de Aplicativo** | Clique em **Novo** e crie um novo Plano do Serviço de Aplicativo chamado *TestingPlan*. <br />Use o mesmo **Local** usado ao criar o cache. <br />Escolha **Gratuito** para o tamanho. | Um Plano do Serviço de Aplicativo define um conjunto de recursos de computação com o qual um aplicativo Web será executado. |

![Caixa de diálogo Serviço de Aplicativo](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

Depois de definir as configurações de hospedagem do Serviço de Aplicativo, clique em **Criar** para criar um novo Serviço de Aplicativo para seu aplicativo.

Monitore a janela **Saída** no Visual Studio para ver o status da publicação no Azure. Quando a publicação é concluída com êxito, a URL do Serviço de Aplicativo é registrada em log, conforme mostrado abaixo:

![Saída de publicação](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Adicionar a configuração de aplicativo ao cache

Após a conclusão da publicação para o novo Serviço de Aplicativo, adicione uma nova configuração de aplicativo. Essa configuração será usada para armazenar as informações de conexão do cache. Digite o nome do aplicativo na barra de pesquisa na parte superior do portal do Azure para encontrar o novo Serviço de Aplicativo recém-criado.

![Localizar o Serviço de Aplicativo](./media/cache-web-app-howto/cache-find-app-service.png)

Adicione uma nova configuração de aplicativo chamada **CacheConnection** ao aplicativo a ser usada para se conectar ao cache. Use o mesmo valor configurado para `CacheConnection` no arquivo *CacheSecrets.config*. O valor contém o nome do host do cache e a chave de acesso.

![Adicionar configuração de aplicativo](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Executar o aplicativo no Azure

No navegador, navegue para a URL do Serviço de Aplicativo. A URL é mostrada nos resultados da operação de publicação na janela Saída do Visual Studio. Ela também é fornecida no portal do Azure na página Visão Geral do Serviço de Aplicativo criado.

Clique em **Teste do Cache Redis do Azure** na barra de navegação para testar o acesso ao cache.

![Teste simples concluído no Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
>

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *TestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...**, depois em **Excluir grupo de recursos**.

![Excluir](./media/cache-web-app-howto/cache-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e clique em **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, você usará o Cache Redis do Azure em um cenário mais semelhante ao mundo real para melhorar o desempenho de um aplicativo. Você atualizará esse aplicativo para armazenar em cache os resultados do placar de líderes usando o padrão cache-aside com o ASP.NET e um banco de dados.

> [!div class="nextstepaction"]
> [Criar um placar de líderes cache-aside no ASP.NET](cache-web-app-cache-aside-leaderboard.md)
