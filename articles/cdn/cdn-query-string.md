<properties 
	pageTitle="CDN – controlando o comportamento do cache de solicitações com cadeias de caracteres de consulta" 
	description="O cache da cadeia de caracteres de consulta CDN controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta." 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

#Controlando o comportamento do cache de solicitações CDN com cadeias de caracteres de consulta

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##Visão geral

O cache da cadeia de caracteres de consulta controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta.

> [AZURE.NOTE]As camadas CDN Standard e Premium fornecem a mesma funcionalidade de cache de cadeia de consulta, mas a interface do usuário varia. Este documento descreve a interface do usuário da camada **Standard**. Para a camada Premium, confira [Controlando o comportamento de cache de solicitações CDN com cadeias de caracteres de consulta - Premium](cdn-query-string-premium.md).

Existem três modos disponíveis:

- **Ignorar cadeias de caracteres de consulta**: esse é o modo padrão. O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e deixará o ativo em cache. Todas as solicitações subsequentes para esse ativo que são atendidas a partir do nó de borda ignorarão a cadeia de caracteres de consulta até que o ativo em cache expire.
- **Ignorar o cache de URL com cadeias de caracteres de consulta**: nesse modo, as solicitações com cadeias de caracteres de consulta não estão armazenadas em cache no nó de borda da CDN. O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
- **Cada URL exclusiva de cache**: esse modo trata cada solicitação com uma cadeia de caracteres de consulta como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta da origem de uma solicitação de *foo.ashx?q=bar* seria armazenada em cache no nó de borda e retornada para os caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação de *foo.ashx?q=somethingelse* será armazenada em cache como um ativo separado com seu próprio tempo de vida.
	
	>[AZURE.WARNING]Esse modo não deve ser usado quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados com cada solicitação, como uma ID de sessão ou um nome de usuário, pois isso resultaria em um cache muito baixo taxa de acertos.

##Alterando as configurações de cache da cadeia de consulta

1. Na folha Perfil CDN, clique no ponto de extremidade da CDN que deseja gerenciar.
	
	![Pontos de extremidade da folha Perfil CDN](./media/cdn-query-string/cdn-endpoints.png)

	A folha do ponto de extremidade da CDN se abre.

2. Clique no botão **Configurar**.

	![botão gerenciar da folha Perfil CDN](./media/cdn-query-string/cdn-config-btn.png)
	
	A folha Configuração da CDN se abre.
	
3. Selecione uma configuração da lista suspensa **Comportamento do cache da cadeia de consulta**.
	
	![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string/cdn-query-string.png)
	
4. Após fazer suas seleções, clique no botão **Salvar**.




	

<!---HONumber=AcomDC_1203_2015-->