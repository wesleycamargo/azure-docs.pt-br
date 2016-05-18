<properties 
	pageTitle="Introdução a um Provedor de Microsoft Azure Multi-Factor Authentication" 
	description="Saiba como criar um Provedor de Autenticação Multifator do Azure." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="billmath"/>



# Introdução a um Provedor de Autenticação Multifator do Azure
A autenticação multifator está disponível por padrão para administradores globais que tenham usuários do Azure Active Directory e do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](multi-factor-authentication-whats-next.md), então, deverá adquirir a versão completa do Azure MFA.

> [AZURE.NOTE]  Um Provedor do Azure Multi-Factor Auth é usado para aproveitar as vantagens dos recursos fornecidos pela versão completa do Azure MFA. É para os usuários que **não têm licenças por meio do Azure MFA, Azure AD Premium ou EMS**. O Azure MFA, o Azure AD Premium e o EMS incluem a versão completa do Azure MFA por padrão. Se você tiver licenças, não precisará de um Provedor do Azure Multi-Factor Auth.
 
Use as etapas a seguir para criar um Provedor de Autenticação Multifator do Azure.

## Para criar um Provedor do Multi-Factor Auth
--------------------------------------------------------------------------------

1. Faça logon no **portal clássico do Azure** como Administrador.
2. Selecione **Active Directory** à esquerda.
3. Na página do Active Directory, na parte superior, selecione **Provedores de Autenticação Multifator**. ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Na parte inferior, clique em **Novo**. ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Em **Serviços de Aplicativo**, selecione **Provedor de Autenticação Multifator** ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selecione **Criação Rápida**. ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Preencha os seguintes campos e selecione **Criar**.
	1. **Nome** – o nome do Provedor de Autenticação Multifator.
	2. **Modelo de Uso** – o modelo de uso do Provedor de Autenticação Multifator.
		- Por Autenticação: modelo de compra que cobra por autenticação. Normalmente usado para cenários que usam o Azure Multi-Factor Authentication em um aplicativo voltado para o consumidor.
		- Por Usuário Habilitado: o modelo de compra que cobra por usuário habilitado. Normalmente usado para acesso de funcionários a aplicativos como o Office 365.
	2. **Diretório** – o locatário do Azure Active Directory ao qual o Provedor de Autenticação Multifator está associado. Esteja ciente do seguinte:
		- Não é necessário um diretório do AD do Azure para criar um Provedor do Multi-Factor Auth. Caso planeje usar somente o SDK ou o Servidor Azure Multi-Factor Authentication, basta deixar a caixa desmarcada.
		- O Provedor do Multi-Factor Auth precisa estar associado a um diretório do Azure AD para aproveitar os recursos avançados.
		- O Azure AD Connect, o AAD Sync ou o DirSync são apenas um requisito se você estiver sincronizando o ambiente do Active Directory local com um diretório do AD do Azure. Se você usa apenas um diretório do AD do Azure que não está sincronizado, então ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)	
5. Depois que você clicar em criar, o Provedor de Autenticação Multifator será criado e você deverá ver uma mensagem informando: **Provedor de Autenticação Multifator criado com êxito**. Clique em **Ok**. ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)	

<!---HONumber=AcomDC_0511_2016-->