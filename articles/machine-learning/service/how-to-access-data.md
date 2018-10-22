---
title: Usar repositórios de dados no Azure Machine Learning para acessar dados
description: Como usar repositórios de dados para acessar o armazenamento de dados durante o treinamento
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990800"
---
# <a name="how-to-access-data-during-training"></a>Como acessar dados durante o treinamento
Nos serviços do Azure Machine Learning, um Repositório de dados é uma abstração do [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). O repositório de dados pode referenciar um contêiner de [Blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ou [compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) como o armazenamento subjacente. Repositórios de dados permitem que você facilmente acesse e interaja com seu armazenamento de dados durante os fluxos de trabalho do Azure Machine Learning por meio do SDK do Python ou a CLI.

## <a name="create-a-datastore"></a>Criar um repositório de dados
Para usar repositórios de dados, primeiro você precisa de um [Workspace](concept-azure-machine-learning-architecture.md#workspace). É possível criar um novo workspace ou recuperar um existente:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Usar o repositório de dados padrão
Cada workspace tem um repositório de dados padrão que é possível começar a usar imediatamente, o qual dispensa você do trabalho de criar e configurar suas próprias contas de armazenamento.

Para obter acesso ao repositório de dados padrão do workspace:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrar um repositório de dados
Se você já tiver o Armazenamento do Azure existente, é possível registrá-lo como um repositório de dados no workspace. O Azure Machine Learning fornece a funcionalidade para registrar um contêiner de Blob do Azure ou o Compartilhamento de Arquivos do Azure como um repositório de dados. Todos os métodos de registro estão na classe `Datastore` e têm o formato `register_azure_*`.

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
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
```

Para conveniência, se você deseja definir um dos seus repositórios de dados registrados como o repositório de dados padrão para seu workspace, é possível fazer isso da seguinte maneira:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Carregar e baixar dados
### <a name="upload"></a>Carregar
É possível carregar um diretório ou arquivos individuais no repositório de dados usando o SDK do Python.

Para carregar um diretório em um repositório de dados `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` especifica o local no compartilhamento de arquivos (ou contêiner de blob) para o carregamento. O padrão é `None`, nesse caso, os dados são carregados para a raiz. `overwrite=True` substituirá quaisquer dados existentes em `target_path`.

Como alternativa, é possível carregar uma lista de arquivos individuais no repositório de dados por meio do método `upload_files()` do repositório de dados.

### <a name="download"></a>Baixar
Da mesma forma, é possível baixar os dados de um repositório de dados para o sistema de arquivos local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` é a localização do diretório local para o qual os dados serão baixados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do compartilhamento de arquivos (ou contêiner de blob) será baixado.

## <a name="access-datastores-for-training"></a>Acesso a repositórios de dados para treinamento
É possível acessar um repositório de dados durante uma execução de treinamento (por exemplo, para treinamento ou validação de dados) em uma computação de destino remota por meio do SDK do Python. 

Há dois modos com suporte para disponibilizar o repositório de dados na computação remota:
* **Mount**  
`ds.as_mount()`: ao especificar esse modo de montagem, o repositório de dados será montado para você na computação remota. 
* **Download/upload**  
    * `ds.as_download(path_on_compute='your path on compute')`: com esse modo de download, os dados serão baixados do repositório de dados para a computação remota no local especificado por `path_on_compute`.
    * Por outro lado, também é possível carregar os dados que foram produzidos pela execução de treinamento em um repositório de dados. Por exemplo, se o script de treinamento cria um arquivo `foo.pkl` no diretório de trabalho atual na computação remota, é possível especificar que seja carregado no repositório de dados após o script ter sido executado: `ds.as_upload(path_on_compute='./foo.pkl')`. Isso carregará o arquivo na raiz do repositório de dados.
    
Se você quiser fazer referência a uma pasta ou arquivo específico em seu repositório de dados, é possível usar a função **`path`** do repositório de dados. Por exemplo, se o repositório de dados tiver um diretório com o caminho relativo `./bar` e você apenas quiser baixar o conteúdo dessa pasta na computação de destino, é possível fazer isso da seguinte maneira: `ds.path('./bar').as_download()`

Qualquer objeto `ds` ou `ds.path` é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de mount/download na computação remota. O caminho do repositório de dados na computação remota talvez não seja o mesmo que o caminho de execução do script.

Para acessar o repositório de dados durante o treinamento, é possível aprová-lo no script de treinamento como um argumento de linha de comando por meio de `script_params`:

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

Como alternativa, é possível aprovar uma lista de repositórios de dados para o parâmetro `inputs` do construtor Avaliador para montar ou copiar para/de repositórios de dados:

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
