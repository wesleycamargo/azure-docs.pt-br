<properties 
	pageTitle="Autenticar com o Active Directory local em seu aplicativo do Azure | Microsoft Azure" 
	description="Saiba mais sobre as opções diferentes para aplicativos de linha de negócios no Serviço de Aplicativo do Azure para autenticar com o Active Directory local" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Autenticar com o Active Directory local em seu aplicativo do Azure #

Este artigo mostra como autenticar com o Active Directory (AD) local em [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). Um aplicativo do Azure está hospedado na nuvem, mas há maneiras de autenticar usuários do AD local com segurança.

## Autenticar por meio do Azure Active Directory
Um locatário do Azure Active Directory pode ser sincronizado com o diretório com um AD local. Essa abordagem permite que os usuários do AD acessem seu Aplicativo na internet e autentiquem usando suas credenciais locais. Além disso, o Serviço de Aplicativo do Azure fornece uma [solução completa para esse método](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Com alguns cliques, você poderá habilitar a autenticação com um locatário sincronizado com diretório para seu aplicativo do Azure. Esta abordagem tem as seguintes vantagens:

-	Não exige um código de autenticação em seu aplicativo. Permitem que o Serviço de Aplicativo faça a autenticação para você, para que você possa gastar seu tempo fornecendo funcionalidade em seu aplicativo.
-	A [API do Graph do Azure AD](http://msdn.microsoft.com/library/azure/hh974476.aspx) habilita o acesso aos dados de diretório no Azure AD.
-	Fornece SSO a [todos os aplicativos com suporte do Azure Active Directory](/marketplace/active-directory/), incluindo Azure, Office 365, Dynamics CRM Online, o Microsoft Intune e milhares de aplicativos em nuvem que não são da Microsoft.
-	O Azure Active Directory oferece suporte ao controle de acesso baseado em função. Você pode usar o padrão [Authorize(Roles="X")] com alterações mínimas em seu código.

Para ver como escrever um aplicativo de linha de negócios do Azure que autentica com o Azure Active Directory, confira [Criar um aplicativo de linha de negócios do Azure com a autenticação do Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## Autenticação por meio de um STS local
Se você tiver um STS (serviço de token seguro) local como os AD FS (Serviços de Federação do Active Directory), poderá usá-lo para federar a autenticação para seu aplicativo do Azure. Essa abordagem é mais adequada quando a política da empresa proíbe o armazenamento dos dados do AD no Azure. No entanto, observe o seguinte:

-	A topologia do STS deve ser implantada no local, com sobrecarga de gerenciamento e de custo.
-	Apenas administradores do AD FS podem configurar [regras de declaração e relações de confiança de terceiras partes confiáveis](http://technet.microsoft.com/library/dd807108.aspx), o que pode limitar as opções do desenvolvedor. Por outro lado, é possível gerenciar e personalizar [declarações](http://technet.microsoft.com/library/ee913571.aspx) de acordo com o aplicativo.
-	O acesso aos dados do AD local exige uma solução separada através do firewall corporativo.

Para ver como escrever um aplicativo de linha de negócios do Azure que autentica com o STS local, consulte [Criar um aplicativo de linha de negócios do Azure com a autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md).
 

<!---HONumber=AcomDC_0831_2016-->