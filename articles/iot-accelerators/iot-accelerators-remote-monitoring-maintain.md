---
title: Solucionar problemas de dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como solucionar e corrigir problemas de dispositivo na solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9a620d91238393ba0bde89f521f790b58ab35baf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628065"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Solucionar e corrigir problemas de dispositivo

Este tutorial mostra como usar a página **Manutenção** na solução para solucionar e corrigir problemas de dispositivo. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso está testando um novo dispositivo **Protótipo** em campo. Como operador da Contoso, você percebeu durante o teste que o dispositivo **Protótipo** está disparando inesperadamente um alerta de temperatura no painel. Agora será necessário investigar o comportamento deste dispositivo **Protótipo** com falha.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alerta
> * Chamar um método de dispositivo para corrigir o problema

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada da solução de Monitoramento Remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Usar o painel de manutenção

Na página **Painel**, você perceberá que há alertas de temperatura inesperados provenientes da regra associada aos dispositivos **Protótipos**:

![Alertas mostrados no painel](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Para investigar o problema, escolha a opção **Explorar alerta** ao lado do alerta:

![Explorar o alerta no painel](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

A exibição de detalhes do alerta mostra:

* Quando o alerta foi disparado
* Informações de status sobre os dispositivos associados ao alerta
* Telemetria dos dispositivos associados ao alerta

![Detalhes do Alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Para reconhecer o alerta, selecione as **Ocorrências do alerta** e escolha **Reconhecer**. Essa ação permite que os outros operadores saibam que você viu o alerta e está trabalhando nele.

![Reconhecer os alertas](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

Quando você confirma o alerta, o status da ocorrência altera para **Confirmado**.

Na lista, é possível ver o dispositivo **Protótipo** responsável por disparar o alerta de temperatura:

![Lista dos dispositivos que estão causando o alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Corrigir o problema

Para corrigir o problema do dispositivo **Protótipo**, será necessário chamar o método **DecreaseTemperature** no dispositivo.

Para agir em um dispositivo, selecione-o na lista de dispositivos e, em seguida, escolha **Trabalhos**. O modelo de dispositivo **Protótipo** especifica seis métodos que precisam ter suporte em um dispositivo:

![Exibir os métodos com suporte no dispositivo](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Escolha **DecreaseTemperature** e defina o nome do trabalho para **DecreaseTemperature**. Depois, escolha **Aplicar**:

![Criar o trabalho para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

Para acompanhar o status do trabalho na página **Manutenção**, escolha **Trabalhos**. Use a exibição **Trabalhos** para acompanhar todos os trabalhos e chamadas de método na solução:

![Monitorar o trabalho para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Para exibir os detalhes de um trabalho ou de uma chamada de método específicos, escolha-os na lista na exibição de **Trabalhos**:

![Exibir detalhes do trabalho](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar a página **Manutenção** para investigar o alerta
> * Chamar um método de dispositivo para corrigir o problema

Agora que você aprendeu como gerenciar problemas de dispositivo, a próxima etapa sugerida é saber como [Testar sua solução com dispositivos simulados](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->