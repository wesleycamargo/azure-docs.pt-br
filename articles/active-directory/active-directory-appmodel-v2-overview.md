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
	ms.date="11/06/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: conectar usuários à conta da Microsoft e ao AD do Azure em um único aplicativo

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

Antigamente, um desenvolvedor de aplicativo que desejava oferecer suporte a contas da Microsoft e ao Active Directory do Azure tinha que se integrar a dois sistemas distintos. Com o modelo de aplicativo v2.0, você pode conectar usuários a ambos os tipos de conta. Uma integração simples permite atingir um público que se estende por milhões de usuários com contas pessoais e corporativas/de estudante.

Seus aplicativos também podem consumir um [conjunto de APIs REST do Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) usando qualquer tipo de conta. Atualmente, essas APIs incluem APIs de Calendários, Contatos e Email do Outlook. Os serviços adicionais serão adicionados em um futuro próximo.
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

O modelo de aplicativo v2.0 está em visualização. Durante esse período de visualização, estamos ansiosos para ouvir seus comentários e sobre sua experiência com o novo modelo de aplicativo à medida que testa os recursos. Com base nesses comentários, podemos fazer mudanças inovadoras com o intuito de aprimorar o serviço. Você não deve lançar um aplicativo de produção usando o modelo de aplicativo v2.0 durante esse período.
<!-- TODO: Get approval on how it looks  -->

## Introdução
Existem duas maneiras de colocar seu próprio aplicativo em atividade com o modelo de aplicativo v2.0. Você pode optar por enviar mensagens de protocolo diretamente usando o [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) ou [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Como alternativa, é possível usar nossas bibliotecas para fazer o trabalho para você — escolha a sua plataforma favorita abaixo e comece.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## O que há de novo
Volte aqui com frequência para saber sobre alterações futuras na visualização pública do modelo de aplicativo v2.0. Também vamos tweetar sobre as atualizações usando @AzureAD.

- Saiba mais sobre os [tipos de aplicativo que é possível criar com um modelo de aplicativo v2.0](active-directory-v2-flows.md).
- Para desenvolvedores familiarizados com o Active Directory do Azure, você deve verificar as [atualizações em nossos protocolos e as diferenças no modelo de aplicativo v2.0](active-directory-v2-compare.md).
- [Limitações e restrições atuais da visualização](active-directory-v2-limitations.md).

## Referência
Estes links serão úteis na exploração em profundidade da plataforma:

- Obtenha ajuda sobre o Estouro de Pilha usando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- Envie-nos sua opinião sobre a visualização usando [Voz do Usuário](http://feedback.azure.com/forums/169401-azure-active-directory) — queremos ouvi-la! Use a frase "AppModelv2:" no título da sua publicação para que possamos encontrá-la.
- [Referência ao protocolo do modelo aplicativo v 2.0](active-directory-v2-protocols.md)
- [Referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md)
- [Referência à API REST do Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Escopos e consentimento no ponto de extremidade v2](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=AcomDC_1217_2015-->