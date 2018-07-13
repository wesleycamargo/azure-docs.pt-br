---
title: 'Pesquisa Personalizada do Bing: definir uma exibição personalizada | Microsoft Docs'
description: Descreve como criar um site e serviços de pesquisa vertical
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ffe3087df398d6310828e41d0c6992199fafbed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363859"
---
# <a name="configure-your-custom-search-experience"></a>Configurar a experiência de pesquisa personalizada
Uma instância de Pesquisa Personalizada permite adaptar a experiência de pesquisa para incluir conteúdo apenas de sites com os quais os usuários se importam. Em vez de realizar uma pesquisa em toda a Web, o Bing pesquisa apenas a parte da Web de interesse do usuário.
Para criar a exibição personalizada da Web, use o [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing. Para obter mais informações sobre como entrar no portal, consulte [Criar a primeira instância da Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). O portal permite criar uma instância de pesquisa que especifica os domínios, as subpáginas e páginas da Web que você quer que o Bing pesquise, e aqueles que você não quer que ele pesquise. Além de especificar as URLs do conteúdo que você conhece, também é possível informar ao portal para sugerir conteúdo que você queira adicionar à exibição. A seguir, são apresentadas as maneiras de como é possível definir uma fatia da Web: 

1.  Domínio. Uma fatia de domínio inclui todo o conteúdo localizado em um domínio da Internet. Por exemplo, www.microsoft.com. Omitir 'www' faz com que o Bing também pesquise os subdomínios do domínio. Por exemplo, se você especificar microsoft.com, o Bing também retornará resultados de support.microsoft.com ou technet.microsoft.com.
2.  Subpágina. Uma fatia de subpágina inclui todo o conteúdo localizado na subpágina e nos caminhos abaixo. É possível especificar no máximo duas subpáginas no caminho. Por exemplo, www.microsoft.com/en-us/windows/ 
3.  Página da Web. Uma fatia da página da Web pode incluir apenas essa página da Web em uma pesquisa personalizada. Opcionalmente, é possível especificar se quer incluir subpáginas.

Todos os domínios, subpáginas e páginas da Web que forem especificados devem ser públicos e indexados pelo Bing. Se você possui um site público que deseja incluir na pesquisa e o Bing não o indexou, consulte a [documentação de Webmasters](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) do Bing para obter detalhes sobre como obter o Bing para indexá-lo. Além disso, consulte a documentação do Webmaster para obter detalhes sobre como fazer com que o Bing atualize o site rastreado se o índice estiver desatualizado.

## <a name="adding-slices-to-your-custom-search"></a>Adicionar fatias à pesquisa personalizada
Ao definir a instância de pesquisa personalizada, você especifica as subpáginas, páginas da Web e os domínios bloqueados e ativos que deseja pesquisar ou não.  

- Ativo: uma lista de subpáginas, páginas da Web ou domínios para serem incluídos na pesquisa. 
- Bloqueado: uma lista de subpáginas, páginas da Web ou domínios a serem excluídos da pesquisa. Os itens que você bloqueia devem ser localizados nos domínios e subpáginas listados na lista Ativo.

Para acessar cada lista, clique nas guias Ativo e Bloqueado na instância de pesquisa personalizada. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Listas Ativo e Bloqueado 
Para especificar uma fatia da Web que você quer que o Bing pesquise, clique na guia **Ativo** e liste as subpáginas, páginas da Web e os domínios a serem pesquisados. É possível adicionar uma fatia diretamente à lista ou adicionar mais de uma fatia fazendo upload de um arquivo de texto usando o ícone de upload.

Detalhes de upload do arquivo: 

- O upload de arquivo está disponível apenas para adicionar fatias à lista Ativo e não é possível utilizá-lo para adicionar fatias à lista Bloqueado. 
- Crie um arquivo de texto e especifique um único domínio, subpágina ou página da Web por linha. O upload inteiro será rejeitado se ocorrer um erro. 
- Se a lista Bloqueado contiver o domínio, a subpágina ou página da Web que você especificou no arquivo de upload, o serviço removerá da lista Bloqueado e a adicionará à lista Ativo. 
- O serviço ignora as duplicatas no arquivo de upload.

Para editar ou excluir fatias, use as opções na coluna Controles. 

De modo semelhante, é possível adicionar fatias à lista Bloqueado (exceto se não for possível usar um arquivo de upload para especificar as fatias).

## <a name="pinned-list"></a>Lista fixada
O portal também permite fixar uma página específica na parte superior do resultado da pesquisa, se o usuário inserir um termo de pesquisa específico. A guia **Fixado** contém uma lista de termos de consulta e pares de páginas da Web que especificam a página da Web que aparece como o resultado principal de uma consulta específica. O termo de consulta do usuário deve corresponder exatamente ao termo da consulta fixada.
Para obter mais informações sobre como fixar resultados, consulte [Ajustar Classificação](#adjustrank).

A fixação de resultados não está disponível para a experiência de Pesquisa de Imagem.

## <a name="site-suggestions"></a>Sugestões de site
Após adicionar fatias à lista Ativo, o serviço gerará sugestões de site e subpágina que você pode querer adicionar à pesquisa. A seção **Você pode querer adicionar** contém as sugestões. A página de configurações de instância incluirá essa seção, somente se as sugestões estiverem disponíveis. 

Para adicionar sugestões à lista Ativo, clique no ícone +.  Como o serviço gera sugestões com base nas configurações, clique em **Atualizar** após adicionar as sugestões. 

## <a name="preview-pane"></a>Painel de Visualização
É possível testar a instância de pesquisa usando o painel de Visualização à direita para enviar consultas de pesquisa e exibir os resultados. Selecione **Minha Instância**, selecionar um filtro de pesquisa seguro e qual mercado pesquisar (consulte [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)). Insira uma consulta e pressione Enter ou clique no ícone de pesquisa para exibir os resultados da configuração atual. Para ver os resultados da Web, clique em **Web**, para ver os resultados da imagem, clique em **Imagem**. 

 Usando o painel de Visualização, também será possível analisar os resultados do Bing, selecionando **Bing** em vez de **Minha Instância**. Isso pode ser útil para comparar os resultados da sua experiência de pesquisa com os resultados retornados pelo Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Ajustar classificação
O portal permite ajustar a classificação para manipular os resultados que o Bing retorna. No painel de Visualização, insira um termo de pesquisa e execute a consulta. O painel de Visualização lista o resultado da pesquisa para a consulta. À direita de cada resultado está a lista de ajustes que podem ser feitos. 

- Bloquear. Mova o domínio, a subpágina ou a página da Web para a lista Bloqueado. Você seleciona o nível para bloquear. O Bing exclui o conteúdo do site selecionado nos resultados da pesquisa. 
- Aumentar. Aumenta o conteúdo do domínio ou da subpágina nos resultados da pesquisa. Você seleciona se quer aumentar o conteúdo do domínio ou da subpágina à qual a página da Web pertence.
- Rebaixar. Rebaixa o conteúdo do domínio ou subpágina ao nível abaixo nos resultados da pesquisa. Você seleciona se quer rebaixar o conteúdo do domínio ou da subpágina à qual a página da Web pertence. 
- Fixar no início. Defina a página da Web exibida na parte superior dos resultados, se o termo de consulta do usuário corresponder exatamente ao termo de consulta usado. A lista Ativo não precisa conter a página da Web para você fixá-la. 

A classificação de ajuste não está disponível para a experiência de Pesquisa de Imagem.

## <a name="boosting-and-demoting"></a>Aumento ou rebaixamento
É possível aumentar, superaumentar ou rebaixar qualquer domínio ou subpágina na lista Ativo. Por padrão, todas as fatias são adicionadas com o mesmo peso. Os itens que são Superaumentados ou Aumentados são classificados mais altos nos resultados da pesquisa (com classificação superaumentar maior que aumentar). Itens rebaixados são classificados abaixo nos resultados da pesquisa.

É importante observar que superaumentar, aumentar e rebaixar fornecem as respectivas variantes de peso para os domínios ou subpáginas. Esse é apenas um dos muitos sinais usados pelo classificador para determinar a ordem dos resultados. Isso significa que o efeito deles para uma consulta específica não é garantido, pois muitos outros fatores podem influenciar a classificação geral dos resultados da Web.  Para determinar o possível efeito que o aumento ou rebaixamento tem sobre o classificador, teste a experiência de pesquisa usando o painel de Visualização.

É possível superaumentar, aumentar ou rebaixar itens, usando os controles de Ajuste de Classificação na lista Ativo ou usando os controles Aumentar e Rebaixar no painel de Visualização. O serviço adiciona a fatia à lista Ativo e ajusta a classificação adequadamente.

As alterações de superaumentar, aumentar e rebaixar são salvas automaticamente e refletem instantaneamente no ponto de extremidade de pesquisa personalizado. 

Superaumentar, aumentar e rebaixar não estão disponíveis para a experiência de Pesquisa de Imagem.

## <a name="pin-to-top"></a>Fixar no início
Para fixar uma página da web na parte superior dos resultados da pesquisa para uma consulta específica, escolha uma das opções a seguir:

1.  Na guia Fixado, insira a URL da página da Web para fixar na parte superior dos resultados e a consulta exata que acionará a fixação. 
2.  No painel de Visualização, insira um termo de consulta e clique em pesquisar. Em seguida, identifique a página da Web nos resultados que você quer fixar na parte superior dos resultados, se o usuário inserir a mesma consulta. Em seguida, clique em Fixar no início. O serviço adiciona a página da Web e a consulta à lista Fixado. 

Você pode acompanhar as fixações na guia Fixado. As fixações são mostradas como pares de '\<consulta\>, \<página da Web\>'. 

A página da Web somente será fixada se a consulta do usuário corresponder exatamente à sua consulta. 

Para uma consulta específica, você pode fixar no máximo uma página da Web na parte superior dos resultados.

Os pinos não estão disponíveis para a experiência de Pesquisa de Imagem.

## <a name="use-bing-to-specify-slices"></a>Usar o Bing para especificar fatias
Há algumas maneiras de especificar as fatias da Web que compõem a pesquisa personalizada. Se você conhecer as fatias que quer incluir na instância, adicione-as à lista Ativo da instância. Para obter mais informações sobre como adicionar itens à lista Ativo, consulte [Listas Ativo e Bloqueado](#active-and-blocked-lists).
Mas, se não tiver certeza de quais fatias incluir você poderá executar as consultas do Bing no painel de Visualização e ver o que o Bing retorna. É possível selecionar as fatias que quer incluir na pesquisa personalizada. É provável que seja necessário executar vários termos de consulta para garantir a identificação de todas as fatias desejadas para a instância. 

Siga estas etapas para usar o Bing e adicionar fatias à instância da Pesquisa Personalizada. 
1.  Entre no [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing.
2.  Crie uma instância ou selecione uma instância para atualizar.
3.  No painel de Visualização, à direita, selecione Bing na lista suspensa.
4.  Na caixa de pesquisa, insira um termo da consulta que seja relevante para a instância.
5.  Clique em **Adicionar site** ao lado do resultado que você quer incluir.
6.  Clique no botão **Ok** .

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Exibir estatísticas
Se você inscreveu-se na Pesquisa Personalizada no nível apropriado (consulte as [Páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), uma guia **Estatísticas** será adicionada às instâncias de produção. A guia Estatísticas mostra detalhes sobre como os pontos de extremidade da Pesquisa Personalizada são usados, incluindo volume da chamada, principais consultas, distribuição geográfica, códigos de resposta e pesquisa segura. É possível filtrar detalhes usando os controles fornecidos.

## <a name="understanding-quota"></a>Entendendo a cota
- Para cada instância de pesquisa personalizada, o número máximo de ajustes de classificação que você pode fazer nas fatias **Ativo** e **Bloqueado** é limitado a 400.
- Adicionar uma fatia às guias Ativo ou Bloqueado conta como um ajuste de classificação.
- Aumentar e rebaixar contam como dois ajustes na classificação.
- Para cada instância de pesquisa personalizada, o número máximo de fixações que você pode fazer é limitado a 200.

## <a name="next-steps"></a>Próximas etapas

- [Chamar sua pesquisa personalizada](./search-your-custom-view.md)
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)