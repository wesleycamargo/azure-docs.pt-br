---
title: "Cliente de cobrança e estorno na pilha do Azure | Microsoft Docs"
description: "Saiba como recuperar informações de uso do recurso da pilha do Azure."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Uso e a cobrança na pilha do Azure

Uso representa a quantidade de recursos consumidos por um usuário. A pilha do Azure coleta informações de uso para cada usuário e o utiliza para cobrá-los. Este artigo descreve como os usuários de pilha do Azure são cobrados para uso de recursos e como as informações de cobrança são acessadas para análise de chargeback, etc.

A pilha do Azure contém a infraestrutura para coletar e agregar dados de uso para todos os recursos e encaminhar esses dados para comércio do Azure. Você pode acessar esses dados e exportá-lo para um sistema de cobrança usando um adaptador de cobrança ou exportá-lo para uma ferramenta de business intelligence como o Microsoft Power BI. Depois de exportar, essas informações de cobrança são usadas para análise ou transferidas para um sistema de estorno.

![Modelo conceitual de um adaptador de cobrança conectar-se a pilha do Azure para uma cobrança aplicativo](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Pipeline de uso

Cada provedor de recursos na pilha do Azure emite os dados de uso de acordo com a utilização de recursos. O serviço de uso periodicamente (por hora ou diariamente) agrega esses dados de uso e armazena-o no banco de dados de uso. Os dados de uso armazenados podem ser acessados por usuários e operadores de pilha do Azure localmente usando o uso de APIs. 

Se você tiver [registrado sua instância de pilha do Azure com o Azure](azure-stack-register.md), ponte de uso é configurado para enviar os dados de uso para comércio do Azure. Depois que os dados estão disponíveis no Azure, você pode acessá-lo por meio do portal de cobrança ou por meio da API de uso do Azure. Consulte o [relatórios de dados de uso](azure-stack-usage-reporting.md) tópico para saber mais sobre o uso de dados são relatados para o Azure. 

A imagem a seguir mostra os principais componentes do pipeline de uso:

![Pipeline de uso](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>As informações de uso pode localizar e como?

Provedores de recursos da pilha do Azure, como computação, armazenamento e rede, geram dados de uso de hora em hora para cada assinatura. Os dados de uso contém informações sobre o recurso usado como nome do recurso, a assinatura usada, a quantidade usada, etc. Para saber mais sobre os recursos de identificação de medidores, consulte o [uso perguntas frequentes sobre o API](azure-stack-usage-related-faq.md) artigo. 

Após a coleta de dados de uso é [relatados para o Azure](azure-stack-usage-reporting.md) para gerar uma lista, que pode ser visualizada por meio do portal de cobrança do Azure. 

> [!NOTE]
> Relatórios de dados de uso não é necessário para o Kit de desenvolvimento de pilha do Azure e para usuários do sistema de pilha do Azure integradas que no modelo de capacidade de licença. Para saber mais sobre licenciamento na pilha do Azure, consulte o [pacotes e preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) folha de dados.

O portal de cobrança do Azure mostra os dados de uso somente pelos recursos passíveis de cobrança. Além dos recursos passíveis de cobrança, a pilha do Azure captura dados de uso para um conjunto mais amplo de recursos, que pode ser acessado em seu ambiente de pilha do Azure por meio de APIs REST ou PowerShell. Operadores de pilha do Azure podem recuperar os dados de uso para todas as assinaturas de usuário, enquanto que um usuário pode obter apenas os detalhes de uso.

## <a name="retrieve-usage-information"></a>Recuperar informações de uso

Para gerar os dados de uso, você deve ter recursos que estão em execução e usando ativamente o sistema, por exemplo, uma máquina virtual ativa ou uma conta de armazenamento que contém alguns dados etc. Se você não tiver certeza se você tem todos os recursos em execução no Azure Marketplace de pilha, implanta uma máquina virtual (VM) e verifique se a VM monitoramento folha para verificar se ele está em execução. Use os seguintes cmdlets do PowerShell para exibir os dados de uso:

1. [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)
2. [Configurar o usuário de pilha do Azure](user/azure-stack-powershell-configure-user.md) ou [do operador da pilha do Azure](azure-stack-powershell-configure-admin.md) ambiente do PowerShell 

3. Para recuperar os dados de uso, use o [UsageAggregates Get](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet do PowerShell:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Próximas etapas

[Relatar dados de uso de pilha do Azure para o Azure](azure-stack-usage-reporting.md)

[Uso do provedor de recursos API](azure-stack-provider-resource-api.md)

[API de uso de recursos de locatário](azure-stack-tenant-resource-usage-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)

