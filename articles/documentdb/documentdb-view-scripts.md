---
title: 'Ferramenta do portal do Azure Cosmos DB: Gerenciador de Scripts | Microsoft Docs'
description: "Saiba mais sobre o Gerenciador de Script do Cosmos DB, uma ferramenta do Portal do Azure que permite gerenciar artefatos de programação do lado do servidor do Azure Cosmos DB, incluindo procedimentos armazenados do JavaScript, gatilhos e funções definidas pelo usuário."
keywords: editor de JavaScript
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e9c3e92dd1c35ff6893565e61f7e7b940b6ede7e
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-azure-cosmos-db-script-explorer"></a>Criar e executar procedimentos armazenados, gatilhos e funções definidas pelo usuário usando o Gerenciador de Script do Azure Cosmos DB
Este artigo fornece uma visão geral do Gerenciador de Script do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), um editor de JavaScript do portal do Azure que permite exibir e executar artefatos de programação do lado do servidor do Cosmos DB, incluindo procedimentos armazenados, gatilhos e funções definidas pelo usuário. Leia mais sobre a programação do lado do servidor do Cosmos DB no artigo [Procedimentos armazenados, gatilhos de banco de dados e UDFs](documentdb-programming.md).

## <a name="launch-script-explorer"></a>Iniciar Gerenciador de Script
1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique no ![ícone do Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**. 

    Se o **Azure Cosmos DB** não estiver visível, clique em **Mais Serviços** na parte inferior e, depois, clique no ![ícone do Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**.
2. No menu de recursos, clique em **Gerenciador de Scripts**.
   
    ![Captura de tela do comando do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    As caixas das listas suspensas **Banco de dados** e **Coleção** são preenchidas previamente dependendo do contexto em que você inicia o Gerenciador de Script.  Por exemplo, se você iniciá-lo por meio de uma lâmina de banco de dados, o banco de dados atual será preenchido.  Se iniciá-lo por meio de uma lâmina de coleção, a coleção atual será preenchida.
3. Use as caixas das listas suspensas **Banco de dados** e **Coleção** para alterar a coleção cujos scripts estejam sendo exibidos sem precisar fechar e iniciar o Gerenciador de Script novamente.  
4. O Gerenciador de Script também dá suporte à filtragem do conjunto de scripts carregado atualmente segundo a propriedade de ID.  Digite na caixa de filtro e os resultados da lista do Gerenciador de Script são filtrados com base nos critérios fornecidos.
   
    ![Captura de tela do Gerenciador de Script com resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [!IMPORTANT] 
    > A funcionalidade de filtro do Gerenciador de Script filtra somente o conjunto de scripts carregado ***atualmente*** e não atualiza a coleção selecionada no momento.

1. Para atualizar a lista de scripts carregada pelo Gerenciador de Script, basta clicar no comando **Atualizar** no topo da folha.
   
    ![Captura de tela do comando de atualização do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Criar, exibir e editar procedimentos armazenados, gatilhos e funções definidas pelo usuário
O Gerenciador de Script permite que você execute facilmente operações CRUD em artefatos de programação do lado do servidor do Cosmos DB.  

* Para criar um script, basta clicar no comando de criação aplicável no gerenciador de scripts , fornecer uma id, inserir o conteúdo do script e clicar em **Salvar**.
  
    ![Captura de tela da opção Criar do Gerenciador de Script mostrando o editor de JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* Ao criar um disparador, você também deve especificar a operação e o tipo de gatilho
  
    ![Captura de tela da opção de disparo de do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* Para exibir um script, basta clicar no script no qual você está interessado.
  
    ![Captura de tela da experiência de exibição de script do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* Para editar um script, basta fazer as alterações desejadas no editor de JavaScript editor e clicar em **Salvar**.
  
    ![Captura de tela da experiência de exibição de script do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* Para descartar alterações pendentes em um script, basta clicar no comando **Descartar** .
  
    ![Captura de tela da experiência de descartar alterações do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* Por fim, o Gerenciador de Script também permite que você exiba facilmente as propriedades de sistema do script carregado no momento clicando no comando **Propriedades** .
  
    ![Captura de tela da exibição de propriedades do script do Gerenciador de Script](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > A propriedade (_ts) de carimbo de data/hora é representada internamente como tempo de época, mas o Gerenciador de Script exibe o valor em um formato GMT legível.
  > 
  > 
* Para excluir um script, selecione-o no Gerenciador de Script e clique no comando **Excluir** .
  
    ![Captura de tela do comando excluir do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* Confirme a ação de exclusão clicando em **Sim** ou cancele a ação de exclusão clicando em **Não**.
  
    ![Captura de tela do comando excluir do Gerenciador de Script](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Executar um procedimento armazenado
> [!WARNING]
> A execução de procedimentos armazenados no Gerenciador de Script ainda não tem suporte para coleções particionadas do lado do servidor. Para saber mais, visite [Particionar e dimensionar dados no DocumentDB](documentdb-partition-data.md).
> 
> 

O Gerenciador de Script permite que você execute os procedimentos armazenados no servidor usando o portal do Azure.

* Quando abrir uma nova folha Criar procedimento armazenado, um script padrão (*prefixo*) já será fornecido. Para executar o script *prefixo* ou seu próprio script, adicione uma *id* e *entradas*. Para procedimentos armazenados que aceitam vários parâmetros, todas as entradas devem estar em uma matriz (por exemplo, *["foo", "bar"]*).
  
    ![Captura de tela da folha Gerenciador de Script para adicionar entrada e executar procedimento armazenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* Para executar um procedimento armazenado, basta clicar no comando **Salvar e Executar** no painel do editor de script.
  
  > [!NOTE]
  > O comando **Salvar e Executar** salvará o procedimento armazenado antes da execução, o que significa que ele substituirá a versão do procedimento armazenado salva anteriormente.
  > 
  > 
* As execuções de procedimento armazenado bem-sucedidas terão status *Procedimento armazenado salvo e executado com êxito* e os resultados retornados serão preenchidos no painel *Resultados*.
  
    ![Captura de tela da folha Procedimentos Armazenados do Gerenciador de Script para executar procedimento armazenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* Se a execução encontrar um erro, ele será preenchido no painel *Resultados* .
  
    ![Captura de tela da exibição de propriedades do script do Gerenciador de Script. Executar um procedimento armazenado com erros](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Trabalhar com scripts fora do portal
O Gerenciador de Script no portal do Azure é apenas uma maneira de trabalhar com procedimentos armazenados, disparadores e funções definidas pelo usuário no Banco de Dados de Documentos. Você também pode trabalhar com scripts usando a API REST e os [SDKs clientes](documentdb-sdk-dotnet.md). A documentação da API REST inclui exemplos para trabalhar com [procedimentos armazenados usando REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funções definidas pelo usuário usando REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e [gatilhos usando REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Existem exemplos disponíveis mostrando como [trabalhar com scripts que usam C#](documentdb-dotnet-samples.md#server-side-programming-examples) e [com os scripts que usam o Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre programação no lado do servidor do DocumentDB no artigo [UDFs, gatilhos de banco de dados e procedimentos armazenados](documentdb-programming.md) .



