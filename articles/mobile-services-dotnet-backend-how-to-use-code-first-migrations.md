<properties pageTitle="Como usar o back-end do .NET para Migrações Code First (Serviços Móveis)" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerações para oferecer suporte a vários clientes de um único serviço móvel" authors="glenga" solutions="" writer="glenga" manager="dwrede" editor="" />

# Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET

Em um projeto de serviço móvel de back-end do .NET, o inicializador de banco de dados padrão do Entity Framework Code First deriva da classe [DropCreateDatabaseIfModelChanges]. Esse inicializador informa ao Entity Framework para remover e recriar o banco de dados sempre que detecta uma alteração no modelo de dados exposta por seu [DbContext]. Você deve continuar a usar esse inicializador durante o desenvolvimento local de seu projeto de serviço móvel, e os tutoriais de back-end do .NET pressupõem que você esteja usando esse inicializador. No entanto, em situações em que você deseja fazer alterações no modelo de dados e manter os dados existentes no banco de dados, você deve usar as Migrações Code First. O uso de Migrações Code First também é uma boa solução para publicar alterações do modelo de dados no Azure, pois um Banco de Dados SQL não pode ser removido.

Este tópico mostra como usar Migrações Code First para fazer alterações no modelo de dados para um Banco de Dados SQL existente sem perder os dados existentes. Este procedimento pressupõe que você já tenha publicado o projeto de serviço móvel no Azure, que há dados existentes no banco de dados, e que os modelos de dados locais e remotos ainda estão sincronizados.

>[WACOM.NOTE]Recomendamos concluir o máximo possível do desenvolvimento de seu modelo de dados em seu computador local antes de publicar no Azure. Se você já publicou o projeto de serviço móvel de back-end do .NET para o Azure, e o esquema de tabela do Banco de Dados SQL não coincide com o modelo de dados atual do projeto, você deve remover as tabelas ou de outra forma colocá-las em sincronia manualmente antes de tentar publicar com o uso de Migrações Code First. 

Ao desenvolver um projeto de serviço móvel de back-end do .NET no computador local, a maneira mais fácil de lidar com as alterações do modelo de dados é continuar a usar o inicializador padrão que remove e recria o banco de dados sempre que for detectada uma alteração do modelo de dados. Essa mesma abordagem não funciona ao republicar seu projeto no Azure. O inicializador falha porque o tempo de execução não tem permissões para remover um Banco de Dados SQL no Azure, o que é uma coisa boa. 

>[WACOM.NOTE]Ao desenvolver e testar o projeto de serviço móvel em serviços ativos do Azure, você deve sempre usar uma instância do serviço móvel dedicada para teste. Você nunca deve desenvolver ou testar em um serviço móvel que esteja em produção ou sendo usado por aplicativos cliente.

## Remover tabelas no Banco de Dados SQL

Para que você possa fazer com que as Migrações funcionem no Azure em um Banco de Dados SQL, você deve remover manualmente todas as tabelas existentes no esquema do banco de dados usado pelo serviço móvel. Use as seguintes etapas para remover tabelas existentes do banco de dados SQL. Se o esquema do banco de dados já estiver em sincronia com o modelo de dados atual, você poderá ignorar isso e começar com [Migrações].

1. Faça logon no [Portal de Gerenciamento do Azure], selecione o serviço móvel, clique na guia **Configurar** e, em seguida, clique no link **Banco de Dados SQL**. 

	![][0]

	Isso abre a página do portal do banco de dados usado pelo serviço móvel.

2. Clique no botão **Gerenciar** e faça logon no servidor do Banco de Dados SQL. 

	![][1]

3. No gerenciador de Banco de Dados SQL, clique em **Design**, clique em **Tabelas**, selecione uma tabela no esquema do seu serviço móvel, clique em **Remover tabela**e, em seguida, em **OK** para confirmar.

	![][2]
   
4. Repita a etapa anterior para cada tabela no esquema do serviço móvel.

	Com as tabelas existentes removidas, as Migrações Code First podem ser inicializadas no Banco de Dados SQL. As tabelas que não pertencem ao esquema do serviço móvel não afetam o serviço móvel e não devem ser removidas.

## <a name="migrations"></a>Habilitar as Migrações Code First

As Migrações Code First usam um método de instantâneo para gerar código que, quando executado, faz alterações no esquema do banco de dados. Com as Migrações, você pode fazer alterações incrementais em seu modelo de dados e manter os dados existentes no banco de dados. As etapas a seguir ativam as Migrações e aplicam as alterações no modelo de dados no projeto, no banco de dados local e no Azure. 

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

	No código acima, você deve substituir a cadeia de caracteres _todolistService_ pelo namespace de seu projeto que, para o projeto quickstart baixado é quickstart <em>mobile&#95;service&#95;name</em>Service.  
 
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

Você pode fazer com que as Migrações adicionem dados semente ao banco de dados quando uma migração é executada. A classe Configuration tem um método Seed que você pode substituir para inserir ou atualizar dados. O arquivo de código Configuration.cs é adicionado à pasta Migrations quando as Migrações são habilitadas. Esses exemplos mostram como substituir o método [Seed] para propagar dados para a tabela **TodoItems**. O método [Seed] é chamado após a migração para a versão mais recente. 

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
[Migrações]: #migrations
[Enviando dados em migrações]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/query/dev12.query?appId=Dev12IDEF1&l=pt-br&k=k("System.Data.Entity.DropCreateDatabaseIfModelChanges`1");k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5);k(DevLang-csharp)&rd=true
[Semente]: http://msdn.microsoft.com/pt-br/library/hh829453(v=vs.113).aspx
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/pt-br/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/pt-br/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx

