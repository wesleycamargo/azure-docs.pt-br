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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 759ae1c077a2c93ee4450843a796b84d95701a10
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769888"
---
# <a name="access-data-during-training-from-your-datastores"></a>Acesse os dados durante o treinamento de seus armazenamentos de dados
Use um datastore para acessar e interagir com seus dados nos fluxos de trabalho do AML.

No serviço de Azure Machine Learning, o repositório de dados é uma abstração de [Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). O repositório de dados pode referenciar um contêiner de [Blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ou [compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) como o armazenamento subjacente. 

## <a name="create-a-datastore"></a>Criar um repositório de dados
Para usar datastores, primeiro você precisa de uma [área de trabalho](concept-azure-machine-learning-architecture.md#workspace). Início das [criando um novo espaço de trabalho](quickstart-create-workspace-with-python.md) ou recuperar um existente:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Usar o repositório de dados padrão
Não há necessidade de criar ou configurar uma conta de armazenamento.  Cada espaço de trabalho possui um armazenamento de dados padrão que você pode começar a usar imediatamente.

Para obter acesso ao repositório de dados padrão do workspace:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrar um repositório de dados
Se você tiver o Armazenamento do Microsoft Azure existente, poderá registrá-lo como um armazenamento de dados em sua área de trabalho. Você pode registrar um contêiner de BLOBs do Azure ou o compartilhamento de arquivos do Azure como um repositório de dados. Todos os métodos de registro estão na classe `Datastore` e têm o formato `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Repositório de Dados do Contêiner de Blob do Azure
Para registrar um repositório de dados do contêiner de Blob do Azure:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Repositório de Dados do Compartilhamento de Arquivos do Azure
Para registrar um repositório de dados do Compartilhamento de Dados do Azure:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Obter um repositório de dados existente
Para consultar um repositório de dados registrado pelo nome:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Também é possível obter todos os repositórios de dados para um workspace:
```Python
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Por conveniência, defina um dos seus datastores registrados como o armazenamento de dados padrão para o seu espaço de trabalho:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Carregar e baixar dados
### <a name="upload"></a>Carregar
Carregue um diretório ou arquivos individuais para o armazenamento de dados usando o SDK do Python.

Para carregar um diretório em um repositório de dados `ds`:
```Python
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

## <a name="access-datastores-for-training"></a>Acesso a repositórios de dados para treinamento
Você pode acessar um datastore durante uma execução de treinamento (por exemplo, para dados de treinamento ou validação) em um destino de computação remoto por meio do SDK do Python. 

Há dois modos com suporte para disponibilizar o repositório de dados na computação remota:
* **Mount**  
`ds.as_mount()`: ao especificar esse modo de montagem, o repositório de dados será montado para você na computação remota. 
* **Download/upload**  
    * `ds.as_download(path_on_compute='your path on compute')` faz o download de dados do seu armazenamento de dados para o computador remoto para o local especificado por `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` carrega dados no repositório de dados.  Suponha que seu script de treinamento crie um arquivo `foo.pkl` no diretório de trabalho atual na computação remota. Carregue este arquivo no seu armazenamento de dados com `ds.as_upload(path_on_compute='./foo.pkl')` depois que o script criar o arquivo. O arquivo é enviado para a raiz do seu armazenamento de dados.
    
Para fazer referência a uma pasta ou arquivo específico em seu armazenamento de dados, use a função **`path`** do armazenamento de dados. Por exemplo, para fazer o download do conteúdo do diretório `./bar` do armazenamento de dados para seu destino de computação, use `ds.path('./bar').as_download()`.

Qualquer objeto `ds` ou `ds.path` é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de mount/download na computação remota. O caminho do repositório de dados na computação remota talvez não seja o mesmo que o caminho de execução do script.

Para acessar seu datastore durante o treinamento, passe-o para o seu script de treinamento como um argumento de linha de comando via `script_params`:

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
`as_mount()` é o modo padrão para um repositório de dados, assim, também é possível apenas aprovar diretamente `ds` para o `'--data_dir'` argumento.

Ou passe uma lista de datastores para o parâmetro `inputs` do construtor Estimator para montar ou copiar para / de seu (s) armazenamento (s) de dados:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
O código acima irá:
* baixar todo o conteúdo no repositório de dados `ds1` para a computação remota antes que o script de treinamento `train.py` seja executado
* baixar a pasta `'./foo'` no repositório de dados `ds2` para a computação remota antes que `train.py` seja executado
* carregar o arquivo `'./bar.pkl'` da computação remota ao repositório de dados `d3` após o script ser executado

## <a name="next-steps"></a>Próximas etapas
* [Treinar um modelo](how-to-train-ml-models.md)
