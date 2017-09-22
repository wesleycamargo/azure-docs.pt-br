---
title: Configurar dispositivos adicionados ao Azure Active Directory | Microsoft Docs
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configurar dispositivos adicionados ao Azure Active Directory

O gerenciamento de dispositivos no Azure Active Directory (Azure AD) pode ajudar a garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais informações, confira [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md).

Com os [dispositivos ingressados no Azure AD](device-management-introduction.md#azure-ad-joined-devices), você pode colocar os dispositivos Windows 10 pertencentes à sua organização sob o controle do Azure AD. Esses dispositivos não devem estar já [ingressados em um AD local](device-management-introduction.md#hybrid-azure-ad-joined-devices).

Este tópico fornece instruções sobre como registrar dispositivos Windows 10 no Azure AD. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve verificar se:

- Você tem permissões para ingressar dispositivos no Azure AD.

    ![Conectado](./media/device-management-azuread-joined-devices-setup/21.png)

- Você ainda não excedeu o número máximo de dispositivos por usuário 

    ![Conectado](./media/device-management-azuread-joined-devices-setup/22.png)


Para mais informações, consulte [Definir configurações do dispositivo](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>O que você deve saber


- O Windows registra o dispositivo no diretório da organização no Azure AD.

- Talvez seja necessário percorrer o desafio de autenticação multifator. Seu administrador de TI pode configurar esse desafio.

- O Azure AD verifica se um dispositivo exige o registro de gerenciamento de dispositivo móvel. Ele registra o dispositivo, se aplicável.

- O Windows redireciona os usuários gerenciados por meio de entrada automática no desktop.

- Os usuários federados são redirecionados para uma página de logon do Windows para inserir credenciais.


## <a name="joining-a-device"></a>Ingressar em um dispositivo

Esta seção fornece as etapas para adicionar seu dispositivo Windows 10 ao Azure AD. Se você adicionou com êxito o dispositivo ao Azure AD, a caixa de diálogo **Acesso corporativo ou de estudante** indicará isso com uma entrada **Conectado ao \<seu Azure AD\>**.

![Conectado](./media/device-management-azuread-joined-devices-setup/13.png)


**Para adicionar seu dispositivo Windows 10:**

1. No menu **Iniciar**, clique em **Configurações**.

    ![Configurações](./media/device-management-azuread-joined-devices-setup/01.png)

2. Clique em **Contas**.

    ![Contas](./media/device-management-azuread-joined-devices-setup/02.png)


3. Clique em **Acesso corporativo ou de estudante**.

    ![Acesso corporativo ou de estudante](./media/device-management-azuread-joined-devices-setup/03.png)

4. Na caixa de diálogo **Acesso corporativo ou de estudante**, clique em **Conectar**.

    ![Connect](./media/device-management-azuread-joined-devices-setup/04.png)


5. Na caixa de diálogo **Configurar uma conta corporativa ou de estudante**, clique em **Adicionar esse dispositivo ao Azure Active Directory**.

    ![Connect](./media/device-management-azuread-joined-devices-setup/08.png)


6. Na caixa de diálogo **Vamos conectá-lo**, digite seu nome da conta (por exemplo, someone@example.com) e, em seguida, clique em **Avançar**.

    ![Vamos conectá-lo](./media/device-management-azuread-joined-devices-setup/10.png)


6. Na caixa de diálogo **Digitar senha**, digite sua senha e, em seguida, clique em **Entrar**.

    ![Digitar senha](./media/device-management-azuread-joined-devices-setup/05.png)


7. Na caixa de diálogo **Certificar-se de que esta é sua organização**, clique em **Ingressar**.

    ![Certificar-se de que esta é sua organização](./media/device-management-azuread-joined-devices-setup/11.png)


8. Na caixa de diálogo **Tudo pronto**, clique em **Concluído**.

    ![Você está pronto](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao seu Azure AD, examine a caixa de diálogo **Acesso corporativo ou de estudante** no seu dispositivo.

![Conectado](./media/device-management-azuread-joined-devices-setup/13.png)

Como alternativa, você pode executar o seguinte comando: `dsregcmd /status`  
Em um dispositivo associado com êxito, **AzureAdJoined** é **Sim**.

![Conectado](./media/device-management-azuread-joined-devices-setup/14.png)

Você também pode examinar as configurações do dispositivo no portal do Azure AD.

![Conectado](./media/device-management-azuread-joined-devices-setup/15.png)

Para obter mais informações, consulte [Localizar dispositivos](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte: 

- A [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)

- [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md)





