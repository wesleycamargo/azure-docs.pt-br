<properties
	pageTitle="Limitações e restrições do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Uma lista de limitações e restrições com o modelo de aplicativo v2.0 do AD do Azure."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: limitações e restrições

Há vários recursos e funcionalidades do modelo de aplicativo v2.0 que ainda não são compatíveis no período de visualização pública. Cada uma dessas limitações será removida antes que o modelo de aplicativo v2.0 seja disponível ao público geral, mas é preciso estar ciente delas se você estiver criando aplicativos durante a visualização pública.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Suporte para aplicativos de produção
Aplicativos que se integram ao modelo de aplicativo v2.0 não devem ser apresentados ao público como aplicativos de nível de produção. O modelo de aplicativo v2.0 está em visualização pública neste momento; alterações significativas podem ser introduzidas a qualquer momento e não há nenhum SLA garantido pelo serviço. Não haverá suporte para qualquer incidente que possa ocorrer. Se você estiver disposto a aceitar os riscos de fazer uma dependência em um serviço que ainda está em desenvolvimento, deve entrar em contato com @AzureAD para discutir o escopo de seu aplicativo ou serviço.

## Restrições em aplicativos
Os tipos de aplicativos a seguir não são compatíveis atualmente na visualização pública do modelo de aplicativo v2.0. Para obter uma descrição dos tipos de aplicativos compatíveis, consulte [este artigo](active-directory-v2-flows.md).

