<properties 
	pageTitle="Limpar um ponto de extremidade da CDN do Azure" 
	description="Saiba como limpar todo o conteúdo armazenado em cache de um ponto de extremidade da CDN." 
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
	ms.date="12/03/2015" 
	ms.author="casoper"/>
	
# Limpar um ponto de extremidade da CDN do Azure

## Visão geral 

Os nós de borda da CDN do Azure armazenarão ativos em cache até a TTL (vida útil) do ativo expirar. Depois que a TTL do ativo expira, quando um cliente solicita o ativo do nó de borda, o nó recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e atualizar o armazenamento do cache.

Às vezes, convém limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados. Isso pode ocorrer devido a atualizações do aplicativo Web ou para atualizar rapidamente ativos que contenham informações incorretas.

Este tutorial o orienta durante a limpeza de ativos de todos os nós de borda de um ponto de extremidade.

## Passo a passo

1. No [Portal do Azure](http://portal.azure.com), navegue até o perfil CDN que contém o ponto de extremidade que você deseja limpar.

2. Na folha do perfil da CDN, clique no botão Limpar.
	
	![Folha Perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	A folha Limpeza é aberta.
	
	![Folha Limpeza da CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. Na folha Limpeza, selecione o endereço do serviço que deseja limpar na lista suspensa de URLs.

	![Formulário de limpeza](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE]Você também pode acessar a folha Limpeza clicando no botão **Limpar** na folha de ponto de extremidade da CDN. Nesse caso, o campo **URL** será pré-populado com o endereço do serviço do ponto de extremidade específico.
	
4. Selecione os ativos que você deseja limpar dos nós de borda. Para limpar todos os ativos, clique na caixa de seleção **Limpar todos**. Caso contrário, digite o caminho completo de cada ativo que você deseja limpar (por exemplo, */imagens/gatinho.png*) na caixa de texto **Caminho**.

	> [AZURE.TIP]Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos. Para excluir ativos da lista, clique no botão de reticências (...).
	>
	> Os caminhos devem ser uma URL relativa. O asterisco (*) pode ser usado como curinga.
	
5. Clique no botão **Limpar**.

	![Botão Limpar](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## Consulte também
[Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0107_2016-->