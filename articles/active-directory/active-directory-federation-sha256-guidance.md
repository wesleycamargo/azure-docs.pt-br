<properties
	pageTitle="Alterar o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável Office 365 | Microsoft Azure"
	description="Esta página fornece diretrizes para alterar o algoritmo SHA para confiança de federação com o Office 365"
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

# Alterar o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável Office 365

## Visão geral

O Azure AD FS (Serviços de Federação do Active Directory) assina seus tokens para o Microsoft Azure Active Directory para garantir que eles não possam ser violados. Essa assinatura pode ser baseada em SHA1 ou em SHA256. O Azure Active Directory agora dá suporte para tokens assinados com um algoritmo SHA256, e recomendamos configurar o algoritmo de assinatura do token para SHA256 no nível mais alto de segurança. Este artigo descreve as etapas necessárias para definir o algoritmo de assinatura de token para o nível SHA256 mais seguro.

## Alterar o algoritmo de assinatura de token

Depois que você define o algoritmo de assinatura com um dos dois processos abaixo, o AD FS assina os tokens para o objeto de confiança de terceira parte confiável do Office 365 com SHA256. Você não precisa fazer alterações de configuração adicionais, e essa alteração não tem nenhum impacto sobre a capacidade de acessar o Office 365 ou outros aplicativos do Azure AD.

### Console de gerenciamento do AD FS

1. Abra o console de gerenciamento do AD FS no servidor primário do AD FS.
2. Expanda o nó do AD FS e clique em **Objetos de Confiança de Terceira Parte Confiável **.
3. Clique com o botão direito do mouse no Office 365/objeto de confiança de terceira parte confiável e selecione **Propriedades**.
4. Selecione a guia **Avançado** e o Secure Hash Algorithm SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### Cmdlets do PowerShell do AD FS

1. Em qualquer servidor do AD FS, abra o PowerShell com privilégios de administrador.
2. Definir o algoritmo de hash seguro usando o cmdlet **Set-AdfsRelyingPartyTrust**.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## Leia também

* [Reparar confiança do Office 365 com o Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0817_2016-->