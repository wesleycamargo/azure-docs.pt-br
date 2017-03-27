---
title: Azure Monitor de Script CLI e escala de um Banco de Dados SQL individual | Microsoft Docs
description: "Exemplo de Script da CLI do Azure – Monitorar e dimensionar um único Banco de Dados SQL usando a CLI do Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 4cfc4ab09f7adead289cb949373730bffcfa13ec
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-the-azure-cli"></a>Monitorar e dimensionar um único Banco de Dados SQL usando a CLI do Azure

Este script de exemplo da CLI dimensiona um Banco de Dados SQL do Azure individual para um nível de desempenho diferente depois de consultar as informações de tamanho do banco de dados. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. 

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitorar e dimensionar um Banco de Dados SQL individual")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Cria um servidor lógico que hospeda um banco de dados. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | Mostra as informações de uso do tamanho de um banco de dados. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Atualiza as propriedades do banco de dados (como o nível de desempenho ou de camada de serviço) ou move um banco de dados para dentro, para fora entre pools elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).

