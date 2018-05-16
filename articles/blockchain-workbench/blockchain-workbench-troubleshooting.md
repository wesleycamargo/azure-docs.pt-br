---
title: Solução de problemas do Azure Blockchain Workbench
description: Como solucionar problemas do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Solução de problemas do Azure Blockchain Workbench

Um script do PowerShell está disponível para auxiliar na depuração de desenvolvedor ou suporte. O script gera um resumo e coleta logs detalhados para solução de problemas. Os logs coletados incluem:

* Rede de Blockchain, como Ethereum
* Microservices Blockchain Workbench
* Application Insights
* Monitoramento do Azure (OMS)

Você pode usar as informações para determinar as próximas etapas e determinar a causa raiz de problemas. 

## <a name="troubleshooting-script"></a>Script de solução de problemas

O script de solução de problemas PowerShell está disponível no GitHub. [Baixe um arquivo zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Execute o script
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Execute o `collectBlockchainWorkbenchTroubleshooting.ps1` script para coletar logs e criar um arquivo ZIP que contém uma pasta de informações de solução de problemas. Por exemplo: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Este script aceita os seguintes parâmetros:

| Parâmetro  | DESCRIÇÃO | Obrigatório |
|---------|---------|----|
| SubscriptionID | SubscriptionID para criar ou localizar todos os recursos. | sim |
| ResourceGroupName | Nome do grupo de recursos do Azure onde o Blockchain Workbench foi implantado. | sim |
| OutputDirectory | Caminho para criar a saída do arquivo ZIP. Se não for especificado, o diretório atual é o padrão. | Não 
| OmsSubscriptionId | A id de assinatura em que o OMS é implantado. Passe apenas esse parâmetro se o OMS para a rede blockchain for implantado fora do grupo de recursos do Blockchain Workbench.| Não  |
| OmsResourceGroup |O grupo de recursos onde o OMS é implantado. Passe apenas esse parâmetro se o OMS para a rede blockchain for implantado fora do grupo de recursos do Blockchain Workbench.| Não  |
| OmsWorkspaceName | Nome do espaço de trabalho do OMS. Passe apenas esse parâmetro se o OMS para a rede blockchain for implantado fora do grupo de recursos do Blockchain Workbench | Não  |

## <a name="what-is-collected"></a>O que é coletado?

O arquivo ZIP de saída contém a seguinte estrutura de pasta:

| Pasta \ arquivo | DESCRIÇÃO  |
|---------|---------|
| \Summary.txt | Resumo do sistema |
| \metrics\blockchain | Métricas sobre o blockchain |
| \métricas\workbench | Métricas sobre o workbench |
| \detalhes\blockchain | Logs detalhados sobre o blockchain |
| \detalhes\workbench | Logs detalhados sobre o workbench |

O arquivo de resumo oferece um instantâneo da integridade do aplicativo e o estado geral do aplicativo. O resumo fornece as ações recomendadas, destaca os principais erros e os metadados sobre a execução de serviços.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Arquitetura do Blockchain Workbench](blockchain-workbench-architecture.md)