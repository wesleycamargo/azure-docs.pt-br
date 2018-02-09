---
title: "Alta disponibilidade e recuperação de desastres do Hub IoT do Azure | Microsoft Docs"
description: "Descreve os recursos do Hub IoT e do Azure que ajudam a criar soluções IoT do Azure altamente disponíveis habilitadas para recuperação de desastre."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: ecc5da8daf0f5c93dffc93798f40507f8eac48be
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres do Hub IoT
Por ser um serviço do Azure, o Hub IoT fornece alta disponibilidade (HA) com redundâncias no nível de região do Azure, sem a necessidade de qualquer trabalho adicional da solução. A plataforma Microsoft Azure também inclui recursos para ajudá-lo a criar soluções com recursos de DR (recuperação de desastres) ou disponibilidade entre regiões. Se você quiser fornecer alta disponibilidade global entre regiões para dispositivos ou usuários, aproveite esses recursos de recuperação de desastres do Azure. O artigo [Orientação Técnica de Continuidade de Negócios do Azure](../resiliency/resiliency-technical-guidance.md) descreve os recursos internos do Azure para recuperação de desastres e continuidade de negócios. O documento [Recuperação de desastres e alta disponibilidade para aplicativos do Azure][Disaster recovery and high availability for Azure applications] fornece uma orientação de arquitetura sobre estratégias para que os aplicativos do Azure obtenham alta disponibilidade e recuperação de desastres.

## <a name="azure-iot-hub-dr"></a>DR do Hub IoT do Azure
Além de HA entre regiões, o Hub IoT implementa mecanismos de failover para recuperação de desastres que não exigem intervenção do usuário. A recuperação de desastre do Hub IoT é iniciada automaticamente e tem um RTO (objetivo de tempo de recuperação) de 2 a 26 horas e os RPOs (objetivos de ponto de recuperação) a seguir:

| Funcionalidade | RPO |
| --- | --- |
| Disponibilidade de serviço para operações de registro e comunicação |Possível perda de CName |
| Dados de identidade no registro de identidade |Perda de dados de 0 a 5 minutos |
| Mensagens do dispositivo para a nuvem |Todas as mensagens não lidas são perdidas |
| Mensagens de monitoramento de operações |Todas as mensagens não lidas são perdidas |
| Mensagens da nuvem para o dispositivo |Perda de dados de 0 a 5 minutos |
| Fila de comentários da nuvem para o dispositivo |Todas as mensagens não lidas são perdidas |
| Dados do dispositivo gêmeo |Perda de dados de 0 a 5 minutos |
| Trabalhos do pai e do dispositivo |Perda de dados de 0 a 5 minutos |

## <a name="regional-failover-with-iot-hub"></a>Failover regional com o Hub IoT
Um tratamento completo das topologias de implantação em soluções de IoT está fora do escopo deste artigo. O artigo discute o modelo de implantação de *failover regional* para fins de alta disponibilidade e recuperação de desastres.

Em um modelo de failover regional, o back-end da solução é executado primariamente em um local de datacenter. Um hub IoT secundário e o back-end são implantadas em outro local do datacenter. Se o Hub IoT no datacenter primário sofrer uma interrupção ou a conectividade de rede do dispositivo ao datacenter primário for interrompida, os dispositivos usará um ponto de extremidade de serviço secundário. Você pode melhorar a disponibilidade da solução com a implementação de um modelo de failover entre regiões, em vez de manter-se dentro de uma única região. 

Em um alto nível, para implementar um modelo de failover regional com o Hub IoT, você precisa do seguinte:

* **Uma lógica de roteamento de dispositivo e Hub IoT secundário**: se o serviço na sua região primária for interrompido, os dispositivos deverão começar a conectar-se da sua região secundária. Com base na natureza consciente do estado da maioria dos serviços envolvidos, é comum os administradores de solução acionarem o processo de failover entre regiões. A melhor maneira de comunicar o novo ponto de extremidade para os dispositivos, enquanto mantém o controle do processo, é fazer com que eles verifiquem regularmente um serviço de *concierge* para o ponto de extremidade ativo atual. O serviço de concierge pode ser um aplicativo Web que é replicado e acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de Tráfego do Azure][Azure Traffic Manager]).
* **Replicação do registro de identidade**: para ser utilizável, o Hub IoT secundário deverá conter todas as identidades de dispositivo que possam se conectar à solução. A solução deve manter backups replicados geograficamente das identidades do dispositivo e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é útil neste contexto. Para saber mais, confira [Guia do desenvolvedor do Hub IoT ‑ Registro de identidade][IoT Hub developer guide - identity registry].
* **Lógica de mesclagem**: quando a região primária ficar disponível novamente, o estado e os dados criados no site secundário deverão ser migrados de volta para a região primária. Esse estado e os dados estão relacionados principalmente às identidades de dispositivo e aos metadados do aplicativo, que deverão ser mesclados ao Hub IoT primário e com todos os outros armazenamentos específicos do aplicativo na região primária. Para simplificar essa etapa, você deve usar operações idempotentes. Operações idempotentes minimizam os efeitos colaterais da distribuição eventual e consistente de eventos e também de duplicatas ou a entrega de eventos fora de ordem. Além disso, a lógica do aplicativo deve ser projetada para tolerar possíveis inconsistências ou estados “ligeiramente” desatualizados. Essa situação pode ocorrer devido ao tempo adicional necessário para que o sistema seja “corrigido” com base nos objetivos de ponto de recuperação (RPO).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Hub IoT do Azure, siga estes links:

* [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
* [O que é o Hub IoT do Azure?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
