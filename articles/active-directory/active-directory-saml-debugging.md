<properties 
    pageTitle="Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure | Microsoft Azure" 
    description="Saiba como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure

Ao depurar uma integração de aplicativo baseada em SAML, geralmente é útil usar uma ferramenta como o [Fiddler](http://www.telerik.com/fiddler) para ver a solicitação SAML, a resposta SAML e o token SAML real que é emitido para o aplicativo. Examinando o token SAML, você pode garantir que todos os atributos necessários, o nome de usuário no assunto SAML e o URI do emissor estejam chegando conforme o esperado.

![][1]

A resposta do AD do Azure que contém o token SAML normalmente é aquela que ocorre depois de um redirecionamento HTTP 302 de https://login.windows.net e é enviada para a **URL de resposta** do aplicativo configurado.
 
Você pode exibir o token SAML selecionando essa linha e, em seguida, selecionando a guia **Inspetores > WebForms** no painel à direita. A partir daí, clique com botão direito no valor **SAMLResponse** e selecione **Enviar para TextWizard**. Selecione **De Base64** no menu **Transformar** para decodificar o token e ver seu conteúdo.
 
**Observação**: para ver o conteúdo dessa solicitação HTTP, o Fiddler pode solicitar que você configure a descriptografia de tráfego HTTPS, o que você precisará fazer.

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

<!---HONumber=Nov15_HO4-->