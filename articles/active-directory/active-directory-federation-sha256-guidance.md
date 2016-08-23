<properties
	pageTitle="Alterar o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável O365 | Microsoft Azure"
	description="Esta página fornece diretrizes para alterar o algoritmo SHA para confiança de federação com o O365"
    keywords="SHA1, SHA256, O365, federação, aadconnect, adfs, ad fs, alterar o sha, confiança de federação, objeto de confiança de terceira parte confiável"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#Alterar o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável O365

##Visão geral

O AD FS assina seus tokens no Azure Active Directory para garantir que eles não poderão ser violados. Essa assinatura pode ser baseada em SHA1 ou em SHA256. O Microsoft Azure Active Directory agora dá suporte para tokens assinados com um algoritmo SHA256 e recomenda configurar o algoritmo de assinatura do token para SHA256 no nível mais alto de segurança. Este artigo fornece as etapas para definir o algoritmo de assinatura do token no nível de SHA256 mais seguro.

##Alteração do algoritmo de assinatura do token

Depois de ter configurado o algoritmo de assinatura conforme indicado nas etapas a seguir, o AD FS começará a assinar os tokens para o objeto de confiança de terceira parte confiável do O365 com o SHA-256. Não há alterações de configuração adicionais necessárias e fazer esta alteração não causará impacto sobre os usuários finais que acessarem o Office 365 ou outros aplicativos do Azure AD.

###Uso do console de gerenciamento

* Abra o console de gerenciamento do AD FS no servidor primário do AD FS.
* Expanda o nó do AD FS e clique nos objetos de confiança de terceira parte confiável.
* Clique com o botão direito do mouse no O365/objeto de confiança de terceira parte confiável e selecione Propriedades.
* Selecione a guia Avançado e o Secure Hash Algorithm como SHA256
* Clique em OK

![Algoritmo de assinatura SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###Uso de cmdlets do PowerShell do AD FS

* Em qualquer servidor do AD FS, abra o PowerShell com privilégios de administrador
* Defina o Secure Hash Algorithm usando o cmdlet Set-AdfsRelyingPartyTrust

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##Leia também

* [Repair O365 trust with AAD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust) (Restaurar a confiança do O365 com o AAD Connect)

<!---HONumber=AcomDC_0810_2016-->