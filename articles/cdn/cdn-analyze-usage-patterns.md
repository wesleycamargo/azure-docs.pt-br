---
title: "Analisar padrões de uso da CDN do Azure | Microsoft Docs"
description: "Você pode exibir os padrões de uso do CDN usando os seguintes relatórios: Largura de Banda, Dados Transferidos, Acertos, Status do Cache, Taxa de Acertos de Cache, Dados IPV4/IPV6 Transferidos."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: aadbe872dd3384c8d337b432fb3be69422ca322b
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
<a id="analyze-azure-cdn-usage-patterns" class="xliff"></a>

# Analisar os padrões de uso da CDN do Azure

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A guia abaixo percorre as etapas para exibir os relatórios principais por meio do Portal de gerenciamento para perfis Verizon. Você também pode exportar dados de análise principal para o armazenamento, hub de eventos ou Log Analytics (OMS) para os perfis Verizon e Akamai [por meio do Portal do Azure](cdn-log-analysis.md).

Você pode exibir os padrões de uso para o CDN usando os seguintes relatórios:

* Largura de banda
* Dados Transferidos
* Acertos
* Status do Cache
* Taxa de Acertos do Cache
* Dados IPv4/IPV6 Transferidos

<a id="accessing-core-reports" class="xliff"></a>

## Acessando relatórios principais
1. Na folha do perfil do CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **Análise**, em seguida, sobre o submenu **Relatórios Principais**.  Clique no relatório desejado no menu.
   
    ![Portal de gerenciamento da CDN - menu Relatórios Principais](./media/cdn-reports/cdn-core-reports.png)

<a id="bandwidth" class="xliff"></a>

## Largura de banda
O relatório de largura de banda consiste em uma tabela e de um gráfico de dados que indicam o uso de largura de banda para HTTP e HTTPS em um período específico. Você pode exibir o uso de largura de banda entre todos os POPs do CDN ou em um POP específico. Isso permite exibir os picos de tráfego e a distribuição entre os POPs do CDN em Mbps.

* Selecione Todos os Nós de Borda para ver o tráfego de todos os nós ou escolha uma região/nó específica na lista suspensa.
* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas, clique em "ir" para garantir que sua seleção seja atualizada.
* Você pode exportar e baixar os dados clicando no ícone de planilha do excel localizado ao lado de "ir".

O relatório é atualizado a cada cinco minutos.

![Relatório Largura de banda](./media/cdn-reports/cdn-bandwidth.png)

<a id="data-transferred" class="xliff"></a>

## Dados Transferidos
Esse relatório consiste em uma tabela e um gráfico de dados que indicam o uso de largura de banda para HTTP e HTTPS em um período específico. Você pode exibir o uso de tráfego em todos os POPs do CDN ou em um POP específico. Isso permite exibir os picos de tráfego e a distribuição entre os POPs do CDN em GB.

* Selecione Todos os Nós de Borda para ver o tráfego de todos os nós ou escolha uma região/nó específica na lista suspensa.
* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas, clique em "ir" para garantir que sua seleção seja atualizada.
* Você pode exportar e baixar os dados clicando no ícone de planilha do excel localizado ao lado de "ir".

O relatório é atualizado a cada cinco minutos.

![Relatório Dados transferidos](./media/cdn-reports/cdn-data-transferred.png)

<a id="hits-status-codes" class="xliff"></a>

