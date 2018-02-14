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
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Como remover o provisionamento dos dispositivos registrados pelo serviço de provisionamento

Talvez seja necessário remover o provisionamento dos dispositivos que foram provisionados através do Serviço de Provisionamento de Dispositivo. Por exemplo, um dispositivo pode ser vendido ou movido para um Hub IoT diferente, ou ser perdido, roubado ou comprometido de qualquer outro modo. 

Em geral, a remoção do provisionamento de um dispositivo envolve duas etapas:

1. Revogar o acesso do dispositivo ao serviço de provisionamento. Se você pretende revogar o acesso temporariamente ou permanentemente, ou, no caso do mecanismo de certificação X.509, na hierarquia dos grupos de registros existentes, é possível desabilitar ou excluir uma entrada de registro. 
 
   - Para saber como revogar o acesso do dispositivo usando o portal, consulte [Revogar o acesso do dispositivo](how-to-revoke-device-access-portal.md).
   - Para saber como revogar o acesso do dispositivo programaticamente usando um dos SDKs do serviço de provisionamento, consulte [Gerenciar registros de dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Desabilitar ou excluir a entrada do dispositivo no registro de identidade para o Hub IoT onde foi provisionado. Para saber mais, consulte [Gerenciar identidades de dispositivo](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) na documentação do Hub IoT do Azure. 

As etapas exatas que devem executadas para cancelar o provisionamento de um dispositivo dependem do respectivo mecanismo de atestado e da entrada de registro aplicável com seu serviço de provisionamento.

## <a name="individual-enrollments"></a>Registros individuais
Dispositivos que utilizam atestado de TPM ou atestado X.509 com um certificado secundário são provisionados através de uma entrada de registro individual. 

Para desprovisionar um dispositivo que tenha um registro individual: 
1. Para dispositivos que usam atestado de TPM, exclua a entrada individual do registro para revogar permanentemente o acesso do dispositivo ao serviço de provisionamento ou desabilite a entrada para revogar temporariamente o acesso. Para dispositivos que usam o atestado X.509, você pode excluir ou desabilitar a entrada. No entanto, lembre-se que, se você excluir um registro individual de um dispositivo que usa o atestado X.509 e houver um grupo de registro habilitado para um certificado de autenticação na cadeia de certificados desse dispositivo, o dispositivo poderá registrar-se novamente. Para esses dispositivos, é mais seguro desabilitar a entrada de registro. Isso impedirá que o dispositivo registre-se novamente, independentemente da existência de um grupo de registro habilitado para um dos certificados de autenticação.
2. Desabilite ou exclua o dispositivo no registro de identidade do Hub IoT para o qual ele foi provisionado. 


## <a name="enrollment-groups"></a>Grupos de registro
Com o atestado X.509, os dispositivos também podem ser provisionados através de um grupo de registros. Os grupos de registro são configurados com um certificado de autenticação, um certificado de AC raiz ou intermediário e controlam o acesso ao serviço de provisionamento para dispositivos com esse certificado na cadeia de certificados. Para saber mais sobre grupos de registro e certificados X.509 com o serviço de provisionamento, consulte [Certificados X.509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos provisionados através de um grupo de registros, você pode visualizar os detalhes do grupo de registros. Essa é uma maneira fácil de entender qual Hub IoT para o qual cada dispositivo foi provisionado. Para visualizar a lista de dispositivos: 

1. Faça o logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Clique no serviço de provisionamento na lista de recursos.
3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
4. Clique no grupo de registros para abri-lo.

   ![Exibir a entrada do grupo de registros no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com grupos de registro há dois cenários a considerar:

- Para desprovisionar todos os dispositivos que foram provisionados por meio de um grupo de registro:
  1. Desabilite o grupo de registro para adicionar o respectivo certificado de autenticação à lista de bloqueios. 
  2. Use a lista de dispositivos provisionados desse grupo de registros para desabilitar ou excluir cada dispositivo do registro de identidade do respectivo Hub IoT. 
  3. Após desabilitar ou excluir todos os dispositivos dos respectivos Hubs IoT, você poderá optar por excluir o grupo de registros. No entanto, esteja ciente de que se excluir o grupo de registros e houver um grupo de registros habilitado para um certificado de autenticação superior na cadeia de certificados de um ou mais dispositivos, esses dispositivos poderão registar novamente. 
- Para desprovisionar um único dispositivo de um grupo de registros:
  1. Crie um registro individual desabilitado para o seu certificado secundário (dispositivo). Isso revoga o acesso ao serviço de provisionamento para esse dispositivo enquanto ainda permite o acesso a outros dispositivos que possuem o certificado de autenticação do grupo de registros em sua cadeia. Não exclua o registro individual desabilitado para o dispositivo. Isso permitirá ao dispositivo registrar novamente através do grupo de registros. 
  2. Use a lista de dispositivos provisionados desse grupo de registros para encontrar o Hub IoT ao qual o dispositivo foi provisionado e desabilitá-lo ou excluí-lo do registro de identidade desse hub. 
  
  










