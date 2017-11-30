---
title: "Migrar do Serviço de Controle de Acesso (ACS) do Azure"
description: "Opções para mover aplicativos e serviços a partir do Serviço de Controle de Acesso do Azure | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migrar do Serviço de Controle de Acesso (ACS) do Azure

O Serviço de Controle de Acesso do Microsoft Azure Active Directory (também conhecido como Serviço de Controle de Acesso ou ACS) será desativado em novembro de 2018.  Os aplicativos e serviços que utilizam atualmente o ACS precisam migrar completamente para um mecanismo de autenticação diferente antes dessa data. Este documento descreve recomendações para os clientes atuais conforme eles planejam substituir o uso do ACS. Se não estiver usando atualmente o ACS, você não precisa realizar nenhuma ação.


## <a name="brief-acs-overview"></a>Breve visão geral do ACS

O ACS é um serviço de autenticação na nuvem que fornece uma maneira fácil de autenticar e autorizar os usuários para obterem acesso a seus aplicativos e serviços web permitindo, ao mesmo tempo, que muitos recursos de autenticação e autorização sejam fatorados de seu código. Ele é usado principalmente por desenvolvedores e arquitetos de clientes .NET, aplicativos Web ASP.NET e serviços Web WCF.

Os casos de uso para ACS podem ser subdivididos em três categorias:

- Autenticação para certos serviços em nuvem da Microsoft, incluindo o Barramento de Serviço do Azure, Dynamics CRM e outros. Os aplicativos clientes podem obter certos tokens do ACS que podem ser usados para autenticar esses serviços para realizar várias ações.
- Adição da autenticação a aplicativos Web, tanto da variedade com criação personalizada, quanto da variedade predefinida (como o Sharepoint). Usando a autenticação “passiva” do ACS, os aplicativos Web podem dar suporte ao logon com contas do Google, Facebook, Yahoo, conta da Microsoft (Live ID), Azure Active Directory e ADFS.
- Proteção de serviços Web criados de forma personalizada com tokens emitidos pelo ACS. Uso da autenticação "ativa", os serviços Web podem garantir que eles só permitam o acesso de clientes conhecidos que foram autenticadas com o ACS.

Cada um desses casos de uso e as estratégias de migração recomendadas são discutidos nas seções a seguir. Na grande maioria dos casos, alterações significativas do código são necessárias para migrar serviços e aplicativos existentes para tecnologias mais novas. É recomendável que você comece a planejar e executar a migração imediatamente para evitar qualquer possibilidade de interrupções ou tempo de inatividade.

> [!WARNING]
> Na maioria dos casos, as alterações significativas do código são necessárias para migrar serviços e aplicativos existentes para tecnologias mais novas. É recomendável que você comece a planejar e executar a migração imediatamente para evitar qualquer possibilidade de interrupções ou tempo de inatividade.

Em termos de arquitetura, o ACS é totalmente composto pelos seguintes componentes:

- Um serviço de token de segurança (STS), que recebe solicitações de autenticação e emite tokens de segurança em troca.
- O portal do Azure clássico, que é usado para criar, excluir e habilitar/desabilitar namespaces do ACS.
- Um portal distinto de gerenciamento do ACS, que é usado para personalizar e configurar o comportamento de um namespace do ACS.
- Um serviço de gerenciamento, que pode ser usado para automatizar as funções dos portais.
- Um mecanismo de regra de transformação de token, que pode ser usado para definir a lógica complexa para manipular o formato de saída de tokens emitidos pelo ACS.

