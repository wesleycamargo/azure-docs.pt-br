---
title: "Conexões de fonte de dados | Microsoft Docs"
description: "Descreve as conexões de fonte de dados para modelos de dados no Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 1b439e2b48cc009e727a49c271318cfd4ead9ef2


---
# <a name="datasource-connections"></a>Conexões de fonte de dados
Os modelos de dados no Azure Analysis Services podem exigir diferentes provedores de dados durante a conexão com certas fontes de dados. Em alguns casos, modelos de tabela que se conectam a fontes de dados usando provedores nativos, como o SQL Server Native Client (SQLNCLI11), podem retornar um erro.

Por exemplo, se você tiver um modelo de dados na memória de Consulta Direta que se conecta a uma fonte de dados de nuvem, como o Banco de Dados SQL do Azure, se você usar provedores nativos diferentes de SQLOLEDB, verá a mensagem de erro: **"O provedor 'SQLNCLI11.1' não está registrado"**.

Ou, se você tiver um modelo DirectQuery que se conecta a fontes de dados locais, se você usar provedores nativos, verá a mensagem de erro: **"Erro ao criar o conjunto de linhas do OLE DB. Sintaxe incorreta próxima a 'LIMIT' "**.

## <a name="data-source-providers"></a>Provedores de fonte de dados
Os provedores de fonte de dados a seguir têm suporte para modelos de dados na memória ou de Consulta Direta ao se conectar a fontes de dados em nuvem ou locais:

|  | **Fonte de dados** | **Na memória** | **Consulta Direta** |
| --- | --- | --- | --- |
| **Nuvem** |SQL Data Warehouse do Azure |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server |
| Banco de Dados SQL do Azure |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |
| **Local** (via Gateway) |SQL Server |SQL Server Native Client 11.0 |Provedor de Dados .NET Framework para SQL Server |
| SQL Server |Provedor Microsoft OLE DB para SQL Server |Provedor de Dados .NET Framework para SQL Server | |
| SQL Server |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |
| Oracle |Provedor Microsoft OLE DB para Oracle |Provedor de Dados Oracle para .NET | |
| Oracle |Provedor de Dados Oracle para .NET |Provedor de Dados Oracle para .NET | |
| Teradata |Provedor OLE DB para Teradata |Provedor de Dados Teradata para .NET | |
| Teradata |Provedor de Dados Teradata para .NET |Provedor de Dados Teradata para .NET | |
| Analytics Platform System |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |

> [!NOTE]
> Certifique-se de que os provedores de 64 bits estejam instalados ao usar o Gateway Local.
> 
> 

Ao migrar um modelo de tabela local do SQL Server Analysis Services para o Azure Analysis Services, talvez seja necessário alterar o provedor.

**Para especificar um provedor de fonte de dados**

1. No SSDT > **Gerenciador de Modelo de Tabela** > **Fontes de Dados**, clique com o botão direito em uma conexão de fonte de dados e, em seguida, clique em **Editar Fonte de Dados**.
2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela Propriedades avançadas.
3. Em **Definir Propriedades Avançadas** > **Provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no SSDT ou o no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="next-steps"></a>Próximas etapas
Se você tiver fontes de dados locais, instale o [Gateway local](analysis-services-gateway.md). Para saber mais sobre como gerenciar seu servidor no SSDT ou SSMS, consulte [Gerenciar seu servidor](analysis-services-manage.md).




<!--HONumber=Nov16_HO3-->


