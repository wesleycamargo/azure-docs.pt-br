<properties
	pageTitle="Criar um painel personalizado no Log Analytics | Microsoft Azure"
	description="Este guia ajuda a entender como os Painéis do Log Analytics podem visualizar todas as pesquisas de log salvas, dando a você uma lente única para exibir seu ambiente."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Criar um painel personalizado no Log Analytics

Este guia ajuda a entender como os Painéis do Log Analytics podem visualizar todas as pesquisas de log salvas, dando a você uma lente única para exibir seu ambiente.

![Painel de Exemplo](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## Como crio meu painel?

Para começar, vá para a Visão Geral do OMS clicando no botão Visão Geral na navegação à esquerda. Você verá o bloco "Meu Painel" à esquerda. Clique para fazer uma busca detalhada do painel.

![Visão geral](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## Adicionando um bloco

Nos painéis, os blocos são ativados pelas pesquisas de log salvas. O OMS vem com muitas pesquisas de log salvas previamente, portanto, você pode começar imediatamente. Você verá a seguinte ilustração explicando como começar.

![Ilustração](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

Na exibição Meu Painel, basta clicar na engrenagem 'personalizar' na parte inferior da página para entrar no modo de personalização. O painel que é aberto no lado direito da página mostra todas as pesquisas de log salvas do seu espaço de trabalho.

![Adicionar Blocos 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

Para visualizar uma pesquisa de log salva como um bloco, simplesmente arraste-a para o espaço vazio à esquerda. Quando você arrastar, ela se transformará em um bloco.

![Adicionar Blocos 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![Adicionar Blocos 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## Editar um bloco

Na exibição Meu Painel, basta clicar na engrenagem 'personalizar' na parte inferior da página para entrar no modo de personalização. Clique no bloco que você deseja editar. O painel direito muda para editar e fornece uma seleção de opções:

![Editar Bloco](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### Visualizações do bloco#
Há dois tipos de visualizações do bloco para escolher:

|tipo de gráfico|o que ele faz|
|---|---|
|![Gráfico de Barras](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|Exibe uma linha do tempo dos resultados salvos da pesquisa de log ou uma lista de resultados por campo, dependendo de se sua pesquisa de log agrega os resultados por campo ou não.
|![métrica](./media/log-analytics-dashboards/oms-dashboards-metric.png)|Exibe as visitas totais dos resultados da pesquisa de log como um número em um bloco. Os blocos da métrica permitem que você defina um limite que destacará o bloco quando o limite for atingido.|

### Limite
Você pode criar um limite em um bloco usando a visualização Métrica. Selecione para criar um valor de limite no bloco. Escolha se é para destacar o bloco quando o valor fica acima ou abaixo do limite escolhido, então, defina o valor de limite abaixo.

## Organizando o painel
Para organizar seu painel, navegue para a exibição Meu Painel e clique na engrenagem 'personalizar' na parte inferior da página para entrar no modo de personalização. Clique e arraste o bloco que você deseja mover e mova-o para onde deseja que fique.

![Organizar seu Painel](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## Remover um bloco
Para remover um bloco, navegue para a exibição Meu Painel e clique na engrenagem **personalizar** na parte inferior da página para entrar no modo de personalização. Selecione o bloco que deseja remover e clique em **Remover Bloco** no painel direito.

![Remover um Bloco](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## Próximas etapas

- Crie alertas no Log Analytics para gerar notificações e para corrigir problemas.

<!---HONumber=AcomDC_0504_2016-->