---
title: "APIs de Cobrança do Azure | Microsoft Docs"
description: "Aprenda sobre as APIs RateCard e de Uso de Cobrança do Azure, que são usadas para fornecer informações sobre o consumo de recursos e as tendências do Azure."
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/25/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 869734ad4eb2d7a408cc0d33d1492b50b1b8dc0f
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Usar APIs de Cobrança do Azure para obter informações programaticamente sobre o uso do Azure
Use APIs de cobrança do Azure para efetuar pull de dados de uso e de recurso em suas ferramentas de análise de dados preferidas. As APIs RateCard e de Uso de Recursos do Azure e podem ajudá-lo a prever e gerenciar seus custos com precisão. As APIs são implementadas como um Provedor de Recursos e como parte da família de APIs expostas pelo Azure Resource Manager.  

## <a name="azure-resource-usage-api-preview"></a>API de uso de recursos do Azure (visualização)
Os clientes e parceiros podem usar a API de Uso de Recursos do Azure para obter seus dados de consumo estimado do Azure. A API inclui:

* **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs) para especificarem quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. Adicione o chamador à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.
* **Agregações diárias ou por hora** - os chamadores podem especificar se eles desejam seus dados de uso do Azure em buckets por hora ou buckets diários. O padrão é diário.
* **Metadados da instância (inclui as marcas do recurso)** – obtenha detalhes no nível da instância, como o URI de recurso totalmente qualificado (/subscriptions/{subscription-id}/..), as informações do grupo de recursos e marcas do recurso. Esses metadados ajudam você a alocar de forma determinista e programática o uso por marcas, para casos de uso como cobrança cruzada.
* **Metadados de recurso** – detalhes de recurso como nome do medidor, categoria do medidor, subcategoria do medidor, unidade e região proporcionam ao chamador um melhor entendimento do que foi consumido. Também estamos trabalhando para alinhar a terminologia de metadados de recursos entre o portal do Azure, o CSV de uso do Azure, o CSV de cobrança EA e outras experiências públicas, para permitir que você correlacione dados entre as experiências.
* **Uso para todos os tipos de oferta** – os dados de uso estão disponíveis para todos os tipos de oferta, assim como pré-pago, MSDN, investimento e crédito monetário e EA.

## <a name="azure-resource-ratecard-api-preview"></a>API RateCard de Recursos do Azure (visualização)
Use a API RateCard de Recursos do Azure para obter a lista de recursos do Azure disponíveis, juntamente com as informações estimadas de preço para cada um deles. A API inclui:

* **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs) para especificarem quais usuários ou aplicativos podem ter acesso aos dados do RateCard. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. Adicione o chamador à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura particular do Azure.
* **Suporte para pré-pago, MSDN, ofertas de investimento e crédito monetário (EA não tem suporte)** – Esta API fornece informações de taxa no nível da oferta do Azure.  O chamador dessa API deve passar as informações de oferta para obter taxas e detalhes do recurso. Já que as ofertas de EA têm taxas personalizadas por registro, não seremos capazes de fornecer as taxas de EA neste momento. 

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários possíveis com a combinação das APIs de Uso e RateCard:

* **Gasto do Azure durante o mês** – use a combinação das APIs RateCard e de uso para obter melhores informações sobre o seu gasto com nuvem durante o mês. Você pode analisar os buckets horários e diários de uso e as estimativas de custo.
* **Configurar alertas** – use as APIs RateCard e de uso para obter o consumo de nuvem e encargos estimados e configurar alertas com base em recursos ou em valores financeiros.
* **Previsão de fatura** – obtenha seu consumo estimado e gasto de nuvem e aplicar algoritmos de aprendizado de máquina para prever qual seria sua fatura no final do ciclo de cobrança.
* **Análise de custo de pré-consumo** – usar a API RateCard para prever quanto sua fatura seria para seu uso esperado ao mover suas cargas de trabalho para o Azure. Se você tem cargas de trabalho existentes em outras nuvens ou nuvens privadas, você também pode mapear seu uso com as taxas do Azure para obter uma melhor estimativa do seu gasto com o Azure. Essa estimativa fornece a capacidade de dinamizar a oferta e comparar e contrastar os tipos diferentes de oferta além de pré-pago, tais como o compromisso monetário e o crédito monetário. A API também oferece a capacidade de ver as diferenças de custo por região e permite que você faça uma análise hipotética do custo para lhe ajudar a tomar decisões de implantação.
* **Hipóteses** -
  
  * Você pode determinar se é mais econômico executar suas cargas de trabalho em outra região ou em outra configuração do recurso do Azure. Os custos de recursos do Azure podem mudar com base na região do Azure que você está usando.
  * Você também pode determinar se outro tipo de oferta do Azure oferece uma melhor taxa em um recurso do Azure.

## <a name="partner-solutions"></a>Soluções de parceiros
[As APIs de Uso e RateCard do Microsoft Azure APIs permitem Cloudyn para fornecer ITFM para clientes](billing-usage-rate-card-partner-solution-cloudyn.md) descrevem a experiência de integração oferecida pelo parceiro de API de cobrança do Azure [Cloudyn](https://www.cloudyn.com/microsoft-azure/). Este artigo fala sobre suas experiências e inclui um vídeo que mostra como você pode usar Cloudyn e as APIs de Cobrança do Azure para obter informações sobre dados de consumo do Azure.

[Integração da API de Cobrança do Microsoft Azure e Cloud Cruiser](billing-usage-rate-card-partner-solution-cloudcruiser.md) descreve como o [Express do Cloud Cruiser para Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funciona diretamente do portal do WAP (Microsoft Azure Pack). Você pode gerenciar sem inconvenientes os aspectos operacionais e financeiros da nuvem pública particular ou hospedada do Microsoft Azure de uma única interface do usuário.   

## <a name="next-steps"></a>Próximas etapas
* Confira a [Referência da API REST de Cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais detalhes sobre as duas APIs.
* Se você gostaria de se aprofundar no exemplo de código, confira nossos exemplos de código da API de Cobrança do Microsoft Azure em [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Saiba mais
* Confira também a [Visão Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para obter mais informações sobre o pacote de ferramentas necessárias para ajudá-lo a compreender os gastos de nuvem, consulte o artigo da Gartner [Guia de mercado para as ferramentas de gestão financeira de TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


