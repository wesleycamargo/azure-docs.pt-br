---
title: Solucionar problemas de dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como solucionar e corrigir problemas de dispositivo na solução de monitoramento remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 99cf9e341edf0acee434e2d01f6346291cbcea5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Solucionar e corrigir problemas de dispositivo

Este tutorial mostra como usar a página **Manutenção** na solução para solucionar e corrigir problemas de dispositivo. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso está testando um novo dispositivo **Protótipo** em campo. Como operador da Contoso, você percebeu durante o teste que o dispositivo **Protótipo** está disparando inesperadamente um alerta de temperatura no painel. Agora será necessário investigar o comportamento deste dispositivo **Protótipo** com falha.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alerta
> * Chamar um método de dispositivo para corrigir o problema

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Usar o painel de manutenção

Na página **Painel**, você perceberá que há alertas de temperatura inesperados provenientes da regra associada aos dispositivos **Protótipos**:

![Alertas mostrados no painel](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Para investigar o problema, escolha a opção **Explorar alerta** ao lado do alerta:

![Explorar o alerta no painel](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

A exibição de detalhes do alerta mostra:

* Quando o alerta foi disparado
* Informações de status sobre os dispositivos associados ao alerta
* Telemetria dos dispositivos associados ao alerta

![Detalhes do Alerta](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Para reconhecer o alerta, selecione as **Ocorrências do alerta** e escolha **Reconhecer**. Essa ação permite que os outros operadores saibam que você viu o alerta e está trabalhando nele.

![Reconhecer os alertas](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Quando você confirma o alerta, o status da ocorrência altera para **Confirmado**.

Na lista, é possível ver o dispositivo **Protótipo** responsável por disparar o alerta de temperatura:

![Lista dos dispositivos que estão causando o alerta](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Corrigir o problema

Para corrigir o problema do dispositivo **Protótipo**, será necessário chamar o método **DecreaseTemperature** no dispositivo.

Para agir em um dispositivo, selecione-o na lista de dispositivos e, em seguida, escolha **Trabalhos**. O modelo de dispositivo **Protótipo** especifica seis métodos que precisam ter suporte em um dispositivo:

![Exibir os métodos com suporte no dispositivo](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Escolha **DecreaseTemperature** e defina o nome do trabalho para **DecreaseTemperature**. Depois, escolha **Aplicar**:

![Criar o trabalho para diminuir a temperatura](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Para acompanhar o status do trabalho na página **Manutenção**, escolha **Trabalhos**. Use a exibição **Trabalhos** para acompanhar todos os trabalhos e chamadas de método na solução:

![Monitorar o trabalho para diminuir a temperatura](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Para exibir os detalhes de um trabalho ou de uma chamada de método específicos, escolha-os na lista na exibição de **Trabalhos**:

![Exibir detalhes do trabalho](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alerta
> * Chamar um método de dispositivo para corrigir o problema

Agora que você aprendeu como gerenciar problemas de dispositivo, a próxima etapa sugerida é saber como [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->