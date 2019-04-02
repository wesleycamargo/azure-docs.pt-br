---
title: Criar um módulo gêmeo de segurança para a Versão Prévia da ASC para IoT | Microsoft Docs
description: Aprenda como criar um módulo gêmeo da ASC para IoT para usar com a ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541702"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Início Rápido: Criar um módulo gêmeo da ASC para IoT

> [!IMPORTANT]
> A ASC para IoT está atualmente em versão prévia pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de início rápido explica como criar módulos gêmeos individuais da ASC para IoT para novos dispositivos ou criar módulos gêmeos em lote para todos os dispositivos em um Hub IoT.  

## <a name="understanding-asc-for-iot-module-twins"></a>Conheça os módulos gêmeos da ASC para IoT 

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo. 

A ASC para IoT oferece uma integração completa com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie seu status de segurança de dispositivo, bem como faça o uso de recursos de controle de dispositivo existente. A integração da ASC para IoT é obtida usando o mecanismo gêmeo do Hub IoT.  

Veja [módulos gêmeos do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral dos módulos gêmeos no Hub IoT do Azure. 
 
A ASC para IoT usa o mecanismo de módulo gêmeo e mantém um módulo gêmeo de segurança para cada um dos seus dispositivos. O módulo gêmeo de segurança contém todas as informações relevantes para a segurança de dispositivo para cada um dos seus dispositivos. 
 
Para aproveitar totalmente os recursos da ASC para IoT, você precisará criar, configurar e usar esses módulos gêmeos de segurança para cada dispositivo no serviço.  

## <a name="create-asc-for-iot-module-twin"></a>Criar um módulo gêmeo da ASC para IoT 

Os módulos gêmeos da ASC para IoT podem ser criados em modo lote usando a configuração padrão ou individualmente com configurações específicas para cada dispositivo. Para criar em modo lote novos dispositivos ou dispositivos sem um módulo gêmeo, use o [Módulo script de lote](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> Usar o método de lote não substitui os módulos gêmeos existentes. Usar o método de lote APENAS cria módulos gêmeos para dispositivos que ainda não têm um módulo gêmeo. 

Veja [Modificar um módulo gêmeo de segurança](how-to-modify-security-module-twin.md) para aprender como modificar ou alterar a configuração de um módulo gêmeo existente. 

Para criar um módulo gêmeo da ASC para IoT para um dispositivo, use as instruções a seguir: 

1. Em seu Hub IoT, localize e selecione o dispositivo para o qual deseja criar um módulo gêmeo de segurança. 
1. No campo **Nome de Identidade da Microsoft**, insira **ascforiotsecurity**.
1. Clique em **Salvar**. 

## <a name="verify-creation-of-a-module-twin"></a>Verificar a criação de um módulo gêmeo

Para verificar a existência de um módulo gêmeo de segurança para um dispositivo específico:

1. No seu Hub IoT do Azure, selecione **Dispositivos IoT** no menu **Explorers**.    
1. Insira a ID do dispositivo ou selecione uma opção no **Campo de dispositivo de consulta** e clique em **Dispositivos de consulta**. 
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou clique nele duas vezes para abrir a página de detalhes do Dispositivo. 
1. Selecione o menu **Identidades do módulo** e confirme a existência de um módulo **ascforiotsecurity** e uma **Estado de Conexão** de **Conectado** na lista de identidades de módulo associadas ao dispositivo. 
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-2.png)


Para aprender mais sobre como personalizar propriedades de módulos gêmeos da ASC para IoT, consulte [Configuração do agente](concept-agent-configuration.md).

## <a name="next-steps"></a>Próximas etapas

Avance ao próximo artigo para aprender como criar alertas personalizados...

> [!div class="nextstepaction"]
> [Configurar alertas personalizados](quickstart-create-custom-alerts.md)
