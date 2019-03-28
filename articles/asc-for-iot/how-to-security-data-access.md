---
title: Acessando dados com o ASC para versão prévia do IoT | Microsoft Docs
description: Saiba mais sobre como acessar seus dados de alerta e a recomendação de segurança ao usar o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541986"
---
# <a name="access-your-security-data"></a>Acessar seus dados de segurança 

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC para IoT armazena dados brutos de segurança, recomendações e alertas de segurança (se você optar por salvá-lo) em seu espaço de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual espaço de trabalho do Log Analytics é usado:

1. Abrir seu Hub IoT.
1. Clique em **segurança**
2. Clique em **configurações**e alterar a configuração do espaço de trabalho do Log Analytics.

Para acessar seu espaço de trabalho do Log Analytics após a configuração:

1. Escolha um alerta no ASC para IoT. 
2. Clique em **investigação adicional**, em seguida, clique em **para ver quais dispositivos têm esse alerta, clique aqui e exibir a coluna DeviceId**.

Para obter detalhes sobre como consultar dados do Log Analytics, consulte [Introdução às consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Alertas de segurança são armazenados em do **ASCforIoT.SecurityAlert** tabela dentro do seu espaço de trabalho do Log Analytics configurado.

Use as seguintes consultas kql básicas para começar a explorar os alertas de segurança.

### <a name="sample-records-query"></a>Exemplo de consulta de registros

Para selecionar aleatoriamente alguns registros: 

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

#### <a name="sample-query-results"></a>Resultados da consulta de exemplo 

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | DESCRIÇÃO                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta foi bem-sucedida           | Um ataque de força bruta no dispositivo foi bem-sucedida        |    {"Endereço de código-fonte completo": "[\"10.165.12.18:\"]", "Nomes de usuário": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Logon local com êxito no dispositivo      | Um logon bem-sucedido de local para o dispositivo foi detectado     | {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Login Shell": "/ bin/su", "Id do processo de logon": "28207", "User Name": "invasor", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Falha na tentativa de logon local no dispositivo  | Uma tentativa de logon local com falha para o dispositivo foi detectada |  {"Endereço remoto": "?", "Porta remota": "", "Porta Local": "", "Login Shell": "/ bin/su", "Id do processo de logon": "22644", "User Name": "invasor", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Consulta de resumo do dispositivo

Use esta consulta kql para selecionar um número de alertas de segurança distintas detectado na semana passada pelo IoT Hub, dispositivo, severidade do alerta, o tipo de alerta.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

#### <a name="device-summary-query-results"></a>Resultados da consulta de resumo de dispositivo

| IoTHubId | deviceId| AlertSeverity| DisplayName | Contagem |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Ataque de força bruta foi bem-sucedida           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | Falha na tentativa de logon local no dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Alto          | Logon local com êxito no dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Média        | Minerador de moeda de criptografia                     | 4   |
|

### <a name="iot-hub-summary"></a>Resumo do IoT Hub

Use esta consulta kql para selecionar um número de dispositivos distintos que não teve alertas na última semana, pelo hub IoT, severidade do alerta, o tipo de alerta:

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

#### <a name="iot-hub-summary-query-results"></a>Resultados de resumo de consulta do IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Ataque de força bruta foi bem-sucedida           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Média        | Falha na tentativa de logon local no dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Logon local com êxito no dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Média        | Minerador de moeda de criptografia                     | 1          |



## <a name="next-steps"></a>Próximas etapas

- Leia o ASC para IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
