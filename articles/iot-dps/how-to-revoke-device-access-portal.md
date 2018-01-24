---
title: "Como gerenciar o acesso ao dispositivo para o Serviço de Provisionamento de Dispositivo do Hub IoT do Azure | Microsoft Docs"
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
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Como revogar o acesso ao dispositivo para seu serviço de provisionamento no Portal do Azure

O gerenciamento adequado de credenciais do dispositivo é crucial para sistemas importantes como soluções de IoT. Uma melhor prática para tais sistemas é ter um plano claro de como revogar o acesso de dispositivos em casos em que suas credenciais, sejam um token SAS ou um certificado X.509, podem estar comprometidas. Este artigo descreve como revogar o acesso ao dispositivo na etapa de provisionamento.

Para saber mais sobre como revogar o acesso ao dispositivo para um Hub IoT depois que o dispositivo tiver sido provisionado, consulte [Desabilitar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Atente-se à política de repetição dos dispositivos para os quais você revoga o acesso. Por exemplo, um dispositivo com uma política de repetição infinita pode tentar continuamente se registrar com o serviço de provisionamento, consumindo recursos de serviço e possivelmente afetando o desempenho.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Adicionar os dispositivos com uma entrada de registro individual à lista de bloqueios

Os registros individuais se aplicam a um único dispositivo e podem usar certificados X.509 ou tokens SAS (em um TPM real ou virtual) como o mecanismo de atestado. (Dispositivos que usam tokens SAS como seu mecanismo de atestado podem ser provisionados apenas com um registro individual.) Para adicionar um dispositivo que tem um registro individual à lista de bloqueios, você pode desabilitar ou excluir sua entrada de registro: 

- Para adicionar temporariamente o dispositivo à lista de bloqueios, você pode desabilitar sua entrada de registro. 

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
    4. Clique na entrada de registro do dispositivo que você deseja adicionar à lista de bloqueios e abra-a. 
    5. Clique em **Desabilitar** na parte inferior da entrada da lista de registros e clique em **Salvar**.  

        ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Para adicionar permanentemente um dispositivo à lista de bloqueios, você pode excluir sua entrada de registro.

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
    4. Marque a caixa ao lado da entrada de registro do dispositivo que você deseja adicionar à lista de bloqueios. 
    5. Clique em **Excluir** na parte superior da janela, em seguida, clique em **Sim** para confirmar que deseja remover o registro. 

        ![Excluir a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Depois que a ação for concluída, você verá sua entrada removida da lista de registros individuais.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Adicionar um intermediário X.509 ou Certificado de AC raiz à lista de bloqueios usando um grupo de registro

Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança. Se um certificado em qualquer estágio de uma cadeia é comprometido, a relação de confiança é interrompida e o certificado precisa estar na lista negra para evitar que os dispositivos adiante no fluxo em qualquer cadeia que contêm esse certificado sejam provisionados pelo Serviço de Provisionamento de Dispositivo. Para saber mais sobre certificados X.509 e como eles são usados com o serviço de provisionamento, consulte [Certificados X.509](./concepts-security.md#x509-certificates). 

Um grupo de registros é uma entrada para dispositivos que compartilham um mecanismo de atestado comum de certificados X.509 assinados pela mesma AC intermediária ou raiz. A entrada do grupo de registros é configurada com um certificado X.509 associado a uma AC intermediária ou raiz, bem como quaisquer valores de configuração, como um estado gêmeo e uma conexão de Hub IoT, que são compartilhados pelos dispositivos com esse certificado em sua cadeia de certificados. Para adicionar o certificado à lista de bloqueios, você pode desabilitar ou excluir seu grupo de registros:

- Para adicionar temporariamente o certificado à lista de bloqueios, você pode desabilitar sua entrada de registro. 

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do qual você deseja adicionar o certificado de autenticação à lista de bloqueios na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
    4. Clique no grupo de registros do certificado que você deseja adicionar à lista de bloqueios e abra-o.
    5. Clique em **Editar grupo** no canto superior esquerdo da entrada do grupo de registros.
    6. Para desabilitar a entrada de registro, selecione **Desabilitar** na opção **Habilitar entrada** e clique em **Salvar**.  

        ![Desabilitar a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Para adicionar permanentemente o certificado à lista de bloqueios, você pode excluir seu grupo de registros.

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    2. Clique no serviço de provisionamento do qual você deseja adicionar seu dispositivo à lista de bloqueios na lista de recursos.
    3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
    4. Clique na caixa ao lado do grupo de registros do certificado que você deseja adicionar à lista de bloqueios. 
    5. Clique em **Excluir** na parte superior da janela, em seguida, clique em **Sim** para confirmar que deseja remover o grupo de registros. 

        ![Excluir a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Depois que a ação for concluída, você verá sua entrada removida da lista de grupos de registro.  

> [!NOTE]
> Se você excluir um grupo de registros de um certificado, os dispositivos que têm esse certificado na cadeia de certificados ainda poderão se registrar se houver um grupo de registro habilitado para o certificado raiz ou outro certificado intermediário mais alto na cadeia de certificados.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Adicionar dispositivos específicos em um grupo de registros à lista de bloqueios

Os dispositivos que implementam o mecanismo de atestado X.509 usam a chave privada e a cadeia de certificados do dispositivo para se autenticarem. Quando um dispositivo se conecta e se autentica com o Serviço de Provisionamento de Dispositivo, o serviço primeiro procura por um registro individual correspondente às credenciais do dispositivo antes de pesquisar os grupos de registros para determinar se o dispositivo pode ser provisionado. Se o serviço encontra um registro individual desabilitado para o dispositivo, ele impede que o dispositivo se conecte, mesmo se existe um grupo de registro habilitado para uma AC intermediária ou raiz na cadeia de certificados do dispositivo. Para adicionar um dispositivo individual à lista de bloqueios em um grupo de registros, siga essas etapas:

1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, clique no serviço de provisionamento que contém o grupo de registros do dispositivo que você deseja adicionar à lista de bloqueios.
3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Clique no botão **Adicionar** na parte superior. 
5. Selecione **X.509** como o mecanismo de segurança para o dispositivo e carregue o certificado do dispositivo. Esse é o certificado de entidade final assinado instalado no dispositivo, que ele usa para gerar certificados para autenticação.
6. Insira a **ID de dispositivo do Hub IoT** para o dispositivo. 
7. Para desabilitar a entrada de registro, selecione **Desabilitar** na opção **Habilitar entrada**. 
8. Clique em **Salvar**. Após a criação bem-sucedida de seu registro, você deverá ver o dispositivo aparecer sob a guia **Registros Individuais**. 

    ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




