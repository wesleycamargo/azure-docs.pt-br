<properties
	pageTitle="Importar dados para a Pesquisa do Azure usando indexadores no Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Use o Assistente de Dados de Importação do Azure Search no Portal do Azure para rastrear dados do Armazenamento de Blobs do Azure, armazenamento de tabela, Banco de Dados SQL e SQL Server em VMs do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist"/>

# Importar dados para a Pesquisa do Azure usando o portal

O Portal do Azure fornece um assistente de **Importação de Dados** no painel do Azure Search para carregar os dados em um índice.

  ![Importar Dados na barra de comandos][1]

Internamente, o assistente configura e invoca um *indexador*, automatizando várias etapas do processo de indexação:

- Conexão a uma fonte de dados externa na assinatura atual do Azure
- Geração automática de um esquema de índice com base na estrutura de dados de origem
- Criação de documentos com base em um conjunto de linhas recuperado da fonte de dados
- Upload de documentos no índice em seu serviço de pesquisa

Você pode experimentar este fluxo de trabalho usando dados de exemplo no DocumentDB. Visite [Introdução ao Azure Search no Portal do Azure](search-get-started-portal.md) para obter instruções.

## Fontes de dados com suporte do Assistente para Importação de Dados

A automação e as ferramentas de indexação estão disponíveis para as seguintes fontes de dados:

- Banco de Dados SQL Azure
- Dados relacionais do SQL Server em uma VM do Azure
- Banco de Dados de Documentos do Azure
- Armazenamento de Blobs do Azure (em versão de visualização)
- Armazenamento de Tabelas do Azure (em versão de visualização)

Um conjunto de dados bidimensional é uma entrada exigida. Você só pode importar de uma única tabela, exibição do banco de dados ou estrutura de dados equivalente. Você deve criar essa estrutura de dados antes de executar o assistente.

Observe que algumas indexadores ainda estão em versão de visualização, isso significa que a definição do indexador é amparada pela versão de visualização da API. Consulte [Visão geral do indexador](search-indexer-overview.md) para saber mais e obter links.

## Conectar aos seus dados

