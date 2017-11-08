---
title: Exemplos do Azure PowerShell para o Azure Data Factory | Microsoft Docs
description: "Exemplos do Azure PowerShell – scripts para ajudá-lo a criar e gerenciar data factories."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: f223cd8efbff77b02598293afd3b30a662d3abf3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Exemplos do Azure PowerShell para o Azure Data Factory

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Azure Data Factory.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usar a versão 1 do serviço do Data Factory, que está em GA (disponibilidade geral), consulte os [exemplos do Data Factory versão 1](v1/data-factory-samples.md).

| |  |
|---|---|
|**Copiar dados**||
|[Copiar blobs de uma pasta para outra pasta em um Armazenamento de Blobs do Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| O script do PowerShell copia blobs de uma pasta no Armazenamento de Blobs do Azure para outra pasta no mesmo Armazenamento de Blobs. |
|[Copiar dados do SQL Server local para o Armazenamento de Blobs do Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| O script do PowerShell copia dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure. |
|[Cópia em massa](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell de exemplo copia dados de diversas tabelas em um banco de dados SQL do Azure para um SQL Data Warehouse do Azure. |
|[Cópia incremental](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell de exemplo carrega somente os registros novos ou atualizados de um armazenamento de dados de origem para um armazenamento de dados do coletor após a cópia completa inicial dos dados da origem para o coletor. |
|**Transformar dados**||
|[Transformar dados usando o cluster Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell transforma dados executando um programa em um cluster Spark. |
|**Migrar pacotes SSIS usando o método lift-and-shift para o Azure**||
|[Criar o Integration Runtime do Azure-SSIS](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell provisiona um tempo de execução de integração Azure-SSIS que executa pacotes SSIS (SQL Server Integration Services) no Azure. |



