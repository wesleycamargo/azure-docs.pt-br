<properties
	pageTitle="Gerenciador de Script do Banco de Dados de Documentos, um editor de JavaScript | Microsoft Azure"
	description="Saiba mais sobre o Gerenciador de Script do Banco de Dados de Documentos, uma ferramenta do Portal do Azure que permite gerenciar artefatos de programação do Banco de Dados de Documentos no lado do servidor, incluindo procedimentos armazenados, gatilhos e funções definidas pelo usuário."
	keywords="editor de JavaScript"
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
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="anhoh"/>

# Criar e executar procedimentos armazenados, gatilhos e funções definidas pelo usuário usando o Gerenciador de Script do Banco de Dados de Documentos

Este artigo fornece uma visão geral do Gerenciador de Script do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/), um editor de JavaScript no portal do Azure que permite exibir e executar artefatos de programação do Banco de Dados de Documentos no lado do servidor, incluindo procedimentos armazenados, gatilhos e funções definidas pelo usuário. Leia mais sobre programação no lado do servidor do Banco de Dados de Documentos no artigo [Procedimentos armazenados, disparadores de banco de dados e UDFs](documentdb-programming.md).

## Iniciar Gerenciador de Script

1. No portal do Azure, na barra de navegação, clique em **DocumentDB (NoSQL)**. Se **Contas do DocumentDB** não estiver visível, clique em **Mais Serviços** e clique em **DocumentDB (NoSQL)**.

2. No menu de recursos, clique em **Gerenciador de Scripts**.

	![Captura de tela do comando do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    As caixas das listas suspensas **Banco de dados** e **Coleção** são preenchidas previamente dependendo do contexto em que você inicia o Gerenciador de Script. Por exemplo, se você iniciá-lo por meio de uma lâmina de banco de dados, o banco de dados atual será preenchido. Se iniciá-lo por meio de uma lâmina de coleção, a coleção atual será preenchida.

4.  Use as caixas das listas suspensas **Banco de dados** e **Coleção** para alterar a coleção cujos scripts estejam sendo exibidos sem precisar fechar e iniciar o Gerenciador de Script novamente.

5. O Gerenciador de Script também dá suporte à filtragem do conjunto de scripts carregado atualmente segundo a propriedade de ID. Digite na caixa de filtro e os resultados da lista do Gerenciador de Script são filtrados com base nos critérios fornecidos.

	![Captura de tela do Gerenciador de Script com resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT] A funcionalidade de filtro do Gerenciador de Script filtra somente o conjunto de scripts carregado ***atualmente*** e não atualiza a coleção selecionada no momento.

5. Para atualizar a lista de scripts carregada pelo Gerenciador de Script, basta clicar no comando **Atualizar** no topo da folha.

	![Captura de tela do comando de atualização do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Criar, exibir e editar procedimentos armazenados, gatilhos e funções definidas pelo usuário

O Gerenciador de Script permite que você execute facilmente operações de CRUD em artefatos de programação do Banco de Dados de Documentos do lado do servidor.

- Para criar um script, basta clicar no comando de criação aplicável no gerenciador de scripts , fornecer uma id, inserir o conteúdo do script e clicar em **Salvar**.

	![Captura de tela da opção Criar do Gerenciador de Script mostrando o editor de JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Ao criar um disparador, você também deve especificar a operação e o tipo de gatilho

	![Captura de tela da opção de disparo de do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Para exibir um script, basta clicar no script no qual você está interessado.

	![Captura de tela da experiência de exibição de script do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Para editar um script, basta fazer as alterações desejadas no editor de JavaScript editor e clicar em **Salvar**.

	![Captura de tela da experiência de exibição de script do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Para descartar alterações pendentes em um script, basta clicar no comando **Descartar**.

	![Captura de tela da experiência de descartar alterações do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- Por fim, o Gerenciador de Script também permite que você exiba facilmente as propriedades de sistema do script carregado no momento clicando no comando **Propriedades**.

	![Captura de tela da exibição de propriedades do script do Gerenciador de Script](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE] A propriedade (\_ts) de carimbo de data/hora é representada internamente como tempo de época, mas o Gerenciador de Script exibe o valor em um formato GMT legível.

- Para excluir um script, selecione-o no Gerenciador de Script e clique no comando **Excluir**.

	![Captura de tela do comando excluir do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Confirme a ação de exclusão clicando em **Sim** ou cancele a ação de exclusão clicando em **Não**.

	![Captura de tela do comando excluir do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## Executar um procedimento armazenado

> [AZURE.WARNING] A execução de procedimentos armazenados no Gerenciador de Script ainda não tem suporte para coleções particionadas do lado do servidor. Para saber mais, visite [Particionar e dimensionar dados no DocumentDB](documentdb-partition-data.md).

O Gerenciador de Script permite que você execute os procedimentos armazenados no servidor usando o portal do Azure.

- Quando abrir uma nova folha Criar procedimento armazenado, um script padrão (*prefixo*) já será fornecido. Para executar o script *prefixo* ou seu próprio script, adicione uma *id* e *entradas*. Para procedimentos armazenados que aceitam vários parâmetros, todas as entradas devem estar em uma matriz (por exemplo, *["foo", "bar"]*).

	![Captura de tela da folha Gerenciador de Script para adicionar entrada e executar procedimento armazenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Para executar um procedimento armazenado, basta clicar no comando **Salvar e Executar** no painel do editor de script.

	> [AZURE.NOTE] O comando **Salvar e Executar** salvará o procedimento armazenado antes da execução, o que significa que ele substituirá a versão do procedimento armazenado salva anteriormente.

- As execuções de procedimento armazenado bem-sucedidas terão status *Procedimento armazenado salvo e executado com êxito* e os resultados retornados serão preenchidos no painel *Resultados*.

	![Captura de tela da folha Procedimentos Armazenados do Gerenciador de Script para executar procedimento armazenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Se a execução encontrar um erro, ele será preenchido no painel *Resultados*.

	![Captura de tela da exibição de propriedades do script do Gerenciador de Script. Executar um procedimento armazenado com erros](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## Trabalhar com scripts fora do portal

O Gerenciador de Script no portal do Azure é apenas uma maneira de trabalhar com procedimentos armazenados, disparadores e funções definidas pelo usuário no Banco de Dados de Documentos. Você também pode trabalhar com scripts usando a API REST e os [SDKs clientes](documentdb-sdk-dotnet.md). A documentação da API REST inclui exemplos para trabalhar com [procedimentos armazenados usando REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funções definidas pelo usuário usando REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e [gatilhos usando REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Existem exemplos disponíveis mostrando como [trabalhar com scripts que usam C#](documentdb-dotnet-samples.md#server-side-programming-examples) e [com os scripts que usam o Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## Próximas etapas

Saiba mais sobre programação no lado do servidor do DocumentDB no artigo [UDFs, gatilhos de banco de dados e procedimentos armazenados](documentdb-programming.md).

O [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/documentdb/) também é um recurso útil para orientar você à medida que for aprendendo mais sobre o DocumentDB.

<!---HONumber=AcomDC_0831_2016-->