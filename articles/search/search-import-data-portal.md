<properties
	pageTitle="Importar dados para a Pesquisa do Azure usando o portal | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Como carregar dados em um índice na Pesquisa do Azure usando o portal"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importar dados para a Pesquisa do Azure usando o portal
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

O Portal do Azure inclui um comando **Importar Dados** no painel da Pesquisa do Azure que orienta você na ingestão de dados na Pesquisa do Azure. O comando depende do recurso de indexadores internos que rastreia a fonte de dados existente, criando e carregando documentos com base no conjunto de linhas encontrado na fonte de dados.

Usando o assistente, a importação de dados é uma construção com 3 partes:

- uma conexão de fonte de dados
- um índice de destino para o qual os dados são carregados (com frequência, o assistente poderá gerar isso para você)
- uma agenda que é executada agora ou em intervalos regulares

Para usar um indexador ou o comando **Importar Dados**, a fonte de dados primária deverá ser uma das fontes de dados com suporte: Banco de Dados SQL do Azure, bancos de dados relacionais do SQL Server em uma VM do Azure ou o Banco de Dados de Documentos do Azure.

Você só pode importar de uma única tabela, exibição ou estrutura de dados equivalente. Talvez seja necessário criar primeiro essa estrutura de dados na fonte de dados do aplicativo para enviar os metadados e as entradas de dados corretos para seu índice de pesquisa.

##Configurar a importação de dados

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Aqui estão algumas maneiras para localizar o painel.
	- Na barra de navegação rápida, clique em **Home**. A home page conta com blocos para cada serviço em sua assinatura. Clique no bloco para abrir o painel de serviços.
	- Na barra de navegação, clique em **Procurar tudo** > **Filtrar por** > **Serviços de Pesquisa** para localizar o serviço de Pesquisa na lista.

3. No painel do serviço, você verá uma barra de comandos na parte superior, incluindo uma para **Importar Dados**. Clique em **Importar Dados** para deslizar e abrir a folha Importar Dados.

4. Clique em **Conectar a seus dados** para especificar uma definição de fonte de dados usada por um indexador. As opções incluem:
	- 	A fonte de dados existente se refere a uma definição de fonte de dados criada anteriormente para um indexador. Se você já tiver indexadores definidos em seu serviço de pesquisa, poderá criar uma nova finalidade para uma definição de fonte de dados para outra importação.
	- 	O SQL do Azure é usado para especificar uma conexão de fonte de dados para um banco de dados SQL no Azure ou um banco de dados do SQL Server em uma VM do Azure. 
	- 	O Banco de Dados de Documentos é usado para especificar uma conexão de fonte de dados para esse tipo de fonte de dados. 

   Para o SQL do Azure e o Banco de Dados de Documentos, o banco de dados deverá existir em sua assinatura. Você pode colar em uma cadeia de conexão se ela for conhecida ou escolher uma fonte de dados criada anteriormente por alguém com privilégios de gravação para sua assinatura.

5. Clique em **Personalizar índice de destino** para concluir o índice padrão.
	- A **Chave** é obrigatória. O campo selecionado para a chave deve ser um campo de cadeia de caracteres com valores exclusivos.
	- O nome e o tipo do campo geralmente são preenchidos para você. Você pode alterar o tipo de dados.
	- Selecione os atributos de cada campo:
		- Recuperável retorna o campo nos resultados da pesquisa.
		- Filtrável permite que o campo seja referenciado em expressões de filtro.
		- Classificável permite que o campo seja usado em uma classificação.
		- Com faceta habilita o campo para a navegação com faceta.
		- Pesquisável habilita a pesquisa de texto completo.
	- Clique na guia **Analisador** se desejar especificar um analisador de idiomas no nível do campo. Confira [Criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.
	- Clique no **Encarregado da sugestão** se desejar habilitar as sugestões de preenchimento automático ou sugestões de consulta.

6. Clique em **Importar seus dados** para executar a operação de importação usando a opção Executar Agora ou configure um agendamento recorrente.

A operação de importação de dados que você acabou de preencher criou um indexador em segundo plano. Agora você pode editar o indexador diretamente para alterar qualquer um dos seus componentes.
	
##Editar um indexador existente

No painel do serviço, clique duas vezes no bloco Indexador para deslizar uma lista de todos os indexadores criados para sua assinatura. Clique duas vezes em um dos indexadores para executá-lo, editá-lo ou excluí-lo.

<!---HONumber=Nov15_HO3-->