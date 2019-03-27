---
title: Início rápido da política de término para grupos do Office 365 – Azure Active Directory | Microsoft Docs
description: Expiração de grupos do Office 365 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0573448c753c763e818d641216033dbeacb9e9a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199300"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Início rápido: Configurar os grupos do Office 365 para expirar no Azure Active Directory

Neste início rápido, você pode definir a política de expiração para os grupos do Office 365. Quando os usuários podem configurar seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma maneira de gerenciar os grupos não utilizados é definir esses grupos para expirar, para reduzir o trabalho de ter que exclui-los manualmente.

A política de expiração é simples:

* Os proprietários do grupo serão notificados para renovar um grupo prestes a expirar
* Um grupo que não é renovado será excluído
* Um grupo excluído do Office 365 pode ser restaurado em 30 dias, por um proprietário de grupo ou por um administrador do Azure AD

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

Você deve ser um Administrador global ou Administrador de usuários na organização para configurar a expiração de grupo.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de usuário para grupos

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global ou Administrador de usuários da organização.

2. Selecione **Grupos**e, em seguida, selecione **Geral**.
  
   ![Página de configurações de grupos de autoatendimento](./media/groups-quickstart-expiration/self-service-settings.png)

3. Defina **Usuários podem criar grupos do Office 365** para **Sim**.

4. Selecione **Salvar** para salvar as configurações de grupos quando você terminar.

## <a name="set-group-expiration"></a>Definir a expiração de grupo

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** > **Grupos** > **Expiração** para abrir as configurações de expiração.
  
   ![Página de configurações de expiração para grupo](./media/groups-quickstart-expiration/expiration-settings.png)

2. Defina o intervalo de expiração. Selecione um valor predefinido ou insira um valor personalizado superior a 31 dias. 

3. Forneça um endereço de email para o qual as notificações de expiração e renovação devem ser enviadas quando um grupo não tem nenhum proprietário.

4. Para este início rápido, defina **Habilitar expiração para esses grupos do Office 365** para **Todos**.

5. Selecione **Salvar** para salvar as configurações de expiração quando você terminar.

É isso! Neste início rápido, você definiu com sucesso a política de expiração para os grupos do Office 365 selecionados.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-remove-the-expiration-policy"></a>Para remover a política de expiração

1. Certifique-se de que você está conectado no [portal do Azure](https://portal.azure.com) com uma conta que seja de Administrador Global para o seu locatário.
2. Selecione **Azure Active Directory** > **Grupos** > **Expiração**.
3. Definir **Habilitar expiração para esses grupos do Office 365** para **Nenhum**.

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação de usuários para grupos

1. Selecione **Azure Active Directory** > **Grupos** > **Geral**. 
2. Definir **Usuários podem criar grupos do Office 365 nos portais do Azure** para **Não**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a expiração, incluindo restrições técnicas, adicionando uma lista de palavras bloqueadas personalizadas e experiências de usuário final entre aplicativos do Office 365, consulte o artigo a seguir que contém os detalhes da política de expiração:

> [!div class="nextstepaction"]
> [Política de expiração e todos os seus detalhes](groups-lifecycle.md)
