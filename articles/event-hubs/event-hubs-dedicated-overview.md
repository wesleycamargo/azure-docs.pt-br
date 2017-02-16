---
title: "Visão geral dos Hubs de Eventos Dedicados do Azure | Microsoft Docs"
description: Hubs de Eventos do Microsoft Azure
services: event-hubs
author: banisadr
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 6aebaeccc502d20c8a80297972b8f3b5e8e363c6
ms.openlocfilehash: 3cd3d3fa7a540f9608ac07172bb78ee719d6b0a6


---


# <a name="dedicated-event-hubs--an-overview"></a>Hubs de eventos Dedicados: uma visão geral

O recurso de Hubs de Eventos Dedicados oferece implantações de único locatário para clientes com exigências mais rígidas. Em larga escala, os Hubs de Eventos do Azure recebem mais de dois milhões de eventos por segundo ou até 2 GB por segundo de telemetria com latência de subsegundos e armazenamento durável. Isso também permite soluções integradas processando em lotes e em tempo real no mesmo sistema. Com o Arquivo Morto dos Hubs de Eventos incluídos na oferta, você pode ter um único fluxo que dê suporte a pipelines baseados em tempo real e em lote, reduzindo a complexidade da solução.

A tabela a seguir compara os tipos de serviço disponíveis dos Hubs de Eventos. A oferta de Hubs de Eventos Dedicados é com preço mensal fixo, comparado com o preço de uso da maioria dos recursos Standard e Basic. A camada Dedicada oferece os recursos do plano Standard, mas com capacidade em escala empresarial para clientes com cargas de trabalho exigentes.

|  | Básica | Standard | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pagamento por milhão de eventos | Pagamento por milhão de eventos | Incluso |
| Unidade de produtividade (1 MB/s de entrada, 2MB/s de saída) | Pagamento por hora | Pagamento por hora | Incluso |
| Tamanho da mensagem | 256 KB | 256 KB | 1 MB |
| Políticas do publicador | N/D | ✓ | ✓ |     
| Grupos de consumidores | 1 - Padrão | 20 | 20 |
| Reprodução de mensagem | ✓ | ✓  | ✓ |
| Unidades de produtividade máxima | 20 | 20 (flexível até 100)  | 1 CU≈200 |
| Conexões orientadas | 100 incluídos | 1000 incluídos | 100 mil incluídos |
| Conexões agenciadas adicionais | N/D | ✓ | ✓ |
| Retenção de mensagem | 1 dia incluído | 1 dia incluído | Até 7 dias incluídos |
| Visualização <sup>Arquivo Morto</sup> | N/D  | Pagamento por hora | Incluso |

## <a name="benefits-of-event-hubs-at-dedicated-capacity-include"></a>Os benefícios dos Hubs de Eventos com capacidade dedicada incluem:

* Hospedagem de único locatário, sem ruído de outros locatários
* O tamanho de mensagem aumenta para 1MB em comparação com os 256 KB para Standard e Basic
* Desempenho sempre igual
* Capacidade garantida para atender às suas necessidades de pico
* Escalonável entre 1 e 8 UC (unidades de capacidade) – permite até 2 milhões de eventos de entrada por segundo
  * As UC (unidades de capacidade) gerenciam a escala dos Hubs de Eventos dedicados, em que cada UC pode fornecer aproximadamente o equivalente a 200 TU (unidades de produtividade)
* Sem manutenção – gerenciamos o balanceamento de carga, as atualizações do SO, os patches de segurança e o particionamento
* Preço mensal fixo

Os Hubs de Eventos Dedicados também removem algumas limitações de taxa de produtividade da oferta Standard. As TU (unidades de produtividade) nas camadas Basic e Standard dão ao cliente mil eventos por segundo, ou 1 MBps de entrada por TU e duas vezes essa quantidade de saída. A oferta de escala Dedicada não tem restrições de contagens de eventos de entrada e saída. Esses limites são regidos apenas pela capacidade de processamento dos Hubs de Eventos Dedicados adquiridos.

Este serviço destina-se aos usuários com maior telemetria e está disponível para clientes com Contrato Enterprise.

## <a name="how-to-onboard"></a>Como fazer a integração?

A plataforma Hubs de Eventos Dedicados é oferecida ao público por meio de um Contrato Enterprise com vários tamanhos de UC (unidades de capacidade). Cada UC fornece aproximadamente equivalente de 200 unidades de produtividade e é cobrado a US$ 31/h. Você pode dimensionar sua capacidade para cima ou para baixo ao longo do mês a fim de atender às suas necessidades, adicionando ou removendo UCs. O plano dedicado é exclusivo porque oferece uma integração mais prática da equipe de produto dos Hubs de Eventos para lhe dar a implantação flexível ideal. 


## <a name="next-steps"></a>Próximas etapas

[Preços dos Hubs de Eventos Dedicados](https://azure.microsoft.com/en-us/pricing/details/event-hubs/)

Entre em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.


<!--HONumber=Jan17_HO4-->


