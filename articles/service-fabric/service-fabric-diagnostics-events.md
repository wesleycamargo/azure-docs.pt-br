---
title: Azure Service Fabric Events | Microsoft Docs
description: Saiba mais sobre os eventos do Service Fabric fornecidos para ajudá-lo a monitorar o cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: b9372c806eab1b0ca69ba078d972b076c8a7d6f6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212699"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma do Service Fabric grava vários eventos estruturados para atividades operacionais chave acontecendo em seu cluster. Esses variam de atualizações de cluster a decisões de posicionamento de réplica. Cada evento do Service Fabric expõe mapas para uma das seguintes entidades no cluster:
* HDInsight
* Aplicativo
* Serviço
* Partition
* Réplica 
* Contêiner

Para ver uma lista completa dos eventos expostos por plataforma - [Listar eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários importantes para os quais você deve ver eventos em seu cluster. 
1. Eventos de ciclo de vida de nó: conforme os nós surgirem, forem ativados/desativados ou reiniciados, os eventos serão expostos, mostrando o que aconteceu e o ajudarão a identificar se há algo errado com o próprio computador ou se uma API foi chamada por meio de SF para modificar o status de um nó.
1. Atualização de cluster: conforme o cluster for atualizado (versão SF ou alteração de configuração), você verá a atualização iniciar, reverter cada um dos seus UDs e concluir (ou reverter). 
1. Atualizações de aplicativo: da mesma forma que atualizações de cluster, há um conjunto abrangente de eventos, conforme a atualização é passada. Esses eventos podem ser úteis para entender quando uma atualização foi agendada, o estado atual de uma atualização e a sequência geral de eventos. Isso é útil para pesquisar novamente para ver quais atualizações foram revertidas com êxito.
1. Implantação/exclusão de aplicativo/serviço: há eventos para cada aplicativo, serviço e contêiner, sendo criado ou excluído.
1. Movimentos de partição (reconfiguração): sempre que uma partição com estado passa por uma reconfiguração (uma alteração no conjunto de réplicas), um evento é registrado. Isso é útil se você estiver tentando entender com que frequência seu conjunto de réplicas de partição está sendo alterado ou rastrear o nó que estava executando sua réplica primária em qualquer ponto no tempo.
1. Eventos de caos: ao usar o serviço [Caos](service-fabric-controlled-chaos.md) do Service Fabric, você verá eventos toda vez que o serviço for iniciado ou interrompido, ou quando ele injetar uma falha no sistema.
1. Eventos de integridade: o Service Fabric expõe eventos de integridade sempre que um aviso ou um relatório de integridade de erro é criado, ou uma entidade volta para um estado de integridade OK ou um relatório de integridade expira. Esses eventos são muito úteis para rastrear as estatísticas de integridade históricas para uma entidade. 

## <a name="how-to-access-events"></a>Como acessar os eventos

Há algumas maneiras diferentes pelas quais os eventos do Service Fabric podem ser acessados:
* por meio do canal operacional. Esses podem ser coletados por meio da extensão de Diagnóstico do Azure e enviados para uma tabela de armazenamento para consumo ou ingestão em uma ferramenta como o OMS Log Analytics. Quando o “Diagnóstico” está habilitado em um cluster, o agente do Diagnóstico do Azure é implantado no cluster e, por padrão, é configurado para ler os logs do canal operacional. Leia mais sobre como configurar o [agente do Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnóstico do cluster, a fim de obter mais logs ou contadores de desempenho. 
* por meio de APIs de Rest do serviço EventStore que permitem que você consulte o cluster diretamente ou por meio da biblioteca de clientes do Service Fabric. Consulte [Consultar APIs do EventStore para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais informações sobre o monitoramento de seu cluster - [Monitoramento do cluster e da plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Saiba mais sobre o serviço EventStore - [Visão geral do serviço EventStore](service-fabric-diagnostics-eventstore.md)
