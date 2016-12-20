---
title: Criar recursos para dados de armazenamento de blobs do Azure usando o Panda | Microsoft Docs
description: "Como criar recursos para os dados armazenados no contêiner de blob do Azure com o pacote Python Pandas."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6751bf2367ca849f35c81a7857b4f85d75ef6a2


---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Criar recursos para dados de armazenamento de blob do Azure usando o Panda
Este documento mostra como criar recursos para os dados armazenados no contêiner de blobs do Azure usando o pacote Python [Pandas](http://pandas.pydata.org/) . Depois de descrever como carregar os dados em um quadro de dados do Panda, ele mostrará como gerar recursos categóricos usando os scripts Python com os valores de indicador e recursos de agrupamento.

[!INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Este **menu** leva você até os tópicos que descrevem como criar recursos para dados em vários ambientes. Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você criou uma conta de armazenamento de blobs do Azure e armazenou os dados lá. Se você precisar de instruções para configurar uma conta, confira [Criar uma conta de Armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carregar os dados em um quadro de dados Pandas
Para explorar e manipular um conjunto de dados, eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um quadro de dados Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do Blob do Azure com o seguinte código de Python de exemplo e usando o serviço blob a seguir. Substitua a variável no código abaixo pelos valores específicos:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Leia os dados em um quadro de dados Pandas do arquivo baixado.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora você está pronto para explorar os dados e gerar recursos neste conjunto de dados.

## <a name="a-nameblob-featuregenafeature-generation"></a><a name="blob-featuregen"></a>Geração de recursos
As duas seções a seguir mostram como gerar recursos categóricos com valores de indicador e de compartimentalização usando scripts Python.

### <a name="a-nameblob-countfeatureaindicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Geração de Recursos baseada no valor do indicador
Recursos categóricos podem ser criados da seguinte maneira:

1. Inspecionar a distribuição da coluna categórica:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Gerar valores de indicador para cada um dos valores da coluna
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Unir a coluna de indicador com o quadro de dados original
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Remover a própria variável original:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="a-nameblob-binningfeatureabinning-feature-generation"></a><a name="blob-binningfeature"></a>Agrupamento da Geração de Recursos
Para gerar recursos compartimentalizados, faça o seguinte:

1. Adicione uma sequência de colunas a ser compartimentalizada a coluna numérica
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Converta a compartimentalização em uma sequência de variáveis boolianas
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Por fim, associe as variáveis fictícias ao quadro de dados original
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="a-namesql-featuregenawriting-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Gravar dados de volta ao blob do Azure e consumi-los no Aprendizado de Máquina do Azure
Depois que você já explorou os dados e criou os recursos necessários, pode carregar os dados (amostra ou recurso) para um blob do Azure e consumi-los no Aprendizado de Máquina do Azure usando as seguintes etapas: observe que os recursos adicionais podem ser criados no Estúdio de Aprendizado de Máquina do Azure também.

1. Grave o quadro de dados no arquivo local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Carregue os dados para o blob do Azure da seguinte maneira:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Agora, os dados podem ser lidos do blob usando o módulo [Importar Dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) do Aprendizado de Máquina do Azure, como mostra a tela abaixo:

![blob de leitor](./media/machine-learning-data-science-process-data-blob/reader_blob.png)




<!--HONumber=Nov16_HO3-->


