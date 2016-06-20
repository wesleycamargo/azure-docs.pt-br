<properties
	pageTitle="Práticas recomendadas para o OAuth 2.0 no Azure AD | Microsoft Azure"
	description="Este artigo descreve as práticas recomendadas para o desenvolvimento de aplicativos que usam o OAuth 2.0 no Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>


# Práticas recomendadas para o OAuth 2.0 no Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

## Usar o parâmetro State

O parâmetro `state` é um valor gerado aleatoriamente, não reutilizável (normalmente um GUID) que o cliente envia na solicitação. É um parâmetro opcional mas é altamente recomendável em solicitações para o código de autorização. A resposta também inclui o mesmo valor `state` e o aplicativo deve verificar se os valores de estado são idênticos antes de usar a resposta.

O parâmetro `state` ajuda a detectar ataques de solicitação intersite forjada (CSRF) contra o cliente. Para saber mais sobre ataques CSRF, confira [Solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12) no OAuth 2.0 Authorization Framework.

## Armazenar tokens de acesso em cache

Para minimizar as chamadas de rede do aplicativo cliente e sua latência associada, o aplicativo cliente deve armazenar em cache os tokens de acesso durante a vida útil do token especificada na resposta do OAuth 2.0. Para determinar o tempo de vida do token, use os valores de parâmetro `expires_in` ou `expires_on`.

Se um recurso da API Web retornar um código de erro `invalid_token`, isso poderá indicar que o recurso determinou que o token expirou. Se as horas de relógio do cliente e do recurso forem diferentes (conhecido como "diferença de horário"), o recurso poderá considerar o token expirado antes que o token seja removido do cache do cliente. Se isso ocorrer, remova o token do cache, mesmo se ele ainda estiver dentro de seu tempo de vida calculado.

## Manipulação de tokens de atualização

Os tokens de atualização não têm um tempo de vida especificado. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. O aplicativo cliente precisa esperar e tratar os erros retornados pelo ponto de extremidade de emissão de token corretamente.

Quando você receber uma resposta com um erro de token de atualização, descarte o token de atualização atual e solicite um novo código de autorização ou um token de acesso. Em particular, quando usar um token de atualização no fluxo de Concessão de Código de Autorização, se você receber uma resposta com os códigos de erro `interaction_required` ou `invalid_grant`, descarte o token de atualização e solicite um novo código de autorização.

## Verificar o parâmetro de ID do recurso

O aplicativo cliente deve verificar o valor do parâmetro `resource_id` na resposta. Caso contrário, um serviço mal-intencionado poderá induzir um ataque de elevação de privilégios.

 A estratégia recomendada para evitar um ataque é verificar se a resource\_id corresponde à base da URL da API Web que está sendo acessada. Por exemplo, se https://service.contoso.com/data estiver sendo acessado, o `resource_id` poderá ser https://service.contoso.com/. O aplicativo cliente deverá rejeitar um `resource_id` que não comece com a URL base, a menos que haja uma maneira alternativa confiável para verificar a ID.

## Emissão de token e diretrizes de repetição

O Azure AD dá suporte a diversos pontos de extremidade de emissão de token que os clientes podem consultar. Use as diretrizes a seguir para implementar a lógica de repetição para que esses pontos de extremidade lidem com uma falha inesperada de rede ou de servidor.

As falhas que respondem às novas tentativas normalmente retornam códigos de erro da série HTTP 500 para uma solicitação para um ponto de extremidade do Azure AD. Em alguns cenários, o cliente é um aplicativo ou um serviço que faz solicitações automatizadas ao Azure AD. Em outros cenários, como a federação baseada na Web que usa o protocolo WS-Federation, o cliente é um navegador da Web e o usuário final deverá repetir manualmente.

### Implementar a lógica de repetição com base nas respostas de erro da série HTTP 500

A lógica de repetição é altamente recomendável quando o Active Directory Access Control Service (ACS) retorna erros da série HTTP 500. Isso inclui:

- Erro HTTP 500 - Erro Interno do Servidor
- Erro HTTP 502 - Gateway Incorreto
- Erro HTTP 503 - Serviço indisponível
- Erro HTTP 504 - Tempo Limite do Gateway

Embora os códigos HTTP individuais possam ser listados explicitamente na lógica de repetição, será suficiente para a invocação da lógica de repetição se algum erro da série HTTP 500 for retornado.

Normalmente, a lógica de repetição não é recomendável quando são retornados códigos de erro da série HTTP 400. Um código de resposta de erro da série HTTP 400 do ACS significa que a solicitação é inválida e precisa ser revisada.

A lógica de repetição deve ser acionada por códigos de erro HTTP, como o HTTP 504 (Tempo limite do servidor externo), ou pela série de códigos de erro HTTP 500, e não por códigos de erro do ACS, como ACS90005. Os códigos de erro do ACS são informativos e estão sujeitos a alteração.

### As repetições devem usar um temporizador de retirada para controle de fluxo ideal

Quando um cliente recebe um erro da série HTTP 500, deve esperar antes de repetir a solicitação. Para obter melhores resultados, é recomendável que esse período aumente a cada repetição subsequente. Essa abordagem permite que os erros transitórios sejam resolvidos rapidamente ao otimizar a taxa de solicitação para problemas transitórios de rede ou de servidor que demorem mais para serem resolvidos.

Por exemplo, use um temporizador de retirada exponencial onde o atraso antes da repetição aumente com cada instância, como Repetir 1:1 segundo, Repetir 2:2 segundos, Repetir 3:4 segundos e assim por diante.

Ajuste o número de repetições e o tempo entre cada repetição com base em seus requisitos de experiência do usuário. No entanto, é recomendável até cinco repetições em um período de cinco minutos. As falhas causadas por um tempo limite levam mais tempo para serem resolvidas.

## Próximas etapas

[Bibliotecas de Autenticação do Active Directory (ADAL)](active-directory-authentication-libraries.md)

<!---HONumber=AcomDC_0608_2016-->