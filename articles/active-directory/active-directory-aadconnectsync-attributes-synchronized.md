<properties
	pageTitle="Sincronização do Azure AD Connect: atributos sincronizados com o Active Directory do Azure | Microsoft Azure"
	description="Lista os atributos sincronizados com o Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="markusvi;andkjell"/>


# Sincronização do Azure AD Connect: atributos sincronizados com o Active Directory do Azure

Este tópico lista os atributos que são sincronizados pela sincronização do Azure AD Connect.<br> Os atributos são agrupados pelos aplicativos relacionados do AD do Azure.


## Office 365 ProPlus

| Nome do atributo| Usuário| Comentário |
| --- | :-: | --- |
| accountEnabled| X| Define se uma conta está habilitada.|
| cn| X| |
| displayName| X| |
| objectSID| X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| pwdLastSet| X| propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| sourceAnchor| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| usageLocation| X| propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|


## Exchange Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
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
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
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
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
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
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
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
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|
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
| accountEnabled| X| | | Define se uma conta está habilitada.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| securityEnabled| | | X| Derivado de groupType|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|



## Dynamics CRM

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
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
| objectSID| X| | X| propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| securityEnabled| | | X| Derivado de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| título| X| X| | |
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|

## aplicativos de terceira parte
Este é um conjunto de atributos que podem ser usados se o diretório do AD do Azure não for usado para dar suporte ao Office 365, Dynamics ou Intune. Ele tem um pequeno conjunto de atributos principais.

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define se uma conta está habilitada.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o AD do Azure e o AD.|
| proxyAddresses| X| X| x| |
| pwdLastSet| X| | | propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela federação.|
| sn| X| X| | |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o AD do Azure.|
| usageLocation| X| | | propriedade mecânica. O país do usuário. Usado para atribuição de licença.|
| userPrincipalName| X| | | UPN é a ID de logon do usuário. Geralmente, o mesmo valor de [mail].|


## Write-back híbrido do Exchange
É feito write-back desses atributos do AD do Azure para o Active Directory local quando você optar por habilitar híbridos do Exchange. Dependendo da sua versão do Exchange, menos atributos poderão ser sincronizados.

| Nome do atributo| Usuário| Contato| Agrupar| Comentário |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObject| X| | | Derivado de cloudAnchor no AD do Azure.|
| msExchArchiveStatus| X| | | Arquivo online: permite que os clientes arquivem emails.|
| msExchBlockedSendersHash| X| | | Filtragem: faz write-back de dados de remetentes bloqueados e seguros de filtragem local e online por meio de clientes.|
| msExchSafeRecipientsHash| X| | | Filtragem: faz write-back de dados de remetentes bloqueados e seguros de filtragem local e online por meio de clientes.|
| msExchSafeSendersHash| X| | | Filtragem: faz write-back de dados de remetentes bloqueados e seguros de filtragem local e online por meio de clientes.|
| msExchUCVoiceMailSettings| X| | | Habilitar UM (Unificação de Mensagens) – Caixa postal online: usado pela integração do Microsoft Lync Server para indicar ao Lync Server local que o usuário tem caixa postal nos serviços online.|
| msExchUserHoldPolicies| X| | | Retenção de Litígio: permite que os serviços de nuvem determinem quais usuários estão em Retenção de Litígio.|
| proxyAddresses| X| X| X| Somente o endereço x500 do Exchange Online é inserido.|

## Observações sobre os atributos
- Ao usar uma ID Alternativa, o atributo local userPrincipalName será sincronizado com o atributo do AD do Azure onPremisesUserPrincipalName. O atributo de ID Alternativa, por exemplo, email, será sincronizado com o atributo do AD do Azure userPrincipalName.


## Recursos adicionais

* [Sincronização do Azure AD Connect: personalizando as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=August15_HO9-->