---
title: Relatar dados de uso do Azure Stack no Azure | Microsoft Docs
description: Saiba como configurar relatórios no Azure Stack de dados de uso.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: e4e1b3cb823d08948c31aa6486c08a930a377dfa
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161949"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Relatar dados de uso do Azure Stack para o Azure 

Dados de uso, também chamados de dados de consumo, representam a quantidade de recursos usados. 

Sistemas de vários nós de pilha do Azure que usam o modelo de cobrança baseado em consumo devem relatar dados de uso para o Azure para fins de cobrança. Operadores do Azure Stack devem configurar sua instância do Azure Stack para relatar dados de uso para o Azure.

> [!NOTE]
> Relatórios de dados de uso é necessário para os usuários de vários nós do Azure Stack que no modelo de pagamento-como-o uso de licença. É opcional para os clientes que licenciam sob o modelo de capacidade (consulte a [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). Para usuários do Kit de desenvolvimento do Azure Stack, os operadores do Azure Stack podem relatar dados de uso e testar o recurso. No entanto, os usuários não serão cobrados por qualquer uso incorrem. 

![fluxo de cobrança](media/azure-stack-usage-reporting/billing-flow.png)

Dados de uso são enviados do Azure Stack para o Azure por meio da ponte do Azure. No Azure, o sistema de comércio processa os dados de uso e gera a fatura. Depois que a fatura é gerada, o proprietário da assinatura do Azure pode exibir e baixá-lo a [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). Para saber mais sobre como o Azure Stack é licenciado, consulte o [do Azure Stack, pacotes e preços documento](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Configurar os relatórios de dados de uso

Para configurar os relatórios de dados de uso, você deve [Registre sua instância do Azure Stack com o Azure](azure-stack-register.md). Como parte do processo de registro, o componente de ponte do Azure do Azure Stack, que conecta o Azure Stack para o Azure e envia os dados de uso, está configurado. Os seguintes dados de uso são enviados da pilha do Azure para o Azure:

- **ID do medidor** – ID exclusiva para o recurso que foi consumido.
- **Quantidade** – a quantidade de uso de recursos.
- **Local** – local em que o recurso atual do Azure Stack é implantado.
- **URI do recurso** – totalmente qualificado do URI do recurso para o qual o uso está sendo relatado.
- **ID da assinatura** – ID da assinatura do usuário do Azure Stack, o que é a assinatura (Azure Stack) local.
- **Tempo** – hora de início e término dos dados de uso. Há algum atraso entre a hora quando esses recursos serão consumidos no Azure Stack e quando os dados de uso são relatados ao comércio. Azure agrega dados de uso de pilha para cada 24 horas e dados de uso de relatórios para o pipeline de comércio no Azure usa outro algumas horas. Então, uso ocorre logo antes da meia-noite pode aparecer no Azure no dia seguinte.

## <a name="generate-usage-data-reporting"></a>Gerar relatórios de dados de uso

- Para testar os relatórios de dados de uso, crie alguns recursos no Azure Stack. Por exemplo, você pode criar uma [conta de armazenamento](azure-stack-provision-storage-account.md), [VM do Windows Server](azure-stack-provision-vm.md) e uma VM Linux com SKUs básico e Standard para ver como o uso do núcleo é reportado. Os dados de uso para diferentes tipos de recursos são relatados em medidores diferentes.

- Deixe seus recursos em execução por algumas horas. Informações de uso são coletadas aproximadamente uma vez a cada hora. Depois de coletar, esses dados são transmitidos para o Azure e processados no sistema de comércio do Azure. Esse processo pode demorar algumas horas.

## <a name="view-usage---csp-subscriptions"></a>Exibir uso - assinaturas de CSP

Se você registrou sua pilha do Azure usando uma assinatura do CSP, você pode exibir seu uso e os encargos da mesma maneira na qual você exibir o consumo do Azure. Uso da pilha do Azure serão incluído na sua fatura e no arquivo de reconciliação, disponível por meio de [Partner Center](https://partnercenter.microsoft.com/partner/home). O arquivo de reconciliação é atualizado mensalmente. Se você precisar acessar informações de uso recentes do Azure Stack, você pode usar as APIs do Partner Center.

![Centro de parceiros](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage--enterprise-agreement-subscriptions"></a>Exibir uso – assinaturas do Enterprise Agreement

Se você registrou sua pilha do Azure usando uma assinatura do Enterprise Agreement, você pode exibir seu uso e os encargos na [portal de EA](https://ea.azure.com/). Uso da pilha do Azure está incluído na downloads avançados, juntamente com o uso do Azure na seção relatórios neste portal. 

## <a name="view-usage--other-subscriptions"></a>Exibir uso – outras assinaturas

Se você registrou sua pilha do Azure usando qualquer outra assinatura digite; Por exemplo, uma assinatura pré-paga, você pode exibir o uso e cobranças no Centro de contas do Azure. Entrar para o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o Azure conta administrador e selecione a assinatura do Azure que você usou para registrar o Azure Stack. Você pode exibir os dados de uso do Azure Stack, o valor cobrado por cada um dos recursos usados, conforme mostrado na imagem a seguir:

![fluxo de cobrança](media/azure-stack-usage-reporting/pricing-details.png)

O Kit de desenvolvimento do Azure Stack, recursos do Azure Stack não são cobrados, portanto, o preço é mostrado como US $0,00.

## <a name="which-azure-stack-deployments-are-charged"></a>Quais implantações do Azure Stack são cobradas?

Uso de recursos é gratuito para o Kit de desenvolvimento do Azure Stack. Sistemas de vários nós de pilha do Azure, as VMs da carga de trabalho, serviços de armazenamento e serviços de aplicativos, são cobrados.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Os usuários são cobrados para a infraestrutura de máquinas virtuais?

 Não. Dados de uso de algumas máquinas virtuais do provedor de recursos do Azure Stack são relatados para o Azure, mas não há encargos para essas VMs, nem para as VMs criadas durante a implantação habilitar a infraestrutura do Azure Stack.  

Os usuários pagam apenas para VMs que são executados sob as assinaturas de locatários. Todas as cargas de trabalho devem ser implantadas em assinaturas de locatários para estar em conformidade com os termos de licenciamento do Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Eu tenho uma licença do Windows Server que deseja usar no Azure Stack, como fazer isso?

Usando as licenças existentes, você evita gerar medidores de uso. Licenças existentes do Windows Server podem ser usadas no Azure Stack, conforme descrito na seção "Usando o software existente com o Azure Stack" das [guia de licenciamento do Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536). Os clientes devem implantar suas máquinas de virtuais do Windows Server, conforme descrito na [o benefício híbrido para licença do Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) artigo para usar suas licenças existentes.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Qual assinatura é cobrada pelos recursos consumidos?

A assinatura que é fornecida quando [registro de pilha do Azure com o Azure](azure-stack-register.md) é cobrado.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quais tipos de assinaturas têm suporte para relatórios de dados de uso?

Para vários nós de pilha do Azure, há suporte para assinaturas do Enterprise Agreement (EA) e o CSP. Para o Kit de desenvolvimento do Azure Stack, assinaturas pré-pago, Enterprise Agreement (EA), CSP e MSDN dão suporte a relatórios de dados de uso.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Relatório de trabalho em nuvens soberanas de dados de uso?

No Kit de desenvolvimento do Azure Stack, relatórios de dados de uso requer assinaturas que são criadas no sistema global do Azure. As assinaturas criadas em uma das nuvens soberanas (as nuvens do Azure governamental, Azure Alemanha e Azure China) não podem ser registradas com o Azure, portanto, eles não oferecem suporte a relatórios de dados de uso.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Por uso relatado no Azure Stack não coincidem com o relatório gerado a partir do Centro de contas do Azure?

Sempre há um atraso entre os dados de uso relatados pelo uso do Azure Stack APIs e os dados de uso relatados pelo Centro de contas do Azure. Esse atraso é o tempo necessário para carregar dados de uso do Azure Stack para comércio do Azure. Devido a esse atraso, uso ocorre logo antes da meia-noite pode aparecer no Azure no dia seguinte. Se você usar o [uso do Azure Stack APIs](azure-stack-provider-resource-api.md)e comparar os resultados para uso relatado no portal de cobrança do Azure, você poderá ver uma diferença.

## <a name="next-steps"></a>Próximas etapas

* [API de uso do provedor](azure-stack-provider-resource-api.md)  
* [API de uso do locatário](azure-stack-tenant-resource-usage-api.md)
* [Perguntas frequentes sobre o uso](azure-stack-usage-related-faq.md)
* [Gerenciar o uso e cobrança que um provedor de serviços de nuvem](azure-stack-add-manage-billing-as-a-csp.md)
