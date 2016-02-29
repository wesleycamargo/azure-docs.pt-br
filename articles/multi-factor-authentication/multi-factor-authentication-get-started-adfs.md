<properties 
	pageTitle="Introdução ao Azure Multi-Factor Authentication e aos Serviços de Federação do Active Directory" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Introdução ao Azure Multi-Factor Authentication e aos Serviços de Federação do Active Directory



<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se sua organização tiver federado seu Active Directory local com o Active Directory do Azure usando o AD FS, as duas opções a seguir para usar o Azure Multi-Factor Authentication estarão disponíveis.

- Proteger os recursos de nuvem usando o Azure Multi-Factor Authentication ou os Serviços de Federação do Active Directory 
- Proteger os recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication 

A tabela a seguir resume a experiência de autenticação entre a proteção de recursos com o Azure Multi-Factor Authentication e o AD FS

|Experiência de autenticação – Aplicativos baseados em navegador|Experiência de autenticação – Aplicativos não baseados em navegador
:------------- | :------------- | :------------- |
Protegendo os recursos do AD do Azure usando o Azure Multi-Factor Authentication |<li>O 1º fator de autenticação é executado no local usando o AD FS.</li> <li>O 2º fator é um método baseado em telefone executado com a autenticação na nuvem.</li>|Os usuários finais podem usar senhas de aplicativo para entrar.
Protegendo recursos do AD do Azure usando os Serviços de Federação do Active Directory |<li>O 1º fator da autenticação é executado no local usando o AD FS.</li><li>O 2º fator é executado no local honrando a declaração.</li>|Os usuários finais podem usar senhas de aplicativo para entrar.

Advertências sobre senhas de aplicativo para usuários federados:

- A senha de aplicativo é verificada usando a autenticação na nuvem e, portanto, ignora as federações. A federação é usada ativamente apenas ao configurar a senha de aplicativo.
- As configurações do Controle de Acesso do Cliente local não são consideradas pela senha de aplicativo.
- Você perde a capacidade de registro de autenticação local para senha de aplicativo.
- A desabilitação/exclusão da conta pode levar até 3 horas para dirsync, atrasando a desabilitação/exclusão da senha de aplicativo na identidade da nuvem.

Para obter informações sobre como configurar o Azure Multi-Factor Authentication ou o Servidor Multi-Factor Authentication com o AD FS, consulte:

- [Proteger os recursos de nuvem usando o Azure Multi-Factor Authentication e o AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com o AD FS do Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteger recursos de nuvem e locais usando o Servidor Azure Multi-Factor Authentication com AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=AcomDC_0218_2016-->