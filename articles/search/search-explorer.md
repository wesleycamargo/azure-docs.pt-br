<properties
	pageTitle="Consultar um índice de Pesquisa do Azure usando o Gerenciador de Pesquisa no Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="O Gerenciador de Pesquisa é uma abordagem sem código para consultar um índice de Pesquisa do Azure no Portal do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Consultar um índice de Pesquisa do Azure usando o Gerenciador de Pesquisa no Portal do Azure
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

O **Gerenciador de Pesquisa** é uma ferramenta de consulta interna do Portal do Azure para consultas sem código em um índice de Pesquisa do Azure. Ele se conecta a qualquer índice em seu serviço e fornece uma caixa de pesquisa para a inserção de expressões e cadeias de caracteres de pesquisa. A sintaxe de consulta válida é gerada com base na entrada que você fornece. Os resultados são exibidos na página do portal.

O Gerenciador de Pesquisa é ideal para aprender a sintaxe de consulta, executar consultas ad hoc ocasionais ou refinar uma expressão de consulta antes de tentar colocá-la no código. Para usá-lo, você já deve ter um serviço de Pesquisa do Azure e um índice. Confira [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) e [Importar dados para a Pesquisa do Azure usando o portal](search-import-data-portal.md) para obter ajuda com essas tarefas.

## Abrir o Gerenciador de Pesquisa

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação rápida, clique em **Home**. A home page conta com blocos para cada serviço em sua assinatura. Clique no bloco para abrir o painel de serviços.
	- Na barra de navegação rápida, clique em **Procurar Tudo** > **Filtrar por** > **Serviços de pesquisa** para localizar o serviço Pesquisa na lista.

3. No painel do serviço, você verá uma barra de comandos na parte superior, incluindo uma para o **Gerenciador de Pesquisa**.

  	![][1]

4. Clique em **Gerenciador de Pesquisa** para abrir a folha do Gerenciador de Pesquisa.
5. Defina o índice e a versão da API. O Gerenciador de Pesquisa se conecta automaticamente ao primeiro índice na lista de índice, mas você pode clicar em **Alterar Índice** para alternar para outro. **Definir versão da API** permite que você especifique versões com disponibilidade geral ou versões de visualização. Algumas sintaxes de consulta são apenas para visualização.
6. Se você seguiu a [Introdução à Pesquisa do Azure](search-get-started-portal.md) para criar e popular um índice com base em dados do USGS (Serviço Geológico dos Estados Unidos) de Rhode Island, pode usar esse termo de pesquisa para verificar se os mesmos três resultados são retornados no Gerenciador de Pesquisa: `roger williams +school -building`

Observe a sintaxe de consulta que é gerada automaticamente em resposta à entrada do termo de pesquisa.

![][2]

## Próximas etapas

Mais informações sobre a sintaxe de consulta e exemplos podem ser encontradas em [Pesquisar Documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx) no MSDN.

Consulte estes links para obter abordagens sem código adicionais para criar ou gerenciar um serviço de pesquisa ou índice:

- [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md)
- [Importar dados para a Pesquisa do Azure usando o portal](search-import-data-portal.md)
- [Gerencie seu serviço de Pesquisa no Azure](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->