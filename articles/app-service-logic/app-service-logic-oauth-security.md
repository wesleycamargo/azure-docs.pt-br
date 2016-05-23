<properties
	pageTitle="Segurança OAuth em conectores de SaaS e aplicativos de API| Azure"
	description="Leia sobre a segurança OAuth nos conectores e aplicativos de API no Serviço de Aplicativo do Azure; arquitetura de microsserviços; saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="mandia"/>


# Saiba mais sobre a segurança OAuth em conectores de SaaS

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

Muitos dos conectores de SaaS (software como serviço), como Facebook, Twitter, DropBox e assim por diante, exigem que os usuários autentiquem usando o protocolo OAUTH. Ao usar esses conectores de SaaS por meio de Aplicativos Lógicos, fornecemos uma experiência do usuário simplificada, na qual você clica em "Autorizar" no designer de Aplicativos Lógicos. Ao **Autorizar**, você será solicitado a se conectar (se já não o tiver feito) e a fornecer consentimento para se conectar ao serviço SaaS em seu nome. Após seu consentimento e autorização, seus Aplicativos Lógicos poderão acessar esses serviços SaaS.

## Criar seu próprio aplicativo de SaaS
Essa experiência simplificada é possível porque criamos e registramos anteriormente o aplicativo nesses serviços SaaS. Em alguns casos, você pode registrar e usar seu próprio aplicativo. Isso é necessário, por exemplo, quando você quer usar esses conectores de SaaS em seus aplicativos personalizados. Este exemplo usa o Conector do DropBox, mas o processo é o mesmo para todos os conectores que dependem de OAUTH.

Até mesmo no contexto de Aplicativos Lógicos, você pode usar seu próprio aplicativo em vez do aplicativo padrão que fornecemos. Se ocorrer falha ao se conectar usando o botão "Autorizar", você pode tentar criar seu próprio aplicativo. As etapas usadas para o conector do Twitter são listadas abaixo:

1. Abra o conector do Twitter na Versão Prévia do Portal do Azure. Vá para **Procurar** > **Aplicativos de API**. Selecione o conector do Twitter: ![][1]

2. Selecione **Configurações** > **Autenticação**: ![][2]

3. Copie o valor de **URI de Redirecionamento**: ![][3]

4. Vá para [Twitter](http://apps.twitter.com) e **Criar um Novo Aplicativo**. Na propriedade **URL de Retorno de Chamada**, cole o valor de **URI de Redirecionamento** copiado do seu conector do Twitter: ![][4]
5. Quando o aplicativo do Twitter é criado, selecione **Chave e Tokens de Acesso**. Copie esses valores.
6. Nas configurações de autenticação do conector do Twitter, cole esses valores nas propriedades **ID do Cliente** e **Segredo do Cliente**: ![][5]  
7. Salve as configurações do conector.  

Agora, você pode usar o conector por meio de Aplicativos Lógicos. Ao usar o conector por meio de Aplicativos Lógicos, ele usa seu aplicativo em vez do aplicativo padrão.

> [AZURE.NOTE] Se você autorizou um aplicativo anteriormente, talvez precise autorizá-lo novamente.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=AcomDC_0511_2016-->