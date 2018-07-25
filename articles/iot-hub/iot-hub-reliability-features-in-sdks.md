---
title: Como gerenciar conectividade e mensagens confiáveis usando SDKs do dispositivo do Hub IoT
description: Saiba como melhorar a conectividade do dispositivo e as mensagens ao usar SDKs do dispositivo do Hub IoT
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a318a1ef8b13b8fcb4f4401ac4d0e45037958d63
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127459"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Como gerenciar conectividade e mensagens confiáveis usando SDKs do dispositivo do Hub IoT

Este guia fornece diretrizes de alto nível para o design de aplicativos de dispositivos resilientes, aproveitando os recursos de conectividade e mensagens confiáveis dos SDKs do dispositivo IoT do Azure. O objetivo deste artigo é ajudar a responder às perguntas e lidar com esses cenários:

- gerenciar uma conexão de rede removida
- gerenciar a comutação entre diferentes conexões de rede
- gerenciar reconexão devido a erros de conexão transitória de serviço

Os detalhes da implementação podem variar de acordo com a linguagem; consulte a documentação da API vinculada ou o SDK específico para obter mais detalhes.

- [SDK do C/Python/iOS](https://github.com/azure/azure-iot-sdk-c)
- [SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [SDK do Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Design para resiliência

Os dispositivos do IoT geralmente dependem de conexões de rede não contínuas e/ou instáveis, como GSM ou satélite. Além disso, ao interagir com serviços baseados em nuvem, podem ocorrer erros devido a condições temporárias, como disponibilidade intermitente do serviço e falhas no nível da infraestrutura (comumente chamadas de falhas transitórias). Um aplicativo em execução em um dispositivo precisa gerenciar os mecanismos de conexão e reconexão, bem como a lógica de nova tentativa de envio/recebimento de mensagens. Além disso, os requisitos de estratégia de repetição dependem muito do cenário de IoT do qual o dispositivo participa e do contexto e dos recursos do dispositivo.

Os SDKs do dispositivo do Hub IoT visam simplificar a conexão e a comunicação de nuvem para dispositivo e dispositivo para nuvem, fornecendo uma maneira robusta e abrangente de conectar e enviar/receber mensagens para e do Hub IoT. Os desenvolvedores também podem modificar a implementação existente para desenvolver a estratégia correta de novas tentativas para um determinado cenário.

Os recursos relevantes do SDK que dão suporte para conectividade e mensagens confiáveis são abordados nas seções a seguir.

## <a name="connection-and-retry"></a>Conexão e repetição

Esta seção fornece uma visão geral dos padrões de repetição e reconexão disponíveis ao gerenciar conexões, diretrizes de implementação para usar uma política de repetição diferente no aplicativo do dispositivo e APIs relevantes para os SDKs do dispositivo.

### <a name="error-patterns"></a>Padrões de erros
Falhas de conexão podem acontecer em vários níveis:

-  Erros de rede, como um soquete desconectado e erros de resolução de nome
- Erros no nível de protocolo para transporte MQTT, AMQP e HTTP como links desconectados ou sessões expiradas
- Erros no nível de aplicativo que resultam de erros locais, como credenciais inválidas ou comportamento de serviço, como exceder cota ou limitação

Os SDKs do dispositivo detectam erros nos três níveis.  Erros relacionados a SO e erros de hardware não são detectados e manipulados pelos SDKs do dispositivo.  O design é baseado nas [Diretrizes de Tratamento de Falhas Transitórias](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) do Azure Architecture Center.

### <a name="retry-patterns"></a>Padrões de repetição

O processo geral para repetir quando erros de conexão são detectados é: 
1. O SDK detecta o erro e o erro associado na rede, protocolo ou aplicativo.
2. Com base no tipo de erro, o SDK usa o filtro de erros para decidir se a nova repetição precisa ser executada.  Se um **erro irrecuperável** for identificado pelo SDK, as operações (conexão e envio/recebimento) serão interrompidas e o SDK notificará o usuário. Um erro irrecuperável é um erro que o SDK pode identificar e determinar que não pode ser recuperado, por exemplo, uma autenticação ou erro de ponto de extremidade incorreto.
3. Se um **erro irrecuperável** for identificado, o SDK começará a repetir usando a política de repetição especificada até que um tempo limite definido expire.
4. Quando o tempo limite definido expira, o SDK para de tentar conectar ou enviar, e notifica o usuário.
5.  O SDK permite ao usuário anexar um retorno de chamada para receber alterações no status da conexão. 

Três políticas de repetição são fornecidas:
- **Retirada exponencial com tremulação**: essa é a política de repetição padrão aplicada.  Ela tende a ser agressiva no início, desacelera e, em seguida, atinge um atraso máximo que não é excedido.  O design é baseado nas [Diretrizes de repetição do Azure Architecture Center](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Repetição personalizada**: você pode implementar uma política de repetição personalizada e injetá-la no RetryPolicy, dependendo da linguagem escolhida. É possível criar uma política de repetição adequada ao seu cenário.  Isso não está disponível no C do SDK.
- **Sem repetição**: há uma opção para definir a política de repetição para "sem repetição", o que desabilita a lógica de repetição.  O SDK tenta conectar uma vez e enviar uma mensagem uma vez, assumindo que a conexão foi estabelecida. Essa política normalmente seria usada em casos em que há preocupações de largura de banda ou custo.   Se essa opção for escolhida, as mensagens que não forem enviadas serão perdidas e não poderão ser recuperadas. 

### <a name="retry-policy-apis"></a>APIs da política de repetição

   | . | Método SetRetryPolicy | Implementações de política | Diretrizes de implementação |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Padrão**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizado:** usa [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponível<BR>**Sem repetição:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementação C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Padrão**: [classe ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizado:** implementa [interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Sem repetição:** [classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementação Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Padrão**: [classe ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizado:** implementa [interface IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Sem repetição:** [classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementação C#]() |
   | Nó| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Padrão**: [classe ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizado:** implementa [interface RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Sem repetição:** [classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementação Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Abaixo estão exemplos de código que ilustram esse fluxo. 

#### <a name="net-implementation-guidance"></a>Diretrizes de implementação .NET

O exemplo de código abaixo mostra como definir e definir a política de repetição padrão:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Para evitar o alto uso da CPU, as repetições serão aceleradas se o código falhar imediatamente (por exemplo, quando não houver rede nem rota para o destino), de forma que o tempo mínimo para executar a próxima repetição seja de 1 segundo. 

Se o serviço estiver respondendo com um erro de limitação, a política de repetição será diferente e não poderá ser alterada por meio da API pública:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

O mecanismo de repetição será interrompido após `DefaultOperationTimeoutInMilliseconds`, que atualmente é definido como 4 minutos.

#### <a name="other-languages-implementation-guidance"></a>Outras diretrizes de implementação de linguagens
Para outras linguagens, revise a documentação de implementação a seguir.  Os exemplos que demonstram o uso das APIs de política de repetição são fornecidos no repositório.
- [SDK do C/Python/iOS](https://github.com/azure/azure-iot-sdk-c)
- [SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [SDK do Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

