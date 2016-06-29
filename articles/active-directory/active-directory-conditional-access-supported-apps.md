
<properties
	pageTitle="Acesso condicional – Quais aplicativos têm suporte | Microsoft Azure"
	description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/15/2016"
	ms.author="femila"/>


# Suporte de acesso condicional para aplicativos
O acesso condicional funciona com aplicativos móveis e da área de trabalho que usam autenticação moderna. Aplicativos com autenticação moderna podem exibir a entrada do Azure AD nas páginas. Isso permite que um usuário seja solicitado de forma embutida para autenticação multifator ou mostre uma mensagem voltada para o usuário final quando o acesso é bloqueado. É importante entender quais aplicativos têm suporte, bem como as etapas que podem ser necessárias para proteger outros pontos de entrada.

## Aplicativos que usam autenticação moderna
Os aplicativos a seguir foram testados com MFA (autenticação multifator) e a política de localização definida no serviço de destino.

| Aplicativo | Serviço de Destino | Plataforma |
|--------------|-----------------|----------------------------------------------------------------|
| Outlook 2016 | Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
| Outlook 2013 (exige autenticação moderna para ser habilitado)| Exchange |Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Skype for Business (com autenticação moderna)|Exchange (o Exchange é acessado para o histórico de conversa e calendário)| Windows 10, Windows 8.1, Windows 7 |
|Aplicativo móvel do Outlook|Exchange| iOS e Android |
|Office 2016; Word, Excel, Sharepoint|SharePoint| Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
|Office 2013 (exige autenticação moderna para ser habilitado)|SharePoint|Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Aplicativo Dynamics CRM|Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Aplicativo Yammer|Yammer| Windows Mobile 10, iOS, Android|
|Aplicativo Remoto do Azure|Serviço de Aplicativo Remoto do Azure|Windows 10, Windows 8.1, Windows 7, Mac, iOS, Android|

## Aplicativos que não usam autenticação moderna

Atualmente, os aplicativos que não usam autenticação moderna devem ter acesso bloqueado ao usar outros métodos, pois eles não são impostos por acesso condicional. Isso é basicamente uma consideração para acesso ao Exchange e SharePoint, pois versões anteriores do aplicativo foram compiladas usando protocolos mais antigos.

## SharePoint
Protocolos herdados podem ser desabilitados no SharePoint, usando o cmdlet Set-SPOTenant. Esse cmdlet impedirá que os clientes do Office usando os protocolos de autenticação não modernas acessem recursos do SharePoint Online.

**Exemplo de saída de comando**: `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## Exchange

No Exchange, há duas categorias principais de protocolo. Analise e selecione a política certa para a sua organização:

1. Exchange ActiveSync. Por padrão, a política de acesso condicional para o MFA e a Localização não é imposta para o Exchange ActiveSync. Isso permite que o acesso seja protegido por meio da configuração de política do Exchange ActiveSync diretamente ou ao bloquear o Exchange ActiveSync usando regras do AD FS.
2. Protocolos herdados. Protocolos herdados podem ser bloqueados no AD FS. Isso bloqueará o acesso de clientes mais antigos do Office, como o Office 2013 sem autenticação moderna habilitada e versões anteriores.


### Regras do AD FS de exemplo
As regras a seguir podem ser usadas para bloquear o acesso de protocolo herdado no AD FS, em duas configurações comuns. Opção 1: permitir o Exchange ActiveSync e só permitir aplicativos herdados na intranet.

Ao aplicar as três regras a seguir ao Objeto de Confiança de Terceira Parte Confiável do AD FS para a Plataforma de Identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync será permitido, juntamente com o navegador e o tráfego de autenticação moderna. Aplicativos herdados serão bloqueados da extranet.

Regra 1

    `@RuleName = “Allow all intranet traffic”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Regra 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Regra 3

	@RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### Opção 2: permitir o Exchange ActiveSync e bloquear aplicativos herdados 
Ao aplicar as três regras a seguir ao Objeto de Confiança de Terceira Parte Confiável do AD FS para a Plataforma de Identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync será permitido, juntamente com o navegador e o tráfego de autenticação moderna. Aplicativos herdados serão bloqueados de qualquer localização.

Regra 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regra 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regra 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

<!---HONumber=AcomDC_0615_2016-->