<properties
	pageTitle="Azure AD Connect Sync: atributos sincronizados com o Active Directory do Azure"
	description="Lista os atributos sincronizados com o Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: atributos sincronizados com o Active Directory do Azure

Este tópico lista os atributos que são sincronizados pelo Azure AD Connect Sync.<br> Os atributos são agrupados pelos aplicativos do AD do Azure relacionados.
 




## Office 365 ProPlus

| Nome do atributo| Usuário| Comentário |
| --- | :-: | --- |
| accountEnabled| X| Derivado de userAccountControl|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| O atributo usado para os usuários é configurado no guia de instalação.|
| usageLocation| X| msExchUsageLocation no AD DS|
| userPrincipalName| X| |


## Exchange Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Derivado de userAccountControl|
| assistente| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| informações| X| X| X| |
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| Serviço Móvel| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| O atributo usado para os usuários é configurado no guia de instalação.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | msExchUsageLocation no AD DS|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Derivado de userAccountControl|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| informações| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| Serviço Móvel| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| O atributo usado para os usuários é configurado no guia de instalação.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | msExchUsageLocation no AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Derivado de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| Serviço Móvel| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| O atributo usado para os usuários é configurado no guia de instalação.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| usageLocation| X| | | msExchUsageLocation no AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |


## Azure RMS

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
| cn| X| | X| Alias ou nome comum. Geralmente, o prefixo do valor [mail].|
| displayName| X| X| X| Uma cadeia de caracteres que representa o nome geralmente é mostrada como um nome amigável (nome sobrenome).|
| mail| X| X| X| endereço de email completo.|
| member| | | X| |
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| proxyAddresses| X| X| X| propriedade mecânica. Usado pelo AD do Azure. Contém todos os endereços de email secundários para o usuário.|
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos.|
| securityEnabled| | | X| Derivado de groupType.|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | Este UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|


## Intune

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Derivado de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Derivado de groupType|
| sourceAnchor| X| X| X| O atributo usado para os usuários é configurado no guia de instalação.|
| usageLocation| X| | | msExchUsageLocation no AD DS|
| userPrincipalName| X| | | |


## Dynamics CRM

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Derivado de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| Serviço Móvel| X| X| | |
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| O atributo usado para os usuários é configurado no guia de instalação.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| título| X| X| | |
| usageLocation| X| | | msExchUsageLocation no AD DS|
| userPrincipalName| X| | | |


## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->