<properties
	pageTitle="Pré-carregar ativos em um ponto de extremidade da CDN do Azure | Microsoft Azure"
	description="Saiba como pré-carregar o conteúdo armazenado em cache em um ponto de extremidade da CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Pré-carregar ativos em um ponto de extremidade da CDN do Azure

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por padrão, os ativos são armazenados primeiro em cache à medida que são solicitados. Isso significa que a primeira solicitação de cada região pode demorar mais tempo, pois os servidores de borda não terão o conteúdo armazenado em cache e precisarão encaminhar a solicitação para o servidor de origem. O pré-carregamento de conteúdo evita essa latência de primeiro acesso.

Além de fornecer uma experiência mais adequada ao cliente, o pré-carregamento de seus ativos em cache também pode reduzir o tráfego de rede no servidor de origem.

> [AZURE.NOTE] O pré-carregamento de ativos é útil para grandes eventos ou para conteúdo disponibilizado simultaneamente a um grande número de usuários, como o lançamento de um novo filme ou uma atualização de software.

Esse tutorial orienta você carregando previamente o conteúdo armazenado em cache em todos os nós de borda da CDN do Azure.

## Passo a passo

1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil da CDN que contém o ponto de extremidade que você deseja pré-carregar. A folha do perfil é aberta.

2. Clique no ponto de extremidade na lista. A folha do ponto de extremidade é aberta.

3. Na folha do ponto de extremidade da CDN, clique no botão carregar.

	![Folha do ponto de extremidade da CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

	A folha Carregar é aberta.

	![Folha de carregamento da CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Insira o caminho completo de cada ativo que você deseja carregar (por exemplo, `/pictures/kitten.png`) na caixa de texto **Caminho**.

	> [AZURE.TIP] Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos. Para excluir ativos da lista, clique no botão de reticências (...).
	>
	> Os caminhos devem ser uma URL relativa que atenda à seguinte [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`. Cada ativo deve ter seu próprio caminho. Não há nenhuma funcionalidade de curinga para pré-carregar ativos.

    ![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Clique no botão **Carregar**.

	![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Há uma limitação de 10 solicitações de carga por minuto para cada perfil CDN.

## Consulte também
- [Limpar um ponto de extremidade da CDN do Azure](cdn-purge-endpoint.md)
- [Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0803_2016-->