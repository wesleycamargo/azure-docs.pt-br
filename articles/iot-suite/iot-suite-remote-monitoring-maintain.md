---
title: "Solucionar problemas de dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como solucionar e corrigir problemas de dispositivo na solução de monitoramento remoto."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ac284126c2c940caf97fb95bd46234de49b1f678
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Solucionar e corrigir problemas de dispositivo

Este tutorial mostra como usar a página **Manutenção** na solução para solucionar e corrigir problemas de dispositivo. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso está testando um novo dispositivo **Protótipo** em campo. Como um operador da Contoso, você observa durante o teste que o dispositivo **Protótipo** está disparando um alarme de temperatura no painel inesperadamente. Agora será necessário investigar o comportamento deste dispositivo **Protótipo** com falha.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alarme
> * Chamar um método de dispositivo para corrigir o problema

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

## <a name="use-the-maintenance-dashboard"></a>Usar o painel de manutenção

Na página **Painel**, você perceberá que há alarmes de temperatura inesperados provenientes da regra associada aos dispositivos **Protótipos**:

![Alarmes mostrados no painel](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Para investigar o problema, escolha a opção **Explorar alarme** ao lado de alarme:

![Explorar o alarme no painel](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Agora você poderá ver uma lista de alarmes na página **Manutenção**:

![Lista de alarmes na página Manutenção](media/iot-suite-remote-monitoring-maintain/maintenancealarms.png)

Para exibir os detalhes do alarme, escolha-o na lista **Alarmes**. A exibição de detalhes mostra:

* Quando o alarme foi disparado
* Informações de status sobre os dispositivos associados ao alarme
* Telemetria dos dispositivos associados ao alarme

![Detalhes do alarme](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Para reconhecer o alarme, selecione as **Ocorrências do alarme** e escolha **Reconhecer**. Essa ação permite que os outros operadores saibam que você viu o alarme e está trabalhando nele.

![Reconhecer os alarmes](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Na lista, é possível ver o dispositivo **Protótipo** responsável por disparar o alarme de temperatura:

![Lista dos dispositivos que estão causando o alarme](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Corrigir o problema

Para corrigir o problema do dispositivo **Protótipo**, será necessário chamar o método **DecreaseTemperature** no dispositivo.

Para agir em um dispositivo, selecione-o na lista de dispositivos e, em seguida, escolha **Agendar**. O modelo do dispositivo do **Mecanismo** especifica três métodos que precisam ter suporte em um dispositivo:

![Exibir os métodos com suporte no dispositivo](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Escolha **DecreaseTemperature** e defina o nome do trabalho para **DecreaseTemperature**. Depois, escolha **Aplicar**:

![Criar o trabalho para diminuir a temperatura](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Para acompanhar o status do trabalho na página **Manutenção** escolha **Status do Sistema**. Use a exibição **Status do sistema** para acompanhar todos os trabalhos e chamadas de método na solução:

![Monitorar o trabalho para diminuir a temperatura](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Para exibir os detalhes de um trabalho ou de uma chamada de método específicos, escolha-os na lista na exibição de **Status do sistema**:

![Exibir detalhes do trabalho](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial mostramos como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alarme
> * Chamar um método de dispositivo para corrigir o problema

Agora que você aprendeu como gerenciar problemas de dispositivo, a próxima etapa sugerida é saber como [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->