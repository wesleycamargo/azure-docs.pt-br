---
title: Usar R para consultar um Banco de Dados SQL do Azure
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra como usar script R para conectar-se a um Banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 2b1206e3087b0573736174d4eed502653d76f7a5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001177"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Início Rápido: Usar R para consultar um Banco de Dados SQL do Azure (versão prévia)

 Este início rápido demonstra como usar [R](https://www.r-project.org/) com os Serviços do Machine Learning para conectar-se a um Banco de Dados SQL do Azure e usar instruções Transact-SQL para consultar dados. Os Serviços do Machine Learning são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. Para obter mais informações, consulte [Serviços do Machine Learning do Banco de Dados SQL do Azure com R (versão prévia)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, tenha o seguinte:

- Um banco de dados SQL do Azure. Você pode usar um destes guias de início rápido para criar e, em seguida, configurar um banco de dados no Banco de Dados SQL do Azure:

  || Banco de dados individual | Instância gerenciada |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall de IP no nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade do local](sql-database-managed-instance-configure-p2s.md)
  |||

- Serviços do Machine Learning (com o R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- O SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)) mais recente. Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   O script é passado por meio do argumento `@script`. Tudo dentro do argumento `@script` precisa ser código R válido.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se você receber erros, talvez a versão prévia pública dos Serviços de Machine Learning (com R) não esteja habilitada para seu banco de dados SQL. Veja os [Pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas na janela **Mensagens**.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Serviços do Machine Learning do Banco de Dados SQL do Azure (com o R)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-create-script.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
