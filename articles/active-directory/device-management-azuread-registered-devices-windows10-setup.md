---
title: Configurar dispositivos Windows 10 registrados no Azure Active Directory | Microsoft Docs
description: Saiba como configurar dispositivos Windows 10 registrados no Azure Active Directory.
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
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurar dispositivos Windows 10 registrados no Azure Active Directory

O gerenciamento de dispositivos no Azure Active Directory (Azure AD) pode ajudar a garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais informações, confira [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md).

Com [dispositivos registrados no Azure AD](device-management-introduction.md#azure-ad-registered-devices), você pode habilitar o cenário **BYOD (traga seu próprio dispositivo)**, que permite que os usuários acessem recursos da sua organização usando dispositivos pessoais.  

No Azure AD, você pode configurar dispositivos registrados no Azure AD para:

- Windows 10
- iOS
- Android
- macOS.  

Este tópico fornece instruções sobre como registrar dispositivos Windows 10 no Azure AD. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve verificar se:

- As permissões para registrar dispositivos 

    ![Registrar](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- Você ainda não excedeu o número máximo de dispositivos por usuário 

    ![Registrar](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

Para saber mais, veja [Definir configurações do dispositivo](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>O que você deve saber

Quando você registrar um dispositivo, observe o seguinte:

- O Windows registra o dispositivo no diretório da organização no Azure AD.

- Talvez seja necessário percorrer o desafio de autenticação multifator. Seu administrador de TI pode configurar esse desafio.

- O Azure AD verifica se um dispositivo exige o registro de gerenciamento de dispositivo móvel. Ele registra o dispositivo, se aplicável.

- O Windows redireciona os usuários gerenciados por meio de entrada automática no desktop.

- Os usuários federados são redirecionados para uma página de logon do Windows para inserir credenciais.


## <a name="register-a-device"></a>Registrar um dispositivo

Para registrar seu dispositivo Windows 10 no Azure AD, conclua as etapas a seguir. Se você tiver registrado com êxito o dispositivo no Azure AD, a página **Acesso corporativo ou de estudante** indicará isso com uma entrada **Conta corporativa ou de estudante**.

![Registrar](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Para registrar seu dispositivo Windows 10:

1. No menu **Iniciar**, clique em **Configurações**.

    ![Escolha Configurações](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Clique em **Contas**.

    ![Selecione Contas](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Clique em **Acesso corporativo ou de estudante**.

    ![Selecione Acesso corporativo ou de estudante](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na página **Acesso corporativo ou de estudante**, clique em **Conectar**.

    ![A página Acesso corporativo ou de estudante](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na página **Configurar uma conta corporativa ou de estudante**, insira seu nome de conta (por exemplo: someone@example.com) e clique em **Avançar**.

    ![A página Configurar uma conta corporativa ou de estudante](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na página **Digitar senha**, digite sua senha e, em seguida, clique em **Avançar**.

    ![Digitar senha](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na página **Tudo pronto**, clique em **Concluído**.

    ![A página Tudo pronto](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao Azure AD, confira a página **Acessar trabalho ou escola** em seu dispositivo.

![Status da conta corporativa ou de estudante](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Como alternativa, você pode examinar as configurações do dispositivo no portal do Azure AD.

![Dispositivos registrados no Azure AD](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte: 

- A [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)

- [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md)





