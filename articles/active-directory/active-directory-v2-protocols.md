<properties
	pageTitle="Protocolos do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Os protocolos com suporte da visualização pública do modelo de aplicativo v2.0 do AD do Azure."
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
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: protocolos — OAuth 2.0 e OpenID Connect

O modelo de aplicativo v2.0 fornece identidade como um serviço para seus aplicativos oferecendo suporte a protocolos padrão do setor, OpenID Connect e OAuth 2.0. Embora o serviço esteja em conformidade padrão, pode haver diferenças sutis entre quaisquer duas implementações desses protocolos. As informações aqui serão úteis se você optar por escrever seu código diretamente enviando e tratando solicitações HTTP, em vez de usar uma das nossas bibliotecas de software livre. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Noções básicas
Cada aplicativo que usa o modelo de aplicativo v2.0 precisará ser registrado em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). O processo de registro de aplicativo vai coletar e atribuir alguns valores para seu aplicativo:

- Uma **ID de aplicativo** que identifica exclusivamente o aplicativo
- Um **URI de Redirecionamento** ou **Identificador de Pacote** que pode ser usado para direcionar as respostas de volta ao aplicativo
- Alguns outros valores específicos de cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Depois de registrado, o aplicativo se comunica com o AD do Azure enviando solicitações ao ponto de extremidade v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Em quase todos os fluxos do OAuth e OpenID Connect, há quatro partes envolvidas na troca:

![Funções do OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- O **Servidor de Autorização** é o ponto de extremidade v2.0. Ele é responsável por garantir a identidade do usuário, conceder e revogar o acesso a recursos e emitir tokens. Ele também é conhecido como o provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
- O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados, ou recurso.
- O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva ID de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
- O **Servidor de Recurso** é o local no qual o recurso ou os dados residem. Ele confia no Servidor de Autorização para autenticar e autorizar o Cliente OAuth com segurança, além de usar access\_tokens de portador para garantir que o acesso a um recurso possa ser concedido.


## Tokens
A implementação do modelo de aplicativo v2.0 do OAuth 2.0 e OpenID Connect faz amplo uso de tokens de portador, incluindo os representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Mais detalhes sobre os diferentes tipos de token usados no modelo de aplicativo v2.0 estão disponíveis na [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md).

## Protocolos

Se você estiver pronto para ver alguns exemplos de solicitação, inicie com um dos tutoriais a seguir. Cada um corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual é o fluxo certo para você, confira os [tipos de aplicativos que você pode criar com o modelo de aplicativo v2.0](active-directory-v2-flows.md).

- [Criar aplicativos nativos e móveis com o OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Criar aplicativos Web com o Open ID Connect](active-directory-v2-protocols-oidc.md)
- [Criar Aplicativos de Página Única com o Fluxo Implícito do OAuth 2.0](active-directory-v2-protocols-implicit.md)
- Criar Daemons ou Processos Paralelos do Servidor com o Fluxo de Credenciais do Cliente OAuth 2.0 (em breve)
- Obter tokens em uma API Web com o OAuth 2.0 em nome do fluxo (em breve)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) -->
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_1125_2015-->