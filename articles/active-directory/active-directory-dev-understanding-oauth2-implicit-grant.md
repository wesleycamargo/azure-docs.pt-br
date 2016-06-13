<properties
   pageTitle="Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure Active Directory | Microsoft Azure"
   description="Saiba mais sobre a implementação do fluxo de concessão implícita OAuth2 pelo Azure Active Directory do OAuth2 e se ele é adequado para o aplicativo."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2016"
   ms.author="vittorib;bryanla"/>

# Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure AD (Active Directory)

A concessão implícita OAuth2 é famosa por ser a concessão com a lista mais longa de questões de segurança na especificação OAuth2. Apesar disso, é a abordagem implementada pelo ADAL JS e a que recomendamos ao se escrever aplicativos SPA. O que acontece? É uma questão de compensação: de fato, a concessão implícita é a melhor abordagem que você pode adotar para aplicativos que consomem uma API Web via JavaScript em um navegador.

## O que é a concessão implícita OAuth2?

A [concessão de código de autorização OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) por excelência é a concessão de autorização que usa dois pontos de extremidade separados. O ponto de extremidade de autorização é usado para a fase de interação do usuário, que resulta em um código de autorização. O ponto de extremidade de token é usado então pelo cliente para trocar o código por um token de acesso e, muitas vezes, um token de atualização também. Os aplicativos Web devem apresentar suas próprias credenciais de aplicativo ao ponto de extremidade de token, para que o servidor de autorização possa autenticar o cliente.

A [concessão implícita OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) é definida como uma variante em que um cliente pode obter um token de acesso (e, no caso de [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html), um id\_token) diretamente do ponto de extremidade de autorização, sem a necessidade de contatar o ponto de extremidade de token nem autenticar o aplicativo cliente. Essa variante foi projetada especificamente para aplicativos baseados em JavaScript em execução em um navegador da Web: na especificação original OAuth2, os tokens são retornados em um fragmento de URI. Isso disponibiliza os bits de token para o código JavaScript no cliente, mas garante que eles não sejam incluídos em redirecionamentos para o servidor. Retornar tokens por meio de redirecionamentos de navegador diretamente de pontos de extremidade de autorização também tem a vantagem de eliminar requisitos de chamadas entre origens, o que seria necessário se o aplicativo JavaScript precisasse contatar o ponto de extremidade de token.

Uma característica importante da concessão implícita OAuth2 é o fato de que esses fluxos nunca retornam tokens de atualização ao cliente. Como veremos na próxima seção, isso não é realmente necessário e, de fato, seria um problema de segurança.

## Cenários adequados para a concessão implícita OAuth2

Como a própria especificação OAuth2 declara, a concessão implícita foi concebida para habilitar aplicativos de agente do usuário, ou seja, aplicativos JavaScript em execução em um navegador. A característica que define esses aplicativos é que o código JavaScript é usado para acessar recursos de servidor (normalmente, uma API Web) e para atualizar a experiência do usuário do aplicativo UX da maneira adequada. Pense em aplicativos como Gmail ou Outlook Web Access: quando você seleciona uma mensagem da caixa de entrada, apenas o painel de visualização da mensagem muda para exibir a nova seleção, enquanto o restante da página permanece inalterado. Isso difere de aplicativos Web tradicionais baseados em redirecionamento, em que cada interação do usuário resulta em um postback de página inteira e em uma renderização de página inteira da nova resposta do servidor.

Os aplicativos que usam a abordagem baseada em JavaScript ao extremo são chamados de Aplicativos de Página Única, ou SPAs: a ideia é que esses aplicativos fornecem apenas uma página HTML inicial e o JavaScript associado, com todas as interações subsequentes conduzidas por chamadas à API Web realizadas por meio de JavaScript. No entanto, abordagens híbridas, em que o aplicativo é principalmente orientado por postback, mas executa chamadas JS ocasionais para implementar algumas experiências, não são incomuns. A discussão sobre o uso de fluxo implícitos também é relevante para elas.

Os aplicativos baseados em redirecionamento normalmente protegem suas solicitações por meio de cookies. No entanto, essa abordagem não funciona tão bem para aplicativos JavaScript, já que os cookies só funcionam em relação ao domínio em que foram gerados, enquanto as chamadas JavaScript podem ser direcionadas para outros domínios. De fato, muitas vezes esse pode ser o caso: pense em aplicativos que invocam a API do Microsoft Graph, a API do Office e a API do Azure. Todos eles residem fora do domínio de onde o aplicativo é fornecido. Uma tendência crescente para aplicativos JavaScript é não ter um back-end, recorrendo totalmente a APIs Web de terceiros para implementar sua função de negócios.