##### Aplicativos de página única (Javascript)
Muitos aplicativos modernos têm um front-end de Aplicativo de Página Única escrito principalmente em javascript e muitas vezes usando estruturas de SPA, como AngularJS, Ember.js, Durandal, etc. O serviço do AD do Azure disponível ao público geral é compatível com esses aplicativos usando o [Fluxo Implícito do OAuth 2.0](active-directory-v2-protocols.md#oauth2-implicit-flow); no entanto, esse fluxo ainda não está disponível no modelo de aplicativo v2.0. Em breve ele será adicionado.

Se você está ansioso para obter um SPA que funcione com o modelo de aplicativo v2.0, poderá implementar a autenticação usando o [fluxo de aplicativo Web](active-directory-v2-flows.md#web-apps). Mas essa não é a abordagem recomendada, e a documentação para esse cenário será limitada. Se você quiser ter uma ideia do cenário de SPA, é possível conferir o [exemplo de código SPA do AD do Azure disponível ao público geral](active-directory-devquickstarts-angular.md).

##### Aplicativos do lado do servidor/daemons
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs da Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade de um usuário delegado) usando o [fluxo de credenciais de cliente do OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Esse fluxo não é compatível com o modelo de aplicativo v2.0, o que equivale a dizer que os aplicativos só podem obter tokens após a ocorrência de um fluxo de entrada do usuário interativo. O fluxo de credenciais de cliente será adicionado em breve. Se você quiser ver o fluxo de credenciais de cliente no modelo de aplicativo do AD do Azure disponível ao público geral, confira o [Exemplo de daemon no GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### APIs de Web encadeadas (em nome de)
Muitas arquiteturas incluem uma API da Web que precisa chamar outra API da Web downstream, ambas protegidas pelo modelo de aplicativo v2.0. Este cenário é comum em clientes nativos que têm um back-end de API da Web que, por sua vez, chama um serviço Microsoft Online, como o Office 365 ou o Graph API.

Este cenário de API da Web encadeado pode ter suporte com o uso da concessão OAuth 2.0 Jwt Bearer Credential, também conhecida como [Fluxo Em Nome De](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). No entanto, o fluxo Em Nome De não está implementado atualmente na visualização do modelo de aplicativo v2.0. Para ver como esse fluxo funciona no serviço do AD do Azure disponível ao público geral, confira o [exemplo de código Em Nome De no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### APIs da Web autônomas
Na visualização do modelo de aplicativo v2.0, você tem a capacidade de [criar uma API da Web que é protegida usando tokens OAuth](active-directory-v2-flows.md#web-apis) a partir do ponto de extremidade v2.0. No entanto, essa API da Web só poderá receber tokens de um cliente que compartilha a mesma ID de aplicativo. A criação de um serviço da Web de terceiros que é acessado de vários clientes diferentes não é compatível.

Para ver como criar uma API da Web que aceita tokens de um cliente conhecido com a mesma Id de aplicativo, consulte os exemplos de API da Web do modelo de aplicativo v2.0 em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

## Restrições em usuários
Atualmente, todos os aplicativos criados com o modelo de aplicativo v2.0 serão expostos publicamente a todos os usuários com uma Conta da Microsoft ou uma conta do AD do Azure. Qualquer usuário com qualquer tipo de conta poderá navegar com êxito até o aplicativo ou instalá-lo, inserir as credenciais no modelo de aplicativo v2.0 e concordar com as permissões do seu aplicativo. É possível escrever o código do aplicativo para rejeitar entradas de determinados conjuntos de usuários, mas isso não impedirá que eles consigam concordar com o aplicativo.

Na verdade, seus aplicativos não podem restringir os tipos de usuários que podem entrar no aplicativo. Não será possível criar aplicativos de Linha de Negócios (restringidos a usuários em uma organização), aplicativos disponíveis apenas para usuários corporativos (com uma conta do AD do Azure) ou aplicativos disponíveis apenas aos consumidores (com uma Conta da Microsoft).

## Restrições em registros de aplicativos
Neste momento, todos os aplicativos que desejam integrar-se com o modelo de aplicativo v2.0 devem criar um novo registro de aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Nenhum aplicativo existente da Conta da Microsoft ou do AD do Azure será compatível com o modelo de aplicativo v2.0, nem os aplicativos serão registrados em nenhum portal além do novo Portal de Registro de Aplicativo. Não há um caminho de migração para um aplicativo a partir do serviço do AD do Azure disponível ao público geral para o modelo de aplicativo v2.0.

Da mesma forma, os aplicativos registrados no novo Portal de Registro de Aplicativo funcionarão exclusivamente com o modelo de aplicativo v2.0. Não é possível usar o Portal de Registro de Aplicativo para criar aplicativos que vão se integrar com êxito aos serviços de Conta da Microsoft ou do AD do Azure.

Aplicativos que são registrados no novo Portal de Registro de Aplicativo são atualmente restritos a um conjunto limitado de valores redirect\_uri. O redirect\_uri para serviços e aplicativos Web deve começar com o esquema ou `https`, enquanto o redirect\_uri para todas as outras plataformas deve usar o valor embutido em código de `urn:ietf:oauth:2.0:oob`.

Para saber como registrar um aplicativo no novo Portal de Registro de Aplicativo, consulte [este artigo](active-directory-v2-app-registration.md).

## Restrições em serviços e APIs
O modelo de aplicativo v2.0 atualmente é compatível com entrada para qualquer aplicativo registrado no novo Portal de Registro de Aplicativo, desde que ele se enquadre na lista de [fluxos de autenticação compatíveis](active-directory-v2-flows.md). No entanto, esses aplicativos só poderão adquirir tokens de acesso do OAuth 2.0 para um conjunto muito limitado de recursos. O ponto de extremidade v2.0 emitirá somente access\_tokens para:

- O aplicativo que solicitou o token. Um aplicativo pode adquirir um access\_token para si mesmo, se o aplicativo lógico é constituído de várias camadas ou componentes diferentes. Para ver esse cenário em ação, confira nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
- As APIs REST do Outlook Mail, Calendar e Contacts, que estão localizadas em https://outlook.office.com. Para saber como escrever um aplicativo que acessa essas APIs, consulte estes tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

Mais serviços Microsoft Online serão adicionados em breve, bem como suporte para seus próprios serviços e APIs da Web.

## Restrições em bibliotecas e SDKs
Nem todas as linguagens e plataformas têm bibliotecas compatíveis com a visualização do modelo de aplicativo v2.0. O conjunto de bibliotecas de autenticação atualmente é limitado para .NET, iOS, Android, NodeJS e Javascript. Tutoriais e exemplos de código correspondentes para cada um estão disponíveis em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

Se você deseja integrar um aplicativo com o modelo de aplicativo v2.0 usando outra linguagem ou plataforma, consulte a [Referência de Protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md), que instruirá sobre como construir as mensagens HTTP necessárias para se comunicar com o ponto de extremidade v2.0.

## Restrições em protocolos
O modelo de aplicativo v2.0 é compatível com Open ID Connect e OAuth 2.0. No entanto, nem todos os recursos e capacidades de cada protocolo foram incorporados ao modelo de aplicativo v2.0. Alguns exemplos incluem:

- Suporte total para o parâmetro `prompt` do OpenID Connect
- O parâmetro `login_hint` do OpenID Connect
- O parâmetro `domain_hint` do OpenID Connect
- O `end_sesssion_endpoint` do OpenID Connect

Para entender melhor o escopo da funcionalidade de protocolo compatível no modelo de aplicativo v2.0, leia nossa [Referência de Protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

<!---HONumber=Sept15_HO3-->