<properties
	pageTitle="CDN - Estatísticas em tempo real"
	description="Estatísticas em tempo real na CDN do Microsoft Azure. As Estatísticas em tempo real fornecem dados em tempo real sobre o desempenho da nossa CDN ao entregar conteúdo aos clientes."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Estatísticas em tempo real na CDN do Microsoft Azure

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## Visão geral

Este documento explica as estatísticas em tempo real na CDN do Microsoft Azure. Essa funcionalidade fornece dados em tempo real sobre o desempenho da nossa CDN ao entregar conteúdo aos clientes.

Os seguintes gráficos estão disponíveis ao exibir as estatísticas em tempo real para as plataformas baseadas em HTTP:

* [Largura de banda](#bandwidth)
* [Códigos de status](#status-codes)
* [Status do Cache](#cache-statuses)
* [Conexões](#connections)

> [AZURE.NOTE] Cada um dos gráficos acima exibe estatísticas em tempo real para um determinado período de tempo. Uma janela deslizante de dados é exibida depois que o tempo especificado tiver decorrido. Isso significa que os dados antigos serão removidos do gráfico para liberar espaço para novos dados. O período de tempo para esta janela deslizante pode ser definido pelo período de Tempo da opção de gráficos.

## Acessando as estatísticas em tempo real

1. Na folha do perfil CDN, clique no botão **Gerenciar**.

	![botão gerenciar da folha do perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

	O portal de gerenciamento da CDN é aberto.

2. Focalize a guia **Análise** e, em seguida, focalize o submenu **Estatísticas em Tempo Real**. Clique em **Plataforma Grande HTTP**.

	As opções de relatório são exibidas.

## Largura de banda

O gráfico de Largura de Banda exibe a quantidade de largura de banda usada para a plataforma atual durante um período de tempo especificado. A parte sombreada do gráfico indica o uso de largura de banda. A quantidade exata de largura de banda usada atualmente é exibida diretamente abaixo do gráfico de linha.

> [AZURE.NOTE] As unidades usadas para relatar o uso de largura de banda são um dos seguintes: bits por segundo (b/s), Quilobits por segundo (Kb/s), Megabits por segundo (Mb/s) ou Gigabits por segundo (Gb/s).

## Códigos de status

O gráfico dos Códigos de Status consiste em linhas codificadas por cores que indicam a frequência com a qual os códigos de resposta HTTP ocorrem durante um período de tempo especificado. O lado esquerdo do gráfico (eixo y) indica a frequência com a qual um código de status é retornado em relação às solicitações, enquanto a parte inferior do gráfico (eixo x) indica a progressão do tempo.

É exibida uma lista dos códigos de status diretamente acima do gráfico. Essa lista indica cada código de status que pode ser incluído no gráfico de linha e o número de ocorrências por segundo para esse código de status. Por padrão, uma linha é exibida para cada um destes códigos de status no gráfico. No entanto, você pode optar por monitorar apenas os códigos de status que têm um significado especial para a sua configuração de CDN. Isso pode ser feito somente marcando as opções de código de status desejadas e apagando todas as outras opções. Quando estiver satisfeito com os códigos de status que serão exibidos no gráfico, você deverá clicar em Atualizar Gráfico. Isso impedirá que os códigos de status desmarcados sejam incluídos no gráfico.

> [AZURE.NOTE] A opção **Atualizar Gráfico** apagará o gráfico. Depois disso, ele só exibirá os códigos de status selecionados.

Cada opção de código de status é descrita abaixo.

Nome | Descrição
-----|------------
Total de ocorrências por segundo | Determina se o número total de solicitações por segundo para a plataforma atual será exibido no gráfico. Você pode usar esta opção como um indicador de linha de base para ver o percentual do total de ocorrências em que consiste um código de status específico.
2xx por segundo | Determina se o número total de códigos de status 2xx (por exemplo, 200, 201, 202, etc.) que ocorrem por segundo para a plataforma atual será exibido no gráfico. Esse tipo de código de status indica que a solicitação foi enviada com êxito para o cliente.
304 por segundo | Determina se o número total de códigos de status 304 que ocorrem por segundo para a plataforma atual será exibido no gráfico. Esse código de status indica que o ativo solicitado não foi modificado desde a última vez em que foi recuperado pelo cliente HTTP.
3xx por segundo | Determina se o número total de códigos de status 3xx (por exemplo, 300, 301, 302, etc.) que ocorrem por segundo para a plataforma atual será exibido no gráfico. Essa opção exclui as ocorrências dos códigos de status 304. Esse tipo de código de status indica que a solicitação resultou em um redirecionamento.
403 por segundo | Determina se o número total de códigos de status 403 que ocorrem por segundo para a plataforma atual será exibido no gráfico. Esse código de status indica que a solicitação foi considerada não autorizada. Uma das possíveis causas desse código de status é quando um usuário não autorizado solicita um ativo protegido pela Autenticação Baseada em Token.
404 por segundo | Determina se o número total de códigos de status 404 que ocorrem por segundo para a plataforma atual será exibido no gráfico. Esse código de status indica que não foi possível encontrar o recurso solicitado.
4xx por segundo | Determina se o número total de códigos de status 4xx (por exemplo, 400, 401, 402, 405, etc.) que ocorrem por segundo para a plataforma atual será exibido no gráfico. Essa opção exclui as ocorrências de códigos de status 403 e 404. Esse código de status indica que o recurso solicitado não foi entregue ao cliente.
5xx por segundo | Determina se o número total de códigos de status 5xx (por exemplo, 500, 501, 502, etc.) que ocorrem por segundo para a plataforma atual será exibido no gráfico.
Outros por segundo | Determina se o total de ocorrências para todos os outros códigos de status será relatado no gráfico.

Você também pode optar por ocultar temporariamente os dados registrados para um código de status específico. Você pode fazer isso na área diretamente abaixo do gráfico desmarcando a opção de código de status desejada. O código de status selecionado será imediatamente oculto do gráfico. Marcar essa opção de código de status fará com que essa opção seja exibida novamente.

> [AZURE.NOTE] As opções codificadas por cores diretamente abaixo do gráfico afetam apenas o que é exibido no gráfico. Isso não afeta o fato de o gráfico de manter ou não o controle desse código de status.

## Status do Cache

O gráfico dos Status de Cache consiste em linhas codificadas por cores que indicam a frequência com a qual os status de cache ocorrem durante um período de tempo especificado. O lado esquerdo do gráfico (eixo y) indica a frequência com a qual um status de cache é retornado em relação às solicitações, enquanto a parte inferior do gráfico (eixo x) indica a progressão do tempo.

É exibida uma lista dos status do cache diretamente acima do gráfico. Essa lista indica cada status de cahce que pode ser incluído no gráfico de linha e o número de ocorrências por segundo para esse status de cache. Por padrão, uma linha é exibida para cada um destes status de cache no gráfico. No entanto, você pode optar por monitorar apenas os status de cache que têm um significado especial para a sua configuração de CDN. Isso pode ser feito somente marcando as opções de status de cache desejadas e apagando todas as outras opções. Quando estiver satisfeito com os status de cache que serão exibidos no gráfico, você deverá clicar em Atualizar Gráfico. Isso impedirá que os códigos de status desmarcados sejam incluídos no gráfico.

> [AZURE.NOTE] A opção **Atualizar Gráfico** apagará o gráfico. Depois disso, ele só exibirá os status de cache selecionados.

Você também pode optar por ocultar temporariamente os dados registrados para um código de resposta específico. Você pode fazer isso desmarcando a opção de código de resposta desejada na área diretamente abaixo do gráfico. O código de resposta selecionado será imediatamente oculto do gráfico. Marcar essa opção de código de resposta fará com que essa opção seja exibida novamente.

> [AZURE.NOTE] As opções codificadas por cores diretamente abaixo do gráfico afetam apenas o que é exibido no gráfico. Isso não afeta o fato de o gráfico de manter ou não o controle desse código de status.

## Conexões

Essa representação gráfica da média de conexões por minuto permite exibir quantas conexões foram estabelecidas para os seus servidores. Uma conexão consiste em cada solicitação de um ativo que é transmitida pela nossa CDN.

## Consulte também
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
* [Analisar o desempenho de borda](cdn-edge-performance.md)

<!---HONumber=AcomDC_0518_2016-->