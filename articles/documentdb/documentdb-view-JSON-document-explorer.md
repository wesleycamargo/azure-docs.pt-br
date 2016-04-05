<properties
	pageTitle="Gerenciador de Documentos do Banco de Dados de Documentos para exibir JSON | Microsoft Azure"
	description="Saiba mais sobre o Gerenciador de Documentos do Banco de Dados de Documentos, uma ferramenta do Portal do Azure para exibir JSON, editar, criar e carregar documentos JSON com o Banco de Dados de Documentos, um banco de dados de documentos NoSQL."
    keywords="exibir json"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Exibir, editar, criar e carregar documentos JSON usando o Gerenciador de Documentos do Banco de Dados de Documentos

Este artigo fornece uma visão geral do Gerenciador de Documentos do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/), uma ferramenta do portal do Azure que o habilita a exibir, editar, criar, carregar documentos e filtrar JSON com o Banco de Dados de Documentos.

## Iniciar o Gerenciador de Documentos

1. No portal do Azure, na barra de navegação, clique em **Contas do Banco de Dados de Documentos**. Se **Contas do Banco de Dados de Documentos** não estiver visível, clique em **Procurar** e então clique em **Contas do Banco de Dados de Documentos**.

2. Na parte superior da folha **Conta do Banco de Dados de Documentos**, clique em **Gerenciador de Documentos**.
 
	![Captura de tela do comando do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)

 	>[AZURE.NOTE] O Gerenciador de Consultas também aparece nas folhas do banco de dados e da coleção.

    Na folha **Gerenciador de Documentos**, as listas suspensas **Bancos de dados** e **Coleções** são populadas com antecedência, dependendo do contexto no qual você tiver iniciado o Gerenciador de Consultas.

	![Captura de tela da folha Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

## Criar um documento

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).

2. Na folha **Gerenciador de Documentos**, clique em **Criar Documento**.

    Um trecho mínimo de JSON é fornecido na folha **Documento**.

	![Captura de tela da experiência de criação de documentos do Gerenciador de Documentos, onde você pode exibir JSON e editar JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)

2. Na folha **Documento**, digite ou cole o conteúdo do documento JSON que você deseja criar e clique em **Salvar** para confirmar o documento para o banco de dados e a coleção especificados na folha **Gerenciador de Documentos**.

	![Captura de tela do comando salvar do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Se você não fornecer uma propriedade de "id", o Gerenciador de Documentos adiciona automaticamente uma propriedade de ID e gera um GUID como o valor da ID.

    Se já tiver dados de arquivos JSON, MongoDB, SQL Server, arquivos CSV, armazenamento de Tabela do Azure, Amazon DynamoDB, HBase ou de outras coleções do Banco de Dados de Documentos, você poderá usar a [ferramenta de migração de dados](documentdb-import-data.md) do Banco de Dados de Documentos para importar rapidamente os dados.

## Editar um documento

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).

2. Para editar um documento existente, selecione-o na folha **Gerenciador de Documentos**, edite o documento na folha **Documento** e clique em **Salvar**.

    ![Captura de tela da funcionalidade de edição de documentos do Gerenciador de Documentos usada para exibir JSON](./media/documentdb-view-JSON-document-explorer/editdocument.png)

    Se você estiver editando um documento e decidir que quer descartar as edições feitas, basta clicar em **Descartar** na folha **Documento**, confirmar a ação de descarte e o estado anterior do documento será carregado novamente.

    ![Captura de tela do comando descartar do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## Excluir um documento

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).

