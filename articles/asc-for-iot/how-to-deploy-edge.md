---
title: Implantar a Central de segurança do Azure para o módulo do IoT Edge | Microsoft Docs
description: Saiba mais sobre como implantar a Central de segurança do Azure para o agente de segurança de IoT no IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 2a201fe649d52ad9604c7ac6675b26d60e7f2dd1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754774"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Implantar o módulo de segurança em seu dispositivo IoT Edge

> [!IMPORTANT]
> Central de segurança do Azure para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Central de segurança do Azure (ASC) para IoT **azureiotsecurity** módulo fornece uma solução abrangente de segurança para seu dispositivo IoT Edge.
Módulo de segurança coleta, agrega e analisa os dados brutos de segurança do seu sistema operacional e contêiner do sistema em alertas e recomendações de práticas de segurança.
Para obter mais informações, consulte [módulo de segurança do IoT Edge](security-edge-architecture.md).

Neste guia, você aprenderá como implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar o módulo de segurança

Use as etapas a seguir para implantar um ASC para o módulo de segurança de IoT para o IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. Verifique se o dispositivo está [registrado como um dispositivo IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. O ASC para o módulo do IoT Edge requer o [AuditD framework](https://linux.die.net/man/8/auditd) instalado no dispositivo de borda.

   Instale o framework, executando o seguinte comando no seu dispositivo Edge:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Implantação usando o portal do Azure

1. Abra **Marketplace** no portal do Azure.

1. Pesquise **segurança do azure iot** e clique em **segurança do Azure IoT**.

   ![](media/howto/edge_onboarding_7.png)

1. Clique em **Criar**.

1. Escolha sua **assinatura**, **IoT Hub** e **nome do dispositivo do IoT Edge**, em seguida, clique em **criar**.

1. Clique em **próxima** duas vezes para **revisão implantação**.

1. Certifique-se **edgeHub.settings.createOptions** está configurado da seguinte maneira:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Clique em **enviar** para concluir a implantação.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre opções de configuração, continue para o guia de instruções para configuração do módulo. 
> [!div class="nextstepaction"]
> [Configuração do módulo de guia de instruções](./how-to-agent-configuration.md)
