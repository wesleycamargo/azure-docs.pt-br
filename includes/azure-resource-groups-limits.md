---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012484"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso |800 |Varia por tipo de recurso |
| Implantações por grupo de recursos no histórico de implantações |800<sup>1</sup> |800 |
| Recursos por implantação |800 |800 |
| Bloqueios de gerenciamento por escopo exclusivo |20 |20 |
| Número de marcas por recurso ou grupo de recursos |15 |15 |
| Comprimento da chave de marca |512 |512 |
| Comprimento do valor da marca |256 |256 |

<sup>1</sup>se você atingir o limite de 800 implantações por grupo de recursos, excluir as implantações do histórico que não são mais necessários. Excluindo uma entrada de histórico de implantação não afeta os recursos implantados. Exclua entradas do histórico com [az group deployment delete](/cli/azure/group/deployment) na CLI do Azure ou [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) no PowerShell.  Para um PowerShell script que automatiza a exclusão de implantações em um cenário de entrega contínua (CI/CD) e integração contínua consulte [deployments.ps1 remover](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limites de modelo

| Value | Limite padrão | Limite máximo |
| --- | --- | --- |
| parâmetros |256 |256 |
| variáveis |256 |256 |
| Recursos (incluindo a contagem de cópias) |800 |800 |
| outputs |64 |64 |
| Expressão de modelo |24.576 caracteres |24.576 caracteres |
| Recursos em modelos exportados |200 |200 | 
| Tamanho do modelo |1 MB |1 MB |
| Tamanho do arquivo de parâmetro |64 KB |64 KB |

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [usar modelos vinculados ao implantar recursos do Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
