<properties
	pageTitle="CDN – Controlando o comportamento do cache de solicitações com cadeias de caracteres de consulta - Premium"
	description="O caching da cadeia de caracteres de consulta CDN controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta."
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

#Controlando o comportamento do cache de solicitações CDN com cadeias de caracteres de consulta - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##Visão geral

O cache da cadeia de caracteres de consulta controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta.

> [AZURE.NOTE] As camadas Standard e Premium CDN fornecem a mesma funcionalidade de cache de cadeia de consulta, mas é diferente da interface do usuário. Este documento descreve a interface do usuário da camada **Premium**. Para a camada Padrão, consulte [Controlando o comportamento do cache de solicitações CDN com cadeias de caracteres de consulta](cdn-query-string.md).

Existem três modos disponíveis:

- **cache-padrão**: este é o modo padrão. O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e ocultará o ativo. Todas as solicitações subsequentes para esse ativo que são atendidas a partir do nó de borda ignorarão a cadeia de caracteres de consulta até que o ativo em cache expire.
- **sem cache**: nesse modo, solicitações com cadeias de caracteres de consulta não estão em cache no nó de borda CDN. O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
- **cache exclusivo**: este modo trata cada solicitação com uma cadeia de caracteres de consulta como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta da origem de uma solicitação de *foo.ashx?q=bar* seria armazenada em cache no nó de borda e retornaria para os caches subsequentes com essa mesma cadeia de caracteres de consulta. Uma solicitação de *foo.ashx?q=somethingelse* será armazenada em cache como um ativo separado com seu próprio tempo de vida.

	>[AZURE.WARNING] Esse modo não deve ser usado quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados com cada solicitação, como uma ID de sessão ou um nome de usuário, pois isso resultaria em um cache muito baixo taxa de acertos.

##Alterando as configurações de cache da cadeia de consulta

1. Na folha do perfil CDN, clique no botão **Gerenciar**.

	![Botão gerenciar folha do Perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

	O portal de gerenciamento da CDN é aberto.

2. Passe o ponteiro do mouse sobre a guia **HTTP Grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Cache de Cadeia de Caracteres de Consulta**.

	As opções de cache de cadeia de caracteres de consulta são exibidas.

	![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)

3. Após fazer as seleções, clique no botão Atualizar.

<!---HONumber=AcomDC_0302_2016-->