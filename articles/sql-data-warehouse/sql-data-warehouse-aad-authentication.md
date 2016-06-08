<properties
   pageTitle="Conectar ao SQL Data Warehouse usando a autenticação do Azure Active Directory | Microsoft Azure"
   description="Saiba como se conectar ao SQL Data Warehouse usando a autenticação do Azure Active Directory."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/11/2016"
   ms.author="rick.byham@microsoft.com"/>

# Conexão ao SQL Data Warehouse usando a autenticação do Azure Active Directory


A autenticação do Azure Active Directory é um mecanismo de conexão com o SQL Data Warehouse do Microsoft Azure usando identidades no Azure Active Directory (Azure AD). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em um único local central. O gerenciamento central de ID fornece um único local para gerenciar usuários do SQL Data Warehouse e simplifica o gerenciamento de permissões.

## Benefícios

Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
- Permite o rodízio de senhas em um único lugar
- Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- A autenticação do Active Directory do Azure usa usuários de banco de dados independente para autenticar identidades no nível do banco de dados.
- O Azure Active Directory dá suporte à autenticação baseada em token para aplicativos que se conectam ao SQL Data Warehouse.

> [AZURE.IMPORTANT] A autenticação do Active Directory do Azure é um recurso de visualização e está sujeito aos termos de visualização no contrato de licença (por exemplo, o contrato corporativo, Contrato do Microsoft Azure ou Contrato de Assinatura do Microsoft Online), bem como quaisquer [Termos Complementares de Uso para Visualização do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicáveis.

## Etapas da configuração

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular um Active Directory do Azure
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador do Azure Active Directory para o SQL Data Warehouse do Azure
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar-se ao data warehouse usando identidades do Azure AD

As principais diferenças entre usar autenticação do Azure Active Directory com o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure é que você deve usar o SQL Server Data Tools em vez do SQL Server Management Studio para se conectar ao SQL Data Warehouse. O SQL Data Warehouse exige pelo menos a versão de abril de 2016 (14.0.60311.1) do SQL Server Data Tools para Visual Studio 2015. Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como uma solução alternativa, exiba os usuários em [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
## Próximas etapas 
- Conclua as etapas detalhadas. As etapas detalhadas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Siga as etapas detalhadas no tópico [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte a [Introdução às Permissões do Mecanismo de Banco de Dados](https://msdn.microsoft.com/library/mt667986.aspx).

<!---HONumber=AcomDC_0525_2016-->