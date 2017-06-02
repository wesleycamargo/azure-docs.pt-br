---
title: "Introdução ao catálogo do U-SQL da Análise Azure Data Lake | Microsoft Docs"
description: "Introdução ao catálogo do U-SQL da Análise Azure Data Lake"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 55fef96f-e941-4d09-af5e-dd7c88c502b2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: ef766161f53b92003c266f26af10a37790d8fad8
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="use-azure-data-lake-analytics-u-sql-catalog"></a>Usar o catálogo do Azure Data Lake Analytics (U-SQL)
O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake.

Cada conta da Análise Azure Data Lake tem exatamente um Catálogo do U-SQL associado a ela. Não é possível excluir o Catálogo do U-SQL. Atualmente, os Catálogos do U-SQL não podem ser compartilhados entre contas do Repositório Data Lake.

Cada Catálogo do U-SQL contém um banco de dados chamado **Mestre**. O Banco de Dados Mestre não pode ser excluído.  Cada Catálogo do U-SQL pode conter mais bancos de dados adicionais.

O banco de dados U-SQL contém:

* Assemblies – compartilham códigos do .NET entre scripts U-SQL.
* Funções de valores de tabela – compartilham códigos U-SQL entre scripts U-SQL.
* Tabelas – compartilham dados entre scripts U-SQL.
* Esquemas - compartilham esquemas de tabela entre scripts U-SQL.

## <a name="manage-catalogs"></a>Gerenciar catálogos
Cada conta da Análise Azure Data Lake tem uma conta do Repositório Azure Data Lake padrão associada a ela. Essa conta do Repositório Data Lake é conhecida como a conta padrão do Repositório Data Lake. O catálogo do U-SQL é armazenado na conta padrão do Repositório Data Lake na pasta /catalog. Não exclua nenhum arquivo da pasta /catalog.

### <a name="use-azure-portal"></a>Usar o portal do Azure
Veja [Gerenciar Data Lake Analytics usando o portal](data-lake-analytics-manage-use-portal.md#manage-data-lake-analytics-accounts)

### <a name="use-data-lake-tools-for-visual-studio"></a>Use as Ferramentas do Data Lake para Visual Studio.
Você pode usar as Ferramentas do Data Lake para Visual Studio para gerenciar o catálogo.  Para obter mais informações sobre as ferramentas, veja [Usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Para gerenciar o catálogo**

1. Abra o Visual Studio e conecte-se ao Azure. Para obter instruções, veja [Conectar-se ao Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
2. Abra **Gerenciador de Servidores** pressionando **CTRL+ALT+S**.
3. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda sua conta da Data Lake Analytics, expanda **Bancos de Dados** e expanda **mestre**.

    - Para adicionar um novo Banco de Dados, clique com o botão direito do mouse em **Banco de Dados** e clique em **Criar Banco de Dados**.
    - Para adicionar um novo assembly, clique com o botão direito do mouse em **Assemblies** e clique em **Registrar Assembly**.
    - Para adicionar um novo esquema, clique com o botão direito do mouse em **Esquemas** e, em seguida, em **Criar Esquema**.
    - Para adicionar uma nova tabela, clique com o botão direito do mouse em **Tabelas** e, em seguida, em **Criar Tabela**.
    - Para adicionar uma nova função com valor de tabela, confira [Desenvolver operadores U-SQL definidos pelo usuário para trabalhos do Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Procurar catálogos do U-SQL do Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)

## <a name="see-also"></a>Confira também
* Introdução

  * [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
  * [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Introdução à Análise Data Lake usando o SDK do .NET do Azure](data-lake-analytics-get-started-net-sdk.md)
  * [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Introdução à linguagem U-SQL da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
* U-SQL e desenvolvimento

  * [Introdução à linguagem U-SQL da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
  * [Usar funções da janela do U-SQL para trabalhos da Análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
  * [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos do Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* Gerenciamento

  * [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
  * [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
* Tutorial de ponta a ponta

  * [Usar tutoriais interativos da Análise Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
  * [Analisar logs de site usando a Análise Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

