---
title: Como funciona o cache | Microsoft Docs
description: "O cache é o processo de armazenamento de dados localmente para que as solicitações futuras desses dados possam ser acessadas com maior rapidez."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 284b4bcbeafc422a2ed91cec00a5b5b83bb37b7b
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="how-caching-works"></a>Como funciona o cache

Este artigo fornece uma visão geral dos conceitos gerais de cache e como a [CDN (Rede de Distribuição de Conteúdo)](cdn-overview.md) do Azure usa o cache para melhorar o desempenho. Se deseja saber como personalizar o comportamento de cache no ponto de extremidade da CDN, consulte [Controlar o comportamento de cache da CDN com regras de cache ](cdn-caching-rules.md) e [Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consulta](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introdução ao cache

O cache é o processo de armazenamento de dados localmente para que as solicitações futuras desses dados possam ser acessadas com maior rapidez. No tipo de cache mais comum, o cache do navegador da Web, um navegador da Web armazena cópias de dados estáticos localmente em um disco rígido local. Ao usar o cache, o navegador da Web pode evitar fazer várias viagens de ida e volta ao servidor e, em vez disso, acessar os mesmos dados localmente, economizando tempo e recursos. O cache é adequado para gerenciar localmente pequenos dados estáticos, como imagens estáticas, arquivos CSS e arquivos JavaScript.

Do mesmo modo, o cache é utilizado por uma rede de distribuição de conteúdo em servidores de borda próximos ao usuário para evitar solicitações viajando de volta para a origem e reduzir latência do usuário final. Ao contrário de um cache do navegador da Web, que é utilizado apenas para um usuário único, a CDN possui um cache compartilhado. Em um cache compartilhado da CDN, um outro arquivo que é solicitado por um usuário poderá ser acessado posteriormente por outros usuários, o que diminui consideravelmente o número de solicitações para o servidor de origem.

Os recursos dinâmicos que alteram com frequência ou são exclusivos de um usuário individual não podem ser armazenados em cache. No entanto, esses tipos de recursos podem tirar proveito da otimização de DSA (Aceleração de Site Dinâmico) na Rede de Distribuição de Conteúdo do Microsoft Azure para aprimoramento de desempenho.

O cache pode ocorrer em vários níveis entre o servidor de origem e o usuário final:

- Servidor Web: usa um cache compartilhado (para vários usuários).
- Rede de distribuição de conteúdo: usa um cache compartilhado (para vários usuários).
- ISP (Provedor de Serviços de Internet): usa um cache compartilhado (para vários usuários).
- Navegador da Web: usa um cache privado (para um usuário).

Cada cache normalmente gerencia sua própria atualização de recurso e executa a validação quando um arquivo está obsoleto. Esse comportamento é definido na especificação de cache HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Atualização de recurso

Como um recurso em cache pode estar potencialmente desatualizado ou obsoleto (em comparação com o recurso correspondente no servidor de origem), é importante que qualquer mecanismo de cache seja controlado quando o conteúdo for atualizado. Para economizar tempo e consumo de largura de banda, um recurso armazenado em cache não é comparado à versão no servidor de origem sempre que é acessado. Em vez disso, enquanto um recurso armazenado em cache for considerado atualizado, assume-se que é a versão atual e é enviado diretamente para o cliente. Um recurso armazenado em cache é considerado como atualizado quando o tempo decorrido for menor do que ao tempo decorrido ou o período definido por uma configuração de cache. Por exemplo, quando um navegador recarrega uma página da Web, ele verifica se cada recurso armazenado em cache no disco rígido está atualizado e o carrega. Se o recurso não estiver atualizado (obsoleto), uma cópia atualizada é carregada a partir do servidor.

### <a name="validation"></a>Validação

Se um recurso for considerado obsoleto, o servidor de origem for solicitado para validá-lo, ou seja, determinar se os dados no cache ainda correspondem ao que está no servidor de origem. Se o arquivo foi modificado no servidor de origem, o cache atualiza sua versão do recurso. Caso contrário, se o recurso estiver atualizado, os dados são distribuídos diretamente do cache sem validá-lo primeiro.

### <a name="cdn-caching"></a>Cache da CDN

O cache é integral para a forma como uma CDN opera para acelerar a distribuição e reduzir a carga de origem para ativos estáticos, como imagens, fontes e vídeos. No cache da CDN, os recursos estáticos são armazenados seletivamente em servidores estrategicamente posicionados que são mais locais para um usuário e oferecem as seguintes vantagens:

- Como a maior parte do tráfego da Web é estática (por exemplo, imagens, fontes e vídeos), o cache da CDN reduz a latência da rede ao mover o conteúdo mais próximo do usuário, reduzindo assim a distância de viagem dos dados.

- Ao descarregar o trabalho para uma CDN, o cache pode reduzir o tráfego de rede e a carga no servidor de origem. Isso reduz os custos e os requisitos de recursos para o aplicativo, mesmo quando houver um grande número de usuários.

Semelhante ao modo como o cache é implementado em um navegador da Web, você pode controlar como o cache é executado em uma CDN enviando cabeçalhos de diretiva de cache. Os cabeçalhos de diretiva de cache são cabeçalhos HTTP, que normalmente são adicionados pelo servidor de origem. Embora a maioria desses cabeçalhos tenha sido originalmente projetada para solucionar o cache em navegadores cliente, eles também são utilizados por todos os caches intermediários, como as CDNs. 

Dois cabeçalhos podem ser utilizados para definir a atualização de cache: `Cache-Control` e `Expires`. `Cache-Control` é o mais atual e tem precedência sobre `Expires`, se ambos existirem. Existem também dois tipos de cabeçalhos utilizados para validação (chamados de validadores): `ETag` e `Last-Modified`. `ETag` é mais atual e tem precedência sobre `Last-Modified`, se ambos estiverem definidos.  

## <a name="cache-directive-headers"></a>Cabeçalhos de diretiva de cache

> [!IMPORTANT]
> Por padrão, um ponto de extremidade CDN do Azure que é otimizado para DSA ignora os cabeçalhos de diretiva de cache e ignora o cache. Ajuste como um ponto de extremidade CDN do Azure trata esses cabeçalhos usando regras de cache de CDN para habilitar o cache. Para obter mais informações, consulte [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

A CDN do Azure fornece suporte aos seguintes cabeçalhos de diretiva de cache HTTP, que definem a duração do cache e o compartilhamento de cache: 

`Cache-Control`
- Introduzido no HTTP 1.1 para dar aos editores da Web mais controle sobre seu conteúdo e tratar as limitações do cabeçalho `Expires`.
- Substitui o cabeçalho `Expires`, se ele e `Cache-Control` estiverem definidos.
- Quando usado em um cabeçalho de solicitação, `Cache-Control` é ignorado pela CDN do Azure, por padrão.
- Quando usado em um cabeçalho de resposta, a CDN do Azure dá suporte às seguintes diretivas `Cache-Control`, de acordo com o produto: 
   - **CDN do Azure da Verizon**: dá suporte a todas as diretivas `Cache-Control`. 
   - **CDN da Azure da Akamai**: dá suporte apenas às seguintes diretivas `Cache-Control`; todas as outras são ignoradas: 
      - `max-age`: um cache pode armazenar o conteúdo pelo número de segundos especificado. Por exemplo, `Cache-Control: max-age=5`. Esta diretiva especifica a quantidade máxima de tempo que o conteúdo é considerado como novo.
      - `no-cache`: armazena o conteúdo em cache, mas valida o conteúdo sempre antes de entregá-lo por meio do cache. Equivalente a `Cache-Control: max-age=0`.
      - `no-store`: nunca armazene em cache o conteúdo. Remova o conteúdo se já tiver sido armazenado anteriormente.

`Expires`
- Cabeçalho herdado introduzido no HTTP 1.0; com suporte para compatibilidade com versões anteriores.
- Usa um tempo de expiração baseado em data com segunda precisão. 
- Similar a `Cache-Control: max-age`.
- Usado quando `Cache-Control` não existe.

`Pragma`
   - Não é atendido pela CDN do Azure, por padrão.
   - Cabeçalho herdado introduzido no HTTP 1.0; com suporte para compatibilidade com versões anteriores.
   - Usado como um cabeçalho de solicitação de cliente com a seguinte diretiva: `no-cache`. Essa diretiva instrui o servidor a entregar uma nova versão do recurso.
   - `Pragma: no-cache` é equivalente a `Cache-Control: no-cache`.

## <a name="validators"></a>Validadores

Quando o cache está obsoleto, validadores de cache HTTP são usados para comparar a versão armazenada em cache de um arquivo com a versão no servidor de origem. **A CDN do Azure da Verizon** fornece suporte para validadores ETag e Last-Modified por padrão, enquanto a **CDN do Azure do Akamai** fornece suporte somente para o Last-Modified, por padrão.

`ETag`
- A **CDN do Azure da Verizon** utiliza `ETag` por padrão, enquanto **A CDN do Azure do Akamai** não.
- `ETag` define uma cadeia de caracteres que é exclusiva para cada arquivo e versão de um arquivo. Por exemplo, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introduzido no HTTP 1.1 e é mais atual do que `Last-Modified`. Útil quando a última data de modificação for difícil de determinar.
- Fornece suporte para validação de alta segurança e validação de baixa segurança. No entanto, a CDN do Azure fornece suporte apenas para validação de alta segurança. Para uma validação de alta segurança, as duas representações de recursos devem ser de byte a byte idênticos. 
- Um cache valida um arquivo que usa `ETag` enviando um cabeçalho `If-None-Match` com um ou mais validadores `ETag` na solicitação. Por exemplo, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Se a versão do servidor coincidir com um validador `ETag` na lista, ele enviará o código de status 304 (Não Modificado) em sua resposta. Se a versão for diferente, o servidor responderá com o código de status 200 (OK) e o recurso atualizado.

`Last-Modified`
- Para a **CDN do Azure da Verizon somente**, Last-Modified é usado se ETag não for parte da resposta HTTP. 
- Especifica a data e a hora em que o servidor de origem determinou que o recurso foi modificado pela última vez. Por exemplo, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Um cache valida um arquivo utilizando `Last-Modified` enviando um cabeçalho `If-Modified-Since` com uma data e hora na solicitação. O servidor de origem compara essa data com o cabeçalho `Last-Modified` do recurso mais recente. Se o recurso não foi modificado desde a hora especificada, o servidor retornará o código de status 304 (Não Modificado) em sua resposta. Se o recurso foi modificado, o servidor retornará o código de status 200 (OK) e o recurso atualizado.

## <a name="determining-which-files-can-be-cached"></a>Determinar quais arquivos podem ser armazenados em cache

Nem todos os recursos podem ser armazenados em cache. A tabela a seguir mostra quais recursos podem ser armazenados em cache, com base no tipo de resposta HTTP. Os recursos entregues com respostas HTTP que não atendem a todas essas condições não podem ser armazenados em cache. Para a **CDN do Azure da Verizon Premium** é possível utilizar o mecanismo de regras para personalizar algumas dessas condições.

|                   | CDN do Azure da Verizon | CDN do Azure do Akamai            |
|------------------ |------------------------|----------------------------------|
| Códigos de status HTTP | 200                    | 200, 203, 300, 301, 302 e 401 |
| Método HTTP       | GET                    | GET                              |
| Tamanho do arquivo         | 300 GB                 | - Otimização geral de entrega na Web: 1,8 GB<br />- Otimizações de streaming de mídia: 1,8 GB<br />- Otimização de arquivos grandes: 150 GB |

## <a name="default-caching-behavior"></a>Comportamento de cache padrão

A tabela a seguir descreve o comportamento de cache padrão para os produtos da CDN do Azure e suas otimizações.

|                    | Verizon - entrega Web geral | Verizon – DSA | Akamai - entrega Web geral | Akamai – DSA | Akamai - fazer o download de arquivo grande | Akamai – streaming de mídia geral ou VoD |
|--------------------|--------|------|-----|----|-----|-----|
| **Aceitar a origem**    | sim    | Não    | sim | Não  | sim | sim |
| **Duração do cache da CDN** | 7 dias | Nenhum | 7 dias | Nenhum | 1 dia | 1 ano |

**Aceitar a origem**: especifica se deve aceitar os [cabeçalhos de diretiva de cache com suporte](#http-cache-directive-headers), se eles existirem na resposta HTTP do servidor de origem.

**Duração do cache da CDN**: especifica a quantidade de tempo para o qual um recurso é armazenado em cache na CDN do Azure. No entanto, se **Aceitar a origem** for Sim e a resposta HTTP do servidor de origem inclui o cabeçalho de diretiva de cache `Expires` ou `Cache-Control: max-age`, a CDN do Azure usa o valor de duração especificado pelo cabeçalho. 

## <a name="next-steps"></a>Próximas etapas

- Para saber como personalizar e substituir o comportamento de cache padrão na CDN através de regras de cache, consulte [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md). 
- Para saber como usar sequências de caracteres de consulta para controlar o comportamento de cache, consulte [Controlar o comportamento de cache da CDN do Azure com cadeias de caracteres de consultas](cdn-query-string.md).



