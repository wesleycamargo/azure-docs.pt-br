<properties
	pageTitle="Referência do token do modelo de aplicativo v2 | Microsoft Azure"
	description="Os tipos de token e declaração emitidos pelo ponto de extremidade v2.0"
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: referência ao token

O ponto de extremidade v2.0 emite vários tipos de token de segurança no processamento de cada [fluxo de autenticação](active-directory-v2-flows.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Tipos de token

O ponto de extremidade v2.0 oferece suporte ao [protocolo de autorização do OAuth 2.0](active-directory-v2-protocols.md), que usa access\_tokens e refresh\_tokens. Ele também oferece suporte à autenticação e conexão por meio do [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), que introduz um terceiro tipo de token, o id\_token. Cada um desses tokens é representado como um "token de portador".

Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro se autenticar no Azure AD para receber o token de portador, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não planejada. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, um ataque por parte de intermediários pode ser usado por uma parte mal-intencionada para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo ponto de extremidade v2.0 são implementados como Tokens Web Json, ou JWTs. Um JWT é um meio compacto e protegido por URL de transferir informações entre duas partes. As informações contidas em JWTs são conhecidas como "declarações" ou asserções de informações sobre o portador e o assunto do token. As declarações em JWTs são objetos JSON codificados e serializados para transmissão. Uma vez que os JWTs emitidos pelo ponto de extremidade v2.0 são assinados, mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para fins de depuração. Há várias ferramentas disponíveis para fazer isso, como o [calebb.net](https://calebb.net). Para obter mais informações sobre JWTs, você pode consultar a [Especificação do JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Id\_Tokens

Id\_tokens são uma forma de token de segurança de conexão que seu aplicativo recebe ao executar a autenticação usando o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Eles são representados como [JWTs](#types-of-tokens) e contêm declarações que você pode usar para conectar o usuário ao aplicativo. Você pode usar as declarações em um id\_token como julgar conveniente — geralmente elas são usadas para exibir informações de conta ou tomar decisões de controle de acesso em um aplicativo. O ponto de extremidade v2.0 emitirá somente um tipo de id\_token, que tenha um conjunto consistente de declarações, independentemente do tipo de usuário que tiver se conectado. Isso quer dizer que o formato e o conteúdo dos id\_tokens serão os mesmos para os usuários da Conta da Microsoft e das contas corporativas ou de estudante.

Atualmente, Id\_tokens são assinados, mas não criptografados. Quando seu aplicativo recebe um id\_token, ele deve [validar a assinatura](#validating-tokens) para comprovar a autenticidade do token e validar algumas declarações no token para comprovar sua validade. As declarações validadas por um aplicativo variam de acordo com os requisitos do cenário, mas há algumas [validações comuns de declaração](#validating-tokens) que seu aplicativo deve executar em cada cenário.

Os detalhes completos sobre as declarações nos id\_tokens são fornecidos abaixo, bem como o exemplo de id\_token. Observe que as declarações em id\_tokens não são retornadas em uma ordem específica. Além disso, novas declarações podem ser introduzidas nos id\_tokens a qualquer momento — o aplicativo não deve ser interrompido conforme novas declarações são introduzidas. A lista abaixo inclui as declarações que o aplicativo pode interpretar confiavelmente no momento em que este documento foi escrito. Para praticar, tente inspecionar as declarações no exemplo de id\_token colando-o em [calebb.net](https://calebb.net). Se necessário, mais detalhes podem ser encontrados na [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemplo de Id\_Token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ
19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI0OTIxMDI1My0wYmExLTRhOWEtYTQyNC02MTY5OTlmYWI2MjAiL
CJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMW
YzNzZlL3YyLjAvIiwiaWF0IjoxNDM4NTM1NTQzLCJuYmYiOjE0Mzg1MzU1NDMsImV4cCI6MTQzODUzOTQ0MywidmVyIjoiMi4
wIiwidGlkIjoiYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlIiwib2lkIjoiYTFlYmRkZTgtZTRmOS00NTcx
LWFkOTMtMzA1OWUzNzUwZDIzIiwicHJlZmVycmVkX3VzZXJuYW1lIjoic2FtcGxlLmFkbWluQHN0cm9ja2lzZGV2Lm9ubWljc
m9zb2Z0LmNvbSIsInN1YiI6IjJvMmQ5SVBGVzI5MGo0RVkySXg0RUdoaEtlWnVGaC1LcFhHS2tuZkNxRWMiLCJuYW1lIjoiU2
FtcGxlIEFkbWluIiwibm9uY2UiOiIxMjM0NSIsImNfaGFzaCI6IngxeU92VTZRaXE0Y1lVcVIxeDBvM2cifQ.Qk9exyv04I6a
P6Sju2xNG9O2sj8dG-aEoJeS5dmnjdLo8k1ZzgZd7w-6yCrKXgPh4FJ1YY-08DZnHNmP3oxm3zmEv3RUIBEyTmo3598PRYLWl
vttis1KD5PoNgAyKfHqiOCL5q_Owd0m9oAKDagbJhRVZOS89phllA0AQnaI6hJOKvMsbOYJt-w00y6TXf1Nkzp_Yey8EmRiwN
7gqvudL1UfZ7_UbST2DBjPIyZsv0gT8gpApz6CecCOyX1NNWpUg8ZRkNnOjGL-IMhq4okPCTTfYriOo93z9Y9v6NmaJxV5bBN
V-DIguXSzLVKnnflfSLyvhinsjLKCnu9L3oXHxw
```

#### Declarações em Id\_Tokens
| Nome | Declaração | Exemplo de valor | Descrição |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Público-alvo | `aud` | `49210253-0ba1-4a9a-a424-616999fab620` | Identifica o destinatário pretendido do token. Em id\_tokens, o público-alvo é a Id de Aplicativo do seu aplicativo, conforme atribuída a ele no portal de registro do aplicativo. O aplicativo deve validar esse valor e rejeitar o token, caso ele não corresponda. |
| Emissor | `iss` | `https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0/` | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token, bem como o locatário do AD do Azure no qual o usuário foi autenticado. O aplicativo deve validar a declaração do emissor para garantir que o token venha do ponto de extremidade v2.0. Ele também pode usar a parte de guid da declaração para restringir o conjunto de locatários que têm permissão para entrar no aplicativo. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Data de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Versão | `ver` | `2.0` | A versão do id\_token, conforme definida pelo AD do Azure. Para o modelo de aplicativo v2.0, o valor será `2.0`. |
| ID do locatário | `tid` | `b9410318-09af-49c2-b0c3-653adc1f376e` | Um guid que representa o locatário do AD do Azure de onde vem o usuário. Para contas corporativas e de estudante, o guid será a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor será `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash de código é incluído em id\_tokens apenas quando estes são emitidos juntamente com um código de autorização do OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Consulte a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Hash do token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | O hash do token de acesso é incluído em id\_tokens apenas quando estes são emitidos juntamente com um token de acesso do OAuth 2.0. Ele pode ser usado para validar a autenticidade de um token de acesso. Consulte a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) para obter mais detalhes sobre como realizar essa validação. |
| Nonce | `nonce` | `12345` | O nonce é uma estratégia para migrar ataques de reprodução de token. O aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce`. O valor que você fornece na solicitação será emitido na declaração `nonce` do id\_token, sem modificação. Isso permite ao aplicativo verificar o valor em relação ao valor que ele especificou na solicitação, que associa a sessão do aplicativo a um determinado id\_token. O aplicativo deve executar essa validação durante o processo de validação do id\_token. |
| Nome | `name` | `Leonardo DaVinci` | A declaração de nome fornece um valor legível por humanos que identifica o assunto do token. Não há garantia de que esse valor seja exclusivo, ele é mutável e foi projetado para ser usado apenas para fins de exibição. |
| Nome de usuário preferencial | `preferred_username` | `leo@outlook.com` | O nome de usuário principal que é usado para representar o usuário no ponto de extremidade v2.0. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado para um determinado usuário ao longo do tempo. |
| Assunto | `sub` | `AAAAAAAAAAAAAAAAAAAAAOUtxUJsxQtHuMcFCIA1NC0` | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado, então ele pode ser usado para executar verificações de autorização com segurança, como por exemplo quando o token é usado para acessar um recurso. Como o assunto está sempre presente nos tokens emitidos pelo AD do Azure, é recomendável usar esse valor em um sistema de autorização de uso geral. |
| ObjectId | `oid` | `27cb5cec-7c0c-40b4-a69a-22500b6ea853` | A Id de objeto da conta corporativa ou de estudante no sistema do AD do Azure. Essa declaração não será emitida para contas pessoais da Microsoft. |

<!---
| Not Before | `nbf` | `1438535543` |  The time at which the token becomes valid, represented in epoch time. It is usually the same as the issuance time.  Your app should use this claim to verify the validity of the token lifetime.  |
-->



## Tokens de acesso

Os tokens de acesso emitidos pelo ponto de extremidade v2.0 assumem dois formatos diferentes. Os tokens de acesso que são emitidos em nome das contas corporativas ou de estudante são JWTs, semelhantes a um id\_token. Os tokens de acesso que são emitidos em nome de uma conta pessoal da Microsoft estão em um formato conhecido como "tíquetes compactos". Por esse motivo, no desenvolvimento, você pode perceber um formato de cadeia de caracteres diferente para tokens de acesso emitidos pelo ponto de extremidade v2.0. Com o tempo, essa diferença em tokens de acesso será eliminada do ponto de extremidade v2.0.

Dito isso, os tokens de acesso emitidos pelo ponto de extremidade v2.0, no momento, podem ser consumidos apenas pelos Serviços Microsoft Os aplicativos não precisam executar nenhuma validação ou inspeção de tokens de acesso para qualquer um dos cenários que atualmente têm suporte. É possível tratar tokens de acesso como totalmente opacos; eles são apenas cadeias de caracteres que seu aplicativo pode transferir para a Microsoft em solicitações HTTP.

Em breve, o ponto de extremidade v2.0 apresentará no seu aplicativo a capacidade de receber tokens de acesso de outros clientes. No momento, essa informação será atualizada com as informações que o aplicativo precisa para executar a validação do token de acesso e outras tarefas parecidas.

Quando você solicita um token de acesso do ponto de extremidade v2.0, este também retorna alguns metadados sobre o token de acesso para consumo do aplicativo. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Isso permite ao aplicativo realizar caching, de modo inteligente, dos tokens de acesso sem precisar analisar abertamente o token de acesso em si.

## Tokens de atualização

Os tokens de atualização são tokens de segurança que o aplicativo pode usar para adquirir novos tokens de acesso em um fluxo do OAuth 2.0. Ele permite ao aplicativo obter acesso a longo prazo a recursos em nome de um usuário sem a necessidade de interação do usuário.

Os tokens de atualização têm vários recursos. Isso quer dizer que um token de atualização recebido durante uma solicitação de token para um recurso pode ser resgatado para tokens de acesso para um recurso totalmente diferente.

Para receber uma atualização em uma resposta de token, o aplicativo deve solicitar e receber o escopo `offline_acesss`. Para saber mais sobre o escopo `offline_access`, consulte o [artigo de consentimento e escopos aqui](active-directory-v2-scopes.md).

Os tokens de atualização são, e sempre serão, completamente opacos para seu aplicativo. Eles são emitidos pelo ponto de extremidade v2.0 do AD do Azure e podem ser inspecionados e interpretados apenas pelo ponto de extremidade v2.0. Eles têm longa duração, mas o aplicativo não deve ser escrito para esperar que um token de atualização dure por qualquer período de tempo. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao ponto de extremidade v2.0.

Ao resgatar um token de atualização para um novo token de acesso (e se o aplicativo tiver concedido o escopo `offline_access`), você receberá um novo token de atualização na resposta de token. É preciso salvar o token de atualização recentemente emitido, substituindo o que você usou na solicitação. Isso garantirá que seus tokens de atualização permanecem válidos pelo máximo tempo possível.

## Validando tokens

No momento, a única validação de token que seus aplicativos devem precisar executar é a validação de id-tokens. Para validar um id\_token, o aplicativo deve validar a assinatura do id\_token e as declarações contidas nele.

<!-- TODO: Link -->
Fornecemos bibliotecas e códigos de exemplo que mostram como tratar com facilidade a validação do token. As informações abaixo são fornecidas simplesmente para aqueles que desejam entender o processo subjacente. Também há várias bibliotecas de software livre de terceiros disponíveis para validação de JWT; há, pelo menos, uma opção para quase todos os idiomas e plataformas.

#### Validando a assinatura
Um JWT contém três segmentos, que são separados pelo caractere `.`. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo** e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do id\_token, de modo que seu aplicativo possa confiar nele.

Os Id\_Tokens são assinados usando algoritmos de criptografia assimétrica padrões do setor, como RSA 256. O cabeçalho do id\_token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
		typ: "JWT",
		alg: "RS256",
		x5t: "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token, enquanto as declarações `kid` ou `x5t` indicam a chave pública particular que foi usada para assinar o token.

Em qualquer ponto no tempo, o ponto de extremidade v2.0 pode assinar um id\_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. O ponto de extremidade v2.0 gira o possível conjunto de chaves em intervalos periódicos, de modo que o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo ponto de extremidade v2.0 é de aproximadamente 24 horas.

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o documento de metadados do OpenID Connect localizado em:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Esse documento de metadados é um objeto JSON que contém várias informações úteis, como o local dos vários pontos de extremidade exigidos para execução da autenticação do OpenID Connect. Ele também inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. Esse local é fornecido abaixo, mas é melhor buscar esse local dinamicamente usando o documento de metadados e analisando o `jwks_uri`:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`

O documento de JSON localizado nessa url contém todas as informações de chave pública em uso nesse momento específico. O aplicativo pode usar as declarações `kid` ou `x5t` no cabeçalho do JWT para selecionar qual chave pública neste documento foi usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

Executar a validação da assinatura está fora do escopo deste documento — há muitas bibliotecas de software livre disponíveis para ajudar você a fazer isso, caso seja necessário.

#### Validando as declarações
Quando o aplicativo recebe um id\_token na conexão do usuário, ele também deve fazer algumas verificações nas declarações no id\_token. Estão incluídos:

- A declaração **Público-alvo**: para verificar se o id\_token foi destinado a ser dado ao aplicativo.
- As declarações **Emitido em** e **Data de Expiração**: para verificar se o id\_token não expirou.
- A declaração **Emissor**: para verificar se o token foi de fato emitido para o aplicativo pelo ponto de extremidade v2.0.
- O **Nonce**: como uma redução do ataque de reprodução do token.

Os detalhes dos valores esperados para essas declarações estão incluídos acima na [seção id\_token](#id_tokens).


## Tempos de vida do token

Os tempos de vida do token a seguir são fornecidos puramente para fins de compreensão, pois eles podem ajudar no desenvolvimento e na depuração de aplicativos. Os aplicativos não devem ser escritos para esperar que algum desses tempos de vida permaneça constante; eles podem e vão mudar a qualquer momento.

| A criptografia do token | Tempo de vida | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| Id\_Tokens (contas corporativos ou de estudante) | 1 hora | Id\_Tokens normalmente são válidos por uma hora. Seu aplicativo Web pode usar esse mesmo tempo de vida para manter sua própria sessão com o usuário (recomendável) ou escolher um tempo de vida de sessão totalmente diferente. Se o aplicativo precisar obter um novo id\_token, ele simplesmente precisará fazer uma nova solicitação de conexão ao ponto de extremidade de autorização v2.0. Se o usuário tiver uma sessão de navegador válida com o ponto de extremidade v2.0, talvez ele não precise inserir suas credenciais novamente. |
| Id\_Tokens (contas pessoais) | 24 horas | Os Id\_Tokens para contas pessoais geralmente são válidos por 24 horas. Seu aplicativo Web pode usar esse mesmo tempo de vida para manter sua própria sessão com o usuário (recomendável) ou escolher um tempo de vida de sessão totalmente diferente. Se o aplicativo precisar obter um novo id\_token, ele simplesmente precisará fazer uma nova solicitação de conexão ao ponto de extremidade de autorização v2.0. Se o usuário tiver uma sessão de navegador válida com o ponto de extremidade v2.0, talvez ele não precise inserir suas credenciais novamente. |
| Tokens de acesso (contas corporativas ou de estudante) | 1 hora | Indicado nas respostas de token como parte dos metadados do token. |
| Tokens de acesso (contas pessoais) | 1 hora | Indicado nas respostas de token como parte dos metadados do token. Os access\_tokens emitidos em nome de contas pessoas podem ser configurados para um tempo de vida diferente, mas geralmente o caso é de uma hora. |
| Tokens de atualização (conta corporativa ou de estudante) | Até 14 dias | Um único token de atualização é válido para um máximo de 14 dias. No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos, de modo que o aplicativo deve continuar testando e usando um token de atualização até que este falhe ou até que o aplicativo o substitua por um novo token de atualização. Um token de atualização também se tornará inválido caso se passem 90 dias desde que o usuário inseriu suas credenciais. |
| Tokens de atualização (contas pessoais) | Até 1 ano | Um único token de atualização é válido para um máximo de 1 ano. No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos, de modo que o aplicativo deve continuar testando e usando um token de atualização até que este falhe. |
| Códigos de autorização (contas corporativas ou de estudante) | 10 minutos | Os códigos de autorização são propositadamente de curta duração e devem ser resgatados imediatamente para access\_tokens e refresh\_tokens quando eles são recebidos. |
| Códigos de autorização (contas pessoais) | 5 minutos | Os códigos de autorização são propositadamente de curta duração e devem ser resgatados imediatamente para access\_tokens e refresh\_tokens quando eles são recebidos. Os códigos de autorização emitidos em nome de contas pessoais também são de uso ocasional. |

<!---HONumber=Oct15_HO3-->