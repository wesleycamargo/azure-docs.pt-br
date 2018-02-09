---
title: "Gerenciar o acesso ao dispositivo para o Serviço de Provisionamento de Dispositivos do Hub IoT do Azure | Microsoft Docs"
description: "Como revogar o acesso ao dispositivo para seu serviço DPS no Portal do Azure"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 12aebf3a56aa7469a765ab6fc67aa65b254db71a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Revogar o acesso ao dispositivo para seu serviço de provisionamento no portal do Azure

O gerenciamento adequado de credenciais do dispositivo é crucial para sistemas importantes como soluções de IoT. Uma melhor prática para tais sistemas é ter um plano claro de como revogar o acesso de dispositivos quando suas credenciais, sejam um token SAS (assinatura de acesso compartilhado) ou um certificado X.509, podem estar comprometidas. Este artigo descreve como revogar o acesso ao dispositivo na etapa de provisionamento.

Para saber mais sobre como revogar o acesso ao dispositivo para um Hub IoT depois que o dispositivo tiver sido provisionado, consulte [Desativar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Atente-se à política de repetição dos dispositivos para os quais você revoga o acesso. Por exemplo, um dispositivo que tem uma política de repetição infinita tenta continuamente registrar com o serviço de provisionamento. Essa situação consome recursos de serviço e possivelmente afeta o desempenho.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Adicionar os dispositivos usando uma entrada de registro individual à lista de bloqueios

Os registros individuais se aplicam a um único dispositivo e podem usar certificados X.509 ou tokens SAS (em um TPM real ou virtual) como o mecanismo de atestado. (Dispositivos que usam tokens SAS como seu mecanismo de atestado podem ser provisionados apenas com um registro individual.) Para adicionar um dispositivo que tem um registro individual à lista de bloqueios, você pode desabilitar ou excluir sua entrada de registro. 

Para adicionar temporariamente o dispositivo à lista de bloqueios desabilitando sua entrada de registro: 

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Selecione a entrada de registro do dispositivo que você deseja adicionar à lista de bloqueios. 
5. Selecione **Desabilitar** na botão **Habilitar entrada** e, em seguida, selecione **Salvar**.  

   ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
Para adicionar permanentemente um dispositivo à lista de bloqueios excluindo sua entrada de registro:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Selecione a caixa de seleção ao lado da entrada de registro do dispositivo que você deseja adicionar à lista de bloqueios. 
5. Selecione **Excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o registro. 

   ![Excluir a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Depois de concluir o procedimento, você deve ver sua entrada removida da lista de registros individuais.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Adicionar um intermediário X.509 ou Certificado de AC raiz à lista de bloqueios usando um grupo de registros

Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança. Se um certificado em qualquer estágio de uma cadeia for comprometido, a relação de confiança é quebrada. O certificado deve estar na lista negra para impedir o Serviço de Provisionamento de Dispositivos de provisionar dispositivos downstream em qualquer cadeia que contenham esse certificado. Para saber mais sobre certificados X.509 e como eles são usados com o serviço de provisionamento, consulte [Certificados X.509](./concepts-security.md#x509-certificates). 

Um grupo de registros é uma entrada para dispositivos que compartilham um mecanismo de atestado comum de certificados X.509 assinados pela mesma AC intermediária ou raiz. A entrada de grupo de registros está configurada com o certificado x. 509 associado com a autoridade de certificação raiz ou intermediária. A entrada também é configurada com quaisquer valores de configuração, como um estado gêmeo e uma conexão de Hub IoT, que são compartilhados pelos dispositivos com esse certificado em sua cadeia de certificados. Para adicionar o certificado à lista de bloqueios, você pode desabilitar ou excluir seu grupo de registros.

Para adicionar temporariamente o certificado à lista de bloqueios desabilitando sua entrada de registro: 

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar o certificado de autenticação à lista de bloqueios.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registros**.
4. Selecione o grupo de registros do certificado que você deseja adicionar à lista de bloqueios.
5. Na entrada de grupo de registros, selecione **Editar grupo**.
6. Selecione **Desabilitar** na botão **Habilitar entrada** e, em seguida, selecione **Salvar**.  

   ![Desabilitar a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Para adicionar permanentemente o certificado à lista de bloqueios excluindo seu grupo de registros:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registros**.
4. Marque a caixa de seleção ao lado do grupo de registros para o certificado que você deseja colocar na lista bloqueios. 
5. Selecione **Excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o grupo de registros. 

   ![Excluir a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de concluir o procedimento, você deve ver sua entrada removida da lista de grupos de registros.  

> [!NOTE]
> Se você excluir um grupo de registros de um certificado, os dispositivos que têm esse certificado na cadeia de certificados ainda poderão se registrar se houver um grupo de registro habilitado para o certificado raiz ou outro certificado intermediário mais alto na cadeia de certificados.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Adicionar dispositivos específicos em um grupo de registros à lista de bloqueios

Os dispositivos que implementam o mecanismo de atestado X.509 usam a chave privada e a cadeia de certificados do dispositivo para se autenticarem. Quando um dispositivo se conecta e é autenticado com o Serviço de Provisionamento do Dispositivo, o serviço primeiro procura por um registro individual que faz a correspondência de credenciais do dispositivo. Em seguida, o serviço pesquisa os grupos de registro para determinar se o dispositivo pode ser provisionado. Se o serviço encontrar um registro individual desabilitado para o dispositivo, ela impedirá que o dispositivo se conecte. O serviço impede a conexão, mesmo que exista um grupo de registros habilitado para um intermediário ou a autoridade de certificação raiz da cadeia de certificados do dispositivo. 

Para adicionar um dispositivo individual à lista de bloqueios em um grupo de registros, siga essas etapas:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento que contém o grupo de registros do dispositivo que você deseja adicionar à lista de bloqueios.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Selecione o botão **Adicionar** na parte superior. 
5. Selecione **X.509** como o mecanismo de segurança para o dispositivo e carregue o certificado do dispositivo. Esse é o certificado de entidade final assinado instalado no dispositivo. O dispositivo usa para gerar certificados para autenticação.
6. Para a **ID de dispositivo do Hub IoT**, insira a ID para o dispositivo. 
7. Selecione **Desabilitar** na botão **Habilitar entrada** e, em seguida, selecione **Salvar**. 

   ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Após a criação bem-sucedida de seu registro, você deverá ver o dispositivo aparecer na guia **Registros Individuais**.


