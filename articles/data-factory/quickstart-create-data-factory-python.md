---
title: Criar um Azure Data Factory usando o Python | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local no mesmo Armazenamento de Blobs.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Criar um data factory e pipeline usando o Python
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho controlados por dados (chamados de pipelines) que podem ingerir dados de armazenamentos de dados diferentes, processar/transformar os dados usando serviços de computação como o Hadoop do Azure HDInsight, o Spark, o Azure Data Lake Analytics e o Azure Machine Learning e publicar os dados de saída em armazenamentos de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de BI (business intelligence). 

Este guia de início rápido descreve como usar o Python para criar um Azure Data Factory. O pipeline nesse data factory copia dados de uma pasta para outra em um Armazenamento de Blobs do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de **origem** e de **coletor**. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma. 
* **Crie um aplicativo no Azure Active Directory** seguindo [esta instrução](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Anote os valores a seguir, que você usará em etapas posteriores: **ID do aplicativo**, **chave de autenticação** e **ID do locatário**. Seguindo as instruções no mesmo artigo, atribua o aplicativo à função "**Colaborador**". 

### <a name="create-and-upload-an-input-file"></a>Criar e carregar um arquivo de entrada

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **input.txt** no disco.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Use ferramentas como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar o contêiner **adfv2tutorial** e carregar o arquivo input.txt no contêiner. 

## <a name="install-the-python-package"></a>Instalar o pacote do Python
1. Abra um terminal ou prompt de comando com privilégios de administrador.  
2. Para instalar o pacote do Python para o Data Factory, execute o seguinte comando:

    ```
    pip install azure-mgmt-datafactory
    ```

    O [SDK do Python para Data Factory](https://github.com/Azure/azure-sdk-for-python) dá suporte a Python 2.7, 3.3, 3.4, 3.5 e 3.6.
## <a name="create-a-data-factory-client"></a>Criar um cliente data factory

1. Crie um arquivo chamado **datafactory.py**. Adicione as instruções a seguir para adicionar referências aos namespaces.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Adicione as funções a seguir, que imprimem informações. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Adicione o código a seguir, que cria uma instância da classe DataFactoryManagementClient, ao método **Main**. Você usa esse objeto para criar o data factory, o serviço vinculado, os conjuntos de dados e o pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir, que cria um **data factory**, ao método **Main**. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**.

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure tanto como a origem da cópia quanto como o repositório de coletor, denominado "AzureStorageLinkedService" na amostra.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta seção, você criará dois conjuntos de dados: um para a origem e o outro para o coletor.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem
Adicione o código a seguir, que cria um Conjunto de Dados do Blob do Azure, ao método Main. Para obter informações sobre as propriedades do conjunto de dados do Blob do Azure, confira o artigo sobre o [conector do Blob do Azure](connector-azure-blob-storage.md#dataset-properties). 

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de coletor
Adicione o código a seguir, que cria um Conjunto de Dados do Blob do Azure, ao método Main. Para obter informações sobre as propriedades do conjunto de dados do Blob do Azure, confira o artigo sobre o [conector do Blob do Azure](connector-azure-blob-storage.md#dataset-properties). 

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Criar uma pipeline

Adicione o código a seguir, que cria um **pipeline com uma atividade de cópia**, ao método **Main**.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>Executar o código
Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.


## <a name="clean-up-resources"></a>Limpar recursos
Para excluir programaticamente o data factory, adicione as linhas de código a seguir ao programa: 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