Para usar esses componentes, você deve criar um ou mais **namespaces** do ACS. Um namespace é uma instância dedicada do ACS com os quais seus aplicativos e serviços se comunicam; ele é isolado de todos os outros clientes de ACS que usam seus próprios namespaces. Um namespace no ACS tem uma URL dedicada, como:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Toda a comunicação com o STS e as operações de gerenciamento são feitas nesta URL, com diferentes caminhos para finalidades diferentes. Para determinar se seus aplicativos ou serviços usam o ACS, monitore o tráfego em `https://{namespace}.accesscontrol.windows.net`.  Qualquer tráfego para essa URL é o tráfego que é gerenciado pelo ACS e precisa ser interrompido.  A única exceção é qualquer tráfego para `https://accounts.accesscontrol.windows.net` - o tráfego para esta URL já é gerenciado por um serviço diferente e não é afetado pela substituição do ACS.  Você também deve lembrar de fazer logon no portal clássico do Azure e verificar todos os namespaces do ACS nas assinaturas que você possui.  Os namespaces do ACS estão listados no serviço do **Active Directory** na guia **Namespaces do Controle de Acesso**.

Para obter mais informações sobre o ACS, confira [esta documentação arquivada no MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Cronograma de desativação

A partir de novembro de 2017, todos os componentes do ACS são totalmente suportados e operacionais. A única restrição é que [novos namespaces do ACS não podem ser criados por meio do portal clássico do Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

A linha do tempo para a substituição desses componentes segue este cronograma:

- **Novembro de 2017**: a experiência de administração do Azure AD no portal clássico do Azure [está desativado](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Neste ponto, o gerenciamento de namespace para o ACS estará disponível nesta nova URL dedicada: `http://manage.windowsazure.com?restoreClassic=true`. Isso é para permitir que você visualize seus namespaces existentes, habilite/desabilite-os e exclua-os se desejar.
- **Abril de 2018**: o gerenciamento de namespace do ACS não está mais disponíveis nesta URL dedicada. Neste momento, não é possível ativar/desativar, excluir ou enumerar seus namespaces do ACS. O portal de gerenciamento do ACS, no entanto, é totalmente funcional e está localizado em `https://{namespace}.accesscontrol.windows.net`. Todos os outros componentes do ACS também continuam a operar normalmente.
- **Novembro de 2018**: todos os componentes do ACS serão desativados permanentemente. Isso inclui o portal de gerenciamento do ACS, o serviço de gerenciamento, STS e o mecanismo de regras de transformação de token. Neste momento, qualquer solicitação enviada ao ACS (localizado em `{namespace}.accesscontrol.windows.net`) falhará. Você deve ter migrado todos os aplicativos e serviços existentes para outras tecnologias bem antes disso.


## <a name="migration-strategies"></a>Estratégias de Mitigação

As seções a seguir descrevem as recomendações de alto nível para a migração do ACS para outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes de serviços em nuvem da Microsoft

Cada um dos serviços em nuvem da Microsoft que aceitam tokens emitidos pelo ACS agora dá suporte a pelo menos uma forma alternativa de autenticação. O mecanismo de autenticação correto varia para cada serviço, portanto, recomendamos consultar a documentação específica de cada serviço para obter a orientação oficial. Para sua conveniência, cada conjunto de documentação é fornecido aqui:

| O Barramento de | Diretrizes |
| ------- | -------- |
| Barramento de Serviço do Azure | [Migrar para Assinaturas de Acesso Compartilhado](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Retransmissão do Azure | [Migrar para Assinaturas de Acesso Compartilhado](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache do Azure | [Migrar para o Cache Redis do Azure](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Mercado de Dados do Azure | [Migrar para as APIs de Serviços Cognitivos](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços do BizTalk | [Migrar para os Aplicativo Lógico do Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços de Mídia do Azure | [Migrar para a Autenticação do Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Serviço de Backup do Azure | [Atualizar o Agente de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Aplicativos Web que usam autenticação passiva

Para aplicativos Web que usam o ACS para a autenticação de usuários, o ACS forneceu os seguintes recursos para desenvolvedores e arquitetos de aplicativos Web:

- Integração profunda com o Windows Identity Foundation (WIF).
- Federação com a conta do Google, Facebook, Yahoo, Microsoft (Live ID), Azure Active Directory e ADFS.
- Suporte para os seguintes protocolos de autenticação: OAuth 2.0 Draft 13, Ws-Trust e Ws-Federation.
- Suporte para os seguintes formatos de token: Token Web JSON (JWT), SAML 1.1, SAML 2.0 e Token Web Simples (SWT).
- Uma experiência de descoberta de realm inicial integrada ao WIF, que permitiu aos usuários escolher o tipo de conta que eles usam para entrar. Esta experiência era hospedada pelo aplicativo Web e totalmente personalizável.
- Transformação de token que permitiu uma personalização avançada das declarações recebidas pelo aplicativo Web do ACS, incluindo:
    - declarações de passagem de provedores de identidade
    - adicionar declarações personalizadas extras
    - lógica se-então simples para emitir declarações em certas condições

Infelizmente, não há um serviço que ofereça todos esses recursos equivalentes.  Você deve avaliar de quais recursos do ACS precisa e, em seguida, escolher entre usar o [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/) ou outro serviço de autenticação na nuvem.

#### <a name="migrating-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Um caminho a ser levado em consideração é integrar seus aplicativos e serviços diretamente com o Azure Active Directory. O Azure AD é o provedor de identidade baseado na nuvem para contas corporativas e de estudante da Microsoft. Ele é o provedor de identidade do Office 365, Azure e muito mais. Ele fornece recursos de autenticação federados similares ao ACS, mas não oferece suporte a todos os recursos do ACS. O principal exemplo é a federação com provedores de identidade de redes sociais, como o Facebook, Google e Yahoo. Se seu usuário entrar com esses tipos de credenciais, o Azure AD não é para você. Ele também não necessariamente oferece suporte aos mesmos protocolos de autenticação no ACS. Embora o ACS e o Azure AD ofereçam suporte para o OAuth, por exemplo, há diferenças sutis entre cada implementação que exigem que você modifique o código como parte da migração.

No entanto, o Azure AD oferece várias possíveis vantagens para clientes do ACS. Ele oferece suporte de forma nativa a contas corporativas e de estudante da Microsoft hospedadas na nuvem, que são normalmente usadas por clientes do ACS.  Um locatário do Azure AD também pode ser federado a uma ou mais instâncias do Active Directory no local via ADFS, permitindo que seu aplicativo autentique usuários baseados na nuvem e usuários hospedados no local.  Ele também oferece suporte ao protocolo Ws-Federation, o que o torna relativamente fácil para integrar um aplicativo Web usando o Windows Identity Foundation (WIF).

A tabela a seguir compara os recursos do ACS relevante para aplicativos Web aos disponíveis no Azure AD. Em um alto nível, o **Azure Active Directory provavelmente é a opção apropriada para a sua migração se você permitir apenas que os usuários entrem com suas contas corporativas e de estudante da Microsoft**.

| Recurso | Suporte do ACS | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de Contas** | | |
| Contas corporativas e de estudante da Microsoft | Suportado | Suportado |
| Contas do Windows Server Active Directory e ADFS | Com suporte por meio de federação com um locatário do Azure AD <br /> Com suporte via federação direta com ADFS | Com suporte apenas por meio de federação com um locatário do Azure AD | 
| Contas de outros sistemas de gerenciamento de identidade corporativa | Possível por meio de federação com um locatário do Azure AD <br />Com suporte via federação direta | Possível por meio de federação com um locatário do Azure AD |
| Contas da Microsoft para uso pessoal (Windows Live ID) | Suportado | Com suporte por meio do protocolo do OAuth v2.0 do Azure AD, mas não sobre nenhum outro protocolo. | 
| Contas do Facebook, Google, Yahoo | Suportado | Sem nenhum suporte. |
| **Protocolos e Compatibilidade do SDK** | | |
| Windows Identity Foundation (WIF) | Suportado | Com suporte, mas instruções limitadas disponíveis. |
| Ws-Federation | Suportado | Suportado |
| OAuth 2.0 | Suporte para Draft 13 | Suporte para RFC 6749, a especificação de mais moderna. |
| Ws-Trust | Suportado | Sem suporte |
| **Formatos de Token** | | |
| Tokens Web Json (JWTs) | Com suporte em versão beta | Suportado |
| Tokens SAML 1.1 | Suportado | Suportado |
| Tokens SAML 2.0 | Suportado | Suportado |
| Tokens Web Simples (SWT) | Suportado | Sem suporte |
| **Personalizações** | | |
| Interface de usuário de seleção de conta/descoberta de realm de início personalizável | Código disponível para download que pode ser incorporado a aplicativos | Sem suporte |
| Carregar certificados de autenticação de tokens personalizados | Suportado | Suportado |
| Personalizar declarações em gráficos | Declarações de entrada de passagem de provedores de identidade<br />Obter token de acesso do provedor de identidade como uma declaração<br />Emitir declarações de saída com base nos valores de declarações de entrada<br />Emitir declarações de saída com valores constantes | Não é possível passar declarações de provedores de identidade federados<br />Não pode obter um token de acesso do provedor de identidade como uma declaração<br />Não pode emitir declarações de saída com base nos valores de declarações de entrada<br />Pode emitir declarações de saída com valores constantes<br />Podem emitir declarações de saída com base nas propriedades de usuários sincronizadas ao Azure AD |
| **Automação** | | |
| Automatizar tarefas de gerenciamento/configuração | Suporte por meio do Serviço de Gerenciamento do ACS | Com suporte via Microsoft Graph e Graph API do Azure AD. |

Se você decidir que o Azure AD é o caminho apropriado para avançar para seus aplicativos e serviços, lembre-se de duas maneiras de integrar seu aplicativo com o Azure AD.

Para usar o Ws-Federation/WIF para integração com o Azure AD, recomendamos seguir [a abordagem descrita neste artigo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). O artigo refere-se à configuração do Azure AD para logon único baseado em SAML, mas serve também para a configuração do Ws-Federation. Seguir esta abordagem requer uma licença do Azure AD Premium, mas tem duas vantagens:

- Você obtém toda a flexibilidade da personalização do token do Azure AD. Isso permite personalizar as declarações emitidas pelo Azure AD para corresponder àquelas emitidas pelo ACS, especialmente incluindo o ID do usuário ou a declaração de Identificador de Nome. Você precisa garantir que os IDs de usuários emitidos pelo Azure AD correspondam àqueles emitidos pelo ACS, para que você continue a receber Identificadores de usuário consistentes para seus usuários mesmo depois de mudar as tecnologias.
- Você pode configurar um certificado de autenticação de tokens específico para seu aplicativo, cujo tempo de vida você controla.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> O uso desta abordagem requer uma licença do Azure AD Premium. Se você for um cliente do ACS e precisar de uma licença premium para configurar o logon único para um aplicativo, fale conosco e será um prazer fornecer licenças de desenvolvedor para você usar.

Uma abordagem alternativa é seguir [este código de exemplo](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), que fornece instruções um pouco diferentes sobre como configurar o Ws-Federation. Este exemplo de código não usa o WIF, mas, em vez disso, o middleware ASP.NET 4.5 OWIN. No entanto, as instruções de registro do aplicativo são válidas para aplicativos que usam o WIF e não exigem uma licença do Azure AD Premium.  Se você escolher essa abordagem, é preciso entender a [sobreposição de chave de assinatura no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Esta abordagem usa a chave de assinatura global do Azure AD para emitir tokens. Por padrão, o WIF não atualiza automaticamente as chaves de assinatura. Quando o Azure AD girar suas chaves de assinatura globais, sua implementação do WIF precisará estar preparada para aceitar as alterações.

Se você puder integrar-se ao Azure AD por meio dos protocolos do OAuth ou OpenID Connect, recomendamos que o faça.  Disponibilizamos uma ampla documentação e orientações sobre como integrar o Azure AD ao seu aplicativo Web em nosso [Guia de desenvolvedor do Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migrar para o Azure AD B2C

O outro caminho de migração a considerar é o Azure AD B2C.  O B2C é um serviço de autenticação na nuvem, semelhante ao ACS, que permite que os desenvolvedores terceirizem a sua lógica de gerenciamento de identidade e autenticação para um serviço na nuvem.  Ele é um serviço pago (com camadas gratuitas e premium) projetado para clientes com aplicativos que podem ter até milhões de usuários ativos.

Como o ACS, um dos recursos mais atrativos do B2C é que ele oferece suporte a muitos tipos diferentes de contas. Com o B2C, você pode conectar usuários a contas do Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo e muito mais. Além disso, o B2C oferece suporte a "contas locais" ou nome de usuário e senhas que os usuários criam especificamente para seu aplicativo. O Azure AD B2C também oferece uma extensibilidade avançada que você pode usar para personalizar seus fluxos de login. Entretanto, ele não oferece suporte à amplitude dos protocolos de autenticação e formatos de token que os clientes do ACS possam exigir. Ele também não pode ser usado para obter tokens e informações adicionais sobre o usuário do provedor de identidade, Microsoft ou outro.

A tabela a seguir compara os recursos do ACS relevante para aplicativos Web aos disponíveis no Azure AD B2C. Em um nível elevado, o **Azure AD B2C é provavelmente a escolha certa para a sua migração se seu aplicativo for voltado a clientes, ou se ele oferecer suporte a muitos tipos de contas.**

| Recurso | Suporte do ACS | Suporte do Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de Contas** | | |
| Contas corporativas e de estudante da Microsoft | Suportado | Com suporte via políticas personalizadas  |
| Contas do Windows Server Active Directory e ADFS | Com suporte via federação direta com ADFS | Com suporte via federação SAML usando políticas personalizadas |
| Contas de outros sistemas de gerenciamento de identidade corporativa | Com suporte via federação direta por Ws-Federation | Com suporte via federação SAML usando políticas personalizadas |
| Contas da Microsoft para uso pessoal (Windows Live ID) | Suportado | Suportado | 
| Contas do Facebook, Google, Yahoo | Suportado | Suporte nativo ao Facebook e Google, suporte para Yahoo por meio da federação do OpenID Connect usando políticas personalizadas |
| **Protocolos e Compatibilidade do SDK** | | |
| Windows Identity Foundation (WIF) | Suportado | Sem suporte. |
| Ws-Federation | Suportado | Sem suporte. |
| OAuth 2.0 | Suporte para Draft 13 | Suporte para RFC 6749, a especificação de mais moderna. |
| Ws-Trust | Suportado | Sem suporte. |
| **Formatos de Token** | | |
| Tokens Web Json (JWTs) | Com suporte em versão beta | Suportado |
| Tokens SAML 1.1 | Suportado | Sem suporte |
| Tokens SAML 2.0 | Suportado | Sem suporte |
| Tokens Web Simples (SWT) | Suportado | Sem suporte |
| **Personalizações** | | |
| Interface de usuário de seleção de conta/descoberta de realm de início personalizável | Código disponível para download que pode ser incorporado a aplicativos | Interface do usuário totalmente personalizável via CSS personalizado. |
| Carregar certificados de autenticação de tokens personalizados | Suportado | Chaves de autenticação personalizadas, não certificados, com suporte via políticas personalizadas. |
| Personalizar declarações em gráficos | Declarações de entrada de passagem de provedores de identidade<br />Obter token de acesso do provedor de identidade como uma declaração<br />Emitir declarações de saída com base nos valores de declarações de entrada<br />Emitir declarações de saída com valores constantes | Pode passar declarações de provedores de identidade. Políticas personalizadas necessárias para algumas declarações.<br />Não pode obter um token de acesso do provedor de identidade como uma declaração<br />Pode emitir declarações de saída com base nos valores de declarações de entrada via políticas personalizadas<br />Pode emitir declarações de saída com valores constantes via políticas personalizadas |
| **Automação** | | |
| Automatizar tarefas de gerenciamento/configuração | Suporte por meio do Serviço de Gerenciamento do ACS | Criação de usuários permitidos por meio da API do Graph do Azure AD. Não pode criar políticas, aplicativos ou locatários B2C programaticamente. |

Se você decidir que o Azure AD B2C é o caminho apropriado para avançar para seus aplicativos e serviços, lembre-se de começar com os seguintes recursos:

- [Documentação do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Políticas Personalizadas de Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Outras Opções de Migração

Se nem o Azure AD nem do Azure AD B2C atenderem às necessidades do seu aplicativo Web, entre em contato conosco e poderemos ajudar você a identificar o melhor caminho para seguir.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Serviços Web usando a autenticação ativa

Para serviços Web protegidos por tokens emitidos pelo ACS, o ACS forneceu os seguintes recursos:

- A capacidade de registrar uma ou mais **identidades de serviço** no seu namespace do ACS que pode ser usado para solicitar tokens.
- Suporte para os protocolos OAuth WRAP e OAuth 2.0 Draft 13 para solicitar tokens, usando os seguintes tipos de credenciais:
    - Uma senha simples criada para a identidade do serviço
    - Um SWT usando uma chave simétrica ou certificado X509
    - Um token SAML emitido por um provedor de identidades confiável (geralmente uma instância do ADFS)
- Suporte para os seguintes formatos de token: Token Web JSON (JWT), SAML 1.1, SAML 2.0 e Token Web Simples (SWT).
- Regras simples de transformação do token

As identidades de serviço no ACS são geralmente usadas para implementar uma autenticação ao estilo de servidor para servidor (S2S).  

#### <a name="migrating-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Nossa recomendação para este tipo de fluxo de autenticação é migrar para o [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). O Azure AD é o provedor de identidade baseado na nuvem para contas corporativas e de estudante da Microsoft. Ele é o provedor de identidade do Office 365, Azure e muito mais. Mas ele também pode ser usado para a autenticação de servidor para servidor usando a implementação do Azure AD da concessão de credenciais de cliente do OAuth.  A tabela a seguir compara os recursos do ACS na autenticação de servidor para servidor com aquelas disponíveis no Azure AD.

| Recurso | Suporte do ACS | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| Como registrar um serviço Web | Crie uma terceira parte confiável no portal de gerenciamento do ACS. | Crie um aplicativo Web do Azure AD no portal do Azure. |
| Como registrar um cliente | Crie uma identidade do serviço no portal de gerenciamento do ACS. | Crie outro aplicativo Web do Azure AD no portal do Azure. |
| Protocolo usado | Protocolo OAuth WRAP<br />Concessão de credenciais do cliente do OAuth 2.0 Draft 13 | Concessão de credenciais de cliente do OAuth 2.0 |
| Métodos de autenticação do cliente | Senha simples<br />SWT assinado<br />Token SAML de IDP federado | Senha simples<br />JWT assinado |
| Formatos de Token | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | Somente JWT |
| Transformação de token | Adicionar declarações personalizadas<br />Lógica de emissão se-então simples | Adicionar declarações personalizadas | 
| Automatizar tarefas de gerenciamento/configuração | Suporte por meio do Serviço de Gerenciamento do ACS | Com suporte via Microsoft Graph e Graph API do Azure AD. |

Para obter diretrizes sobre a implementação de cenários de servidor para servidor, consulte os seguintes recursos:

- [Seção de Serviço para Serviço do guia de desenvolvedor do Azure AD](https://aka.ms/aaddev).
- [Exemplo de código do Daemon usando as credenciais do cliente de senha simples](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exemplo de código do Daemon usando as credenciais do cliente de certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Outras Opções de Migração

Se o Azure AD não atender às necessidades do seu serviço Web, deixe um comentário e poderemos ajudar você a identificar o melhor plano para o seu caso específico.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Perguntas, preocupações e comentários

Entendemos que muitos clientes do ACS não conseguirão encontrar um caminho claro para fazer a migração depois de ler este artigo e muitos precisarão de auxílio ou orientações para determinar o plano adequado. Caso queira discutir suas dúvidas e cenários de migração, deixe um comentário nesta página.
