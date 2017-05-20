---
title: Compilar um aplicativo ASP.NET no Azure com Banco de Dados SQL | Microsoft Docs
description: "Saiba como obter um aplicativo ASP.NET funcionando no Azure com conexão a um Banco de Dados SQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 6563d1520149ae5ced7e2de80686ef1624ebb651
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Compilar um aplicativo ASP.NET no Azure com Banco de Dados SQL

Este tutorial mostra como desenvolver um aplicativo Web ASP.NET controlado por dados no Azure, conectá-lo ao Banco de Dados SQL do Azure e habilitar sua funcionalidade controlada por dados. Quando terminar, você terá um aplicativo ASP.NET em execução no [Serviço de Aplicativo do Azure ](../app-service/app-service-value-prop-what-is.md) e conectado ao Banco de Dados SQL.

![Aplicativo ASP.NET publicado no aplicativo Web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo ASP.NET ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar esse exemplo, [baixe e instale o Visual Studio 2017 Community Edition gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo
Nessa etapa, você baixa um aplicativo ASP.NET de exemplo.

### <a name="get-the-sample-project"></a>Obter o projeto de exemplo

Baixe o projeto de exemplos clicando [aqui](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Extraia o _dotnet-sqldb-tutorial-master.zip_ baixada para um diretório de trabalho.

> [!TIP]
> É possível obter o mesmo projeto de exemplo por clonagem do repositório GitHub:
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

Esse projeto de exemplo contém um aplicativo [ASP.NET MVC](https://www.asp.net/mvc) CRUD (criar-ler-atualizar-excluir) simples desenvolvido no [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-application"></a>Executar o aplicativo

No diretório extraído, abra _dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln_ no Visual Studio 2017.

Depois que a solução de exemplo for aberta, digite `F5` para executar no navegador.

Você deve ver uma lista de tarefas simples na home page. Tente adicionar algumas tarefas à lista vazia.

![Caixa de diálogo Novo Projeto ASP .NET](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

O contexto do banco de dados usa uma cadeia de conexão chamada `MyDbConnection`. Essa cadeia de conexão é definida em _Web. config_ e referenciada em _Models\MyDatabaseContext.cs_. O nome da cadeia de conexão é tudo o que você irá precisar posteriormente quando conectar seu aplicativo Web do Azure ao Banco de Dados SQL do Azure. 

## <a name="publish-to-azure-with-sql-database"></a>Publicar no Azure com o banco de dados SQL

No **Gerenciador de Soluções**, clique com botão direito no projeto **DotNetAppSqlD** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Verifique se o **Serviço de Aplicativo do Microsoft Azure** está selecionado e clique em **Publicar**.

![Publicar na página de visão geral do projeto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Isso abre a caixa de diálogo **Criar Serviço de Aplicativo**, o que ajuda a criar todos os recursos do Azure necessários executar seu aplicativo Web ASP.NET no Azure.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Adicionar uma conta**, depois, faça logon em sua assinatura do Azure. Se você já entrou em uma conta da Microsoft, verifique se a conta tem sua assinatura do Azure. Se a conta da Microsoft conectada não tiver sua assinatura do Azure, clique nela para adicionar a conta correta.
   
![Entrar no Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Depois de conectado, você está pronto para criar todos os recursos necessários para seu aplicativo Web do Azure nesta caixa de diálogo.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Primeiro, é necessário um _grupo de recursos_. 

> [!NOTE] 
> Um grupo de recursos é um contêiner lógico no qual os recursos do Azure, como aplicativos Web, bancos de dados e contas de armazenamento, são implantados e gerenciados.
>
>

Lado do **Grupo de Recursos**, clique em **Novo**.

Nome seu grupo de recursos como **myResourceGroup** e clique em **OK**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Seu aplicativo Web também precisa de um _plano do Serviço de Aplicativo_. 

> [!NOTE]
> Um plano do Serviço de Aplicativo representa a coleção de recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do Serviço de Aplicativo compartilham os recursos definidos por ele, permitindo que você economize ao hospedar vários aplicativos. 
>
> Os Planos do Serviço de Aplicativo definem:
>
> - Região (Europa Setentrional, Leste dos EUA, Sudeste Asiático)
> - Tamanha da Instância (Pequena, Média, Grande)
> - Contagem da Escala (uma, duas ou três instâncias etc.) 
> - SKU (Gratuito, Compartilhado, Básico, Standard, Premium)
>
>

Lado do **Plano do Serviço de Aplicativo**, clique em **Novo**. 

Na caixa de diálogo **Configurar Plano do Serviço de Aplicativo**, configure o novo plano do Serviço de Aplicativo com as seguintes definições:

- **Plano do Serviço de Aplicativo**: digite **myAppServicePlan**. 
- **Local**: escolha **Europa Ocidental** ou qualquer outra região desejada.
- **Tamanho**: escolha **Gratuito** ou qualquer outro [tipo de preços](https://azure.microsoft.com/pricing/details/app-service/) desejado.

Clique em **OK**.

![Criar plano de Serviço de Aplicativo](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>Configurar o nome do aplicativo Web

Em **Nome do Aplicativo Web**, digite um nome exclusivo do aplicativo. Esse nome é usado como parte do nome DNS padrão de seu aplicativo (`<app_name>.azurewebsites.net`) e, portanto, deve ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear um nome de domínio personalizado para seu aplicativo antes de expor para seus usuários.

Também é possível aceitar o nome gerado automaticamente, que já é exclusivo.

Para preparar a próxima etapa, clique em **Explorar serviços adicionais do Azure**.

> [!NOTE]
> Não clique ainda em **Criar** neste diálogo. Primeiro, você precisa configurar um Banco de Dados SQL na próxima etapa.

![Configurar o nome do aplicativo Web](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>Criar uma instância do SQL Server

Na guia **Serviços** clique no ícone **+** próximo ao **Banco de Dados SQL**. 

Na caixa de diálogo **Configurar Banco de Dados SQL**, clique em **Novo** próximo ao **SQL Server**. 

Em **Nome do Servidor**, digite um nome exclusivo. Esse nome é usado como parte do nome DNS padrão para seu servidor de banco de dados, `<server_name>.database.windows.net`, e, portanto, deve ser exclusivo em todas as instâncias do SQL Server no Azure. 

Configure o restante dos campos conforme desejado e clique em **OK**.

![Criar instância do SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>Configurar o Banco de Dados SQL

Em **Nome de Banco de Dados**, digite _myToDoAppDb_ ou qualquer nome desejado.

Em **Nome da Cadeia de Conexão**, digite _MyDbConnection_. Esse nome deve corresponder à cadeia de conexão que referenciada em _Models\MyDatabaseContext.cs_.

![Configurar banco de dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>Publicar e publicar o aplicativo Web

Clique em **Criar**. 

Depois do assistente terminar de criar os recursos do Azure, ele publicará automaticamente seu aplicativo ASP.NET para o Azure pela primeira vez e, depois, iniciará o aplicativo Web do Azure publicado no navegador padrão.

Tente adicionar alguns itens de tarefas à lista vazia.

![Aplicativo ASP.NET publicado no aplicativo Web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Parabéns! Seu aplicativo ASP.NET controlado por dados está em execução em tempo real no Serviço de Aplicativo do Azure.

## <a name="access-the-sql-database-locally"></a>Acessar o Banco de Dados SQL localmente

O Visual Studio permite pesquisar e gerenciar seu novo Banco de Dados SQL facilmente no **Pesquisador de Objetos do SQL Server**.

### <a name="create-a-database-connection"></a>Criar uma conexão de banco de dados

Abra **Pesquisador de Objetos do SQL Server** digitando `Ctrl`+`` ` ``, `Ctrl`+`S`.

Na parte superior do **Pesquisador de Objetos do SQL Server**, clique no botão **Adicionar SQL Server**.

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

Na caixa de diálogo **Conectar**, expanda o nó **Azure**. Todos os seus Bancos de Dados SQL no Azure são listados aqui.

Selecione o Banco de Dados SQL criado anteriormente. A conexão usada antes é automaticamente preenchida na parte inferior.

Digite a senha do administrador de banco de dados que você usou anteriormente e clique em **Conectar**.

![Configurar a conexão de Banco de Dados do Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Permitir conexão do cliente a partir de seu computador

A caixa de diálogo **Crie uma nova regra de firewall** é aberta. Por padrão, sua instância do SQL Server somente permite conexões dos serviços do Azure, como o aplicativo web do Azure. Para se conectar ao seu banco de dados diretamente do Visual Studio, é necessário criar uma regra de firewall na instância do SQL Server para permitir o endereço IP público do seu computador local.

No Visual Studio, isso é fácil de fazer. A caixa de diálogo já está preenchida com o endereço IP público do seu computador.

Certifique-se de que **Adicionar meu IP do cliente** está selecionado e clique em **OK**. 

![Definir firewall para instância do SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Quando o Visual Studio terminar de criar a configuração de firewall para a instância do SQL Server sua conexão aparecerá em **Pesquisador de Objetos do SQL Server**.

Aqui, você pode executar as operações de banco de dados mais comuns, como executar consultas, criar exibições, procedimentos armazenados e, muito mais. O exemplo a seguir mostra como exibir dados do banco de dados. 

![Explorar objetos do Banco de Dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Atualizar aplicativo com Migrações do Code First

Nesta etapa, você usará Migrações do Code First no Entity Framework para fazer uma alteração no seu esquema de banco de dados e publicá-lo no Azure.

### <a name="update-your-data-model"></a>Atualizar seu modelo de dados

Abra _Models\Todo.cs_ no editor de códigos. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Executar Migrações do Code First localmente

Em seguida, execute alguns comandos para fazer as atualizações para seu banco de dados localdb. 

A partir do menu **Ferramentas** clique em **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**. O console normalmente é aberto na janela inferior.

Habilite Migrações do Code First como essa:

```PowerShell
Enable-Migrations
```

Adicione uma migração como esta:

```PowerShell
Add-Migration AddProperty
```

Atualize o banco de dados localdb como este:

```PowerShell
Update-Database
```

Teste suas alterações executando o aplicativo com `F5`.

Se o aplicativo for carregado sem erros, as Migrações do Code First tiveram êxito. No entanto, sua página ainda tem a mesma aparência porque a lógica de aplicativo ainda não está usando essa nova propriedade. 

### <a name="use-the-new-property"></a>Usar a nova propriedade

Vamos fazer algumas alterações em seu código para usar a propriedade `Done`. Para simplificar este tutorial, somente as exibições `Index` e `Create` serão alteradas para observar a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o `Create()` método e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` deverá ter a seguinte aparência:

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código do Razor você dever ver uma marca `<div class="form-group">` que usa `model.Description` e, uma outra marca `<div class="form-group">` que usa `model.CreatedDate`. Imediatamente após essas duas marcas, adicione outra marca `<div class="form-group">` que usa `model.Done`, como esta:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure a marca vazia `<th></th>`. Logo acima dessa marca, adicione o seguinte código do Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize a marca `<td>` que contém os métodos auxiliares `Html.ActionLink()`. Logo acima dessa marca, adicione o seguinte código do Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Isso é tudo o que você precisa para ver as alterações nas exibições `Index` e `Create`. 

Digite `F5` novamente para executar o aplicativo.

Agora, será possível adicionar um item de tarefa e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se que isso é tudo o que pode feito por enquanto porque a exibição `Edit` não foi alterada.

### <a name="enable-code-first-migrations-in-azure"></a>Habilitar Migrações do Code First no Azure

Agora que a alteração de código funciona, incluindo a migração de banco de dados, você também publica-o em seu aplicativo Web do Azure e atualiza seu Banco de Dados SQL com as Migrações do Code First.

Exatamente como antes, clique com o botão direito do mouse no projeto e selecione **Publicar**.

Clique em **Configurações** para abrir o assistente de publicação.

![Abrir as configurações de publicação](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

No assistente, clique em **Avançar**.

Certifique-se de que a cadeia de conexão do banco de dados SQL está preenchida em **MyDatabaseContext (MyDbConnection)**. Talvez seja necessário selecionar o banco de dados **myToDoAppDb** da lista suspensa. 

Selecione **Executar o Migrations do Code First (executado na inicialização do aplicativo)** e, em seguida, clique em **Salvar**.

![Habilitar Migrações do Code First no aplicativo Web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicar suas alterações

Agora que as Migrações do Code First estão habilitadas no seu aplicativo Web, basta publicar suas alterações de código.

Na página de publicação, clique em **Publicar**.

Tente adicionar os itens pendentes outra vez, selecione **Concluído** e os itens deverão aparecer na sua página inicial como um item concluído.

![Aplicativo Web do Azure após Migração do Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar seu aplicativo ASP.NET, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Code First apenas alteram o esquema de dados e deixam os dados existentes intactos.
>
>

## <a name="stream-application-logs"></a>Transmitir logs de aplicativos

É possível transmitir mensagens de rastreamento diretamente do seu aplicativo Web do Azure para o Visual Studio.

Abra _Controllers\TodosController.cs_.

Observe que cada ação inicia com um método `Trace.WriteLine()`. Esse código é adicionado para mostrar como é fácil adicionar mensagens de rastreamento ao seu aplicativo Web do Azure.

### <a name="open-server-explorer"></a>Abra o Gerenciador de Servidores

É possível configurar o log para o aplicativo Web do Azure no **Gerenciador de Servidores**. 

Para abri-lo, digite `Ctrl`+`Alt`+`S`.

### <a name="enable-log-streaming"></a>Habilitar streaming de log

No **Gerenciador de Servidores**, expanda **Azure** > **Serviço de Aplicativo**.

Expanda o grupo de recursos**myResourceGroup** que você criou, ao criar pela primeira vez o aplicativo Web do Azure.

Clique com o botão direito do mouse no aplicativo Web do Azure e selecione **Exibir Logs de Streaming**.

![Habilitar streaming de log](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Os logs agora são transmitidos na janela **Saída**. 

![Streaming de log na janela Saída](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

No entanto, ainda não é possível ver nenhuma das mensagens de rastreamento. Isso ocorre porque ao selecionar **Exibir Logs de Streaming**, seu aplicativo Web do Azure define o nível de rastreamento como `Error` que registra apenas eventos de erros (com o método`Trace.TraceError()`).

### <a name="change-trace-levels"></a>Alterar níveis de rastreamento

Para alterar os níveis de rastreamento e exibir outras mensagens de rastreamento, retorne para o **Gerenciador de Servidores**.

Clique com o botão direito do mouse no aplicativo Web do Azure novamente e selecione **Configurações**.

Na lista suspensa **Log de Aplicativo (Sistema de Arquivos)**, selecione **detalhado**. Clique em **Salvar**.

![Alterar nível de rastreamento para Detalhado](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Você pode testar diferentes níveis de rastreamento para ver quais tipos de mensagens são exibidos para cada nível. Por exemplo, o nível **Informações** inclui todos os logs criados por `Trace.TraceInformation()`, `Trace.TraceWarning()` e `Trace.TraceError()`, mas não os logs criados por `Trace.WriteLine()`.
>
>

Em seu navegador, tente clicar no aplicativo de lista de tarefas no Azure. As mensagens de rastreamento agora são transmitidas para a janela **Saída** no Visual Studio.

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>Parar o streaming de log

Para interromper o serviço de streaming de log, clique no botão **Parar Monitoramento** na janela **Saída**.

![Parar o streaming de log](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o portal do Azure para ver o aplicativo Web que você criou. 

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Você foi para a _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente). 

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir. 

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

- mapear um nome DNS personalizado
- associar um certificado SSL personalizado
- configurar uma implantação contínua
- Escalar vertical e horizontalmente
- adicionar a autenticação do usuário

## <a name="clean-up-resources"></a>Limpar recursos
 
Se não precisar desses recursos para outro tutorial (consulte [Próximas etapas](#next)), você poderá excluí-los executando o seguinte comando: 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo ASP.NET ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para aprender a mapear um nome DNS personalizado para ele.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)

