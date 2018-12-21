---
title: Tutorial para desenvolver um aplicativo Web ASP.NET MVC com o Azure Cosmos DB usando o SDK da versão prévia do .NET.
description: Este tutorial descreve como criar um aplicativo Web ASP.NET MVC usando o Azure Cosmos DB. Você vai armazenar e acessar dados JSON de um aplicativo todo (tarefas pendentes) hospedado no Azure.
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: c0b1ed5aff7f22000a179983396239c24d21a311
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101323"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Tutorial: Desenvolver um aplicativo Web ASP.NET MVC com o Azure Cosmos DB usando o SDK da versão prévia do .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Versão prévia do .NET](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Este tutorial mostra a você como usar o Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web ASP.NET MVC hospedado no Azure. Neste tutorial, você usará o SDK do .NET V3 que está atualmente em versão prévia. A imagem abaixo mostra a página da Web que você criará usando o exemplo neste artigo:
 
![Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial - passo a passo do tutorial do ASP.NET MVC](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Se você não tiver tempo para concluir o tutorial, poderá baixar o projeto de exemplo completo do [GitHub][GitHub]. 

Este tutorial abrange:

> [!div class="checklist"]
> * Como criar uma conta do Azure Cosmos
> * Como criar um aplicativo ASP.NET MVC
> * Como conectar o aplicativo ao Azure Cosmos DB 
> * Executar operações CRUD nos dados

> [!TIP]
> Este tutorial pressupõe que você tem experiência anterior com o ASP.NET MVC e com os Sites do Azure. Se você não estiver familiarizado com o ASP.NET ou as [ferramentas de pré-requisito](#prerequisites), recomendamos que você baixe o projeto de exemplo completo do [GitHub][GitHub], adicione os pacotes NuGet necessários e execute-o. Depois de compilar o projeto, você poderá consultar esse artigo para obter insights sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes recursos:

* **Uma conta ativa do Azure:** Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* SDK do Microsoft Azure para .NET para Visual Studio 2017, disponível por meio do Instalador do Visual Studio.

As capturas de tela neste artigo foram feitas usando o Microsoft Visual Studio Community 2017. Se o seu sistema estiver configurado com uma versão diferente, suas telas e opções poderão não corresponder totalmente, mas se você cumprir os pré-requisitos acima, esta solução deverá funcionar.

## <a name="create-an-azure-cosmos-account"></a>Etapa 1: Criar uma conta do Azure Cosmos

Vamos começar criando uma conta do Azure Cosmos. Se você já tiver uma conta da API de SQL do Azure Cosmos DB, ou se estiver usando o emulador do Azure Cosmos DB para este tutorial, poderá avançar para a seção [Criar um novo aplicativo ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na próxima seção, você criará um novo aplicativo ASP.NET MVC. 

## <a name="create-a-new-mvc-application"></a>Etapa 2: Criar um novo aplicativo ASP.NET MVC

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** e **Projeto**. A caixa de diálogo **Novo Projeto** aparecerá.

2. Na janela **Novo Projeto**, expanda os modelos **Instalados**, **Visual C#** , **Web**e selecione **Aplicativo Web ASP.NET**. 

   ![Criar novo projeto de aplicativo Web ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. Na caixa **Nome** , digite o nome do projeto. Este tutorial usará o nome "todo". Se você optar por usar algum outro nome, sempre que este tutorial falar sobre o namespace todo, ajuste os exemplos de código fornecidos para usar o nome de seu aplicativo. 

4. Clique em **Procurar** para navegar até a pasta na qual você deseja criar o projeto e escolha **.NET Framework 4.6.1** ou superior. Selecione **OK**. 

5. A caixa de diálogo **Novo Aplicativo Web ASP .NET** aparece. No painel de modelos, selecione **MVC**.

6. Clique em **OK** e deixe o Visual Studio fazer o scaffolding do modelo ASP.NET MVC vazio. 

7. Depois que o Visual Studio concluir a criação do aplicativo MVC de texto clichê, você terá um aplicativo ASP.NET vazio que poderá ser executado localmente.

## <a name="add-nuget-packages"></a>Etapa 3: Adicionar o pacote NuGet do Azure Cosmos DB ao projeto

Agora que temos a maior parte do código da estrutura do ASP.NET MVC de que precisamos para esta solução, vamos adicionar os pacotes NuGet necessários para a conexão com o Azure Cosmos DB.

1. O SDK .NET do Azure Cosmos DB é empacotado e distribuído como um pacote do NuGet. Para obter o pacote NuGet no Visual Studio, use o gerenciador de pacotes NuGet no Visual Studio clicando com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**.
   
   ![Captura de tela das opções do botão direito do mouse para o projeto de aplicativo Web no Gerenciador de Soluções, com Gerenciar Pacotes NuGet realçado.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. A caixa de diálogo **Gerenciar Pacotes NuGet** será exibida. Na caixa **Procurar** do NuGet, digite **Microsoft.Azure.Cosmos**. Com base nos resultados, instale a versão **Microsoft.Azure.Cosmos** 3.0.0.1-preview. Ela baixa e instala o pacote do Azure Cosmos DB e suas dependências, como Newtonsoft.Json. Selecione **OK** na janela **Versão prévia** e em **Aceito** na janela **Aceitação da Licença** para concluir a instalação.
   
   Como alternativa, você pode usar o Console do Gerenciador de Pacotes para instalar o pacote NuGet. Para isso, no menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes**. No prompt, digite o seguinte comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Depois de instalar o pacote, sua solução do Visual Studio deverá conter as duas novas referências de biblioteca para o Microsoft.Azure.Cosmos.Client e Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Etapa 4: Configurar o aplicativo ASP.NET MVC

Agora vamos adicionar os modelos, as exibições e os controladores ao aplicativo MVC:

* [Adicionar um modelo](#add-a-model).
* [Adicionar um controlador](#add-a-controller).
* [Adicionar exibições](#add-views).

### <a name="add-a-model"></a> Adicionar um modelo

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos**, selecione **Adicionar** e **Classe**. A caixa de diálogo **Adicionar Novo Item** aparecerá.

1. Nomeie a nova classe **TodoItem.cs** e selecione **Adicionar**. 

1. Em seguida, substitua o código na classe "Todoitem" pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Os dados armazenados no Azure Cosmos DB são transferidos e armazenados como JSON. Para controlar a forma como seus objetos são serializados/desserializados pelo JSON.NET, você pode usar o atributo **JsonProperty**, como demonstrado na classe **TodoItem** criada. Além de poder controlar o formato do nome da propriedade que entra no JSON, você também pode renomear as propriedades .NET, como fez com a propriedade **Descrição**. 

### <a name="add-a-controller"></a>Adicionar um controlador

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores**, selecione **Adicionar** e **Controlador**. A caixa de diálogo **Adicionar Scaffold** aparecerá.

1. Selecione **Controlador MVC 5 – Vazio** e clique em **Adicionar**.

   ![Captura de tela da caixa de diálogo Adicionar Scaffold com a opção Controlador MVC 5 - Vazio realçada](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nomeie o novo controlador, **ItemController e substitua o código nesse arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   O atributo **ValidateAntiForgeryToken** é usado aqui para ajudar a proteger esse aplicativo contra ataques de solicitação intersite forjada. Isso envolve mais do que apenas adicionar esse atributo, pois as exibições também precisam trabalhar com esse token antifalsificação. Para saber mais sobre o assunto e ver exemplos de como implementar isso corretamente, consulte [Prevenindo solicitação intersite forjada][Preventing Cross-Site Request Forgery]. O código-fonte fornecido no [GitHub][GitHub] tem a implementação completa estabelecida.
   
   Também usamos o atributo **Bind** no parâmetro de método para ajudar na proteção contra ataques overposting. Para obter mais detalhes, consulte [Operações CRUD básicas no ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Adicionar exibições

Em seguida, vamos criar as três exibições abaixo: 

* [Adicionar uma exibição de item de lista](#AddItemIndexView).
* [Adicionar uma exibição Novo item](#AddNewIndexView).
* [Adicionar uma exibição Editar item](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Adicionar uma exibição de item de lista

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse na pasta vazia **Item** que o Visual Studio criou quando você adicionou **ItemController** anteriormente, clique em **Adicionar** e em **Exibição**.
   
   ![Captura de tela do Gerenciador de Soluções mostrando a pasta Item que o Visual Studio criou com os comandos Adicionar Exibição realçados](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. Na caixa de diálogo **Adicionar Exibição**, atualize os seguintes valores:
   
   * Na caixa **Nome da exibição**, digite ***Índice***.
   * Na caixa **Modelo**, selecione ***Lista***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de tela mostrando a caixa de diálogo Adicionar Exibição](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. Depois de definir todos esses valores, selecione **Adicionar** e deixe o Visual Studio criar uma nova exibição de modelo. Quando terminar, ele abrirá o arquivo cshtml criado. Você pode fechar esse arquivo no Visual Studio, pois voltará a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma nova exibição de item

Da mesma forma que você criou uma exibição para itens de lista, crie uma nova exibição para criar itens usando as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com botão direito do mouse na pasta **Item** novamente, selecione **Adicionar**e selecione **Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, atualize os seguintes valores:
   
   * Na caixa **Nome da exibição**, digite ***Criar***.
   * Na caixa **Modelo**, selecione ***Criar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.
   
#### <a name="AddEditIndexView"></a>Adicionar uma exibição Editar item

E, finalmente, adicione um modo de exibição para editar um item com as seguintes etapas:

1. No **Gerenciador de Soluções**, clique com botão direito do mouse na pasta **Item** novamente, selecione **Adicionar**e selecione **Exibição**.

1. Na caixa de diálogo **Adicionar Exibição** , faça o seguinte:
   
   * Na caixa **Nome da exibição**, digite ***Editar***.
   * Na caixa **Modelo**, selecione ***Editar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.

Feito isso, feche todos os documentos cshtml no Visual Studio, já que você voltará a essas exibições mais tarde.

## <a name="connect-to-cosmosdb"></a>Etapa 5: Conectar-se ao Azure Cosmos DB 

Agora que os detalhes padrão do MVC foram resolvidos, vamos adicionar o código para conexão com o Azure Cosmos DB e executar operações CRUD. 

### <a name="perform-crud-operations"></a> Executar operações CRUD nos dados

A primeira coisa a fazer aqui é adicionar uma classe que contenha a lógica para conectar e usar o Azure Cosmos DB. Para este tutorial, vamos encapsular essa lógica em uma classe chamada TodoItemService.cs. Esse código lê os valores de ponto de extremidade do Azure Cosmos DB do arquivo de configuração e executa operações CRUD, como listar itens incompletos, criar, editar e excluir itens. 

1. No **Gerenciador de Soluções**, crie uma nova pasta em seu projeto chamada **Serviços**.

1. Clique com o botão direito do mouse na pasta **Serviços**, selecione **Adicionar** e selecione **Classe**. Nomeie a nova classe **TodoItemService** e selecione **Adicionar**.

1. Adicione o seguinte código à classe **TodoItemService** e substitua o código no arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. O código anterior lê valores de cadeia de conexão do arquivo de configuração. Para atualizar os valores de cadeia de conexão da sua conta do Cosmos do Azure, abra o arquivo **Web.config** em seu projeto e adicione as linhas abaixo na seção `<AppSettings>`:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Atualize os valores de ponto de extremidade e chave primária com os valores recuperados na folha **Chaves** no portal do Azure. Use o **URI** da folha Chaves como o valor da configuração do ponto de extremidade e use a **CHAVE PRIMÁRIA** ou a **CHAVE SECUNDÁRIA** da folha Chaves como o valor da configuração das chaves. Isso faz a conexão do Azure Cosmos DB com o aplicativo; agora vamos adicionar a lógica do aplicativo.

1. Abra **Global.asax.cs** e adicione a seguinte linha ao método **Application_Start** 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   Até aqui, sua solução deve ser capaz de compilar o projeto sem erros. Se você executou o aplicativo agora, o **HomeController** e a exibição **Índice** desse controlador devem ser abertos. Esse é o comportamento padrão para o projeto do modelo MVC que escolhemos no início. Vamos alterar o roteamento neste aplicativo MVC para alterar seu comportamento.

1. Abra ***App\_Start\RouteConfig.cs***, encontre a linha que começa com "defaults:" e atualize-a com o seguinte código:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  Esse código agora informa ao ASP.NET MVC que se você não especificou um valor na URL para controlar o comportamento de roteamento, em vez de **Home**, ele deve usar **Item** como controlador e **Índice** como exibição.

Agora, se você executar o aplicativo, ele chamará seu **ItemController** que chama os métodos GetItems da classe TodoItemService definida na próxima seção. 

Se você compilar e executar esse projeto agora, deverá ver algo parecido com isto.    

![Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial de banco de dados](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Etapa 6: Executar o aplicativo localmente

Use as etapas abaixo para testar o aplicativo em seu computador local:

1. Pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração. Ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos anteriormente:
   
   ![Captura de tela do aplicativo Web de lista de tarefas pendentes criado por este tutorial de banco de dados](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Clique no link **Criar Novo** e adicione valores ao campos **Nome** e **Descrição**. Deixe a caixa de seleção **Concluído** desmarcada. Caso contrário, o novo item será adicionado em um estado concluído e não aparecerá na lista inicial.
   
3. Clique em **Criar** para ser redirecionado à exibição **Índice** e ver seu item na lista. Você pode adicionar mais alguns itens à lista de tarefas pendentes.

    ![Captura de tela da exibição Índice](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Clique em **Editar** perto de um **Item** na lista e você será levado para a exibição **Editar**, na qual poderá atualizar qualquer propriedade do objeto, incluindo o sinalizador **Concluído**. Se você marcar o sinalizador **Concluir** e clicar em **Salvar**, o **Item** será removido da lista de tarefas incompletas.
   
   ![Captura de tela da exibição Índice com a caixa Concluído marcada](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Depois de testar o aplicativo, pressione Ctrl + F5 para parar a depuração do aplicativo. Você está pronto para implantar!

## <a name="deploy-the-application-to-azure"></a>Etapa 7: Implantar o aplicativo 
Agora que você tem o aplicativo completo funcionando corretamente no Azure Cosmos DB, vamos implantar esse aplicativo Web no Serviço de Aplicativo do Azure.  

1. Para publicar o aplicativo, clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.
   
2. Na caixa de diálogo **Publicar**, selecione **Serviço de Aplicativo do Microsoft Azure**, selecione **Criar Novo** para criar um perfil do Serviço de Aplicativo ou escolha **Selecionar Existente** para usar um perfil existente.

3. Se você tiver um perfil existente do Serviço de Aplicativo do Azure, selecione uma **Assinatura** na lista suspensa. Use o filtro **Exibição** para classificar por tipo de recurso ou grupo de recursos. Em seguida, pesquise o Serviço de Aplicativo do Azure necessário e selecione **OK**. 
   
   ![Caixa de diálogo Serviço de Aplicativo no Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Para criar um novo perfil do Serviço de Aplicativo do Azure, clique em **Criar Novo** na caixa de diálogo **Publicar**. Na caixa de diálogo **Criar Serviço de Aplicativo**, digite seu nome do aplicativo Web e a assinatura apropriada, o grupo de recursos e o plano de Serviço de Aplicativo e selecione **Criar**.

   ![Caixa de diálogo Criar Serviço de Aplicativo no Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Em poucos segundos, o Visual Studio publicará seu aplicativo Web e iniciará um navegador no qual você poderá ver o projeto sendo executado no Azure!

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um aplicativo Web ASP.NET MVC que pode acessar dados armazenados no Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Criar um aplicativo Java para acessar dados armazenados na conta da API de SQL do Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
