---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de fonte de dados com suporte para modelos de dados no Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3b60a5b96d7b8a0c48aacc916b1ba933dcd83705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

Fontes de dados e conectores mostrados no Get Data ou no Assistente de Importação no Visual Studio são mostrados para o Azure Analysis Services e o SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados que você pode conectar dependem de vários fatores, como nível de compatibilidade do modelo, conectores de dados disponíveis, tipo de autenticação, provedores e suporte de gateway de dados local. 

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |
|---------|---------|---------|
|Banco de Dados SQL do Azure     |   sim      |    sim      |
|SQL Data Warehouse do Azure     |   sim      |   sim       |
|Armazenamento de Blobs do Azure*     |   sim       |    Não       |
|Armazenamento de Tabelas do Azure*    |   sim       |    Não       |
|Azure Cosmos DB*     |  sim        |  Não         |
|Azure Data Lake Store*     |   sim       |    Não       |
|Azure HDInsight (HDFS)*     |     sim     |   Não        |
|Azure HDInsight Spark*     |   sim       |   Não        |
||||

\*Somente modelos Tabular 1400.

**Provedor**   
Modelos na memória e DirectQuery que se conectam a fontes de dados do Azure usam o .NET Framework Data Provider para SQL Server.

## <a name="on-premises-data-sources"></a>Fontes de dados locais

Conectar a fontes de dados locais e ao servidor de AS do Azure requer um gateway local. Ao usar um gateway, serão necessários provedores de 64 bits.

### <a name="in-memory-and-directquery"></a>Na memória e DirectQuery

|Fonte de dados | Provedor na memória | Provedor do DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Provedor Microsoft OLE DB para SQL Server, Provedor de Dados .NET Framework para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, Provedor Microsoft OLE DB para SQL Server, Provedor de Dados .NET Framework para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| Oracle |Provedor Microsoft OLE DB para Oracle, Provedor de Dados Oracle para .NET |Provedor de Dados Oracle para .NET | |
| Teradata |Provedor OLE DB para Teradata, Provedor de Dados Teradata para .NET |Provedor de Dados Teradata para .NET | |
| | | |

### <a name="in-memory-only"></a>Somente na memória

|Fonte de dados  |  
|---------|---------|
|Banco de Dados do Access     |  
|Active Directory*     |  
|Serviços de análise     |  
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Pasta de trabalho do Excel     |  
|Exchange*     |  
|Pasta*     | 
|Documento JSON*     |  
|Linhas de binário*     | 
|Banco de Dados MySQL     | 
|Feed OData*     |  
|Consulta ODBC     | 
|OLE DB     |   
|Banco de Dados SQL Postgre*    | 
|SAP HANA*    |  
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Banco de dados Sybase     |  
|Tabela XML*    |  
|||
 
\*Somente modelos Tabular 1400.

## <a name="specifying-a-different-provider"></a>Especificar um provedor diferente

Os modelos de dados no Azure Analysis Services podem exigir diferentes provedores de dados durante a conexão com certas fontes de dados. Em alguns casos, modelos de tabela que se conectam a fontes de dados usando provedores nativos, como o SQL Server Native Client (SQLNCLI11), podem retornar um erro. Se usar provedores nativos diferentes de SQLOLEDB, você poderá ver a mensagem de erro: **O provedor 'SQLNCLI11.1' não está registrado**. Ou, se tiver um modelo DirectQuery que se conecta a fontes de dados locais e usar provedores nativos, você verá a mensagem de erro: **Erro ao criar o conjunto de linhas do OLE DB. Sintaxe incorreta próxima a 'LIMIT'**.

Ao migrar um modelo de tabela local do SQL Server Analysis Services para o Azure Analysis Services, talvez seja necessário alterar o provedor.

**Para especificar um provedor**

1. No SSDT > **Gerenciador de Modelo de Tabela** > **Fontes de Dados**, clique com o botão direito em uma conexão de fonte de dados e, em seguida, clique em **Editar Fonte de Dados**.
2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela Propriedades avançadas.
3. Em **Definir Propriedades Avançadas** > **Provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no SSDT (Visual Studio) ou no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="next-steps"></a>Próximas etapas
[Gateway Local](analysis-services-gateway.md)   
[Gerenciar seu serviço](analysis-services-manage.md)   