1. Entre no [Portal do Azure](https://portal.azure.com) e abra o painel de serviço. Você pode clicar em **Serviços de pesquisa** na barra de salto para mostrar os serviços existentes na assinatura atual.

2. Clique em **Importar Dados** na barra de comandos para deslizar e abrir a folha Importar Dados.

3. Clique em **Conectar a seus dados** para especificar uma definição de fonte de dados usada por um indexador. Para fontes de dados dentro da assinatura, normalmente o assistente pode detectar e ler as informações de conexão, reduzindo os requisitos gerais de configuração.

| | |
|--------|------------|
|**Fonte de dados existente** | Se já houver indexadores definidos em seu serviço de pesquisa, selecione uma definição de fonte de dados existente para outra importação.|
|**Banco de Dados SQL do Azure** | É possível especificar o nome do serviço, credenciais para um usuário de banco de dados com permissão de leitura e um nome de banco de dados na página ou por meio de uma cadeia de conexão do ADO.NET. Escolha a opção de cadeia de conexão para exibir ou personalizar as propriedades. <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção.|
|**SQL Server em VM do Azure** | Especifique um nome de serviço totalmente qualificado, a ID e a senha de usuário e um banco de dados como uma cadeia de conexão. Para usar esta fonte de dados, você deve ter instalado um certificado no repositório local que criptografa a conexão. <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção.
|**Banco de Dados de Documentos** |Os requisitos incluem a conta, o banco de dados e a coleção. Todos os documentos na coleção serão incluídos no índice. Você pode definir uma consulta para nivelar ou filtrar o conjunto de linhas, ou para detectar documentos alterados para operações de atualização de dados subsequentes.|
|**Armazenamento de Blobs do Azure** | Os requisitos incluem a conta de armazenamento e um contêiner. Como opção, se os nomes de blob seguirem uma convenção de nomenclatura virtual para fins de agrupamento, você poderá especificar a parte do diretório virtual do nome como uma pasta no contêiner. Confira [Indexação do Armazenamento de Blobs (visualização)](search-howto-indexing-azure-blob-storage.md) para saber mais. |
|**Armazenamento de Tabelas do Azure** | Os requisitos incluem a conta de armazenamento e um nome de tabela. Como opção, você pode especificar uma consulta para recuperar um subconjunto das tabelas. Confira [Indexação do Armazenamento de Tabelas (visualização)](search-howto-indexing-azure-tables.md) para saber mais. |

## Personalizar o índice de destino

Normalmente, um índice preliminar é inferido do conjunto de dados. Adicionar, editar ou excluir campos para concluir o esquema. Além disso, defina os atributos no nível do campo para determinar seus comportamentos de pesquisa subsequentes.

1. Em **Personalizar índice de destino**, especifique o nome e uma **chave** usados para identificar exclusivamente cada documento. A Chave deve ser uma cadeia de caracteres. Se os valores de campo incluem espaços ou traços, defina as opções avançadas em **Importar seus dados** para suprimir a verificação de validação para esses caracteres.

2. Analise e revise os campos restantes. O nome e o tipo do campo geralmente são preenchidos para você. Você pode alterar o tipo de dados.

3. Defina os atributos de índice de cada campo:

 - Recuperável retorna o campo nos resultados da pesquisa.
 - Filtrável permite que o campo seja referenciado em expressões de filtro.
 - Classificável permite que o campo seja usado em uma classificação.
 - Com faceta habilita o campo para a navegação com faceta.
 - Pesquisável habilita a pesquisa de texto completo.
  
4. Clique na guia **Analisador** se desejar especificar um analisador de idiomas no nível do campo. Somente os analisadores de idioma podem ser especificados no momento. O uso de um analisador personalizado ou de um analisador que não é de linguagem, como o Keyword, o Pattern etc., exige código.

 - Clique em **Pesquisável** para designar a pesquisa de texto completo no campo e habilitar a lista suspensa Analisador.
 - Escolha o analisador que quiser. Confira [Criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.

5. Clique em **Sugestor** para habilitar o preenchimento automático de sugestões de consulta nos campos selecionados.


## Importar seus dados

1. Em **Importar seus dados**, forneça um nome para o indexador. Lembre-se de que o produto do assistente para Importação de Dados é um indexador. Posteriormente, se você quiser exibir ou editá-lo, selecione-o no portal em vez de executar novamente o assistente.

2. Especifique o cronograma, que tem base no fuso horário regional no qual o serviço é provisionado.

3. Defina opções avançadas para especificar os limites de continuação da indexação no caso de um documento ser descartado. Além disso, você pode especificar se os campos **Chave** podem conter espaços e barras.

## Editar um indexador existente

No painel do serviço, clique duas vezes no bloco Indexador para deslizar uma lista de todos os indexadores criados para sua assinatura. Clique duas vezes em um dos indexadores para executá-lo, editá-lo ou excluí-lo. Você pode substituir o índice por outro existente, alterar a fonte de dados e definir opções para os limites de erro durante a indexação.

## Editar um indexador existente

Na Pesquisa do Azure, as atualizações estruturais em um índice exigirão uma recompilação desse índice, que consiste em excluir o índice, recriá-lo e recarregar os dados. As atualizações estruturais incluem alterar um tipo de dados e renomear ou excluir um campo.

As edições que não exigem a recriação incluem adicionar um novo campo, alterar os perfis de pontuação, mudar as sugestões ou alterar os analistas de linguagem. Consulte [Atualizar Índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) para obter mais informações.

## Próxima etapa

Consulte estes links para saber mais sobre os indexadores:

- [Indexação do Banco de Dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexação do DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indexação do Armazenamento de Blobs (preview)](search-howto-indexing-azure-blob-storage.md)
- [Indexação do Armazenamento de Tabelas (preview)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

<!---HONumber=AcomDC_0907_2016-->