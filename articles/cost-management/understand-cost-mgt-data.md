---
title: Entender os dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos em seu Gerenciamento de Custos do Azure e a frequência com que eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0531c106228190fdc40f494e8eee70ec550f6404
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820242"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

Este artigo ajuda você a entender melhor os dados incluídos no Gerenciamento de Custos do Azure. Além disso, ele explica a frequência com que esses dados são processados, coletados, mostrados e fechados. Você é cobrado mensalmente pelo uso do Azure. No entanto, o tipo de sua assinatura do Azure determina quando o mês de cobrança termina. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

## <a name="supported-microsoft-offers"></a>Ofertas da Microsoft com suporte

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure.  Uma oferta do Azure é o tipo de assinatura do Azure que você tem.

| Categoria  | **Nome da oferta** | **Número da oferta** |
| --- | --- | --- |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Enterprise Agreement (EA)**| Desenvolvimento/Teste Enterprise | MS-AZR-0148P |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |
| **Direto/Pago conforme o uso** | [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **Direto/Pago conforme o uso** | Microsoft Azure Internal Consumption | MS-AZR-0015P |
| **Direto/Pago conforme o uso** | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **Direto/Pago conforme o uso** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **Direto/Pago conforme o uso** | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **Direto/Pago conforme o uso** | [Teste Gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **Direto/Pago conforme o uso** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **Direto/Pago conforme o uso** | [Azure for Students](https://azure.microsoft.com/en-us/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **Direto/Pago conforme o uso** | Azure Pass | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P |

A tabela a seguir mostra ofertas sem suporte.

| Categoria  | **Nome da oferta** | **Número da oferta** |
| --- | --- | --- |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure na Alemanha para o Microsoft Cloud Alemanha | MS-AZR-DE-0145P |
| **Direto/Pago conforme o uso** | Azure for Students Starter | MS-AZR-0144P |
| **Planos de suporte** | Suporte Standard | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental | MS-AZR-USGOV-0043P |

Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, clique na assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos.

**Tipos de conta**

| **Incluído** | **Não incluído** |
| --- | --- |
| EA (Enterprise Agreement) | CSP (Provedor de Soluções na Nuvem) – Para obter mais informações, consulte a [Visão geral do Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview). |
| Pagamento conforme o uso (PAYG) |   |
| Desenvolvimento/Teste |   |
| Gratuito, avaliação e patrocinado |   |
| Rede de parceiros |   |
| Azure via Open | &nbsp;  |

**Dados de uso e de custo**

| **Incluído** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>1</sup> | Compras de reserva – Para obter mais informações, consulte [APIs para automação de reserva do Azure](../billing/billing-reservation-apis.md). |
| Uso de oferta do Marketplace | Compras do Marketplace – Para obter mais informações, consulte [Encargos de serviço de terceiros](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
|   | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
|   | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> O uso do serviço do Azure é baseado nos preços negociados e de reserva.

**Metadados**

| **Incluído** | **Não incluído** |
| --- | --- |
| Marcas de recurso<sup>2</sup> | Marcas de grupo de recursos |

<sup>2</sup> Marcas de recurso são aplicadas conforme o uso é transmitido por cada serviço e não ficam disponíveis retroativamente para o uso histórico.

## <a name="rated-usage-data-refresh-schedule"></a>Agenda de atualização de dados de uso calculados

Dados de uso e de custo estão disponíveis em Gerenciamento de Custos + Cobrança no portal do Azure e nas [APIs com suporte](https://aka.ms/costmgmt/docs). Ao avaliar os custos, tenha em mente os seguintes pontos:

- Os encargos estimados do período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados do período de cobrança atual podem ser alterados conforme você incorre em mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _encerra_ o período de cobrança atual até 72 horas (três dias corridos) após o fim período de cobrança.

Os exemplos a seguir ilustram como os períodos de cobrança podem ser encerrados.

Assinaturas de EA (Contrato Enterprise) – Se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas mais tarde. Neste exemplo, até meia-noite (UTC) de 4 de abril.

Assinaturas pagas conforme o uso – Se o mês de cobrança terminar no dia 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados recalculados

Se você usa as [APIs de Gerenciamento de Custos](https://aka.ms/costmgmt/docs), o Power BI ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam recalculados e, consequentemente, alterados até que a fatura seja fechada.

## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de uso varia

A disponibilidade de seus dados de uso incorridos no Gerenciamento de Custos depende de alguns fatores, incluindo:

- A frequência com que os serviços do Azure (como Armazenamento, Computação, CDN e SQL) transmitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços transmitem o uso com mais frequência do que outros. Portanto, talvez você veja dados no Gerenciamento de Custos para alguns serviços antes do que para outros serviços que emitem dados menos frequentemente. Normalmente, o uso de serviços leva 8 a 24 horas para aparecer no Gerenciamento de Custos. Tenha em mente que os dados de um mês em aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="see-also"></a>Consulte também

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
