<properties 
	pageTitle="Pré-carregar ativos em um ponto de extremidade da CDN do Azure" 
	description="Saiba como pré-carregar o conteúdo armazenado em cache em um ponto de extremidade da CDN." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# Pré-carregar ativos em um ponto de extremidade da CDN do Azure

Esse tutorial orienta você carregando previamente o conteúdo armazenado em cache em todos os nós de borda da CDN do Azure.

## Passo a passo

1. No [Portal do Azure](http://portal.azure.com), navegue até o perfil da CDN que contém o ponto de extremidade que você deseja pré-carregar. A folha do perfil é aberta.

2. Clique no ponto de extremidade na lista. A folha do ponto de extremidade é aberta.

3. Na folha do ponto de extremidade da CDN, clique no botão carregar.
	
	![Folha do ponto de extremidade da CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
	
	A folha Carregar é aberta.
	
	![Folha de carregamento da CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
	
4. Insira o caminho completo de cada ativo que você deseja carregar (por exemplo, */pictures/kitten.png*) na caixa de texto **Caminho**.

	> [AZURE.TIP]Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos. Para excluir ativos da lista, clique no botão de reticências (...).
	>
	> Os caminhos devem ser uma URL relativa. O asterisco (*) pode ser usado como curinga.
	
    ![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-paths.png)
    
5. Clique no botão **Carregar**.

	![Botão Carregar](./media/cdn-preload-endpoint/cdn-load-button.png)
	

## Consulte também
- [Limpar um ponto de extremidade da CDN do Azure](cdn-purge-endpoint.md)
- [Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0121_2016-->