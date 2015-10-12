<properties
	pageTitle="Visualização do Azure AD B2C | Microsoft Azure"
	description="Como criar aplicativos diretamente usando os protocolos com suporte da visualização do Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Visualização do Azure AD B2C: protocolos de autenticação

O Azure AD B2C fornece identidade como um serviço para seus aplicativos oferecendo suporte a dois protocolos padrão do setor, OpenID Connect e OAuth 2.0. Embora o serviço esteja em conformidade padrão, pode haver diferenças sutis entre quaisquer duas implementações desses protocolos. As informações aqui serão úteis se você optar por gravar seu código diretamente enviando e tratando solicitações HTTP, em vez de usar uma das nossas bibliotecas de software livre. Recomendamos que você leia as breves informações nesta página antes de mergulhar nos detalhes de cada protocolo específico, mas se você já estiver familiarizado com o Azure AD B2C, poderá ir direto para os [guias de referência de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
## Noções básicas
Todos os aplicativos que usam Azure AD B2C precisam ser registrados no seu diretório B2C no [Portal do Azure](https://portal.azure.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **ID de aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** ou **Identificador de Pacote** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-b2c-app-registration.md).

Depois de registrado, o aplicativo se comunica com o Azure AD enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Em quase todos os fluxos do OAuth e OpenID Connect, há quatro partes envolvidas na troca:

![Funções do OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- O **Servidor de Autorização** é o ponto de extremidade v2.0 do Azure AD. Ele é responsável por garantir a identidade do usuário, conceder e revogar o acesso a recursos e emitir tokens. Ele também é conhecido como o provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
- O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados, ou recurso.
- O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva ID de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
- O **Servidor de Recurso** é onde o recurso ou os dados residem. Ele confia no Servidor de Autorização para autenticar e autorizar o Cliente OAuth com segurança, além de usar access\_tokens de portador para garantir que o acesso a um recurso possa ser concedido.

## Políticas

Sem dúvida, as **políticas** do Azure AD B2C são o recurso mais importante do serviço. O Azure AD B2C estende os protocolos padrão OAuth 2.0 e OpenID Connect introduzindo políticas que permitem que o Azure AD B2C realize muito mais do que simples ações de autenticação e autorização. As políticas descrevem totalmente as experiências de identidade do consumidor, como inscrição, entrada ou edição de perfil. Eles podem ser definidos em uma interface do usuário administrativa e executados usando um parâmetro de consulta especial nas solicitações de autenticação HTTP. As políticas não são um recurso padrão do OAuth 2.0 e do OpenID Connect; portanto, você deve reservar um tempo para entendê-las. Para saber mais, leia o [guia de referência de política do AD B2C do Azure](active-directory-b2c-reference-policies.md).


## Tokens
A implementação do Azure AD B2C do OAuth 2.0 e do OpenID Connect faz amplo uso de tokens de portador, incluindo os representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes sobre os diferentes tipos de tokens usados no Azure AD B2C estão disponíveis na [referência de token do Azure AD](active-directory-b2c-reference-tokens.md).

## Protocolos

Se você estiver pronto para ver alguns exemplos de solicitação, inicie com um dos tutoriais a seguir. Cada um corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual é o fluxo certo para você, confira os [tipos de aplicativos que você pode criar com o AD B2C do Azure](active-directory-b2c-apps.md).

- [Criar aplicativos nativos e móveis com o OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Criar aplicativos Web com o Open ID Connect](active-directory-b2c-reference-oidc.md)
- Criar Aplicativos de Página Única com o Fluxo Implícito do OAuth 2.0 (em breve)
- Criar Daemons ou Processos Paralelos do Servidor com o Fluxo de Credenciais do Cliente OAuth 2.0 (em breve)
- Obter tokens usando um nome de usuário e senha com o Fluxo de Credenciais de Senhas do Proprietário do Recurso OAuth 2.0 (em breve)
- Obter tokens em uma API Web com o OAuth 2.0 em nome do fluxo (em breve)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=Oct15_HO1-->