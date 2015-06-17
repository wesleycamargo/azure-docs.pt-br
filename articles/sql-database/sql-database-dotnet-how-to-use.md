<properties 
	pageTitle="Como usar o banco de dados SQL (.NET) - guia de recursos do Azure" 
	description="Introdução ao banco de dados SQL. Saiba como criar uma instância do banco de dados SQL e conectá-la usando o ADO.NET, ODBC e provedor EntityClient." 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffreyg" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>







# Como usar um Banco de Dados SQL do Azure em aplicativos .NET.

Este guia mostra como criar uma instância de servidor lógico e de banco de dados no Banco de Dados SQL do Azure e conectar-se ao banco de dados usando as seguintes tecnologias de provedor de dados .NET Framework:
ADO.NET, ODBC e o provedor EntityClient.


## O que é o Banco de Dados SQL?

O Banco de Dados SQL fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com uma instância de Banco de Dados SQL, você pode provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que oferece disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.



## Entrar no Azure

O Banco de Dados SQL fornece serviços de armazenamento, acesso e gerenciamento de dados relacionais no Azure. Para usá-lo, você precisará de uma assinatura do Azure.

1. Abra um navegador da web e navegue até [http://azure.microsoft.com/](http://azure.microsoft.com). Para começar com uma conta gratuita, clique em avaliação gratuita no canto superior direito e siga as etapas.

2. Sua conta agora está criada. Você está pronto para começar.


## Criar e configurar o Banco de Dados SQL

Em seguida, crie e configure um servidor e banco de dados. No Portal de Gerenciamento do Azure, os fluxos de trabalho revisados permitem que você crie o banco de dados primeiro e, em seguida, acompanhe o provisionamento do servidor. 

<h3 name="createsrvr">Criar uma instância de banco de dados e um servidor lógico</h3>

1. Entre no [Portal de Gerenciamento do Azure][].

2. Clique em **+NOVO** na parte inferior da página.

3. Clique em **Serviços de Dados**.

4. Clique em **Bancos de Dados SQL**.

5. Clique em **Criação Personalizada**. 

6. Em Nome, digite um nome de banco de dados.

7. Escolha uma edição, um tamanho máximo e um agrupamento. Para a finalidade deste guia, você poderá usar os valores padrão. 

	O Banco de dados SQL oferece três edições de banco de dados, Básico, Normal e Premium.

	O MAXSIZE é especificado quando o banco de dados é primeiro criado e pode ser alterado posteriormente usando ALTER DATABASE. MAXSIZE permite limitar o tamanho do banco de dados.

	Para cada banco de dados SQL criado no Azure, há, na verdade, três réplicas desse banco de dados. Isso é feito para garantir uma alta disponibilidade.
O failover é transparente e faz parte do serviço. O [Contrato de nível
de serviço][] fornece 99,9% de tempo de atividade para o banco de dados SQL.

8. Em Servidor, selecione **Novo Servidor do Banco de Dados SQL**. 

9. Clique na seta para ir para a próxima página.

10. Em Configurações do Servidor, insira um nome de logon de autenticação do SQL Server.

	O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo logon de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido. 

	O logon não pode ser um endereço de email, conta de usuário do Windows ou um ID do Windows Live. Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL.

11. Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

12. Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

13. Certifique-se de manter a opção **Permitir que os Serviços do Azure acessem o servidor** selecionada para que você possa se conectar ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, os serviços de armazenamento e outros serviços no Azure. 

14. Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome do servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Depois que o banco de dados for criado, clique nele para abrir seu painel. O painel fornece as cadeias de conexão que você pode copiar e usar no código do aplicativo. Ele também mostra a URL de gerenciamento que você precisará especificar se estiver se conectando ao banco de dados do Management Studio ou de outra ferramenta administrativa.


![image](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

<h3 name="configFWLogical">Configurar o firewall para o servidor lógico</h3>

1. Clique em **Bancos de Dados SQL**, clique em **Servidores** na parte superior da página e, em seguida, clique no servidor que você acabou de criar.

	![Image2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. Clique em **Configurar**. 

3. Copie o endereço IP atual do cliente. Se você estiver se conectando a partir de uma rede, este é o endereço IP que seu  roteador ou o servidor proxy está escutando. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual para que você possa criar uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 

4. Cole o endereço IP em ENDEREÇO IP INICIAL e ENDEREÇO IP FINAL para estabelecer os endereços do intervalo com permissão para acessar o servidor. Mais tarde, se você encontrar erros de conexão indicando que o intervalo é muito estreito, você poderá editar essa regra para aumentar o intervalo.

	Se os computadores cliente estiverem usando endereços IP atribuídos dinamicamente, você deve especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

5. Digite um nome para a regra de firewall, como o nome de seu computador ou empresa.

6. Clique na marca de seleção ao lado da regra para salvá-la.

	![Image3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. Clique em **Salvar** na parte inferior da página para concluir a etapa. Se você não vir a opção **Salvar**, atualize a página do navegador.

Agora, você tem uma instância de banco de dados, um servidor lógico, uma regra de firewall que permite conexões de entrada do seu endereço IP e um logon de administrador. Agora, você está pronto para se conectar ao banco de dados de forma programática.


## conectar-se ao Banco de Dados SQL

Esta seção mostra como se conectar à instância do banco de dados SQL instância usando diferentes
provedores de dados .NET Framework.

Se você optar por usar o Visual Studio e sua configuração não incluir um aplicativo web do Azure como um front-end, não haverá nenhuma ferramenta adicional ou SDK necessário para ser instalado no computador de desenvolvimento. Você poderá simplesmente iniciar o desenvolvimento do seu aplicativo.

Você pode usar todas as mesmas ferramentas de designer no Visual Studio para trabalhar com o Banco de Dados SQL da mesma forma que você trabalha com o SQL Server. O Gerenciador de Servidores permite que você exiba (mas não edite) os objetos de banco de dados. A entidade do Visual Studio
O Designer do Modelo de Dados de Entidade do Visual Studio é totalmente funcional e você pode usá-lo para criar modelos no Banco de Dados SQL para trabalhar com o Entity Framework.

## Usando o Provedor de Dados .NET Framework para SQL Server

O namespace **System.Data.SqlClient** é o Provedor de dados do .NET Framework
Para o SQL Server.

A cadeia de conexão padrão tem esta aparência:

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

Você pode usar a classe **SQLConnectionStringBuilder** para compilar uma cadeia de conexão, conforme mostrado no exemplo de código a seguir:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Se os elementos de uma cadeia de conexão forem conhecidos antecipadamente, eles poderão ser armazenados em um arquivo de configuração e recuperados em tempo de execução para construir uma cadeia de conexão. Este é um exemplo de cadeia de conexão no arquivo de configuração:

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

Para recuperar a cadeia de conexão em um arquivo de configuração, você deve usar a classe**ConfigurationManager**:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

## Usando o Provedor de Dados .NET Framework para ODBC

O namespace **System.Data.Odbc** é o Provedor de Dados.NET Framework para ODBC. O exemplo a seguir é de uma cadeia de conexão ODBC:

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

A classe **OdbcConnection** representa uma conexão aberta com uma fonte de dados. Este é um exemplo de código sobre como abrir uma conexão:

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Se quiser compilar a cadeia de conexão em tempo de execução, você poderá usar a classe **OdbcConnectionStringBuilder**.

## Usando o provedor EntityClient

O namespace **System.Data.EntityClient** é o Provedor de Dados .NET Framework para o Entity Framework.

O Entity Framework permite que os desenvolvedores criem aplicativos de acesso de dados por meio de programação em relação a um modelo de aplicativo conceitual em vez de programação diretamente em relação a um esquema de armazenamento relacional. O
Entity Framework é compilado sobre provedores de dados ADO.NET específicos do armazenamento, fornecendo uma**EntityConnection** para o provedor de dados subjacente e o banco de dados relacional.

Para construir um objeto **EntityConnection**, você deve fazer referência a um conjunto de metadados que contem o mapeamento e os modelos necessários, e também uma cadeia de conexão e um nome de provedor de dados específico do armazenamento. Depois que**EntityConnection** estiver no local, as entidades poderão ser acessadas através das classes geradas no modelo conceitual.

Este é um exemplo de cadeia de conexão:

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Para obter mais informações, confira [Provedor EntityClient para o Entity
Framework][].

## Próximas etapas

Agora que você aprendeu as noções básicas de conexão com o Banco de Dados SQL, consulte os seguintes recursos para saber mais sobre o Banco de Dados SQL.

-   [Desenvolvimento: Tópicos de procedimentos (Banco de Dados SQL)][]
-   [Banco de Dados SQL][]


  [O que é o Banco de Dados SQL?]: #WhatIs
  [Entrar no Azure]: #PreReq1
  [Criar e configurar o banco de dados SQL]: #PreReq2
  [Conectar-se ao Banco de Dados SQL]: #connect-db
  [Conectar-se usando o ADO.NET]: #using-sql-server
  [Conectar-se usando o ODBC]: #using-ODBC
  [Conectar-se usando o provedor EntityClient]: #using-entity
  [Próximas etapas]: #next-steps
  [Avaliação gratuita do Azure]: {localLink:2187} "Avaliação gratuita"
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Como criar um banco de dados do SQL Server]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-server.aspx
  [Portal de Gerenciamento para o Banco de Dados SQL]: http://msdn.microsoft.com/library/windowsazure/gg442309.aspx
  [Firewall do banco de dados SQL]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-firewall.aspx
  [Suporte a ferramentas e utilitários (banco de dados SQL)]: http://msdn.microsoft.com/library/windowsazure/ee621784.aspx
  [Como criar um banco de dados SQL no Azure]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-database.aspx
  [Contrato de Nível de Serviço]: {localLink:1132} "SLA"
  [Provedor EntityClient para o Entity Framework]: http://msdn.microsoft.com/library/bb738561.aspx
  [Desenvolvimento: Tópicos de procedimentos (Banco de Dados SQL)]: http://msdn.microsoft.com/library/windowsazure/ee621787.aspx
  [Banco de Dados SQL]: http://msdn.microsoft.com/library/windowsazure/ee336279.aspx

<!--HONumber=47-->
 