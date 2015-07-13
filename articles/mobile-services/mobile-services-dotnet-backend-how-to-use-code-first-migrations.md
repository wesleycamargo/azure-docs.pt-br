<properties 
	pageTitle="Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET" 
	description="Este tópico descreve os inicializadores de modelo de dados e como fazer alterações no modelo de dados em um serviço móvel de back-end do .NET." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET

Este tópico mostra como usar Migrações Code First do Entity Framework para fazer alterações no modelo de dados para um Banco de Dados SQL existente para evitar perder os dados existentes. Este procedimento pressupõe que você já tenha publicado o projeto de serviço móvel no Azure, que há dados existentes no banco de dados, e que os modelos de dados locais e remotos ainda estão sincronizados. Este tópico também descreve os inicializadores do Code First implementados pelos Serviços Móveis do Azure que são usados durante o desenvolvimento. Esses inicializadores permitem quem você faça alterações de esquema facilmente sem usar as Migrações Code First quando não for necessário manter seus dados existentes.

>[AZURE.NOTE]O nome do esquema usado para prefixar suas tabelas no banco de dados SQL é definido pela configuração de aplicativo MS_MobileServiceName no arquivo web. config. Ao baixar o projeto inicial do portal, esse valor já está definido como o nome do serviço móvel. Quando o nome do esquema coincide com o serviço móvel, vários serviços móveis podem compartilhar com segurança a mesma instância de banco de dados.

## Atualização do modelo de dados