## Acertos (códigos de status)
Este relatório descreve a distribuição dos códigos de status de solicitação para o seu conteúdo. Todas as solicitações de conteúdo gerarão um código de status HTTP. O código de status descreve como os POPs de borda lidaram com a solicitação. Por exemplo, os códigos de status 2xx indicam que a solicitação foi servida com êxito para um cliente, enquanto um código de status 4xx indica que ocorreu um erro. Para obter mais detalhes sobre o código de status HTTP, consulte [códigos de status](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas, clique em "ir" para garantir que sua seleção seja atualizada.
* Você pode exportar e baixar os dados clicando na planilha do excel localizado ao lado de "ir".

![Relatório Acertos](./media/cdn-reports/cdn-hits.png)

<a id="cache-statuses" class="xliff"></a>

## Status do Cache
Este relatório descreve a distribuição de acertos do cache e de erros de cache para a solicitação do cliente. Uma vez que o desempenho mais rápido provêm de acertos do cache, você pode otimizar velocidades de entrega de dados minimizando erros de cache e acertos de cache expirados. Os erros de cache podem ser reduzidos por meio da configuração do seu servidor de origem para evitar a atribuição de cabeçalhos de resposta "no-cache", evitando o cache de cadeia de caracteres de consulta exceto onde estritamente necessário e evitando códigos de resposta não armazenáveis em cache. Os acertos de cache expirados podem ser evitados tornando uma idade máxima do ativo a maior possível para minimizar o número de solicitações para o servidor de origem.

![Relatório Status do cache](./media/cdn-reports/cdn-cache-statuses.png)

<a id="main-cache-statuses-include" class="xliff"></a>

### Os status do cache principal incluem:
* TCP_HIT: servido da Borda. O objeto estava no cache e não excedeu sua idade máxima.
* TCP_MISS: servido da Origem. O objeto não estava no cache e a resposta voltou para a origem.
* TCP_EXPIRED _MISS: Servido da origem após revalidação com a origem. O objeto estava no cache mas excedeu sua idade máxima. A revalidação com a origem resultou na substituição do objeto por uma nova resposta da origem.
* TCP_EXPIRED _HIT: servido do Edge após revalidação com a origem. O objeto estava no cache mas excedeu sua idade máxima. Uma revalidação com o servidor de origem resultou na não modificação do objeto do cache.
* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas, clique em "ir" para garantir que sua seleção seja atualizada.
* Você pode exportar e baixar os dados clicando no ícone de planilha do excel localizado ao lado de "ir".

<a id="full-list-of-cache-statuses" class="xliff"></a>

### Lista completa de status do cache
* TCP_HIT - esse status é relatado quando uma solicitação é servida diretamente do POP para o cliente. Um ativo é servido imediatamente de um POP quando é armazenado em cache no POP mais próximo ao cliente e tem uma TTL, ou vida útil, válida. O TTL é determinado pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: período máximo s
  * Cache-Control: período máximo
  * Expira
* TCP_MISS - esse status indica que uma versão armazenada em cache do ativo solicitado não foi encontrada no POP mais próximo ao cliente. O ativo será solicitado de um servidor de origem ou de um servidor de escudo de origem. Se o servidor de origem ou o servidor de escudo de origem retornar um ativo, ele será servido ao cliente e armazenado em cache no cliente e no servidor de borda. Caso contrário, um código de status diferente de 200 (por exemplo, 403 Proibido, 404 Não Encontrado etc.) será retornado.
* TCP_EXPIRED _HIT - esse status é relatado quando uma solicitação destinada a um ativo com TTL expirado, como quando a idade máxima do ativo expirou, foi atendida diretamente do POP para o cliente.
  
    Normalmente, uma solicitação expirada resulta em uma solicitação de revalidação para o servidor de origem. Para que um TCP_EXPIRED _HIT ocorra, o servidor de origem deverá indicar que não há uma versão mais recente do ativo. Esse tipo de situação normalmente atualizará os cabeçalhos Cache-Control e Expires do ativo.
* TCP_EXPIRED _MISS - esse status é relatado quando uma versão mais recente de um ativo em cache expirado é servida do POP ao cliente. Isso ocorre quando o TTL para um ativo em cache tiver expirado (por exemplo, idade máxima expirada) e o servidor de origem retorna uma versão mais recente desse ativo. Essa nova versão do ativo será servida para o cliente em vez da versão armazenada em cache. Além disso, ele será ser armazenado em cache no servidor de borda e no cliente.
* CONFIG_NOCACHE - este status indica que uma configuração específica do cliente no nosso POP de borda impediu que o ativo fosse armazenado em cache.
* NONE - esse status indica que uma verificação de atualização de conteúdo do cache não foi executada.
* TCP_ CLIENT_REFRESH _MISS - esse status é relatado quando um cliente HTTP (por exemplo, o navegador) força um POP de borda a recuperar uma nova versão de um ativo obsoleto do servidor de origem.
  
    Por padrão, nossos servidores impedem que um cliente HTTP force nossos servidores de borda a recuperar uma nova versão do ativo do servidor de origem.
* TCP_ PARTIAL_HIT - esse status é relatado quando uma solicitação de intervalo de bytes resulta em um acerto de um ativo parcialmente armazenado em cache. O intervalo de bytes solicitado é servido imediatamente do POP ao cliente.
* UNCACHEABLE - esse status é relatado quando os cabeçalhos Cache-Control e Expires de um ativo indicam que ele não deve ser armazenado em cache em um POP ou pelo cliente HTTP. Esses tipos de solicitações são servidos do servidor de origem

<a id="cache-hit-ratio" class="xliff"></a>

## Taxa de Acertos do Cache
Este relatório indica a porcentagem de solicitações em cache que foram servidas diretamente do cache.

O relatório fornece os seguintes detalhes:

* O conteúdo solicitado foi armazenado em cache no POP mais próximo ao solicitante.
* A solicitação foi servida diretamente da borda da rede.
* A solicitação não exigia revalidação no servidor de origem.

O relatório não inclui:

* Solicitações negadas devido às opções de filtragem de país.
* Solicitações de ativos cujos cabeçalhos indicam que eles não devem ser armazenado em cache. Por exemplo, os cabeçalhos Cache-Control: private, Cache-Control: no-cache ou Pragma: no-cache impedirão que um arquivo seja armazenado em cache.
* Solicitações de intervalo de bytes para conteúdo parcialmente armazenado em cache.

A fórmula é: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas, clique em "ir" para garantir que sua seleção seja atualizada.
* Você pode exportar e baixar os dados clicando no ícone de planilha do excel localizado ao lado de "ir".

![Relatório Taxa de acertos do cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

<a id="ipv4ipv6-data-transferred" class="xliff"></a>

## Dados IPv4/IPV6 Transferidos
Esse relatório mostra a distribuição de uso de tráfego em IPV4 versus IPV6.

![Dados IPv4/IPV6 Transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Selecione Intervalo de datas para exibir dados de hoje/esta semana/este mês etc. ou insira datas personalizadas.
* Em seguida, clique em "ir" para garantir que a seleção seja atualizada.

<a id="considerations" class="xliff"></a>

## Considerações
Os relatórios só podem ser gerados com informações dos últimos 18 meses.


