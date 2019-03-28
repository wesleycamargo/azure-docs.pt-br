---
title: Métodos de autenticação para o ASC para versão prévia do IoT | Microsoft Docs
description: Saiba mais sobre os diferentes métodos de autenticação disponíveis ao usar o ASC para o serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541806"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação do agente de segurança 

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica os diferentes métodos de autenticação que você pode usar com o agente AzureIoTSecurity para autenticar com o IoT Hub.

Para cada integrado de dispositivo ao ASC para IoT no IoT Hub, um módulo de segurança é necessário. Para autenticar o dispositivo, ASC para IoT pode usar um dos dois métodos. Escolha o método que funciona melhor para sua solução de IoT existente. 

> [!div class="checklist"]
> * Opção de módulo de segurança
> * Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente AzureIoTSecurity realizar a autenticação:

 - **Módulo** modo de autenticação<br>
   O módulo é autenticado, independentemente do dispositivo gêmeo.
   As informações necessárias para esse tipo de autenticação está definido pelo arquivo Authentication para C# e o LocalConfiguration.json para C.
        
 - **Dispositivo** modo de autenticação<br>
    Nesse método, o agente de segurança pela primeira vez é autenticado em dispositivo. Após a autenticação inicial, o ASC para agente do IoT realiza **Rest** chamada para o IoT Hub usando a API Rest com os dados de autenticação do dispositivo. O ASC para agente do IoT, em seguida, solicita o método de autenticação do módulo de segurança e os dados do IoT Hub. Na etapa final, o ASC para agente do IoT executa uma autenticação em relação a ASC para o módulo do IoT.    

Ver [parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para aprender a configurar.
                                
## <a name="authentication-methods-known-limitations"></a>Métodos de autenticação limitações conhecidas

- **Módulo** apenas o modo de autenticação dá suporte à autenticação de chave simétrica.
- Certificado assinado por autoridade de certificação não é compatível com **dispositivo** modo de autenticação.  

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Quando [implantar um agente de segurança](select-deploy-agent.md), detalhes de autenticação devem ser fornecidos como argumentos.
Esses argumentos são documentados na tabela a seguir.


|Parâmetro|DESCRIÇÃO|Opções|
|---------|---------------|---------------|
|**identidade**|Modo de autenticação| **Módulo** ou **dispositivo**|
|**tipo**|Tipo de autenticação|**SymmetricKey** ou **SelfSignedCertificate**|
|**filePath**|Absoluto caminho completo do arquivo que contém o certificado ou a chave simétrica| |
|**gatewayHostname**|FQDN do Hub IoT|Exemplo: ContosoIotHub.azure-devices.net|
|**deviceId**|Id do Dispositivo|Exemplo: MyDevice1|
|**certificateLocationKind**|Local de armazenamento de certificado|**LocalFile** ou **Store**|


Ao usar o script de agente de segurança de instalação, a configuração a seguir é executada automaticamente.

Para editar manualmente a autenticação do agente de segurança, edite o arquivo config. 

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, um arquivo de configuração é criado automaticamente.

Para alterar os métodos de autenticação após a implantação, a edição manual do arquivo de configuração é necessária.


### <a name="c-based-security-agent"></a>C#-com base em agente de segurança

Edite _Authentication_ com os seguintes parâmetros:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agente de segurança com base em C

Edite _LocalConfiguration.json_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Consulte também
- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implantar o agente de segurança](select-deploy-agent.md)
- [Acessar os dados brutos de segurança](how-to-security-data-access.md)
