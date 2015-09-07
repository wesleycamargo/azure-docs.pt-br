<properties 
	pageTitle="Azure AD Connect - requisitos de certificado SSL"
	description="Requisitos de certificado SSL do Azure AD Connect para uso com o AD FS."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure AD Connect - requisitos de certificado SSL

**Importante:** é altamente recomendável usar o mesmo certificado SSL em todos os nós do farm do AD FS, bem como em todos os servidores proxy de aplicativo Web.

- Esse certificado deve ser um certificado X509. 
- Você pode usar um certificado autoassinado em servidores de federação em um ambiente de laboratório de teste. No entanto, para um ambiente de produção, recomendamos que você obtenha o certificado de uma CA pública. 
	- Se usar um certificado que não é confiável publicamente, verifique se o certificado instalado em cada servidor proxy de aplicativo Web é confiável no servidor local e em todos os servidores de federação 
- Não há suporte para certificados com base em chaves CNG (CryptoAPI de próxima geração) e provedores de armazenamento de chaves. Isso significa que você deve usar um certificado baseado em um CSP (provedor de serviços de criptografia) e não um KSP (provedor de armazenamento de chaves). 
- A identidade do certificado deve corresponder ao nome do serviço de federação (por exemplo, fs.contoso.com). 
	- A identidade é uma extensão SAN (nome alternativo da entidade) do tipo dNSName ou, se não houver entradas de SAN, o nome de entidade especificado como um nome comum.  
	- Várias entradas de SAN podem estar presentes no certificado, desde que uma delas coincide com o nome do serviço de federação. 
	- Se você planeja usar o Ingresso no Local de Trabalho, um SAN adicional é necessário com o valor "enterpriseregistration" seguido do sufixo de nome UPN de sua organização, por exemplo, enterpriseregistration.contoso.com. 

Há suporte para certificados curinga.
 

<!---HONumber=August15_HO9-->