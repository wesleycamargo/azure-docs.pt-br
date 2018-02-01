---
title: "Como remover o provisionamento dos dispositivos registrados com o Serviço de Provisionamento de Dispositivos do Hub IoT do Azure | Microsoft Docs"
description: "Como remover o provisionamento dos dispositivos registrados pelo serviço de DPS no Portal do Azure"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 83ed72c0f2eb342c372ef97e5443d60eab1e2174
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Como remover o provisionamento dos dispositivos registrados pelo serviço de provisionamento

Talvez seja necessário remover o provisionamento dos dispositivos que foram provisionados através do Serviço de Provisionamento de Dispositivo. Por exemplo, um dispositivo pode ser vendido ou movido para um Hub IoT diferente, ou ser perdido, roubado ou comprometido de qualquer outro modo. 

Em geral, a remoção do provisionamento de um dispositivo envolve duas etapas:

1. Revogar o acesso do dispositivo ao serviço de provisionamento. Se você pretende revogar o acesso temporariamente ou permanentemente, ou, no caso do mecanismo de certificação X.509, na hierarquia dos grupos de registros existentes, é possível desabilitar ou excluir uma entrada de registro. 
 
   - Para saber como revogar o acesso do dispositivo usando o portal, consulte [Revogar o acesso do dispositivo](how-to-revoke-device-access-portal.md).
   - Para saber como revogar o acesso do dispositivo programaticamente usando um dos SDKs do serviço de provisionamento, consulte [Gerenciar registros de dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Desabilitar ou excluir a entrada do dispositivo no registro de identidade para o Hub IoT onde foi provisionado. Para saber mais, consulte [Gerenciar identidades de dispositivo](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) na documentação do Hub IoT do Azure. 

As etapas exatas tomadas para remover o provisionamento de um dispositivo dependerão do mecanismo de certificação utilizado.

Dispositivos que utilizam atestado de TPM ou atestado de X.509 com um certificado secundário autoassinado (sem cadeia de certificados) são provisionados através de uma entrada de registro individual. Para esses dispositivos, é possível excluir a entrada para revogar permanentemente o acesso ao dispositivo do serviço de provisionamento ou desabilitar a entrada para revogar temporariamente o acesso e então acompanhar, excluindo ou desabilitando o dispositivo no registro de identidade com o Hub IoT provisionado.

Com o atestado X.509, os dispositivos também podem ser provisionados através de um grupo de registros. Os grupos de registro são configurados com um certificado de autenticação, um certificado de AC raiz ou intermediário e controlam o acesso ao serviço de provisionamento para dispositivos com esse certificado na cadeia de certificados. Para saber mais sobre grupos de registro e certificados X.509 com o serviço de provisionamento, consulte [Certificados X.509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos provisionados através de um grupo de registros, você pode visualizar os detalhes do grupo de registros. Essa é uma maneira fácil de entender qual Hub IoT para o qual cada dispositivo foi provisionado. Para visualizar a lista de dispositivos: 

1. Faça o logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Clique no serviço de provisionamento na lista de recursos.
3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
4. Clique no grupo de registros para abri-lo.

   ![Exibir a entrada do grupo de registros no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com grupos de registro há dois cenários a considerar:

- Para remover o provisionamento de todos os dispositivos que foram provisionados através de um grupo de registros, será necessário primeiro desabilitar o grupo de registros para a lista de bloqueios do certificado de autenticação. Em seguida, você poderá usar a lista de dispositivos provisionados para esse grupo de registros para desabilitar ou excluir cada dispositivo do registro de identidade do respectivo Hub IoT. Após desabilitar ou excluir todos os dispositivos dos respectivos Hubs IoT, você poderá optar por excluir o grupo de registros. No entanto, esteja ciente de que se excluir o grupo de registros e houver um grupo de registros habilitado para um certificado de autenticação superior na cadeia de certificados de um ou mais dispositivos, esses dispositivos poderão registar novamente. 
- Para remover o provisionamento de um único dispositivo de um grupo de registros, primeiro será necessário criar um registro individual desabilitado para o certificado secundário (dispositivo). Isso revoga o acesso ao serviço de provisionamento para esse dispositivo enquanto ainda permite o acesso a outros dispositivos que possuem o certificado de autenticação do grupo de registros em sua cadeia. Em seguida, será possível usar a lista de dispositivos provisionados nos detalhes do grupo de registros para encontrar o Hub IoT ao qual o dispositivo foi provisionado e desabilitá-lo ou excluí-lo do registro de identidade desse hub. Não exclua o registro individual desabilitado para o dispositivo. Isso permitirá ao dispositivo registrar novamente através do grupo de registros. 










