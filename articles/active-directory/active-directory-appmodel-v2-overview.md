<properties
	pageTitle="Visão geral do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Uma introdução à criação de aplicativos com conexão à conta da Microsoft e ao Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Conecte-se os usuários da Conta da Microsoft e do Azure AD em um único aplicativo

Antigamente, um desenvolvedor de aplicativo que desejava oferecer suporte a contas da Microsoft e ao Active Directory do Azure tinha que se integrar a dois sistemas distintos. Agora apresentamos uma nova versão da API de autenticação que permite aos usuários se conectarem com os dois tipos de contas usando o sistema do Azure AD. Esse sistema de autenticação convergida é conhecido como **o ponto de extremidade v2.0**. Com o ponto de extremidade v2.0, uma simples integração permite alcançar um público que se estende por milhões de usuários com contas pessoais e corporativas/de estudante.

Aplicativos que usam o ponto de extremidade v2.0 também podem consumir APIs REST do [Microsoft Graph](https://graph.microsoft.io) e do [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) usando qualquer tipo de conta.

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).


## Introdução
Escolha sua plataforma favorita abaixo para compilar um aplicativo usando nossas bibliotecas e estruturas de software livre. Como alternativa, você pode usar nossa documentação do protocolo do OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem usar uma biblioteca de autenticação.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## O que há de novo	
As informações conceituais aqui serão úteis para entender o que é possível ou não com o ponto de extremidade v2.0.

- Se você criou um aplicativo durante o período de visualização do ponto de extremidade v2.0 2015, [leia sobre essas significativas alterações de protocolo](active-directory-v2-preview-oidc-changes.md) recentes.
- Saiba mais sobre os [tipos de aplicativos que é possível compilar com um ponto de extremidade v2.0](active-directory-v2-flows.md).
- Para desenvolvedores familiarizados com o Azure Active Directory, você deve verificar as [atualizações em nossos protocolos e as diferenças do ponto de extremidade v2.0](active-directory-v2-compare.md).
- Compreenda as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0.

## Referência
Estes links serão úteis na exploração em profundidade da plataforma:

- Obtenha ajuda sobre o Estouro de Pilha usando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- [Referência do Protocolo v2.0](active-directory-v2-protocols.md)
- [Referência do Token v2.0](active-directory-v2-tokens.md)
- [Escopos e Consentimento no ponto de extremidade v2.0](active-directory-v2-scopes.md)
- [O Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com)
- [Referência à API REST do Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [O Microsoft Graph](https://graph.microsoft.io)
- Abaixo estão listados os exemplos e bibliotecas de clientes de software livre que foram testados com o ponto de extremidade v2.0. Observe que recursos como o [Registro de Cliente Dinâmico do OpenID Connect](https://openid.net/specs/openid-connect-registration-1_0.html) e pontos de extremidade de validação de token ainda não têm suporte e talvez precisem ser desabilitados na biblioteca para funcionar com o ponto de extremidade v2:  

  - [Servidor de Identidade Java WSO2](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Cliente Básico do OpenID Connect PHP](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Exemplo de OpenID Connect Android](https://github.com/learning-layers/android-openid-connect)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0224_2016-->