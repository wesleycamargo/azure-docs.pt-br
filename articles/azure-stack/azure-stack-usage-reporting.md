---
title: Relatar dados de uso de pilha do Azure para o Azure | Microsoft Docs
description: "Saiba como configurar dados de uso de relatórios na pilha do Azure."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.openlocfilehash: 5abc325a6e7c019dc3cb84f7f6ff63c3eb2ff76c
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Relatar dados de uso de pilha do Azure para o Azure 

Dados de uso, também chamados de dados de consumo, representam a quantidade de recursos usados. 

Sistemas com vários nós do Azure pilha que usam o modelo de cobrança com base em consumo devem relatar dados de uso do Azure para a finalidade de cobrança.  Operadores de pilha do Azure devem configurar sua instância de pilha do Azure para relatar dados de uso para o Azure.

> [!NOTE]
> Relatórios de dados de uso é necessário para os usuários de vários nós de pilha do Azure que no modelo de pagamento que você-uso de licença. É opcional para os clientes que no modelo de capacidade de licença (consulte o [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack)). Para usuários do Kit de desenvolvimento de pilha do Azure, os operadores de pilha do Azure podem relatar dados de uso e testar o recurso. No entanto, os usuários não serão cobrados para qualquer uso incorrem. 


![fluxo de cobrança](media/azure-stack-usage-reporting/billing-flow.png)

