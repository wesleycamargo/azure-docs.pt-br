---
title: Configurar um ASC para visualização do agente de IoT | Microsoft Docs
description: Saiba como configurar agentes para uso com o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541911"
---
# <a name="configure-security-agents"></a>Configurar agentes de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como configurar um agente para uso com o ASC para IoT.

## <a name="agents"></a>Agentes

ASC para agentes de segurança de IoT coletam dados de dispositivos IoT e realizar ações de segurança para atenuar as vulnerabilidades detectadas. Configuração do agente de segurança é controlável usando um conjunto de propriedades de gêmeo de módulo, que você pode personalizar. Em geral, as atualizações secundárias para essas propriedades são pouco frequentes.  

ASC para objeto de configuração gêmeo do agente de segurança do IoT é um objeto no formato. JSON. O objeto de configuração é um conjunto de propriedades controláveis que você pode definir para controlar o comportamento do agente. 

Essas configurações ajudam a personalizar o agente para cada cenário exigido. Por exemplo, automaticamente excluindo alguns eventos ou manter o consumo de energia para um nível mínimo são possíveis por meio da configuração dessas propriedades.  

Use o ASC para configuração do agente de segurança de IoT [esquema](https://github.com/azure/asc-for-iot-schemas/security/module/twin) para fazer alterações.  

## <a name="configuration-objects"></a>Objetos de configuração 

Cada ASC para o agente de segurança relacionadas a propriedade está localizada dentro do objeto de configuração do agente, dentro da seção de propriedades desejadas, do módulo azureiotsecurity de IoT. 

Para modificar a configuração, criar e modificar esse objeto dentro da identidade de gêmeo de módulo azureiotsecurity. Se o objeto de configuração do agente não existir no gêmeo do módulo azureiotsecurity, todos os valores de propriedade de agente de segurança são definidos como padrão. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Certifique-se de validar as alterações de configuração do agente em relação a este [esquema](https://aka.ms/iot-security-github-module-schema).
O agente não será iniciado se o objeto de configuração não coincide com o esquema.


## <a name="editing-a-property"></a>Editar uma propriedade 

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente dentro do gêmeo do módulo azureiotsecurity. 

Definir uma propriedade substitui o valor padrão. Para definir uma propriedade, adicione a chave de propriedade para o objeto de configuração com o valor desejado. 

Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Propriedades padrão 
Conjunto de propriedades controláveis que controlam o ASC para agentes de segurança de IoT.

Valores padrão estão disponíveis no esquema adequado no [Github](https://aka.ms/iot-security-module-default).

| NOME| Status | Valores válidos| Valores padrão| DESCRIÇÃO |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obrigatório: false |Valores válidos:  Duração em formato ISO 8601 |Valor padrão: PT7M |Tempo máximo antes das mensagens de alta prioridade são enviados.|
|lowPriorityMessageFrequency |Obrigatório: false|Valores válidos:  Duração em formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes das mensagens de baixa prioridade são enviados.| 
|snapshotFrequency |Exigir: false|Valores: duração válido no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.| 
|maxLocalCacheSizeInBytes |Obrigatório: false |Valores válidos:  |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagens de um agente. Quantidade máxima de espaço permitido para armazenar mensagens do dispositivo antes que as mensagens são enviadas.| 
|maxMessageSizeInBytes |Obrigatório: false |Valores válidos:  Um número positivo maior que 8192, menos 262144 |Valor padrão: 204800 |Máximo tamanho permitido de um agente de mensagem de nuvem. Essa configuração controla a quantidade máxima de dados enviada em cada mensagem. |
|eventPriority${EventName} |Obrigatório: false |Valores válidos:  Alta, baixa, desativado |Valores padrão: |Prioridade de cada agente gerou o evento | 

### <a name="events"></a>Eventos

|Nome do evento| PropertyName | Valor Padrão| Evento de instantâneo| Detalhes de Status  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativar| Falso| Eventos de diagnóstico relacionadas ao agente. Use esse evento para o log detalhado.| 
|Erro de configuração |eventPriorityConfigurationError |Baixo |Falso |Falha ao analisar a configuração do agente. Verifique a configuração em relação ao esquema.| 
|Eventos ignorados estatísticas |eventPriorityDroppedEventsStatistics |Baixo |True|Estatísticas de eventos relacionados ao agente. |
|Estatísticas de mensagem|eventPriorityMessageStatistics |Baixo |True |Estatísticas de mensagens relacionadas ao agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixo |True |Instantâneo de todos os hardwares conectados ao dispositivo.|
|Portas de escuta|eventPriorityListeningPorts |Alto |True |Instantâneo de todas as portas de escuta no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixo |Falso |Auditorias de processam de criação do dispositivo.|
|Encerrar processo|eventPriorityProcessTerminate |Baixo |Falso |Auditorias de processam o encerramento no dispositivo.| 
|Informações do sistema |eventPrioritySystemInformation |Baixo |True |Um instantâneo de informações do sistema (por exemplo: Sistema operacional ou CPU).| 
|Usuários locais| eventPriorityLocalUsers |Alto |True|Um instantâneo dos usuários registrados locais dentro do sistema. |
|Logon|  eventPriorityLogin |Alto|Falso|Auditoria de eventos de logon para o dispositivo (logons locais e remotos).|
|Criar Conexão |eventPriorityConnectionCreate|Baixo|Falso|Audita as conexões TCP criadas para e do dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguration|Baixo|True|Instantâneo da configuração de firewall de dispositivo (regras de firewall). |
|Linha de base do sistema operacional| eventPriorityOSBaseline| Baixo|True|Verifique se o instantâneo de linha de base do dispositivo do sistema operacional.|
 

## <a name="see-also"></a>Veja também

- [Entender o ASC para recomendações de IoT](concept-recommendations.md)
- [Explore o ASC para alertas de IoT](concept-security-alerts.md)
- [Acessar os dados brutos de segurança](how-to-security-data-access.md)