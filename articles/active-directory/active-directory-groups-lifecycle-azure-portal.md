---
title: "Expiração de grupos do Office 365 no Azure Active Directory | Microsoft Docs"
description: "Como configurar o vencimento de grupos do Office 365 no Azure Active Directory (visualização)"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: d485d2e7d22ea79a87dc52dbc063a811f4a1a2ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Configurar a expiração dos grupos do Office 365 (visualização)

Agora é possível gerenciar o ciclo de vida de grupos do Office 365 definindo-se os recursos de expiração para eles. Você pode configurar o vencimento apenas dos grupos do Office 365 no Azure Active Directory (Azure AD). Depois de configurar a expiração de um grupo:
-   Os proprietários do grupo serão notificados para renovar o grupo à medida que a expiração se aproximar
-   Todos os grupos não renovados serão excluídos
-   Os proprietários ou os administradores de grupos poderão restaurar, dentro de 30 dias, qualquer grupo do Office 365 excluído

> [!NOTE]
> A configuração da expiração dos grupos do Office 365 requer uma licença do Azure AD Premium ou uma licença do Azure AD Basic EDU para todos os membros dos grupos aos quais são aplicadas as configurações de expiração.
> 
> Para clientes do Azure AD Basic EDU licenciados: para configurar essa política pela primeira vez, use os cmdlets do PowerShell do Azure Active Directory. Depois disso, você poderá atualizar as configurações de expiração usando o PowerShell ou o portal do Azure AD, com uma conta que seja um Administrador de Conta de Usuário ou Administrador Global em seu locatário do Azure AD.

Para obter informações sobre como baixar e instalar os cmdlets do PowerShell do Azure AD, confira [PowerShell do Azure Active Directory para Graph - Versão de visualização pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="set-group-expiration"></a>Definir a expiração de grupo

1. Abra o [Centro de administração do Azure AD ](https://aad.portal.azure.com) com uma conta de administrador global no seu locatário do Azure AD.

2. Abra o Azure AD e selecione **Usuários e grupos**.

3. Selecione **Configurações de grupo** e, em seguida, selecione **Vencimento** para abrir as configurações de vencimento.
  
  ![Folha Vencimento](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Na folha **Vencimento**, você pode:

  * Definir o tempo de vida do grupo em dias. Selecionar um dos valores predefinidos ou um valor personalizado (deve ser 31 dias ou mais). 
  * Especificar um email para o qual as notificações de vencimento e renovação devem ser enviadas quando um grupo não tem nenhum proprietário. 
  * Selecione quais grupos do Office 365 expirarão. Habilite a o vencimento para **Todos** os grupos do Office 365, selecione entre os grupos do Office 365 ou selecione **Nenhum** para desabilitar a validade de todos os grupos.
  * Salve as configurações quando terminar selecionando **Salvar**.


Os proprietários de grupos do Office 365 receberão emails como este 30 dias, 15 dias e 1 dia antes do vencimento do grupo.

![Notificação de email de vencimento](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Então, o proprietário do grupo pode selecionar: **Renovar grupo** para renovar o grupo do Office 365, ou **Acessar grupo** para ver os membros e outros detalhes sobre o grupo.

Quando um grupo expira, o mesmo é excluído um dia depois da data de vencimento. Os proprietários do grupo do Office 365 recebem um email de notificação como este informando sobre o vencimento e exclusão imediata do grupo do Office 365.

![Notificação de email de exclusão do grupo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Para restaurar o grupo, selecione **Restauração grupo** ou use cmdlets do PowerShell, conforme descrito em [Como restaurar um grupo do Office 365 excluído no Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Se o grupo que você está restaurando contém documentos, sites do SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar completamente o grupo e seu conteúdo.

> [!NOTE]
> * Quando você configura a expiração pela primeira vez, todos os grupos mais antigos que o intervalo de expiração são definidos como 30 dias até a expiração. O primeiro email de notificação de renovação é enviado dentro de um dia. 
>   Por exemplo, um grupo foi criado 400 dias atrás e o intervalo de vencimento é definido para 180 dias. Ao aplicar as configurações de vencimento, um grupo tem 30 dias antes de ser excluído, a menos que seja renovado pelo proprietário.
> * Quando um grupo dinâmico é excluído e restaurado, ele é visto como um grupo novo e preenchido novamente de acordo com a regra. Esse processo pode levar até 24 horas.

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre os grupos do Azure AD.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