Dados de uso são enviados da pilha do Azure para o Azure por meio da ponte do Azure. No Azure, o sistema de comércio processa os dados de uso e gera a fatura. Depois que a fatura é gerada, o proprietário da assinatura do Azure pode exibir e baixá-lo a [Centro de contas Azure](https://account.windowsazure.com/Subscriptions). Para saber mais sobre como a pilha do Azure é licenciada, consulte o [pilha do Azure, pacotes e preços documento](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Configurar os relatórios de dados de uso

Para configurar os relatórios de dados de uso, você deve [registrar sua instância de pilha do Azure com o Azure](azure-stack-register.md). Como parte do processo de registro, o componente de ponte do Azure da pilha do Azure, que se conecta a pilha do Azure para o Azure e envia os dados de uso, está configurado. Os seguintes dados de uso são enviados da pilha do Azure para o Azure:

- **ID de medidor** – a ID exclusiva para o recurso que foi consumido.
- **Quantidade** – a quantidade de uso de recursos.
- **Local** – local em que o recurso do Azure pilha atual é implantado.
- **URI de recurso** – totalmente qualificado o URI do recurso para o qual o uso está sendo relatado.
- **ID da assinatura** – ID de assinatura do usuário a pilha do Azure. Esta é a assinatura de (pilha do Azure) local.
- **Tempo** – hora de início e término dos dados de uso. Há algum atraso entre a hora quando esses recursos são consumidos na pilha do Azure e quando os dados de uso são relatados para comércio. Dados de uso de agregações pilha do Azure para a cada 24 horas e relatório dados de uso para o pipeline de comércio no Azure leva outro algumas horas. Portanto, uso ocorre logo antes da meia-noite pode aparecer no Azure no dia seguinte.

## <a name="generate-usage-data-reporting"></a>Gerar relatórios de dados de uso

1. Para testar os relatórios de dados de uso, crie alguns recursos na pilha do Azure. Por exemplo, você pode criar um [conta de armazenamento](azure-stack-provision-storage-account.md), [VM do Windows Server](azure-stack-provision-vm.md) e uma VM do Linux com Basic e Standard SKUs para ver como o uso do núcleo é relatado. Os dados de uso para diferentes tipos de recursos são relatados em metros diferentes.

2. Deixe os recursos em execução por algumas horas. Informações de uso são coletadas aproximadamente uma vez a cada hora. Depois de coletar, esses dados são transmitidos para o Azure e processados no sistema de comércio do Azure. Esse processo pode demorar algumas horas.

## <a name="view-usage---csp-subscriptions"></a>Exibir uso - assinaturas de CSP

Se você registrou a pilha do Azure usando uma assinatura de CSP, você pode exibir seu uso e encargos da mesma maneira que você visualizar o consumo do Azure. Uso da pilha do Azure serão incluído na sua fatura e no arquivo de reconciliação, disponível por meio de [Partner Center](https://partnercenter.microsoft.com/en-us/partner/home). O arquivo de reconciliação é atualizado mensalmente. Se você precisar acessar as informações de uso recentes da pilha do Azure, você pode usar as APIs do Centro de parceiro.

   ![Centro de parceiros](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Exibir uso – assinaturas do Enterprise Agreement

Se você registrou a pilha do Azure usando uma assinatura do Enterprise Agreement, você pode exibir seu uso e encargos no [Portal EA](https://ea.azure.com/). Uso da pilha do Azure será incluído nos downloads avançados junto com o uso do Azure na seção relatórios no portal EA. 

## <a name="view-usage--other-subscriptions"></a>Exibir uso – outras assinaturas

Se você registrou a pilha do Azure usando qualquer tipo de assinatura, por exemplo, uma assinatura pré-paga, você pode exibir o uso e encargos no centro da conta do Azure. Entrar para o [Centro de contas Azure](https://account.windowsazure.com/Subscriptions) conta administrador como o Azure e selecione a assinatura do Azure que é usado para registrar a pilha do Azure. Você pode exibir os dados de uso da pilha do Azure, o valor cobrado por cada um dos recursos usados, conforme mostrado na imagem a seguir:

   ![fluxo de cobrança](media/azure-stack-usage-reporting/pricing-details.png)

Para o Kit de desenvolvimento de pilha do Azure, os recursos da pilha do Azure não são cobrados isso, o preço é mostrado como $0,00. Quando vários nós de pilha do Azure se torna disponível, você pode ver o custo real para cada um desses recursos.

## <a name="which-azure-stack-deployments-are-charged"></a>As implantações de pilha do Azure são cobradas?

Uso de recursos está livre para o Kit de desenvolvimento de pilha do Azure. Enquanto para sistemas de vários nós de pilha do Azure, a carga de trabalho de máquinas virtuais, serviços de armazenamento e serviços de aplicativos são cobrados.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Os usuários são cobrados para a infraestrutura de máquinas virtuais?

Não. Dados de uso para o provedor de recursos alguns pilha Azure VMs são relatados para o Azure, mas não há nenhum encargo para essas VMs, nem para as máquinas virtuais criadas durante a implantação habilitar a infraestrutura de pilha do Azure.  

Os usuários só são cobrados pelas VMs que executam em assinaturas de locatários. Todas as cargas de trabalho devem ser implantadas em assinaturas do locatário de acordo com os termos de licenciamento da pilha do Azure.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Tenho uma licença do Windows Server que deseja usar na pilha do Azure, como fazer isso?

Usar as licenças existentes evita a geração metros de uso. Licenças existentes do Windows Server podem ser usadas na pilha do Azure, conforme descrito na seção "Usando o software existente com a pilha do Azure" de [guia de licenciamento do Azure pilha](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Os clientes precisam implantar suas máquinas virtuais de Windows Server, conforme descrito no [benefício híbrida de licença do Windows Server](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) tópico para usar suas licenças existentes.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Qual assinatura é cobrada para os recursos consumidos?
A assinatura que é fornecida quando [Registrando a pilha do Azure com o Azure](azure-stack-register.md) é cobrado.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quais tipos de assinaturas têm suporte para relatórios de dados de uso?

Para vários nós de pilha do Azure, há suporte para assinaturas de EA (Enterprise Agreement) e o CSP. Para o Kit de desenvolvimento de pilha do Azure, assinaturas de EA (Enterprise Agreement), pré-pago, CSP e MSDN oferecer suporte a relatórios de dados de uso.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Dá trabalho nas nuvens soberana de relatórios de dados de uso?

No Kit de desenvolvimento de pilha do Azure, relatórios de dados de uso requer assinaturas que são criadas no sistema global do Azure. Assinaturas criadas em uma das nuvens soberana (nuvens governo do Azure, Azure Alemanha e do Azure na China) não podem ser registradas com o Azure, para que eles não oferecem suporte a relatórios de dados de uso.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Como os usuários podem identificar os dados de uso de pilha do Azure no portal de cobrança do Azure?

Os usuários podem ver os dados de uso da pilha do Azure no arquivo de detalhes de uso. Para saber sobre como obter o arquivo de detalhes de uso, consulte o [baixar o arquivo de uso do artigo do Centro de contas Azure](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). O arquivo de detalhes de uso contém os medidores de pilha do Azure que identificam o armazenamento de pilha do Azure e máquinas virtuais. Todos os recursos usados na pilha do Azure são relatados em região denominado "Pilha do Azure".

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Por que o uso registrado na pilha do Azure não corresponde o relatório gerado a partir do Centro de contas do Azure?

Sempre há um delaybetween os dados de uso relatados, o uso da pilha do Azure APIs e os dados de uso relatados pelo Centro de contas do Azure. Esse atraso é o tempo necessário para carregar dados de uso da pilha do Azure para comércio do Azure. Devido a esse atraso que ocorre logo antes da meia-noite pode aparecer no Azure no dia seguinte. Se você usar o [APIs de uso do Azure pilha](azure-stack-provider-resource-api.md)e compare os resultados com o uso de relatado no portal de cobrança do Azure, você pode ver uma diferença.

## <a name="next-steps"></a>Próximas etapas

* [API de uso do provedor](azure-stack-provider-resource-api.md)  
* [API de uso do locatário](azure-stack-tenant-resource-usage-api.md)
* [Perguntas frequentes sobre o uso](azure-stack-usage-related-faq.md)
