---
title: 'Ferramenta do portal do Azure DocumentDB: Gerenciador de Documentos | Microsoft Docs'
description: Saiba mais sobre o Gerenciador de Documentos do Banco de Dados de Documentos, uma ferramenta do Portal do Azure para exibir JSON, editar, criar e carregar documentos JSON com o Banco de Dados de Documentos, um banco de dados de documentos NoSQL.
keywords: exibir json
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 029d81b3-6382-4799-a1bd-0dcbccd9968d
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 4338fc03664175b7c27a48f1ab682179c1035313
ms.lasthandoff: 04/21/2017


---
# <a name="view-edit-create-and-upload-json-documents"></a>Exibir, editar, criar e carregar documentos JSON 

Este artigo fornece uma visão geral das duas maneiras em que é possível criar, editar e consultar documentos no portal: [Document Explorer](#launch-document-explorer) e [Data Explorer (visualização)](#data-explorer).

> [!NOTE]
> O Gerenciador de Documentos não está habilitado em contas do DocumentDB com suporte ao protocolo para MongoDB. Esta página será atualizada quando esse recurso estiver habilitado.

<a id="launch-document-explorer"></a>

## <a name="launch-document-explorer-in-the-azure-portal"></a>Iniciar o Gerenciador de Documentos no Portal do Azure
1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique no ![ícone do Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**. 

    Se **NoSQL (DocumentDB)** não estiver visível, clique em **Mais Serviços** na parte inferior e clique no ![ícone do Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**.
2. Selecione o nome da conta. 
3. No menu de recursos, clique em **Gerenciador de Documentos**. 
   
    ![Captura de tela do comando do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)
   
    Na folha **Gerenciador de Documentos**, as listas suspensas **Bancos de dados** e **Coleções** são preenchidas com antecedência, dependendo do contexto no qual você tiver iniciado o Gerenciador de Documentos. 

## <a name="create-a-json-document"></a>Criar um documento JSON
1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).
2. Na folha **Gerenciador de Documentos**, clique em **Criar Documento**. 
   
    Um trecho mínimo de JSON é fornecido na folha **Documento** .
   
    ![Captura de tela da experiência de criação de documentos do Gerenciador de Documentos, onde você pode exibir JSON e editar JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)
3. Na folha **Documento**, digite ou cole o conteúdo do documento JSON que você deseja criar e clique em **Salvar** para confirmar o documento para o banco de dados e a coleção especificados na folha **Gerenciador de Documentos**.
   
    ![Captura de tela do comando salvar do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/savedocument1.png)
   
   > [!NOTE]
   > Se você não fornecer uma propriedade de "id", o Gerenciador de Documentos adiciona automaticamente uma propriedade de ID e gera um GUID como o valor da ID.
   > 
   > 
   
    Se já tiver dados de arquivos JSON, MongoDB, SQL Server, arquivos CSV, armazenamento de Tabela do Azure, Amazon DynamoDB, HBase ou de outras coleções do Banco de Dados de Documentos, você poderá usar a [ferramenta de migração de dados](documentdb-import-data.md) do Banco de Dados de Documentos para importar rapidamente os dados.

## <a name="edit-a-json-document"></a>Editar um documento JSON
1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).
2. Para editar um documento existente, selecione-o na folha **Gerenciador de Documentos**, edite o documento na folha **Documento** e clique em **Salvar**.
   
    ![Captura de tela da funcionalidade de edição de documentos do Gerenciador de Documentos usada para exibir JSON](./media/documentdb-view-JSON-document-explorer/editdocument.png)
   
    Caso você esteja editando um documento e decidir que quer descartar as edições feitas, basta clicar em **Descartar** na folha **Documento**, confirmar a ação de descarte e o estado anterior do documento será carregado novamente.
   
    ![Captura de tela do comando descartar do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## <a name="delete-a-document-from-documentdb"></a>Excluir um documento do DocumentDB
1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).
2. Selecione o documento no **Gerenciador de Documentos**, clique em **Excluir** e confirme a exclusão. Após a confirmação, o documento será removido imediatamente da lista do Gerenciador de Documentos.
   
    ![Captura de tela do comando excluir do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## <a name="work-with-json-documents"></a>Trabalhar com documentos JSON
Observe que o Gerenciador de Documentos valida que qualquer documento novo ou editado contém JSON válido.  Você pode até exibir erros do JSON ao passar o mouse sobre a seção incorreta para obter detalhes sobre o erro de validação.

![Captura de tela do Gerenciador de Documentos com realce de JSON inválido](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

Além disso, o Gerenciador de Documentos impede que você salve um documento com conteúdo JSON inválido.

![Captura de tela do Gerenciador de Documentos com erro de salvamento de JSON inválido ](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

Por fim, o Gerenciador de Documentos permite que você veja facilmente as propriedades de sistema do documento carregado clicando no comando **Propriedades** .

![Captura de tela da exibição de propriedades de documento do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [!NOTE]
> A propriedade do carimbo de data/hora (_ts) é representada internamente como um tempo de época, mas o Gerenciador de Documentos exibe o valor em um formato GMT legível.
> 
> 

## <a name="filter-documents"></a>Filtrar documentos
O Gerenciador de Documentos oferece suporte a várias opções de navegação e configurações avançadas

Por padrão, o Gerenciador de Documentos carrega os 100 primeiros documentos na coleção selecionada, por sua data de criação, do antigo ao mais recente.  Você pode carregar documentos adicionais (em lotes de 100), selecionando a opção **Carregar mais** na parte inferior da folha do Gerenciador de Documentos. Você pode escolher quais documentos serão carregados por meio do comando **Filtrar** .

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).
2. Na parte superior da folha **Gerenciador de Documentos**, clique em **Filtrar**.  
   
    ![Captura de tela das Configurações de Filtro do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
3. As configurações de filtro aparecem abaixo da barra de comandos. Nas configurações do filtro, forneça uma cláusula WHERE e/ou uma cláusula ORDER BY e clique em **Filtrar**.
   
   ![Captura de tela da folha Configurações do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)
   
   O Gerenciador de Documentos atualiza automaticamente os resultados com os documentos que correspondem à consulta de filtro. Leia mais sobre a gramática SQL do DocumentDB no artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) ou imprima uma cópia da [referência rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md).
   
   As caixas das listas suspensas **Banco de Dados** e **Coleção** podem ser usadas para alterar a coleção cujos documentos estão sendo exibidos sem precisar fechar e iniciar o Gerenciador de Documentos novamente.  
   
   O Gerenciador de Documentos também dá suporte à filtragem do conjunto de documentos carregados atualmente segundo a propriedade de ID.  Simplesmente digite na caixa Filtrar Documentos por id.
   
   ![Captura de tela do Gerenciador de Documentos com filtro realçado](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)
   
   Os resultados da lista do Gerenciador de Documentos são filtrados com base nos critérios fornecidos.
   
   ![Captura de tela do Gerenciador de Documentos com resultados filtrados](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)
   
   > [!IMPORTANT]
   > A funcionalidade de filtro do Gerenciador de Documentos filtra somente o conjunto de documentos carregado ***atualmente*** e não consulta a coleção selecionada.
   > 
   > 
4. Para atualizar a lista de documentos carregada pelo Gerenciador de Documentos, clique em **Atualizar** na parte superior da folha.
   
    ![Captura de tela do comando de atualização do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## <a name="bulk-add-documents"></a>Adicionar documentos em massa
O Gerenciador de Documentos dá suporte à ingestão em massa de um ou mais documentos JSON existentes, até 100 arquivos JSON por operação de upload.  

1. [Inicie o Gerenciador de Documentos](#launch-document-explorer).
2. Para iniciar o processo de upload, clique em **Carregar Documento**.
   
    ![Captura de tela da funcionalidade de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)
   
    A folha **Carregar Documento** será aberta. 
3. Clique no botão Procurar para abrir uma janela do gerenciador de arquivos, selecione um ou mais documentos JSON a serem carregados e clique em **Abrir**.
   
    ![Captura de tela do processo de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)
   
   > [!NOTE]
   > Atualmente, o Gerenciador de Documentos dá suporte a até 100 documentos JSON por operação de carregamento.
   > 
   > 
4. Quando estiver satisfeito com sua seleção, clique no botão **Carregar** .  Os documentos são adicionados automaticamente à grade do Gerenciador de Documentos e os resultados do carregamento são exibidos conforme o andamento da operação. Falhas na importação são informadas por arquivo individual.
   
    ![Captura de tela dos resultados de ingestão em massa do Gerenciador de Documentos](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)
5. Após a conclusão da operação, você poderá selecionar até 100 documentos adicionais para upload.

<a id="data-explorer"></a>
## <a name="create-a-document-by-using-data-explorer-preview"></a>Criar um documento usando o Data Explorer (visualização)

O outro método para criar, editar e consultar documentos no portal é usar o Data Explorer. Para abrir o Data Exploer, clique em **Data Explorer (visualização)** na barra de navegação no portal, em seguida, expanda o nome do banco de dados, expanda o nome da coleção, clique em **documentos**e, em seguida, clique em **Novo documento**, conforme mostrado na captura de tela a seguir.

![Captura de tela mostrando o botão Nova Coleção no portal](./media/documentdb-view-JSON-document-explorer/azure-documentdb-data-explorer.png)

## <a name="work-with-json-documents-outside-the-portal"></a>Trabalhar com documentos JSON fora do portal
O Gerenciador de Documentos no portal do Azure é apenas uma maneira de trabalhar com documentos no Banco de Dados de Documentos. Você também pode trabalhar com documentos usando a [API REST](https://msdn.microsoft.com/library/azure/mt489082.aspx) ou os [SDKs de cliente](documentdb-sdk-dotnet.md). Para obter um exemplo de código, consulte os [exemplos de documento do SDK do .NET](documentdb-dotnet-samples.md#document-examples) e os [exemplos de documento do SDK do Node.js](documentdb-nodejs-samples.md#document-examples).

Se você precisar importar ou migrar arquivos de outra fonte (arquivos JSON, MongoDB, SQL Server, arquivos CSV, armazenamento de Tabelas do Azure, Amazon DynamoDB ou HBase), poderá usar a [ferramenta de migração de dados](documentdb-import-data.md) do DocumentDB para importar rapidamente os dados para o DocumentDB.

## <a name="troubleshoot"></a>Solucionar problemas
**Sintoma**: o Gerenciador de Documentos retorna **Nenhum documento encontrado**.

**Solução**: verifique se você selecionou a assinatura, o banco de dados e a coleção correta na qual os documentos foram inseridos. Além disso, verifique se você está operando dentro de suas cotas de taxa de transferência. Se você estiver operando com seu nível máximo de taxa de transferência e estiver sofrendo limitação, reduza o uso do aplicativo a fim de operar com a cota máxima de taxa de transferência para a coleção.

**Explicação**: o portal é um aplicativo como qualquer outro, fazendo chamadas para o banco de dados e a coleção do seu DocumentDB. Se as suas solicitações estiverem sofrendo restrições no momento devido a chamadas feitas de um aplicativo separado, o portal também poderá sofrer restrições, fazendo com que os recursos não apareçam no portal. Para solucionar o problema, resolva a causa do alto uso de produtividade e, em seguida, atualize a folha do portal. Informações sobre como medir e reduzir o uso da produtividade podem ser encontradas na seção [Produtividade](documentdb-performance-tips.md#throughput) do artigo [Dicas de desempenho](documentdb-performance-tips.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a gramática SQL do DocumentDB com suporte no Gerenciador de Documentos, veja o artigo [Consulta SQL e sintaxe SQL](documentdb-sql-query.md) ou imprima a [referência rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md).


