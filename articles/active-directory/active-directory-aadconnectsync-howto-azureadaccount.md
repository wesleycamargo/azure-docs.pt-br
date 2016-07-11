<properties
	pageTitle="Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure AD | Microsoft Azure"
	description="Este tópico documenta como restaurar a conta de serviço do Azure AD."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054: Como redefinir a senha da conta de serviço do Conector de sincronização do Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure AD
A conta de serviço usada pelo Azure AD Connector deve ter serviço gratuito. Mas, se você precisa redefinir suas credenciais, este tópico é para você. Por exemplo, isso poderá ocorrer se um Administrador Global tiver redefinido, por engano, a senha na conta de serviço usando o PowerShell.

## Redefinir as credenciais
Se a conta de serviço definida no Azure AD Connector não puder contatar o Azure AD devido a problemas de autenticação, a senha poderá ser redefinida.

1. Entre no servidor de sincronização do Azure AD Connector e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`. ![Cmdlet addadsyncaadserviceaccount do PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de Administrador Global do Azure AD.

Esse cmdlet redefinirá a senha da conta de serviço e a atualizará no Azure AD e no mecanismo de sincronização.

## Problemas conhecidos que essas etapas podem resolver
Esta é uma lista de erros relatados por clientes, erros esses que foram corrigidos seguindo estas etapas.

-----------
Evento 6900 O servidor encontrou um erro inesperado ao processar uma notificação de alteração de senha: AADSTS70002: erro ao validar as credenciais. AADSTS50054: A senha antiga é usada para autenticação.

----------
Erro de evento 659 ao recuperar a configuração de sincronização de política de senha. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Erro ao validar as credenciais. AADSTS50054: A senha antiga é usada para autenticação.

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->