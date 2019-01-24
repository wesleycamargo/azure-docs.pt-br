---
title: Ingressar em um novo dispositivo Windows 10 com o Azure AD durante uma primeira execução | Microsoft Docs
description: Um tópico que explica como os usuários podem configurar a Junção do Azure AD durante sua experiência de primeira execução.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3693115d72d9928bea0ca8bbe4fae5858e0f3cb7
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446418"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Tutorial: Ingressar em um novo dispositivo Windows 10 com o Azure AD durante uma primeira execução

Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais informações, consulte a [introdução ao gerenciamento de dispositivos no Active Directory do Azure](overview.md).

Com o Windows 10, você pode adicionar um novo dispositivo ao Azure AD durante a FRX (experiência de primeira execução).  
Isso permite que você distribua dispositivos compactados aos seus funcionários ou alunos.

Se você tiver o Windows 10 Professional ou o Windows 10 Enterprise em um dispositivo, a experiência usará como padrão o processo de instalação de dispositivos de propriedade da empresa.

Não há suporte para o ingresso em um domínio local do AD (Active Directory) na *configuração inicial pelo usuário* do Windows. Se pretender adicionar um computador a um domínio do AD durante a configuração, será necessário selecionar o link **Configurar o Windows com uma conta local**. Em seguida, será possível ingressar no domínio pelas configurações do computador.
 
Neste tutorial, você aprenderá a adicionar um dispositivo ao Azure AD durante a FRX:
 > [!div class="checklist"]
> * Pré-requisitos
> * Ingressar em um dispositivo
> * Verificação

## <a name="prerequisites"></a>Pré-requisitos

Para ingressar em um dispositivo Windows 10, o serviço de registro do dispositivo deverá ser configurado para permitir que você registre dispositivos. Além de ter permissão para ingressar em dispositivos em seu locatário do Azure AD, você deverá ter menos dispositivos registrados que o máximo configurado. Para mais informações, consulte [Definir configurações do dispositivo](device-management-azure-portal.md#configure-device-settings).

Além disso, se seu locatário for federado, seu provedor de identidade PRECISARÁ dar suporte aos pontos de extremidade de nome de usuário/senha WS-Fed e WS-Trust. Pode ser a versão 1.3 ou 2005. O suporte deste protocolo é necessário para ingressar o dispositivo no Azure AD e para fazer logon no dispositivo com senha.

## <a name="joining-a-device"></a>Ingressar em um dispositivo

**Para ingressar em um dispositivo Windows 10 ao Azure AD durante a FRX:**


1. Ao ligar o novo dispositivo e iniciar o processo de instalação, você deverá ver a mensagem **Preparando-se** . Siga os prompts para configurar o dispositivo.

2. Inicie personalizando a região e o idioma. Em seguida, aceite os Termos de Licença para Software Microsoft.
 
    ![Personalizar a região](./media/azuread-joined-devices-frx/01.png)

3. Selecione a rede que deseja usar para se conectar à Internet.

4. Clique em **Este dispositivo pertence à minha organização**. 

    ![Tela Quem é o proprietário deste computador?](./media/azuread-joined-devices-frx/02.png)

5. Insira as credenciais que foram fornecidas a você por sua organização e clique em **Entrar**.

    ![Tela de entrada](./media/azuread-joined-devices-frx/03.png)

6. Seu dispositivo localiza um locatário correspondente no Azure AD. Se estiver em um domínio federado, você será redirecionado para o servidor local do STS (Serviço de Token Seguro), por exemplo, o Serviços de Federação do Active Directory (AD FS).

7. Se você for um usuário em um domínio não federado, será necessário inserir suas credenciais diretamente na página hospedada pelo Azure AD. 

8. Um desafio da autenticação multifator será apresentado a você. 
 
9. O Azure AD verifica se é necessário um registro no gerenciamento de dispositivo móvel.

10. O Windows registra o dispositivo no diretório da organização no Azure AD e faz o registro no gerenciamento de dispositivo móvel, se aplicável.

11. Se você for:
    - Um usuário gerenciado, o Windows o levará para a área de trabalho por meio do processo de entrada automática.

    - Um usuário federado, será direcionado para a tela de entrada do Windows para inserir suas credenciais.

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao seu Azure AD, examine a caixa de diálogo **Acesso corporativo ou de estudante** no seu dispositivo do Windows. A caixa de diálogo deverá indicar que você está conectado ao seu diretório do Azure AD.

![Acesso corporativo ou de estudante](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte a [introdução ao gerenciamento de dispositivos no Active Directory do Azure](overview.md).

- Para obter mais informações sobre como gerenciar dispositivos no portal do Azure AD, consulte [gerenciamento de dispositivos usando o portal do Azure](device-management-azure-portal.md).
