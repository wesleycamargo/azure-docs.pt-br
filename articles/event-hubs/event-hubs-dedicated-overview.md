---
title: "Visão geral da capacidade de Hubs de Eventos Dedicados do Azure | Microsoft Docs"
description: "Visão geral da capacidade de Hubs de Eventos Dedicados do Microsoft Azure ."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 613ea691e38b6f0bcd8873fc2ec6bcafb3cc6c78
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

A capacidade dos *Hubs de Eventos Dedicados* oferece implantações de único locatário para clientes com exigências mais rígidas. Em larga escala, os Hubs de Eventos do Azure recebem mais de dois milhões de eventos por segundo ou até 2 GB por segundo de telemetria com latência de subsegundos e armazenamento durável. Isso também permite soluções integradas processando em lotes e em tempo real no mesmo sistema. Com a [Captura de Hubs de Evento](event-hubs-capture-overview.md) inclusa na oferta, você pode reduzir a complexidade da sua solução ao ter um único fluxo que dê suporte a pipelines baseados em tempo real e em lote.

A tabela a seguir compara os tipos de serviço disponíveis dos Hubs de Eventos. A oferta de Hubs de Eventos Dedicados tem um preço mensal fixo, em comparação ao preço de uso da maioria dos recursos Standard. A camada Dedicada oferece os recursos do plano Standard, mas com capacidade em escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Unidade de taxa de transferência (entrada de 1 MB/s, saída de 2 MB/s) | Pagamento por hora | Incluso |
| Tamanho da mensagem | 256 KB | 1 MB |
| Políticas do publicador | Sim | Sim |   
| Grupos de consumidores | 20 | 20 |
| Reprodução de mensagem | Sim | Sim |
| Unidades de produtividade máxima | 20 (flexível até 100)   | 1 CU≈50 |
| Conexões orientadas | 1000 incluídos | 100.000 incluídos |
| Conexões agenciadas adicionais | Sim | Sim |
| Retenção de mensagem | 1 dia incluído | Até 7 dias incluídos |
| Captura | Pagamento por hora | Incluso |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Os benefícios da capacidade dos Hubs de Eventos Dedicados

Os seguintes benefícios estão disponíveis ao usar os Hubs de Eventos Dedicados:

* Hospedagem de único locatário, sem ruído de outros locatários.
* O tamanho de mensagem aumenta para 1 MB em comparação aos 256 KB para Standard.
* Desempenho repetível em todas as ocasiões.
* Capacidade garantida para atender às suas necessidades de pico.
* Inclui o recurso [Capturar](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) dos Hubs de Eventos do Azure para fornecer uma integração com um microlote e a retenção de longo prazo
* Sem manutenção – gerenciamos o balanceamento de carga, as atualizações do SO, os patches de segurança e o particionamento.
* Preço corrigido por hora.
* Retenção de mensagens de até sete dias sem custos adicionais

O recurso Hubs de Eventos Dedicados também remove algumas limitações de taxa de produtividade da oferta Standard. As unidades de produtividade na camada Standard dão a você 1000 eventos por segundo ou 1 MB por segundo de entrada por TU e duas vezes essa quantidade de saída. A oferta de escala Dedicada não tem restrições de contagens de eventos de entrada e saída. Esses limites são regidos apenas pela capacidade de processamento dos hubs de eventos adquiridos.

Esse ambiente dedicado e reservado fornece outros recursos exclusivos dessa camada, como:

* Controlar o número de namespaces no cluster
* Especificar o limite da taxa de transferência em cada namespace
* Configurar o número de Hubs de Eventos em cada namespace
* Determinar o limite do número de partições

Este serviço destina-se aos usuários com maior telemetria e está disponível para clientes com contrato Enterprise.

## <a name="how-to-onboard"></a>Como fazer a integração

Você pode dimensionar sua capacidade para cima ou para baixo ao longo do mês a fim de atender às suas necessidades, adicionando ou removendo CUs. O plano dedicado é exclusivo porque oferece uma integração mais prática da equipe de produto dos Hubs de Eventos para lhe dar a implantação flexível ideal. Para integrar esse SKU, entre em contato com (suporte de cobrança) [https://ms.portal.azure.com/#create/Microsoft.Support] ou o representante da Microsoft.

## <a name="next-steps"></a>Próximas etapas
Entre em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados. Você também pode saber mais sobre Hubs de Eventos visitando estes links:

Para obter informações detalhadas sobre os preços, visite os links a seguir:

- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos. 
