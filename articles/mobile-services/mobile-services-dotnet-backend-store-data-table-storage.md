<properties
	pageTitle="Criar um serviço móvel de back-end .NET que usa o armazenamento de tabela | Serviços Móveis do Azure"
	description="Aprenda a usar o armazenamento de tabela do Azure com seu serviço móvel de back-end do .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Criar um serviço móvel de back-end .NET que usa o armazenamento de tabela

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

Este tópico mostra como usar um repositório de dados não relacional para seu serviço móvel de back-end .NET. Neste tutorial, você modificará o projeto de início rápido de Serviços Móveis do Azure para usar o armazenamento em tabela do Azure no lugar do repositório de dados padrão do Banco de Dados SQL do Azure.

Este tutorial requer a conclusão do tutorial [Introdução aos Serviços Móveis]. Também será necessário uma conta de armazenamento do Azure.

##Configurar o armazenamento em tabela do Azure no seu serviço móvel de back-end do .NET

Primeiro, você precisa configurar seu serviço móvel e o projeto de código de back-end do .NET para se conectar ao armazenamento do Azure.

1. No **Gerenciador de Soluções** no Visual Studio, clique com o botão direito do mouse no projeto do back-end do .NET e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Apenas Estáveis**, pesquise por **MobileServices** e clique em **Instalar** no pacote **Extensão do Armazenamento do Azure do Back-end do .NET dos Serviços Móveis do Microsoft Azure**; em seguida, aceite os contratos de licença.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Esse procedimento adiciona o suporte para serviços de armazenamento do Azure ao projeto de serviço móvel de back-end do .NET.

3. Se você ainda não criou sua conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/storage-create-storage-account.md).

4. No [portal clássico do Azure], clique em **Armazenamento**, na conta de armazenamento e em **Gerenciar Chaves**.

5. Anote o **Nome da Conta de Armazenamento** e a **Chave de Acesso**.

6. Em seu serviço móvel, clique na guia **Configurar**, role para baixo até **Cadeias de conexão** e insira uma nova cadeia de caracteres de conexão com um **Nome** de `StorageConnectionString` e um **Valor** que é a sua cadeia de conexão da conta de armazenamento no formato a seguir.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. Na sequência de caracteres acima, substitua os valores de `<ACCOUNT_NAME>` e `<ACCESS_KEY>` pelos valores do portal e clique em **Salvar**.

	A cadeia de conexão da conta de armazenamento é armazenada criptografada em configurações do aplicativo. Você pode acessar essa cadeia em qualquer controlador de tabela no tempo de execução.

8. No Gerenciador de Soluções do Visual Studio, abra o arquivo Web.config do projeto de serviço móvel e adicione a seguinte cadeia de conexão nova:

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Substitua o espaço reservado `<STORAGE_CONNECTION_STRING>` pela cadeia de conexão descrita na etapa 6.

	O serviço móvel usa essa cadeia de conexão quando é executado no computador local, o que permite testar o código antes de publicá-lo. Quando em execução no Azure, o serviço móvel usa o valor de cadeia de conexão definido no portal e ignora a cadeia de conexão no projeto.

## <a name="modify-service"></a>Modificar tipos de dados e controladores de tabela

Como o projeto de início rápido da lista de tarefas é projetado para trabalhar com um banco de dados SQL usando o Entity Framework, será necessário fazer algumas atualizações do projeto para trabalhar com armazenamento de tabela.

1. Modifique o tipo de dados **TodoItem** para extrair de **StorageData** em vez de **EntityData**, da maneira descrita a seguir.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]O tipo **StorageData** tem uma propriedade de Id que exige uma chave composta que é uma cadeia de caracteres no formato *partitionId*,*rowValue*.

2. Em **TodoItemController**, adicione a instrução using a seguir.

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Substitua o método **Initialize** do **TodoItemController** pelo seguinte.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName,
                tableName, Request, Services);
        }

	Isso cria um novo gerenciador de domínio do armazenamento para o controlador solicitado usando a cadeia de conexão da conta de armazenamento.

3. Substitua o método **GetAllTodoItems** existente pelo código a seguir.

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        }

	Ao contrário de um Banco de Dados SQL, essa versão não retorna IQueryable<TEntity>; portanto, o resultado pode ser associado a uma consulta, mas não pode mais ser composto nela.

## Atualize o aplicativo de cliente

Você precisa fazer uma alteração no lado do cliente para fazer com que o aplicativo quickstart funcione com o back-end do .NET usando o armazenamento de tabela. Isso ocorre devido à chave composta esperada pelo provedor de armazenamento de tabela.

1. Abra o arquivo de código do cliente que contém o código de acesso a dados e encontre o método onde a operação de inserção foi realizada.

2. Atualize a instância de TodoItem que está sendo adicionada para definir explicitamente o campo Id no formato de cadeia de caracteres `<partitionID>,<rowValue>`.

	Este é um exemplo de como essa ID pode ser definida em um aplicativo em C#, em que a parte de partição é fixa e a parte da linha é baseada em GUID.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

Agora você está pronto para testar o aplicativo.

## <a name="test-application"></a>Testar o aplicativo

1. (Opcional) Publique novamente seu projeto de back-end .NET do serviço móvel.

	Você também pode testar seu serviço móvel localmente antes de publicar o projeto de back-end .NET no Azure. Se você testar localmente ou no Azure, o serviço móvel usará o armazenamento de tabela do Azure.

4. Execute o aplicativo cliente de início rápido conectado ao seu serviço móvel.

	Observe que você não vê os itens adicionados anteriormente usando o tutorial de início rápido. Isso ocorre porque o armazenamento de tabela está vazio.

5. Adicione novos itens para gerar alterações do banco de dados.

	O aplicativo e o serviço móvel devem se comportar como antes, exceto pelo fato de que agora os dados estão sendo armazenados no repositório não relacional, e não no Banco de Dados SQL.

##Próximas etapas

Agora que você viu como é fácil usar o armazenamento de tabela com o back-end .NET, que tal explorar algumas das outra opções de armazenamento de back-end?

+ [Conectar-se a um SQL Server local usando Conexões Híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>As Conexões Híbridas permitem que o serviço móvel se conecte com segurança aos ativos locais. Dessa forma, você pode disponibilizar seus dados locais para os clientes móveis usando o Azure. Os ativos com suporte incluem qualquer recurso que seja executado em uma porta TCP estática, incluindo Microsoft SQL Server, MySQL, APIs da web HTTP e os serviços Web mais personalizados.

+ [Carregar imagens no Armazenamento do Azure usando Serviços Móveis](mobile-services-dotnet-backend-windows-universal-dotnet-upload-data-blob-storage.md)</br>Mostra como estender o exemplo TodoList do projeto para permitir que você carregue imagens do seu aplicativo no armazenamento de Blob do Azure.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[portal clássico do Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab

<!---HONumber=AcomDC_0727_2016-->