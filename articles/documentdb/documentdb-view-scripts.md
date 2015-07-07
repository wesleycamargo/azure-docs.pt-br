<properties
	pageTitle="Exibir procedimentos armazenados, gatilhos e funções definidas pelo usuário usando o Gerenciador de Script do Banco de Dados de Documentos| Microsoft Azure"
	description="Saiba mais sobre o Gerenciador de Script do Banco de Dados de Documentos, uma ferramenta do Portal de Visualização do Azure que permite exibir artefatos de programação do Banco de Dados de Documentos no servidor, incluindo procedimentos armazenados, gatilhos e funções definidas pelo usuário."
	services="documentdb"
	authors="stephbaron"
	manager="johnmac"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/10/2015"
	ms.author="stbaro"/>

# Exibir procedimentos armazenados, gatilhos e funções definidas pelo usuário usando o Gerenciador de Script do Banco de Dados de Documentos

Este artigo fornece uma visão geral do Gerenciador de Script do [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/), uma ferramenta do Portal de Visualização do Azure que permite exibir artefatos de programação do Banco de Dados de Documentos no servidor, incluindo procedimentos armazenados, gatilhos e funções definidas pelo usuário. Clique [aqui](documentdb-programming.md) para saber mais sobre programação do Banco de Dados de Documentos no servidor.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:

-	Como posso exibir facilmente procedimentos armazenados do Banco de Dados de Documentos por meio de um navegador da Web?
-	Como posso exibir facilmente gatilhos do Banco de Dados de Documentos por meio de um navegador da Web?
-	Como posso exibir facilmente funções definidas pelo usuário do Banco de Dados de Documentos por meio de um navegador da Web?

## Iniciar e navegar pelo Gerenciador de Script

O Gerenciador de Script pode ser iniciado por meio de qualquer conta, banco de dados ou folha de coleção do Banco de Dados de Documentos.

1. Na parte superior da conta do Banco de Dados de Documentos ou folha de banco de dados, basta clicar no comando **Gerenciador de Script**.

	![Captura de tela do comando do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. Como alternativa, na parte inferior de cada folha, há uma lente **Ferramentas de Desenvolvimento** que contém a parte **Gerenciador de Script**.

	![Captura de tela da parte do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. Basta clicar no comando ou na parte para iniciar o Gerenciador de Script.

	<p>As caixas das listas suspensas **Banco de dados** e **Coleção** são preenchidas previamente dependendo do contexto em que você inicia o Gerenciador de Script. Por exemplo, se você iniciá-lo por meio de uma lâmina de banco de dados, o banco de dados atual será preenchido. Se iniciá-lo por meio de uma lâmina de coleção, a coleção atual será preenchida.

	![Captura de tela do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. As caixas das listas suspensas **Banco de dados** e **Coleção** podem ser usadas para alterar a coleção cujos scripts estão sendo exibidos sem precisar fechar e iniciar o Gerenciador de Script novamente.

4. O Gerenciador de Script também dá suporte à filtragem do conjunto de scripts carregado atualmente segundo a propriedade de ID. Basta digitar na caixa de filtro.

	![Captura de tela do Gerenciador de Script com filtro realçado](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	E os resultados da lista do Gerenciador de Script são filtrados com base nos critérios fornecidos.

	![Captura de tela do Gerenciador de Script com resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]A funcionalidade de filtro do Gerenciador de Script filtra somente o conjunto de scripts carregado ***atualmente*** e não atualiza a coleção selecionada no momento.

5. Para atualizar a lista de scripts carregada pelo Gerenciador de Script, basta clicar no comando **Atualizar** no topo da folha.

	![Captura de tela do comando de atualização do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Exibir procedimentos armazenados, gatilhos e funções definidas pelo usuário com o Gerenciador de Script

O Gerenciador de Script permite que você exiba facilmente os artefatos de programação do Banco de Dados de Documentos no servidor.

- Basta clicar no script no qual cujo conteúdo você deseja exibir.

	![Captura de tela da experiência de exibição de script do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Por fim, o Gerenciador de Script também permite que você exiba facilmente as propriedades de sistema do script carregado no momento clicando no comando **Propriedades**.

	![Captura de tela da exibição de propriedades do script do Gerenciador de Script](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]A propriedade (_ts) de carimbo de data/hora é representada internamente como tempo de época, mas o Gerenciador de Script exibe o valor em um formato GMT legível.

## Próximas etapas

Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
 

<!---HONumber=62-->