Até agora, o método preferencial para proteger chamadas à API Web é usar a abordagem de token de portador OAuth2, em que cada chamada é acompanhada por um token de acesso OAuth2. A API Web examina o token de acesso de entrada e, se encontrar nele os escopos necessários, concederá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para que aplicativos JavaScript obtenham tokens de acesso para uma API Web, com diversas vantagens em relação aos cookies:

- Os tokens podem ser obtidos de forma confiável sem a necessidade de chamadas entre origens; o registro obrigatório do URI de redirecionamento ao qual os tokens são retornados garante que os tokens não sejam substituídos
- Aplicativos JavaScript podem obter tantos tokens de acesso quantos forem necessários, para todas as APIs Web que direcionam, sem restrição quanto aos domínios
- Recursos de HTML5, como armazenamento local ou por sessão, concedem controle total sobre o cache de tokens e o gerenciamento de tempo de vida, enquanto o gerenciamento de cookies é opaco para o aplicativo
- Tokens de acesso não são suscetíveis a ataques de CSRF (solicitação intersite forjada)

O fluxo de concessão implícita não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não tem escopo tão restrito quanto tokens de acesso, concedendo muito mais capacidade e, assim, causando muito mais danos caso seja vazado. No fluxo de implícita, os tokens são fornecidos na URL, por isso, o risco de interceptação é maior do que na concessão de código de autorização.

No entanto, observe que um aplicativo JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem solicitar repetidamente as credenciais ao usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token em relação ao ponto de extremidade de autorização do Azure AD: enquanto o navegador ainda tiver uma sessão ativa (ou seja, tiver um cookie de sessão) em relação ao domínio do Azure AD, a solicitação de autenticação poderá ocorrer com êxito sem necessidade de interação do usuário.

Esse modelo concede ao aplicativo JavaScript a capacidade de renovar independentemente tokens de acesso e até mesmo adquirir novos para uma nova API (desde que o usuário os tenha consentido anteriormente), sem a sobrecarga adicional de adquirir, manter e proteger um artefato de alto valor, como um token de atualização. O artefato que possibilita a renovação silenciosa, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem: quando um usuário sai do Azure AD de qualquer um dos aplicativos usando o Azure AD em execução em uma das guias do navegador, o que resulta na exclusão do cookie de sessão do Azure AD, o aplicativo JavaScript perde automaticamente a capacidade de renovar tokens para o usuário desconectado.

## A concessão implícita é adequada para meu aplicativo?

A concessão implícita apresenta mais riscos do que outras concessões. As áreas às quais você precisa ficar atento estão bem documentadas (por exemplo, veja [Uso indevido do token de acesso para representar o proprietário do recurso no fluxo implícito][OAuth2-Spec-Implicit-Misuse] e [Modelo de ameaça do OAuth 2.0 e considerações de segurança][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco mais alto é amplamente devido ao fato de que seu objetivo é habilitar aplicativos que executam código ativo, fornecido por um recurso remoto a um navegador. Se você está optando por uma arquitetura SPA, se não tem um componente de back-end ou, em geral, se pretende invocar uma API Web via JavaScript, o uso do fluxo implícito para a aquisição de tokens é recomendado.

Se o aplicativo é um cliente nativo, o fluxo implícito não é uma boa opção. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo priva o aplicativo dos meios de manter uma sessão longa e obter tokens de acesso para novos recursos sem solicitar repetidamente ao usuário.

Se você está desenvolvendo um aplicativo Web que inclui um back-end e que se destina a consumir a API de seu código de back-end, o fluxo implícito também não é uma boa opção. Outras concessões fornecem muito mais capacidade: por exemplo, a concessão de credenciais de cliente OAuth2 permite obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em vez de delegações de usuário, a capacidade de manter o acesso programático a recursos mesmo quando um usuário não está ativamente envolvido em uma sessão e assim por diante. Além disso, essas concessões fornecem garantias de segurança mais elevadas: os tokens de acesso nunca transitam pelo navegador do usuário, não correm o risco de ser salvos no histórico do navegador etc. O aplicativo cliente pode executar autenticação forte ao solicitar um token e assim por diante.

## Próximas etapas

- Para obter uma lista completa de recursos para desenvolvedores, incluindo informações de referência do conjunto completo de protocolos e o suporte a fluxos de concessão de autorização OAuth2 pelo Azure AD, confira o [Guia do Desenvolvedor do Azure AD][AAD-Developers-Guide]
- Confira [Como integrar um aplicativo ao Azure AD][ACOM-How-To-Integrate] para obter detalhes adicionais sobre o processo de integração de aplicativos.

<!--Image references-->
[Scenario-Topology]: ./media/active-directory-devhowto-auth-using-any-aad/multi-tenant-aad-components.png

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

<!---HONumber=AcomDC_0601_2016-->