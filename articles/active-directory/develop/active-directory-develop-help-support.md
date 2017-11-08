---
title: "Opções de suporte e ajuda para desenvolvedores do Azure Identity | Microsoft Docs"
description: "Saiba como obter ajuda e suporte para problemas e dúvidas relacionados ao desenvolvimento ao criar aplicativos que se integram com as identidades do Microsoft Azure (Azure Active Directory e MSA)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7c382da9bd9032b30f7c620e839c41c1756ba3f6
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2017
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para desenvolvedores 

Independentemente de você estar começando a integrar com o Azure Active Directory, identidades da Microsoft ou API do Microsoft Graph, ou quando está implementando um novo recurso no aplicativo, há vezes em que você precisa ter a ajuda da comunidade ou entender as opções de suporte que tem como desenvolvedor. Este artigo ajuda a entender essas opções; abaixo, um resumo:

> [!div class="checklist"]
> * Pesquise para verificar se a pergunta para o problema não foi respondida pela comunidade ou se já existe uma documentação para o recurso que você está tentando implementar
> * Em alguns casos, você apenas deseja usar nossas ferramentas de suporte para ajudar a depurar seu problema específico
> * Se você não encontrar a resposta necessária, talvez queira fazer uma pergunta no *Stack Overflow*
> * Se você encontrar um problema em uma de nossas bibliotecas de autenticação, será gerado um problema do *GitHub*
> * Por fim, se precisar falar com alguém, talvez queira abrir uma solicitação de suporte


## <a name="search"></a>Pesquisar

Se você tiver uma pergunta relacionada ao desenvolvimento, talvez possa encontrar a resposta necessária em nossa documentação, nossos [exemplos de github](https://github.com/azure-samples) ou respostas para as perguntas do [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Pesquisa com Escopo
Para obter resultados mais rápidos, direcione sua pesquisa para o Stack Overflow, nossa documentação e nossos exemplos de código usando o seguinte em seu [mecanismo de pesquisa favorito](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
No campo *{Seus Termos de Pesquisa}* estão as palavras-chave de pesquisa.
<br/>

## <a name="use-our-development-support-tools"></a>Use nossas ferramentas de suporte de desenvolvimento

|Ferramenta  |Descrição  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Cole tokens de ID ou acesso para decodificar os nomes das declarações e valores |
|[Analisador do código de erro](https://apps.dev.microsoft.com/portal/tools/errors)| Cole um código de erro recebido durante o logon ou páginas de consentimento para ver as possíveis causas e correções |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramentas que permitem fazer solicitações e ver as respostas na API do Microsoft Graph|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Postar uma pergunta no Stack Overflow

O Stack Overflow é o canal preferido para as perguntas relacionadas ao desenvolvimento, onde os membros da comunidade e os membros da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver o problema.

Se você não encontrar uma resposta para o problema com uma pesquisa, envie uma nova pergunta para o Stack Overflow: use uma das seguintes marcações ao fazer perguntas para ajudar a comunidade a identificar e responder rapidamente:

|Componente/Área  |Marcas  |
|---------|---------|
|Biblioteca ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Biblioteca MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Middleware OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[B2B do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[API do Microsoft Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Qualquer outra área relacionada aos tópicos de autenticação ou autorização |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> As seguintes postagens do Stack Overflow contêm dicas sobre como fazer perguntas e como adicionar o código-fonte. As diretrizes a seguir podem ajudar a aumentar as chances dos membros da comunidade avaliarem e responderem rapidamente à sua pergunta:  
> - [Como fazer uma boa pergunta](https://stackoverflow.com/help/how-to-ask)
> - [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Criar um problema do GitHub

 Se você encontrar um erro ou problema relacionado às nossas bibliotecas, gere um problema em nossos repositórios GitHub. Como nossas bibliotecas são de software livre, você também pode enviar uma solicitação de pull. O artigo a seguir contém uma lista de bibliotecas e seus repositórios GitHub:

- Bibliotecas [ADAL, MSAL e middleware Owin](active-directory-authentication-libraries.md), e repositórios GitHub

<br/>

## <a name="open-a-support-request"></a>Abrir uma solicitação de suporte

Se precisar falar com alguém, poderá abrir uma solicitação de suporte. Se você for um cliente do Azure, há várias opções de suporte disponíveis. Para comparar os planos, confira [esta página](https://azure.microsoft.com/support/plans/). O suporte ao desenvolvedor também está disponível para os clientes do Azure. Para obter informações sobre como adquirir planos de suporte do Desenvolvedor, confira [esta página](https://azure.microsoft.com/support/plans/developer/).

- Se você já tiver um Plano de Suporte do Azure, [abra uma solicitação de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Se você não for um cliente do Azure, também poderá abrir uma solicitação de suporte com a Microsoft por meio do [nosso suporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Você também pode experimentar [nosso agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.

### <a name="free-chat-support-for-a-limited-time"></a>Suporte de bate-papo gratuito por um período limitado

Você também pode usar nosso suporte via chat, que é gratuito para os Parceiros da Microsoft por um período limitado. Se sua empresa não for uma Parceira da Microsoft, você poderá inscrevê-la gratuitamente e obter outros benefícios [aqui](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Depois de inscrever sua empresa, será possível iniciar a solicitação de bate-papo [aqui](https://aka.ms/devchat).