Conforme você adiciona funcionalidades ao serviço móvel de back-end do .NET, pode adicionar novos controladores para expor os novos pontos de extremidade em sua API. Você cria uma nova API como um controlador personalizado ou um controlador de tabela. Um [TableController<TEntity>] expõe um tipo de dados que herda de [EntityData]. Para permitir que dados sejam mantidos no banco de dados, esse tipo de dados deve ser adicionado ao modelo de dados como um novo [DbSet<T>] no [DbContext]. Para saber mais sobre o Code First no Entity Framework, consulte [Criar um modelo com o Code First](https://msdn.microsoft.com/data/ee712907#codefirst).

O Visual Studio facilita a criação de um novo controlador de tabela para expor um novo tipo de dados para aplicativos cliente. Para obter mais informações, consulte [Como usar controladores para acessar dados nos serviços móveis](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx).

## Inicializadores de modelo de dados

Os Serviços Móveis fornecem duas classes básicas do inicializador do modelo de dados em um projeto do serviço móvel de back-end do .NET. Ambos os inicializadores descartam e recriam tabelas no banco de dados quando o Entity Framework detectar uma alteração no modelo de dados no seu [DbContext]. Esses inicializadores são criados para funcionar quando seu serviço móvel está sendo executado em um computador local e quando está hospedado no Azure.

>[AZURE.NOTE]Quando você publica um serviço móvel de back-end do .NET, o inicializador não é executado até que ocorra uma operação de acesso a dados. Isso significa que, para um serviço publicado recentemente, as tabelas de dados usadas para o armazenamento não são criadas até que uma operação de acesso as dados, como uma consulta, é solicitada pelo cliente.
>
>Você também pode executar uma operação de acesso a dados usando a funcionalidade interna de ajuda da API, acessada por meio do link **Experimentar** na página inicial. Para obter mais informações sobre como usar a API de páginas para testar o seu serviço móvel, consulte a seção Testar o projeto de serviço móvel localmente em [Adicionar Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally).

Ambos os inicializadores excluem do banco de dados todas as tabelas, exibições, funções e procedimentos no esquema usado pelo serviço móvel.

+ **ClearDatabaseSchemaIfModelChanges** <br/> Os objetos de esquema são excluídos somente quando o Code First detecta uma alteração no modelo de dados. O inicialiazador padrão em um projeto de back-end do .NET baixado do [Portal de Gerenciamento do Azure] herda desta classe base.
 
+ **ClearDatabaseSchemaAlways**: <br/> os objetos de esquema são excluídos sempre que o modelo de dados é acessado. Use esta classe base para redefinir o banco de dados sem ter que fazer uma alteração no modelo de dados.

No projeto de início rápido baixado, o inicializador do Code First é definido no arquivo WebApiConfig.cs. Substitua o método **Seed** para adicionar linhas iniciais de dados para as novas tabelas. Para exemplos de dados de propagação, consulte [Propagando dados em migrações].Você pode usar os inicializadores de modelo de dados do Code First ao executar em um computador local. No entanto, os inicializadores que tentam remover o banco de dados falharão no Azure, pois o usuário não tem permissões para remover o banco de dados, o que é uma coisa boa.

Você pode continuar a usar inicializadores durante o desenvolvimento local de seu serviço móvel, e os tutoriais de back-end do .NET pressupõem que você esteja usando inicializadores. No entanto, em situações em que você deseja fazer alterações no modelo de dados e manter os dados existentes no banco de dados, você deve usar as Migrações Code First.

>[AZURE.IMPORTANT]Ao desenvolver e testar seu projeto de serviço móvel em serviços ativos do Azure, você sempre deve usar uma instância de serviço móvel dedicada para teste. Você nunca deve desenvolver ou testar em um serviço móvel que esteja em produção ou sendo usado por aplicativos cliente.

## <a name="migrations"></a>Habilitar as Migrações Code First

As Migrações Code First usam um método de instantâneo para gerar código que, quando executado, faz alterações no esquema do banco de dados. Com as Migrações, você pode fazer alterações incrementais em seu modelo de dados e manter os dados existentes no banco de dados.

>[AZURE.NOTE]Se você já publicou o projeto de serviço móvel de back-end do .NET para o Azure, e o esquema de tabela do Banco de Dados SQL não coincide com o modelo de dados atual do projeto, você deve usar um inicializador, remover as tabelas manualmente ou colocar o esquema e modelo de dados em sincronia de outro modo antes de tentar publicar com o uso de Migrações Code First.

As etapas a seguir ativam as Migrações e aplicam as alterações no modelo de dados no projeto, no banco de dados local e no Azure.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do serviço móvel e clique em **Configurar como projeto de inicialização**.
 
2. No menu **Ferramentas**, expanda o **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.

	Isso exibe o Console do Gerenciador de Pacotes, que você usará para gerenciar suas Migrações Code First.

3. No Console do Gerenciador de Pacotes, execute o seguinte comando:

		PM> Enable-Migrations

	Isso ativa as Migrações Code First em seu projeto.

4. No console, execute o seguinte comando:

		PM> Add-Migration Initial

	Isso cria uma nova migração chamada *Initial*. O código de migração é armazenado na pasta do projeto Migrations.

5. Expanda a pasta App_Start, abra o arquivo do projeto WebApiConfig.cs e adicione as seguintes instruções **using**:

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	No código acima, você deve substituir a cadeia de caracteres _todolistService_ pelo namespace do seu projeto que, para o projeto de início rápido baixado é <em>mobile&#95;service&#95;name</em>Service.
 
6. Nesse mesmo arquivo de código, comente a chamada para o método **Database.SetInitializer** e adicione o seguinte código depois dela:

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	Isso desabilita o banco de dados do Code First padrão que remove e recria o banco de dados e o substitui por uma solicitação explícita para aplicar a migração mais recente. Nesse ponto, as alterações no modelo de dados resultarão em uma InvalidOperationException quando os dados forem acessados, a menos que a migração tenha sido criada para ele. Mais adiante, o serviço deve usar Migrações Code First para migrar as alterações no modelo de dados para o banco de dados.

7.  Pressione F5 para iniciar o projeto do serviço móvel no computador local.
 
	Nesse ponto, o banco de dados está em sincronia com o modelo de dados. Se você forneceu dados semente, poderá verificá-los clicando em **Experimentar**, **OBTER tabelas/todoitem**, em seguida, **Experimentar** e **Enviar**. Para obter mais informações, consulte [Enviando dados em migrações].

8.   Agora faça uma alteração em seu modelo de dados, como a adição de uma nova propriedade UserId ao tipo TodoItem, recompile o projeto e, em seguida, no Gerenciador de Pacotes, execute o seguinte comando:

		PM> Add-Migration NewUserId
                                                               
	Isso cria uma nova migração chamada *NewUserId*. Um novo arquivo de código que implementa essa alteração é adicionado à pasta Migrations

9.  Pressione F5 novamente para reiniciar o projeto do serviço móvel no computador local.

	A migração é aplicada ao banco de dados e o banco de dados estará em sincronia com o modelo de dados novamente. Se você forneceu dados semente, poderá verificá-los clicando em **Experimentar**, **OBTER tabelas/todoitem**, em seguida, **Experimentar** e **Enviar**. Para obter mais informações, consulte [Enviando dados em migrações].

10. Republique o serviço móvel no Azure e execute o aplicativo cliente para acessar os dados e verificar se os dados são carregados e se não ocorre nenhum erro.

13. (Opcional) No [Portal de Gerenciamento do Azure], selecione o serviço móvel, clique na guia **Configurar** e, em seguida, clique no link **Banco de Dados SQL**.

	![][0]

	Isso navega para a página Banco de Dados SQL do banco de dados do serviço móvel.

14. (Opcional) Clique em **gerenciar**, faça logon no servidor de Banco de Dados SQL, clique em **Design** e verifique se as alterações do esquema foram feitas no Azure.

    ![][1]


##<a name="seeding"></a>Propagando dados em migrações

Você pode fazer com que as Migrações adicionem dados semente ao banco de dados quando uma migração é executada. A classe **Configuration** tem um método **Seed** que você pode substituir para inserir ou atualizar dados. O arquivo de código Configuration.cs é adicionado à pasta Migrations quando as Migrações são habilitadas. Esses exemplos mostram como substituir o método [Seed] para propagar dados para a tabela **TodoItems**. O método [Seed] é chamado após a migração para a versão mais recente.

###Propagar uma nova tabela

O código a seguir propaga a tabela **TodoItems** com novas linhas de dados:

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###Propagar uma nova coluna em uma tabela

O código a seguir propaga apenas a coluna UserId:
 		    
        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Esse código chama o método de extensão auxiliar [AddOrUpdate] para adicionar dados semente à nova coluna UserId. Usando o [AddOrUpdate], linhas duplicadas não são criadas.

<!-- Anchors -->
[Migrations]: #migrations
[Enviando dados em migrações]: #seeding
[Propagando dados em migrações]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[TableController<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx
 

<!---HONumber=62-->