<properties
   pageTitle="Autenticação no Azure SQL Data Warehouse | Microsoft Azure"
   description="Autenticação do AAD (Azure Active Directory) e SQL Server no Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/17/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Autenticação no Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Drivers](sql-data-warehouse-connection-strings.md)

Para se conectar ao SQL Data Warehouse, você precisará inserir credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, você também descobrirá que determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.

Para saber mais sobre segurança e como habilitar conexões com seu data warehouse, confira [Proteger um banco de dados no SQL Data Warehouse][].

## Autenticação do SQL
Para se conectar ao SQL Data Warehouse, você precisará fornecer as seguintes informações:

- Nome totalmente qualificado do servidor
- Especificar a autenticação SQL
- Nome de Usuário
- Senha
- Banco de dados padrão (opcional)

É importante observar que os usuários devem autenticar usando a autenticação SQL. Não há suporte para autenticação confiável no momento.

Por padrão, a conexão conectará ao banco de dados mestre e não ao banco de dados do usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

1. Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT ou em sua cadeia de conexão do aplicativo. Por exemplo, incluindo o parâmetro InitialCatalog em uma conexão ODBC.
2. Primeiro, selecione o banco de dados do usuário antes de criar uma sessão no SSDT.

> [AZURE.NOTE] Para obter instruções de conexão com o SQL Data Warehouse com SSDT, confira o artigo [Query with Visual Studio (Consulta com o Visual Studio)][].

Novamente, é importante observar que a instrução Transact-SQL **USE <your DB>** não tem suporte para alterar o banco de dados para uma conexão


## Autenticação do AAD (Azure Active Directory)

A autenticação do Azure Active Directory é um mecanismo de conexão com o SQL Data Warehouse do Microsoft Azure usando identidades no Azure Active Directory (Azure AD). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em um único local central. O gerenciamento central de ID fornece um único local para gerenciar usuários do SQL Data Warehouse e simplifica o gerenciamento de permissões.

### Benefícios

Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
- Permite o rodízio de senhas em um único lugar
- Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- A autenticação do Active Directory do Azure usa usuários de banco de dados independente para autenticar identidades no nível do banco de dados.
- O Azure Active Directory dá suporte à autenticação baseada em token para aplicativos que se conectam ao SQL Data Warehouse.

> [AZURE.IMPORTANT] A autenticação do Active Directory do Azure é um recurso de visualização e está sujeito aos termos de visualização no contrato de licença (por exemplo, o contrato corporativo, Contrato do Microsoft Azure ou Contrato de Assinatura do Microsoft Online), bem como quaisquer [Termos Complementares de Uso para Visualização do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicáveis.

### Etapas da configuração

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular um Active Directory do Azure
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador do Azure Active Directory para o SQL Data Warehouse do Azure
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar-se ao data warehouse usando identidades do Azure AD

As principais diferenças entre usar autenticação do Azure Active Directory com o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure é que você deve usar o SQL Server Data Tools em vez do SQL Server Management Studio para se conectar ao SQL Data Warehouse. O SQL Data Warehouse exige pelo menos a versão de abril de 2016 (14.0.60311.1) do SQL Server Data Tools para Visual Studio 2015. Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como uma solução alternativa, exiba os usuários em [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### Localização dos detalhes
- Conclua as etapas detalhadas. As etapas detalhadas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Siga as etapas detalhadas no tópico [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para saber mais, consulte a [Guia de introdução às permissões do mecanismo de banco de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## Próximas etapas

Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, confira [Query with Visual Studio (Consultar com o Visual Studio)][].

<!-- Article references -->
[Proteger um banco de dados no SQL Data Warehouse]: sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio (Consulta com o Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[Query with Visual Studio (Consultar com o Visual Studio)]: sql-data-warehouse-query-visual-studio.md

<!---HONumber=AcomDC_0622_2016-->