2. Selecione o documento no **Gerenciador de Documentos**, clique em **Excluir** e confirme a exclusão. Após a confirmação, o documento será removido imediatamente da lista do Gerenciador de Documentos.

	![Captura de tela do comando excluir do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## Trabalhar com documentos JSON

Observe que o Gerenciador de Documentos valida que qualquer documento novo ou editado contém JSON válido. Você pode até exibir erros do JSON ao passar o mouse sobre a seção incorreta para obter detalhes sobre o erro de validação.

![Captura de tela do Gerenciador de Documentos com realce de JSON inválido](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

Além disso, o Gerenciador de Documentos impede que você salve um documento com conteúdo JSON inválido.

![Captura de tela do Gerenciador de Documentos com erro de salvamento de JSON inválido](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

Por fim, o Gerenciador de Documentos permite que você veja facilmente as propriedades de sistema do documento carregado clicando no comando **Propriedades**.

![Captura de tela da exibição de propriedades de documento do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [AZURE.NOTE] A propriedade do carimbo de data/hora (\_ts) é representada internamente como um tempo de época, mas o Gerenciador de Documentos exibe o valor em um formato GMT legível.

## Filtrar documentos
O Gerenciador de Documentos oferece suporte a várias opções de navegação e configurações avançadas

Por padrão, o Gerenciador de Documentos carrega os 100 primeiros documentos na coleção selecionada, por sua data de criação, do antigo ao mais recente. Você pode carregar documentos adicionais (em lotes de 100), selecionando a opção **Carregar mais** na parte inferior da folha do Gerenciador de Documentos. Você pode escolher quais documentos serão carregados por meio do comando **Filtrar**.

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).

2. Na parte superior da folha **Gerenciador de Documentos**, clique em **Filtrar**.

    ![Captura de tela das Configurações de Filtro do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
  
3.  As configurações de filtro aparecem abaixo da barra de comandos. Nas configurações do filtro, forneça uma cláusula WHERE e/ou uma cláusula ORDER BY e clique em **Filtrar**.

	![Captura de tela da folha Configurações do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)

	O Gerenciador de Documentos atualiza automaticamente os resultados com os documentos que correspondem à consulta de filtro. Leia mais sobre a gramática SQL do Banco de Dados de Documentos no artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) ou imprima uma cópia da [referência rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md).

    As caixas das listas suspensas **Banco de Dados** e **Coleção** podem ser usadas para alterar a coleção cujos documentos estão sendo exibidos sem precisar fechar e iniciar o Gerenciador de Documentos novamente.

    O Gerenciador de Documentos também dá suporte à filtragem do conjunto de documentos carregados atualmente segundo a propriedade de ID. Simplesmente digite na caixa Filtrar Documentos por id.

	![Captura de tela do Gerenciador de Documentos com filtro realçado](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)

	Os resultados da lista do Gerenciador de Documentos são filtrados com base nos critérios fornecidos.

	![Captura de tela do Gerenciador de Documentos com resultados filtrados](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)

	> [AZURE.IMPORTANT] A funcionalidade de filtro do Gerenciador de Documentos filtra somente o conjunto de documentos carregado ***atualmente*** e não consulta a coleção selecionada.

4. Para atualizar a lista de documentos carregada pelo Gerenciador de Documentos, clique em **Atualizar** na parte superior da folha.

	![Captura de tela do comando de atualização do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## Adicionar documentos em massa

O Gerenciador de Documentos dá suporte à ingestão em massa de um ou mais documentos JSON existentes, até 100 arquivos JSON por operação de upload.

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).

2. Para iniciar o processo de upload, clique em **Carregar Documento**.

	![Captura de tela da funcionalidade de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)

    A folha **Carregar Documento** será aberta.

2. Clique no botão Procurar para abrir uma janela do gerenciador de arquivos, selecione um ou mais documentos JSON a serem carregados e clique em **Abrir**.

	![Captura de tela do processo de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)

	> [AZURE.NOTE] Atualmente, o Gerenciador de Documentos dá suporte a até 100 documentos JSON por operação de carregamento.

3. Quando estiver satisfeito com sua seleção, clique no botão **Carregar**. Os documentos são adicionados automaticamente à grade do Gerenciador de Documentos e os resultados do carregamento são exibidos conforme o andamento da operação. Falhas na importação são informadas por arquivo individual.

	![Captura de tela dos resultados de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)

4. Após a conclusão da operação, você poderá selecionar até 100 documentos adicionais para upload.

## Trabalhar com documentos JSON fora do portal

O Gerenciador de Documentos no portal do Azure é apenas uma maneira de trabalhar com documentos no Banco de Dados de Documentos. Você também pode trabalhar com documentos usando a [API REST](https://msdn.microsoft.com/library/azure/mt489082.aspx) ou os [SDKs de cliente](documentdb-sdk-dotnet.md). Por obter um exemplo de código, consulte os [exemplos de documento do SDK do .NET](documentdb-dotnet-samples.md#document-examples) e os [exemplos de documento do SDK do Node.js](documentdb-nodejs-samples.md#document-examples).

Se você precisar importar ou migrar arquivos de outra fonte (arquivos JSON, MongoDB, SQL Server, arquivos CSV, armazenamento de Tabelas do Azure, Amazon DynamoDB ou HBase), poderá usar a [ferramenta de migração de dados](documentdb-import-data.md) do Banco de Dados de Documentos para importar rapidamente os dados para o Banco de Dados de Documentos.

## Próximas etapas

Para saber mais sobre a gramática SQL do Banco de Dados de Documentos com suporte no Gerenciador de Documentos, veja o artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) ou imprima a [referência rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md).

O [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) também é um recurso útil para orientar você à medida que for aprendendo a usar o Banco de Dados de Documentos.

<!---HONumber=AcomDC_0330_2016-->