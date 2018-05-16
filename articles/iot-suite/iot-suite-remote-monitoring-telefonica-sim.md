---
title: Integrar os dados do SIM na Solução de Monitoramento Remoto - Azure | Microsoft Docs
description: Este artigo descreve como integrar dados do SIM da Telefonica na solução de monitoramento remoto.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrar dados do SIM na solução de monitoramento remoto

## <a name="overview"></a>Visão geral
Os dispositivos IoT geralmente se conectam à nuvem usando um cartão SIM que permite enviar fluxos de dados de qualquer lugar. A solução de monitoramento remoto de IoT do Azure permite a integração de dados de gerenciamento do SIM, para que os operadores também possam controlar a integridade do dispositivo através dos dados fornecidos pelo SIM. O monitoramento remoto fornece a integração com IoT da Telefonica, permitindo que os clientes que usam sua plataforma de conectividade de IoT sincronizem os dados de conectividade dos SIMs do seu dispositivo às soluções. Essa solução pode ser estendida para dar suporte a outras companhias de telefonia por meio do repositório GitHub.
Neste tutorial, você aprenderá como:
* Integrar dados do SIM na solução de monitoramento remoto
* Exibir telemetria em tempo real
* Exibir dados do SIM 

## <a name="telefonica-iot-integration-setup"></a>Configuração de integração de IoT da Telefonica

### <a name="prerequisites"></a>pré-requisitos
Para sincronizar seus dados de conectividade na solução de monitoramento remoto do Azure, siga estas etapas:

1.  Preencha uma solicitação no [site da Telefonica](https://iot.telefonica.com/contact), selecione a opção **Monitoramento remoto do Azure**, incluindo seus dados de contato.
2.  A Telefonica ativará sua conta. 
3.  Se você ainda não é um cliente Telefonica e deseja aproveitar esse ou outros serviços prontos para a nuvem de conectividade IoT, visite o [site da Telefonica](https://iot.telefonica.com/contact) e selecione a opção **Conectividade**.

### <a name="telefonica-sim-setup"></a>Configuração do SIM da Telefonica
A associação do SIM da Telefonica SIM e da ID do dispositivo gêmeo do Azure será baseada na propriedade “alias” do SIM de IoT da Telefonica. 

Navegue até o [Portal da plataforma de conectividade de IoT da Telefonica](https://m2m-movistar-es.telefonica.com/) > Inventário de SIMs > Selecione o SIM e atualize cada “alias” de SIM com a ID do dispositivo gêmeo desejado. 

Essa tarefa também pode ser realizada em massa (consulte os manuais do usuário da plataforma de conectividade de IoT da Telefonica)

![Atualização da Telefonica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Para conectar seu dispositivo ao monitoramento remoto, você pode seguir esses tutoriais usando [C](iot-suite-connecting-devices-linux.md) ou [Node](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Exibir a telemetria do dispositivo e as propriedades do SIM
Após a sua conta da Telefonica ser configurada adequadamente e o seu dispositivo estar conectado, será possível exibir os detalhes do dispositivo e os dados do SIM.
Os seguintes parâmetros de conectividade podem ser publicados:
* ICCID
* IP
* Presença de rede
* Status do SIM
* Localização baseada em rede
* Tráfego de dados consumidos

![painel](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Próximas etapas

Agora que você já tem uma visão geral de como integrar os dados do SIM no monitoramento remoto do Azure IoT, aqui estão as próximas etapas sugeridas para os aceleradores de solução:

* [Operar a solução de Monitoramento Remoto do Azure IoT](iot-suite-remote-monitoring-explore.md)
* [Executar o monitoramento avançado](iot-suite-remote-monitoring-monitor.md)
* [Gerenciar seus dispositivos](iot-suite-remote-monitoring-manage.md)
* [Solucionar problemas de dispositivos](iot-suite-remote-monitoring-maintain.md)

