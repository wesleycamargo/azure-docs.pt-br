<properties
	pageTitle="Visualização do Active Directory B2C do Azure: visão geral | Microsoft Azure"
	description="Desenvolvendo aplicativos voltados para o consumidor com o Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/27/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: inscrever e conectar consumidores em seus aplicativos

O Active Directory B2C do Azure é uma solução de gerenciamento de identidade na nuvem abrangente para os seus aplicativos móveis e Web voltados para o consumidor. É um serviço global altamente disponível que se adapta a centenas de milhões de identidades do consumidor. Com base em uma plataforma segura de grau empresarial, o Active Directory B2C do Azure mantém seus aplicativos, sua empresa e seus consumidores protegidos.

No passado, os desenvolvedores de aplicativos que desejavam inscrever e conectar consumidores a seus aplicativos teriam escrito o seu próprio código. E eles teriam usado sistemas ou bancos de dados locais para armazenar nomes de usuário e senhas. O Active Directory B2C do Azure oferece aos desenvolvedores uma maneira melhor de integrar o gerenciamento de identidades do consumidor em seus aplicativos com a ajuda de uma plataforma segura baseada em padrões e um conjunto avançado de políticas extensíveis. Quando você usar o Active Directory B2C do Azure, os consumidores poderão se inscrever nos seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (o endereço de email e a senha ou o nome de usuário e a senha); as últimas são chamadas de "contas locais".

O Active Directory B2C do Azure está no modo visualização. Durante esse período, estamos ansiosos para saber sua opinião e experiência enquanto experimenta o produto. Com base nesses comentários, podemos fazer mudanças inovadoras com o intuito de aprimorar o serviço. Você não deve lançar um aplicativo de produção que use a visualização durante esse período. Envie-nos suas ideias usando a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Introdução

Para criar um aplicativo que aceite a inscrição e a conexão do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Active Directory B2C do Azure. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do AD B2C do Azure](active-directory-b2c-get-started.md).

Você pode gravar seu aplicativo em relação ao serviço Active Directory B2C do Azure optando por enviar mensagens de protocolo diretamente, usando o [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) ou usando as nossas bibliotecas para fazer o trabalho para você. Escolha sua plataforma favorita na tabela a seguir e comece a usá-la.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Novidades

Volte aqui frequentemente para saber mais sobre as alterações que virão na visualização do Active Directory B2C do Azure. Também vamos tweetar todas as atualizações usando @AzureAD.

- Saiba mais sobre nossa [estrutura de política extensível](active-directory-b2c-reference-policies.md) e sobre os tipos de políticas que você pode criar e usar em seus aplicativos.
- [Limitações e restrições atuais da visualização](active-directory-b2c-limitations.md).

## Artigos de instruções

Aprenda a usar os recursos específicos da visualização do Active Directory B2C do Azure:

- Configure contas do [Facebook](active-directory-b2c-setup-fb-app.md), do [Google+](active-directory-b2c-setup-goog-app.md), da [Microsoft](active-directory-b2c-setup-msa-app.md), da [Amazon](active-directory-b2c-setup-amzn-app.md) e do [LinkedIn](active-directory-b2c-setup-li-app.md) para usá-las em seus aplicativos voltados para o consumidor.
- [Usar atributos personalizados para coletar informações sobre seus consumidores](active-directory-b2c-reference-custom-attr.md).
- [Habilite o Azure Multi-Factor Authentication em seus aplicativos voltados para o consumidor](active-directory-b2c-reference-mfa.md).
- [Configure a redefinição de senha de autoatendimento para seus consumidores](active-directory-b2c-reference-sspr.md).
- [Personalize a aparência das páginas de inscrição, de entrada e outras voltadas ao consumidor](active-directory-b2c-reference-ui-customization.md) fornecidas pelo Active Directory B2C do Azure.
- [Use a Graph API do Active Directory do Azure para criar, ler, atualizar e excluir programaticamente os consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) em seu locatário do Active Directory B2C do Azure.

## Próximas etapas

Estes links serão úteis na exploração em profundidade do serviço:

- Veja as [informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtenha ajuda sobre o Estouro de Pilha usando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- Envie para nós sua opinião sobre a versão de visualização usando a [Voz do Usuário](https://feedback.azure.com/forums/169401-azure-active-directory/); nós queremos ouvi-la! Use a frase "AzureADB2C:" no título da sua publicação para que possamos encontrá-la.
- O Active Directory B2C do Azure dá suporte a protocolos padrão da indústria, o OpenID Connect e o OAuth 2.0, usando um modelo de registro de aplicativo que chamamos de "Modelo de Aplicativo v 2.0".
  - [Referência ao protocolo do modelo aplicativo v 2.0](active-directory-b2c-reference-protocols.md)
  - [Referência ao token do modelo de aplicativo v2.0](active-directory-b2c-reference-tokens.md)
- Leia as [Perguntas frequentes do Active Directory B2C do Azure](active-directory-b2c-faqs.md).
- [Solicitações de suporte a arquivos para o Active Directory B2C do Azure](active-directory-b2c-support.md).

## Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

<!---HONumber=AcomDC_0629_2016-->