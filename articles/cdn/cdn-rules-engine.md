<properties
	pageTitle="CDN - Substituindo o comportamento HTTP padrão usando o mecanismo de regras"
	description="O mecanismo de regras permite que você personalize a forma como as solicitações HTTP são manipuladas, como o bloqueio de entrega de certos tipos de conteúdo, definição de uma política de caching e modificação dos cabeçalhos HTTP."
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
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Substituir o comportamento HTTP padrão usando o mecanismo de regras

## Visão geral

O mecanismo de regras permite personalizar a forma como as solicitações HTTP são manipuladas, como o bloqueio de entrega de alguns tipos de conteúdo, definição de uma política de caching e modificação dos cabeçalhos HTTP. Este tutorial demonstrará a criação de uma regra que irá alterar o comportamento de caching dos ativos da CDN.

> [AZURE.NOTE] O mecanismo de regras é um recurso da camada Premium da CDN. Para obter uma comparação dos recursos de CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).

## Tutorial

1. Na folha do perfil CDN, clique no botão **Gerenciar**.

	![botão gerenciar da folha do perfil CDN](./media/cdn-rules-engine/cdn-rules-manage-btn.png)

	O portal de gerenciamento da CDN é aberto.

2. Clique na guia **HTTP Grande**, seguida do **Mecanismo de Regras**.

	São exibidas opções para uma nova regra.

	![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)

3. Insira um nome na caixa de texto **Nome/Descrição**.

4. Identifique o tipo de solicitações às quais a regra será aplicada. Por padrão, a condição de correspondência **Sempre** é selecionada. Você usará **Sempre** para este tutorial; portanto, deixe essa opção marcada.

	![Condição de correspondência CDN](./media/cdn-rules-engine/cdn-request-type.png)

	>[AZURE.TIP] Há vários tipos de condições de correspondência disponíveis no menu suspenso. Clicar no ícone de informação azul à esquerda da condição de correspondência explicará a condição atualmente selecionada em detalhes.
	>
	>Para obter a lista completa de condições de correspondência em detalhes, veja [Detalhes de recursos e condição de correspondência do mecanismo de regras](cdn-rules-engine-details.md#match-conditions).

5.  Clique no botão **+** próximo aos **Recursos** para adicionar um novo recurso. Na lista suspensa à esquerda, selecione **Force Internal Max-Age**. Na caixa de texto que aparece, digite **300**. Mantenha os valores padrão restantes.

	![Recurso CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE] Assim como ocorre com condições de correspondência, clicando no ícone de informação azul à esquerda do novo recurso para exibir detalhes sobre esse recurso. No caso de **Force Internal Max-Age**, estamos substituindo os cabeçalhos **Cache-Control** e **Expires** do ativo para controlar quando o nó de borda da CDN atualizará o ativo da origem. Nosso exemplo de 300 segundos significa que o nó de borda da CDN armazenará em cache o ativo por 5 minutos antes de atualizar o ativo de sua origem.
	>
	>Para obter a lista completa de recursos em detalhes, veja [Detalhes de recursos e condição de correspondência do mecanismo de regras](cdn-rules-engine-details.md#features).

6.  Clique no botão **Adicionar** para salvar a nova regra. A nova regra agora está aguardando aprovação. Depois de aprovado, o status será alterado de **XML Pendente** para **XML Ativo**.

## Considerações

- A ordem na qual são listadas várias regras afeta como elas são manipuladas. Uma regra subsequente poderá substituir as ações especificadas por uma regra anterior.

## Consulte também
* [Condição de correspondência do mecanismo de regras e detalhes do recurso](cdn-rules-engine-details.md)
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
* [Analisar o desempenho de borda](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->