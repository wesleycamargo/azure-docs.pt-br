<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Os tipos de tokens emitidos na visualização do AD B2C do Azure."
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

# Visualização do AD B2C do Azure: Referência de Token

O AD B2C do Azure emite vários tipos de token de segurança no processamento de cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Tipos de token

O AD B2C do Azure dá suporte ao [protocolo de autorização do OAuth 2.0](active-directory-b2c-reference-protocols.md), que usa tanto access\_tokens quanto refresh\_tokens. Ele também dá suporte à autenticação e conexão por meio do [OpenID Connect](active-directory-b2c-reference-protocols.md), que introduz um terceiro tipo de token, o id\_token. Cada um desses tokens é representado como um "token de portador".

Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo AD B2C do Azure são implementados, como Tokens da Web JSON ou JWTs. Um JWT é um meio compacto e protegido por URL de transferir informações entre duas partes. As informações contidas em JWTs são conhecidas como "declarações" ou asserções de informações sobre o portador e o assunto do token. As declarações em JWTs são objetos JSON codificados e serializados para transmissão. Uma vez que os JWTs emitidos pelo AD B2C do Azure são assinados mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para fins de depuração. Há várias ferramentas disponíveis para fazer isso, como o [calebb.net](https://calebb.net). Para obter mais informações sobre JWTs, você pode consultar a [Especificação do JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Id\_Tokens

Id\_tokens são uma forma de token de segurança que seu aplicativo recebe dos pontos de extremidade `authorize` e `token` do AD B2C do Azure. Eles são representados como [JWTs](#types-of-tokens) e contêm declarações que você pode usar para identificar o usuário no seu aplicativo. Quando adquiridos do ponto de extremidade `authorize`, id\_tokens geralmente são usados para realizar a entrada do usuário em um aplicativo Web. Quando adquiridos do ponto de extremidade `token`, id\_tokens podem ser enviados em solicitações HTTP durante a comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um id\_token como julgar conveniente — geralmente elas são usadas para exibir informações de conta ou tomar decisões de controle de acesso em um aplicativo.

Atualmente, Id\_tokens são assinados, mas não criptografados. Quando seu aplicativo ou API recebe um id\_token, ele deve [validar a assinatura](#validating-tokens) para comprovar a autenticidade do token e validar algumas declarações no token para comprovar sua validade. As declarações validadas por um aplicativo variam de acordo com os requisitos do cenário, mas há algumas [validações comuns de declaração](#validating-tokens) que seu aplicativo deve executar em cada cenário.

Os detalhes completos sobre as declarações nos id\_tokens são fornecidos abaixo, bem como o exemplo de id\_token. Observe que as declarações em id\_tokens não são retornadas em uma ordem específica. Além disso, novas declarações podem ser introduzidas nos id\_tokens a qualquer momento — o aplicativo não deve ser interrompido conforme novas declarações são introduzidas. A lista abaixo inclui as declarações que o aplicativo pode interpretar confiavelmente no momento em que este documento foi escrito. Para praticar, tente inspecionar as declarações no exemplo de id\_token colando-o em [calebb.net](https://calebb.net). Se necessário, mais detalhes podem ser encontrados na [especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemplo de Id\_Token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### Declarações em Id\_Tokens

Com o AD B2C do Azure, você tem um controle refinado sobre o conteúdo de seus tokens. Você pode configurar [políticas](active-directory-b2c-reference-policies.md) para enviar um determinado conjunto de dados do usuário em declarações que seu aplicativo requer para suas operações. Essas declarações podem incluir propriedades padrão como a `displayName` e `emailAddress` do usuário, bem como [atributos de usuário personalizados](active-directory-b2c-reference-custom-attr) que você pode definir no diretório B2C. No entanto, cada id\_token que você receber conterá um determinado conjunto de declarações relacionadas à segurança que os aplicativos podem usar para autenticar os usuários e solicitações com segurança. Abaixo estão as declarações que você espera que existam em cada id\_token emitido pelo AD B2C do Azure - qualquer outra declaração será determinada pela política.

| Nome | Declaração | Exemplo de valor | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público-alvo | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Em id\_tokens, o público-alvo é a Id de Aplicativo do seu aplicativo, conforme atribuída a ele no portal de registro do aplicativo. O aplicativo deve validar esse valor e rejeitar o token, caso ele não corresponda. |
| Emissor | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token, bem como o diretório do AD do Azure no qual o usuário foi autenticado. O aplicativo deve validar a declaração do emissor para garantir que o token venha do ponto de extremidade v2.0. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Data de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Não Antes De | `nbf` | `1438535543` | O horário em que o token se torna inválido, representado no horário da época. Ele geralmente é o mesmo que o horário de emissão. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Versão | `ver` | `1.0` | A versão do id\_token, conforme definida pelo AD do Azure. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash de código é incluído em id\_tokens apenas quando estes são emitidos juntamente com um código de autorização do OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Consulte a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Hash do token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash do token de acesso é incluído em id\_tokens apenas quando estes são emitidos juntamente com um token de acesso do OAuth 2.0. Ele pode ser usado para validar a autenticidade de um token de acesso. Consulte a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Nonce | `nonce` | `12345` | O nonce é uma estratégia para migrar ataques de reprodução de token. O aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce`. O valor que você fornece na solicitação será emitido na declaração `nonce` do id\_token, sem modificação. Isso permite ao aplicativo verificar o valor em relação ao valor que ele especificou na solicitação, que associa a sessão do aplicativo a um determinado id\_token. O aplicativo deve executar essa validação durante o processo de validação do id\_token. |
| Assunto | `sub` | `Not supported currently. Use oid claim.` | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado, então ele pode ser usado para executar verificações de autorização com segurança, como por exemplo quando o token é usado para acessar um recurso. No entanto, a declaração do assunto ainda não está implementada na visualização do AD B2C do Azure. Em vez de usar a entidade de declaração de autorização, você deve configurar suas políticas para incluir a declaração `oid` da ID de Objeto e usar seu valor para identificar usuários. |
| Referência de classe de contexto de autenticação | `acr` | `b2c_1_sign_in` | O nome da política que foi usada para adquirir o id\_token. |
| Horário da Autenticação | `auth_time | `1438535543` | A hora em que o usuário inseriu suas credenciais pela última vez, no horário da época. |



## Tokens de acesso

O AD B2C do Azure não emite tokens de acesso no momento. Não há suporte para a autenticação entre duas partes diferentes nesta fase inicial da visualização - no entanto, você pode usar id\_tokens para a comunicação entre componentes do mesmo aplicativo. Para saber mais sobre os aplicativos e cenários de autenticação aos quais a visualização do público do AD B2C do Azure dá suporte, consulte [o artigo de aplicativos B2C](active-directory-b2c-apps.md).

## Tokens de atualização

Os tokens de atualização são tokens de segurança que o aplicativo pode usar para adquirir novos id\_tokens e access\_tokens em um fluxo do OAuth 2.0. Ele permite ao aplicativo obter acesso a longo prazo a recursos em nome de um usuário sem a necessidade de interação do usuário.

Para receber uma atualização em uma resposta de token, o aplicativo deve solicitar o escopo `offline_acesss`. Para saber mais sobre o escopo `offline_access`, consulte a [referência de protocolo do AD B2C do Azure](active-directory-b2c-reference-protocols.md).

Os tokens de atualização são, e sempre serão, completamente opacos para seu aplicativo. Eles são emitidos pelo AD do Azure e podem ser inspecionados e interpretados somente pelo AD do Azure. Eles têm longa duração, mas o aplicativo não deve ser escrito para esperar que um token de atualização dure por qualquer período de tempo. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao AD do Azure.

Ao resgatar um token de atualização para um novo token (e se o aplicativo tiver concedido o escopo `offline_access`), você receberá um novo token de atualização na resposta de token. É preciso salvar o token de atualização recentemente emitido, substituindo o que você usou anteriormente na solicitação. Isso garantirá que seus tokens de atualização permanecem válidos pelo máximo tempo possível.

## Validando tokens

No momento, a única validação de token que seus aplicativos devem precisar executar é a validação de id-tokens. Para validar um id\_token, o aplicativo deve validar a assinatura do id\_token e as declarações contidas nele.

<!-- TODO: Link -->
Há muitas bibliotecas de software livre para validar JWTs dependendo do seu idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação. As informações aqui serão úteis para descobrir como usar essas bibliotecas adequadamente.

#### Validando a assinatura
Um JWT contém três segmentos, que são separados pelo caractere `.`. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo** e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do id\_token, de modo que seu aplicativo possa confiar nele.

Os Id\_Tokens são assinados usando algoritmos de criptografia assimétrica padrões do setor, como RSA 256. O cabeçalho do id\_token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token, enquanto as declarações `kid` ou `x5t` indicam a chave pública particular que foi usada para assinar o token.

Em qualquer ponto no tempo, o AD do Azure pode assinar um id\_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. O AD do Azure gira o possível conjunto de chaves em intervalos periódicos, de modo que o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo AD do Azure é de aproximadamente 24 horas.

O AD B2C do Azure tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o AD B2C do Azure no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada política no seu diretório B2C. Por exemplo, o documento de metadados para a política `b2c_1_sign_in` no `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o documento de metadados do OpenID Connect localizado em:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

em que `fabrikamb2c.onmicrosoft.com` é o diretório b2c usado para autenticar o usuário e `b2c_1_sign_in` é a política usada para adquirir o id\_token. Para determinar qual política foi usada na assinatura de um id\_token (e de onde buscar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no id\_token. Você pode analisar as declarações fora do corpo do JWT decodificando em base-64 o corpo e desserializando a cadeia de caracteres JSON resultante. A declaração `acr` será o nome da política que foi usada para emitir o id\_token. A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Qualquer um dos dois métodos é perfeitamente válido.

O documento de metadados é um objeto JSON que contém várias informações úteis, como o local dos vários pontos de extremidade exigidos para execução da autenticação do OpenID Connect. Ele também inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. Esse local é fornecido abaixo, mas é melhor buscar esse local dinamicamente usando o documento de metadados e analisando o `jwks_uri`:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

O documento de JSON localizado nessa url contém todas as informações de chave pública em uso nesse momento específico. O aplicativo pode usar as declarações `kid` ou `x5t` no cabeçalho do JWT para selecionar qual chave pública neste documento foi usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

Executar a validação da assinatura está fora do escopo deste documento — há muitas bibliotecas de software livre disponíveis para ajudar você a fazer isso, caso seja necessário.

#### Validando as declarações
Quando o aplicativo ou API recebe um id\_token, ele também deve fazer algumas verificações nas declarações no id\_token. Estão incluídos:

- A declaração **Público-alvo** - para verificar se o id\_token foi destinado a ser dado ao aplicativo.
- As declarações **Não Antes De** e **Data de Expiração** - para verificar se o id\_token não expirou.
- A declaração **Emissor** - para verificar se o token foi de fato emitido para o aplicativo pelo AD do Azure.
- O **Nonce** - como uma atenuação do ataque de reprodução do token.

Os detalhes dos valores esperados para essas declarações estão incluídos acima na [seção id\_token](#id_tokens).

## Tempos de vida do token

Os tempos de vida do token a seguir são fornecidos puramente para fins de compreensão, pois eles podem ajudar no desenvolvimento e na depuração de aplicativos. Os aplicativos não devem ser escritos para esperar que algum desses tempos de vida permaneça constante; eles podem e vão mudar a qualquer momento.

| A criptografia do token | Tempo de vida | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Id\_Tokens | 1 hora | Id\_Tokens normalmente são válidos por uma hora. Seu aplicativo Web pode usar esse mesmo tempo de vida para manter sua própria sessão com o usuário (recomendável) ou escolher um tempo de vida de sessão totalmente diferente. Se o aplicativo precisar obter um novo id\_token, ele simplesmente precisará fazer uma nova solicitação de conexão ao AD do Azure. Se o usuário tiver uma sessão de navegador válida com o AD do Azure, talvez ele não precise inserir suas credenciais novamente. |
| Tokens de atualização | Até 14 dias | Um único token de atualização é válido para um máximo de 14 dias. No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos, de modo que o aplicativo deve continuar testando e usando um token de atualização até que este falhe ou até que o aplicativo o substitua por um novo token de atualização. Um token de atualização também se tornará inválido caso se passem 90 dias desde que o usuário inseriu suas credenciais. |
| Códigos de Autorização | 5 minutos | Os códigos de autorização são propositadamente de curta duração e devem ser resgatados imediatamente para access\_tokens, id\_tokens e refresh\_tokens quando eles são recebidos. |

<!---HONumber=Oct15_HO3-->