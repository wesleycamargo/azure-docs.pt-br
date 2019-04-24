---
title: Definir a expiração de grupos do Office 365 – Azure Active Directory | Microsoft Docs
description: Como configurar o vencimento de grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c19ee3bdd14ee6a2c5b59294f475f6c18b570fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60471910"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurar a política de expiração de grupos do Office 365

Agora é possível gerenciar o ciclo de vida de grupos do Office 365 definindo-se uma política de expiração para eles. Você pode configurar a política de expiração apenas dos grupos do Office 365 no Azure Active Directory (Azure AD). 

Depois de configurar a expiração de um grupo:

- Os proprietários do grupo serão notificados para renovar o grupo à medida que a expiração se aproximar
- Todos os grupos não renovados serão excluídos
- Os proprietários ou os administradores de grupos poderão restaurar, dentro de 30 dias, qualquer grupo do Office 365 excluído

Atualmente, apenas uma política de expiração pode ser configurada para grupos do Office 365 em um locatário.

> [!NOTE]
> Configurar e usar a política de expiração para os grupos do Office 365 exige que você tenha as licenças Azure AD Premium para todos os membros dos grupos aos quais a política de expiração é aplicada.

Para saber mais sobre como fazer o download e instalar os cmdlets do PowerShell do Azure AD, confira [PowerShell do Azure Active Directory para Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Funções e permissões

As funções a seguir podem configurar e usar a expiração para grupos do Office 365 no Azure AD.

Função | Permissões
-------- | --------
Administrador global ou administrador de usuário | Pode criar, ler, atualizar ou excluir as configurações de política de expiração de grupos do Office 365<br>Pode renovar qualquer grupo do Office 365
Usuário | Pode renovar um grupo do Office 365 que ele possua<br>Pode restaurar um grupo do Office 365 que ele possua<br>Pode ler as configurações da política de expiração

Para saber mais sobre permissões para restaurar um grupo excluído, confira [Restaurar um grupo excluído do Office 365 no Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Definir a expiração de grupo

1. Abra o [Centro de administração do Azure AD ](https://aad.portal.azure.com) com uma conta de administrador global no seu locatário do Azure AD.

2. Selecione **Grupos** e, em seguida, selecione **Vencimento** para abrir as configurações de vencimento.
  
   ![Configurações de expiração para grupos](./media/groups-lifecycle/expiration-settings.png)

3. Na folha **Vencimento**, você pode:

  * Definir o tempo de vida do grupo em dias. Selecionar um dos valores predefinidos ou um valor personalizado (deve ser 31 dias ou mais). 
  * Especificar um email para o qual as notificações de vencimento e renovação devem ser enviadas quando um grupo não tem nenhum proprietário. 
  * Selecione quais grupos do Office 365 expirarão. Você pode habilitar a expiração para **Todos** grupos do Office 365, você pode optar por habilitar apenas grupos **Selecionados** do Office 365, ou selecionar **Nenhum** para desabilitar a expiração para todos os grupos.
  * Salve as configurações quando terminar selecionando **Salvar**.

> [!NOTE]
> * Quando você configura a expiração pela primeira vez, todos os grupos mais antigos que o intervalo de expiração são definidos como 30 dias até a expiração. O primeiro email de notificação de renovação é enviado dentro de um dia. Por exemplo, um grupo foi criado 400 dias atrás e o intervalo de vencimento é definido para 180 dias. Ao aplicar as configurações de vencimento, um grupo tem 30 dias antes de ser excluído, a menos que seja renovado pelo proprietário.
> * Quando um grupo dinâmico é excluído e restaurado, ele é visto como um grupo novo e preenchido novamente de acordo com a regra. Esse processo pode levar até 24 horas.

## <a name="email-notifications"></a>Notificações por email

Os proprietários de grupos do Office 365 receberão emails como este 30 dias, 15 dias e 1 dia antes do vencimento do grupo. O idioma do email é determinado pelo idioma preferencial do proprietário de grupos ou pelo idioma de locatário. Se o proprietário do grupo tiver definido um idioma preferido ou se vários proprietários tiverem o mesmo idioma preferencial, esse idioma será usado. Para todos os outros casos, o idioma de locatário será usado.

![Notificações por email de expiração](./media/groups-lifecycle/expiration-notification.png)

No email de notificação **Renovar o grupo**, os proprietários do grupo podem acessar diretamente a página de detalhes do grupo no Painel de Acesso. Lá, os usuários podem obter mais informações sobre o grupo, como sua descrição, quando foi renovado pela última vez, quando ele expirará, e também a capacidade de renovar o grupo. A página de dados do grupo agora também inclui links para os recursos do grupo do Office 365, para que o proprietário do grupo possivelmente possa visualizar o conteúdo e a atividade em seu grupo.

Quando um grupo expira, o mesmo é excluído um dia depois da data de vencimento. Os proprietários do grupo do Office 365 recebem um email de notificação como este informando sobre o vencimento e exclusão imediata do grupo do Office 365.

![Notificações por email de exclusão do grupo](./media/groups-lifecycle/deletion-notification.png)

Para restaurar o grupo dentro de 30 dias de sua exclusão, selecione **Restaurar grupo** ou use cmdlets do PowerShell, conforme descrito em [Como restaurar um grupo do Office 365 excluído no Azure Active Directory](groups-restore-deleted.md). Observe que o período de restauração de grupo de 30 dias não é personalizável.
    
Se o grupo que você está restaurando contém documentos, sites do SharePoint ou outros objetos persistentes, pode levar até 24 horas para restaurar completamente o grupo e seu conteúdo.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Como recuperar a data de expiração de grupo do Office 365
Além do painel de acesso em que os usuários podem exibir os detalhes do grupo incluindo data de vencimento e data da última renovada, data de expiração de um grupo do Office 365 pode ser recuperada da versão Beta do Microsoft Graph REST API. expirationDateTime como uma propriedade de grupo foi habilitado na versão Beta do Microsoft Graph. Ele pode ser recuperado com uma solicitação GET. Para obter mais detalhes, consulte [Este exemplo](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Para gerenciar associações de grupo no painel de acesso, "Restringir o acesso a grupos no painel de acesso" precisa ser definido como "Não" no Azure Active Directory grupos de configuração geral.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Como a expiração do grupo do Office 365 funciona com uma caixa de correio em retenção legal
Quando um grupo expira e é excluído, 30 dias após a exclusão os dados do grupo de aplicativos como Planner, Sites ou Teams são excluídos permanentemente, mas a caixa de correio do grupo que está em retenção legal é mantida e não é excluída permanentemente. O administrador pode usar cmdlets do Exchange para restaurar a caixa de correio para buscar os dados. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Como a expiração do grupo do Office 365 funciona com a política de retenção
A política de retenção é configurada por meio do Centro de Segurança e Conformidade. Se você configurou uma política de retenção para grupos do Office 365, quando um grupo expira e é excluído, as conversações em grupo na caixa de correio do grupo e nos arquivos no site do grupo são mantidas no recipiente de retenção para o número específico de dias definidos na política de retenção. Os usuários não verão o grupo ou seu conteúdo após a expiração, mas podem recuperar o site e os dados da caixa de correio via descoberta eletrônica.

## <a name="powershell-examples"></a>Exemplos do PowerShell
Aqui estão exemplos de como você pode usar cmdlets do PowerShell para configurar as configurações de expiração para os grupos do Office 365 em seu locatário:

1. Instale o módulo de visualização do PowerShell v2.0 (2.0.0.137) e conecte no prompt do PowerShell:
   ```powershell
   Install-Module -Name AzureADPreview
   connect-azuread 
   ```
2. Defina as configurações de expiração New-AzureADMSGroupLifecyclePolicy:  Esse cmdlet define o tempo de vida de todos os grupos do Office 365 no locatário para 365 dias. As notificações de renovação para grupos do Office 365 sem proprietários serão enviadas para ‘emailaddress@contoso.com’
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Recupere a política existente Get-AzureADMSGroupLifecyclePolicy: Esse cmdlet recupera as configurações de expiração atuais do grupo do Office 365 que foram configuradas. Neste exemplo, você pode ver:
   * O ID da política 
   * O tempo de vida de todos os grupos do Office 365 no locatário está configurado para 365 dias
   * As notificações de renovação para grupos do Office 365 sem proprietários serão enviadas para ‘emailaddress@contoso.com’.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Atualize a política existente Set-AzureADMSGroupLifecyclePolicy: Esse cmdlet é usado para atualizar uma política existente. No exemplo abaixo, o tempo de vida do grupo na política existente é alterado de 365 dias para 180 dias. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Adicione grupos específicos à política Add-AzureADMSLifecyclePolicyGroup: Esse cmdlet adiciona um grupo à política de ciclo de vida. Por exemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Remova a política existente Remove-AzureADMSGroupLifecyclePolicy: Esse cmdlet exclui as configurações de expiração do grupo do Office 365, mas exige a ID da política. Isso desativará a expiração para os grupos do Office 365. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Os seguintes cmdlets podem ser usados ​​para configurar a política com mais detalhes. Para obter mais informações, consulte [documentação do PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre os grupos do Azure AD.

* [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
