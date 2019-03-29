---
title: Enviar suas mensagens de segurança para o ASC para versão prévia do IoT | Microsoft Docs
description: Saiba como enviar suas mensagens de segurança usando ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 7e1229ceaa2ae7113c95cfb202a84c56a3e53d61
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620127"
---
# <a name="send-security-messages-sdk"></a>Enviar mensagens de segurança do SDK

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de instruções explica ASC para recursos do serviço de IoT quando você escolhe coletar e enviar mensagens de segurança de seu dispositivo sem usar um ASC para agente do IoT e explica como fazer isso.  

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Usar a mensagem de segurança de envio API paraC#
> * Usar a mensagem de segurança de envio API para C

## <a name="asc-for-iot-capabilities"></a>ASC para recursos de IoT

ASC para IoT pode processar e analisar qualquer tipo de dados de mensagem de segurança, desde que os dados enviados está de acordo com o [ASC para IoT esquema](https://aka.ms/iot-security-schemas) e a mensagem é definida como uma mensagem de segurança.

## <a name="security-message"></a>Mensagem de segurança

ASC para IoT define uma mensagem de segurança usando os seguintes critérios:
- Se a mensagem foi enviada com o Azure IoT C /C# SDK
- Se a mensagem está em conformidade com o [esquema de mensagem de segurança](https://aka.ms/iot-security-schemas)
- Se a mensagem tiver sido definida como uma mensagem de segurança antes de enviar

Cada mensagem de segurança inclui os metadados do remetente, como `AgentId`, `AgentVersion`, `MessageSchemaVersion` e uma lista de eventos de segurança.
O esquema define as propriedades válidas e necessárias da mensagem de segurança que inclui os tipos de eventos.

[!NOTE]
> As mensagens enviadas que não são compatíveis com o esquema são ignorados. Certifique-se de verificar o esquema antes de iniciar o envio de dados como mensagens ignoradas não são armazenadas no momento. 
> As mensagens enviadas que não foram definidos como uma mensagem de segurança usando o Azure IoT C /C# SDK não será roteado para o ASC para o pipeline de IoT

## <a name="valid-message-example"></a>Exemplo de mensagem válido

O exemplo a seguir mostra um objeto de mensagem de segurança válido. O exemplo contém os metadados de mensagem e um `ProcessCreate` eventos de segurança.

Uma vez definido como uma mensagem de segurança e enviado, essa mensagem será processada pelo ASC para IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Enviar mensagens de segurança 

Enviar mensagens de segurança sem usar o ASC para agente do IoT, usando o [IoT do Azure C# SDK do dispositivo](https://github.com/Azure/azure-iot-sdk-csharp) ou [SDK do dispositivo Azure IoT C](https://github.com/Azure/azure-iot-sdk-c).

Para enviar os dados do dispositivo de seus dispositivos para o processamento do ASC para IoT, use uma das seguintes APIs para marcar as mensagens para o roteamento correto ao ASC para pipeline de processamento de IoT. As mensagens enviadas dessa maneira serão processadas e exibidas como informações de segurança no ASC para IoT em ambos os IoT Hub ou em Central de segurança do Azure. 

Todos os dados que são enviados, mesmo se marcado com o cabeçalho correto, também devem estar em conformidade com o [ASC para esquema de mensagem de IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

### <a name="send-security-message-api"></a>API de mensagem de segurança de envio

O **enviar mensagens de segurança** API está disponível atualmente em C e C#.  

#### <a name="c-api"></a>API do C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>API DE C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- Leia o ASC para o serviço IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [perguntas Frequentes](resources-frequently-asked-questions.md)
- Saiba como acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender [recomendações](concept-recommendations.md)
- Entender [alertas](concept-security-alerts.md)