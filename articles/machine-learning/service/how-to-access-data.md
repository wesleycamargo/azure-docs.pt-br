---
title: Acessar dados em armazenamentos de dados/blobs para treinamento
titleSuffix: Azure Machine Learning service
description: Saiba como usar os armazenamentos de dados para acessar o armazenamento de dados de blobs durante o treinamento com o Serviço do Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: c171e35c6542febffc666ad5abfab50e093bb698
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359272"
---
# <a name="access-data-from-your-datastores"></a>Acessar dados de seus repositórios de dados

Armazenamentos de dados permitem que você interagir com e acessar seus dados, se você estiver executando seu código localmente, em um cluster de computação, ou em uma máquina virtual. Neste artigo, você saiba os fluxos de trabalho do Azure Machine Learning que garantem que seus armazenamentos de dados estão acessíveis e disponíveis para seu contexto de computação.

Estas instruções mostram exemplos para as seguintes tarefas:
* [Escolha um repositório de dados](#access)
* [Obter dados](#get)
* [Carregar e baixar dados para armazenamentos de dados](#up-and-down)
* [Acesso de armazenamento de dados durante o treinamento](#train)

## <a name="prerequisites"></a>Pré-requisitos

Para usar armazenamentos de dados, você precisa de uma [espaço de trabalho](concept-azure-machine-learning-architecture.md#workspace) primeiro. 

Início das [criando um novo espaço de trabalho](setup-create-workspace.md#sdk) ou recuperar um existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Escolha um repositório de dados

Você pode usar o armazenamento de dados padrão ou traga seu próprio.

### <a name="use-the-default-datastore-in-your-workspace"></a>Usar o armazenamento de dados padrão em seu espaço de trabalho

Não há necessidade de criar ou configurar uma conta de armazenamento, pois cada espaço de trabalho tem um repositório de dados padrão. Você pode usar esse armazenamento de dados imediatamente como ela já está registrado no espaço de trabalho. 

Para obter acesso ao repositório de dados padrão do workspace:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrar seu próprio repositório de dados com o espaço de trabalho
Se você tiver o Armazenamento do Microsoft Azure existente, poderá registrá-lo como um armazenamento de dados em sua área de trabalho.   Todos os métodos de registro são sobre o [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) de classe e ter o formulário register_azure_ *. 

Os exemplos a seguir mostram a você registrar um contêiner de Blob do Azure ou um compartilhamento de arquivos do Azure como um repositório de dados.

+ Para um **repositório de dados de contêiner de Blob do Azure**, use [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Para um **repositório de dados de compartilhamento de arquivos do Azure**, use [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Por exemplo:  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Localizar & Definir armazenamentos de dados

Para obter um repositório de dados especificado registrado no espaço de trabalho atual, use [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Para obter uma lista de todos os armazenamentos de dados em um determinado espaço de trabalho, use este código:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Para definir um repositório de dados padrão diferente para o espaço de trabalho atual, use [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar e baixar dados
O [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos descritos nos exemplos a seguir são específicos e operar identicamente para o [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classes.

### <a name="upload"></a>Carregar

 Carregue um diretório ou arquivos individuais para o armazenamento de dados usando o SDK do Python.

Para carregar um diretório em um repositório de dados `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` especifica o local no compartilhamento de arquivos (ou contêiner de blob) para o carregamento. O padrão é `None`, nesse caso, os dados são carregados para a raiz. `overwrite=True` substituirá quaisquer dados existentes em `target_path`.

Ou faça o upload de uma lista de arquivos individuais para o armazenamento de dados por meio do método `upload_files()` do armazenamento de dados.

### <a name="download"></a>Baixar
Da mesma forma, faça o download dos dados de um armazenamento de dados para o sistema de arquivos local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` é a localização do diretório local para o qual os dados serão baixados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do compartilhamento de arquivos (ou contêiner de blob) será baixado.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Acesso a armazenamentos de dados durante o treinamento

Depois que você disponibiliza seu armazenamento de dados na computação remota, você pode acessá-lo durante execuções de treinamento (por exemplo, dados de treinamento ou validação), simplesmente passando o caminho para ele como um parâmetro em seu script de treinamento.

A tabela a seguir lista comum [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) métodos que tornam os armazenamentos de dados disponíveis na computação remota.

# #

forma|Método|DESCRIÇÃO
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Use para montar um armazenamento de dados na computação remota. Modo padrão para armazenamentos de dados.
Baixar|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Usar para baixar dados do local especificado por `path_on_compute` em seu armazenamento de dados para a computação remota.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Use para carregar dados para a raiz do seu armazenamento de dados do local especificado por `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Para fazer referência a uma pasta ou arquivo específico em seu armazenamento de dados, use a função [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) do armazenamento de dados.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Qualquer objeto `ds` ou `ds.path` é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de mount/download na computação remota. O caminho do repositório de dados na computação remota não pode ser o mesmo que o caminho de execução do script de treinamento.

### <a name="examples"></a>Exemplos 

A seguir ilustra exemplos específicos para o [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe para acessar seu repositório de dados durante o treinamento.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

Uma vez que `as_mount()` é o modo padrão para um repositório de dados, você poderia passar diretamente `ds` para o `'--data_dir'` argumento.

Ou passe em uma lista de armazenamentos de dados para o construtor do estimador `inputs` parâmetro montar ou copiar bidirecionalmente em seus armazenamentos de dados. Este exemplo de código:
* Baixa todo o conteúdo no armazenamento de dados `ds1` para a computação remota antes de seu script de treinamento `train.py` é executado
* A pasta de downloads `'./foo'` no repositório de dados `ds2` para a computação remota antes de `train.py` é executado
* Carrega o arquivo `'./bar.pkl'` de computação remota até o repositório de dados `ds3` depois que o script foi executado

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo](how-to-train-ml-models.md)

* [Implantar um modelo](how-to-deploy-and-where.md)
