---
title: Guias de início rápido para se conectar e consultar no Banco de Dados SQL do Azure | Microsoft Docs
description: Guias de início rápido de banco de dados SQL do Azure mostrando como se conectar e fazer consultas em um banco de dados do SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Guias de início rápido para se conectar e consultar no Banco de Dados SQL do Azure

O documento a seguir inclui contém links para exemplos do Azure que mostram como se conectar e consultar um banco de dados SQL do Azure. Ele também fornece algumas recomendações para TLS.

## <a name="quickstarts"></a>Guia de início rápido

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Este guia rápido demonstra como usar o SSMS para conectar um banco de dados SQL do Azure, em seguida, usar instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Este início rápido demonstra como usar o SQL Operations Studio (versão prévia) para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL (T-SQL) para criar o TutorialDB usado nos tutoriais do SQL Operations Studio (versão prévia).|
|[Portal do Azure](sql-database-connect-query-portal.md)|Este guia rápido demonstra como usar o editor de consultas para se conectar a um banco de dados SQL e depois usar instruções do Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Este início rápido demonstra como usar o Visual Studio Code para conectar um banco de dados SQL do Azure e depois usar as instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados.|
|[.NET com Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Este início rápido demonstra como usar o .NET Framework a fim de criar um programa C# com o Visual Studio para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Este início rápido demonstra como usar o .NET Core no Windows/Linux/macOS a fim de criar um programa C# para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Go](sql-database-connect-query-go.md)|Este guia de início rápido demonstra como usar Go para se conectar a um Banco de Dados SQL do Azure. As instruções Transact-SQL para consultar e modificar dados também são demonstradas.|
|[Java](sql-database-connect-query-java.md)|Este guia de início rápido demonstra como usar o Java para conectar um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Node.js](sql-database-connect-query-nodejs.md)|Este início rápido demonstra como usar o Node.js a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[PHP](sql-database-connect-query-php.md)|Este início rápido demonstra como usar o PHP a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Python](sql-database-connect-query-python.md)|Este início rápido demonstra como usar o Python para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados. |
|[Ruby](sql-database-connect-query-ruby.md)|Este início rápido demonstra como usar o Ruby a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerações sobre TLS para conectividade do Banco de Dados SQL
O TLS é usado por todos os drivers que a Microsoft fornece ou dá suporte para conexão ao Banco de Dados SQL do Azure. Nenhuma configuração especial é necessária. Para todas as conexões com o SQL Server ou o Banco de Dados SQL do Azure, recomendamos que todos os aplicativos definam as seguintes configurações ou seus equivalentes:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Alguns sistemas usam palavras-chave diferentes, porém equivalentes, para essas palavras-chave de configuração. Essas configurações garantem que o driver do cliente verifica a identidade do certificado TLS recebida do servidor.

Também recomendamos que você desabilite o TLS 1.1 e 1.0 no cliente, caso precise cumprir o PCI-DSS (Payment Card Industry – Data Security Standard).

Drivers não Microsoft não podem usar o TLS por padrão. Isso pode ser um fator importante ao se conectar ao Banco de Dados SQL do Azure. Aplicativos com drivers inseridos podem não permitir o controle dessas configurações de conexão. Recomendamos que você examine a segurança desses drivers e aplicativos antes de usá-los em sistemas que interagem com os dados confidenciais.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a arquitetura de conectividade, consulte [Arquitetura de conectividade do Banco de Dados SQL do Azure](sql-database-connectivity-architecture.md).