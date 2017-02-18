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
ms.date: 02/01/2017
ms.author: sethm;babanisa
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 7547ff2f6db375644dccb9db4283d83a9837e826


---

# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

A capacidade dos *Hubs de Eventos Dedicados* oferece implantações de único locatário para clientes com exigências mais rígidas. Em larga escala, os Hubs de Eventos do Azure recebem mais de dois milhões de eventos por segundo ou até 2 GB por segundo de telemetria com latência de subsegundos e armazenamento durável. Isso também permite soluções integradas processando em lotes e em tempo real no mesmo sistema. Com o Arquivo Morto dos Hubs de Eventos incluído na oferta, você pode reduzir a complexidade da sua solução ao ter um único fluxo que dê suporte a pipelines baseados em tempo real e em lote.

A tabela a seguir compara os tipos de serviço disponíveis dos Hubs de Eventos. A oferta de Hubs de Eventos Dedicados tem um preço mensal fixo, comparado ao preço de uso da maioria dos recursos Standard e Basic. A camada Dedicada oferece os recursos do plano Standard, mas com capacidade em escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | Basic | Standard | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pagamento por milhão de eventos | Pagamento por milhão de eventos | Incluso |
| Unidade de taxa de transferência (entrada de 1 MB/s, saída de 2 MB/s) | Pagamento por hora | Pagamento por hora | Incluso |
| Tamanho da mensagem | 256 KB | 256 KB | 1 MB |
| Políticas do publicador | N/D | Sim | Sim |     
| Grupos de consumidores | 1 - padrão | 20 | 20 |
| Reprodução de mensagem | Sim | Sim | Sim |
| Unidades de produtividade máxima | 20 | 20 (flexível até 100)  | 1 CU≈200 |
| Conexões orientadas | 100 incluídos | 1000 incluídos | 100.000 incluídos |
| Conexões agenciadas adicionais | N/D | Sim | Sim |
| Retenção de mensagem | 1 dia incluído | 1 dia incluído | Até 7 dias incluídos |
| Arquivo morto (visualização) | N/D   | Pagamento por hora | Incluso |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Os benefícios da capacidade dos Hubs de Eventos Dedicados

Os seguintes benefícios estão disponíveis ao usar os Hubs de Eventos Dedicados:

* Hospedagem de único locatário, sem ruído de outros locatários.
* O tamanho de mensagem aumenta para 1 MB em comparação aos 256 KB para Standard e Basic.
* Desempenho repetível em todas as ocasiões.
* Capacidade garantida para atender às suas necessidades de pico.
* Escalonável entre 1 e 8 CUs (unidades de capacidade) – permite até 2 milhões de eventos de entrada por segundo.
  * As CUs gerenciam a escala dos Hubs de Eventos dedicados, em que cada UC pode fornecer aproximadamente o equivalente a 200 TUs (unidades de produtividade).
* Sem manutenção – gerenciamos o balanceamento de carga, as atualizações do SO, os patches de segurança e o particionamento.
* Preço mensal fixo.

O recurso Hubs de Eventos Dedicados também remove algumas limitações de taxa de produtividade da oferta Standard. As TUs (unidades de produtividade) nas camadas Basic e Standard dão ao cliente 1000 eventos por segundo, ou 1 MBps de entrada por TU e duas vezes essa quantidade de saída. A oferta de escala Dedicada não tem restrições de contagens de eventos de entrada e saída. Esses limites são regidos apenas pela capacidade de processamento dos Hubs de Eventos adquiridos.

Este serviço destina-se aos usuários com maior telemetria e está disponível para clientes com contrato Enterprise.

## <a name="how-to-onboard"></a>Como fazer a integração

A plataforma dos Hubs de Eventos Dedicados é oferecida para o público por meio de um contrato enterprise com tamanhos variados de CUs. Cada CU fornece aproximadamente equivalente de 200 unidades de produtividade e é cobrado a US$ 31 por hora. Você pode dimensionar sua capacidade para cima ou para baixo ao longo do mês a fim de atender às suas necessidades, adicionando ou removendo CUs. O plano dedicado é exclusivo porque oferece uma integração mais prática da equipe de produto dos Hubs de Eventos para lhe dar a implantação flexível ideal. 

## <a name="next-steps"></a>Próximas etapas
Entre em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados. Você também pode saber mais sobre Hubs de Eventos visitando estes links:

Para obter informações detalhadas sobre os preços, visite os links a seguir:

- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos. 



<!--HONumber=Feb17_HO1-->


