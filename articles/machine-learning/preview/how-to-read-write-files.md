---
title: Ler e gravar arquivos de dados grandes | Microsoft Docs
description: Ler e gravar arquivos grandes em experimentos do Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>Como manter alterações e trabalhar com arquivos grandes
Com o serviço de Experimentação do Azure Machine Learning, é possível configurar uma variedade de destinos de execução. Alguns destinos são locais, como um computador local ou um contêiner do Docker em um computador local. Outros são remotos, como um contêiner do Docker em um computador remoto ou um cluster HDInsight. Para saber mais, consulte [Visão geral do serviço de execução de experimento do Microsoft Azure Machine Learning](experimentation-service-configuration.md). 

Para poder executar em um destino, será necessário copiar a pasta do projeto para o destino de computação. Isso será necessário mesmo com uma execução local que usa uma pasta temporária local para essa finalidade. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolamento, portabilidade e capacidade de reprodução da execução
O objetivo deste design é garantir o isolamento, a capacidade de reprodução e a portabilidade da execução. Se executar o mesmo script duas vezes, no mesmo destino de computação ou em outro destino de computação, você com certeza receberá os mesmos resultados. As alterações feitas pela primeira execução não deverão afetar aquelas realizadas pela segunda execução. Com esse design, é possível tratar destinos de computação como recursos de computação sem monitoração de estado e sem afinidade com os trabalhos executados depois de serem concluídos.

## <a name="challenges"></a>Desafios
Embora esse design ofereça benefícios de portabilidade e capacidade de repetição, ele também traz alguns desafios únicos.

### <a name="persisting-state-changes"></a>Como manter alterações de estado
Se o seu script modificar o estado no contexto de computação, as alterações não serão mantidas para a próxima execução, nem serão propagadas de volta para o computador cliente automaticamente. 

Mais especificamente, se o seu script criar uma subpasta ou gravar um arquivo, a pasta ou arquivo em questão não permanecerá no diretório do projeto após a execução. Os arquivos são armazenados em uma pasta temporária no ambiente de computação de destino. Você poderá usá-los para fins de depuração, mas não deverá contar que eles existirão permanentemente.

### <a name="working-with-large-files-in-the-project-folder"></a>Como trabalhar com grandes arquivos na pasta de projeto

Se a sua pasta de projeto contiver arquivos grandes, ocorrerá latência quando a pasta for copiada para o ambiente de computação de destino no início de cada execução. Mesmo que a execução ocorra localmente, o tráfego de disco desnecessário deverá ser evitado. É por isso que o tamanho do projeto máximo está limitado atualmente a 25 MB.

## <a name="option-1-use-the-outputs-folder"></a>Opção 1: usar a pasta *outputs*
Essa opção é a preferencial se todas as seguintes condições se aplicarem:
* O script gera arquivos.
* É esperado que os arquivos sejam alterados a cada experimento.
* Você deseja manter um histórico desses arquivos. 

Estes são alguns casos de uso comuns:
* Treinando um modelo
* Como criar um conjunto de dados
* Como plotar um gráfico como um arquivo de imagem como parte de sua execução do treinamento do modelo 

Além disso, é recomendável comparar as saídas das execuções, selecionar um arquivo de saída (como um modelo) produzido por uma execução anterior e usá-lo para uma tarefa subsequente (como pontuação).

É possível gravar arquivos em uma pasta chamada *outputs* com relação ao diretório raiz. Essa pasta recebe tratamento especial do Serviço de experimentação. Qualquer elemento que seu script criar nessa pasta durante a execução, como um arquivo de modelo, um arquivo de dados ou um arquivo de imagem em gráfico (coletivamente conhecidos como _artefatos_), é copiado para a conta de Armazenamento de Blobs do Azure associada à sua conta de experimentação depois que a execução for concluída. Os arquivos passam a fazer parte do seu registro de histórico de execuções.

Aqui está um breve exemplo de código para armazenar um modelo na pasta *outputs*:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
É possível baixar qualquer artefato navegando para a seção **Arquivos de Saída** da página de detalhes da execução de uma execução específica no Azure Machine Learning Workbench. Basta escolher a execução e selecionar o botão **Baixar**. Outra opção é inserir o comando `az ml asset download` na janela da CLI (interface de linha de comando).

Para ver um exemplo mais completo, consulte o script Python `iris_sklearn.py` no projeto de exemplo _Classificando a íris_.

## <a name="option-2-use-the-shared-folder"></a>Opção 2: usar a pasta compartilhada
Se você não precisar manter um histórico desses arquivos para cada execução, usar uma pasta compartilhada que pode ser acessada entre as execuções independentes pode ser uma ótima opção para os seguintes cenários: 
- Seu script precisa carregar dados de arquivos locais, como arquivos .csv ou um diretório de arquivos de texto ou de imagens, como dados de treinamento ou de teste. 
- Seu script processa dados brutos e grava os resultados intermediários, como dados de treinamento em destaque de arquivos de texto ou de imagens que são usados em uma execução de treinamento posterior. 
- Seu script produz um modelo e o script de pontuação subsequente precisa coletar o modelo e usá-lo para avaliação. 

É importante observar é que a pasta compartilhada reside localmente no destino de computação que você escolher. Portanto, essa opção só funcionará se todas as suas execuções de script que fizerem referência a essa pasta compartilhada ocorrerem no mesmo destino de computação, o qual não deverá ser reciclado entre as execuções.

Aproveitando o recurso de pasta compartilhada, é possível ler ou gravar em uma pasta especial identificada por uma variável de ambiente, `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exemplo
Aqui está um código Python de exemplo para usar essa pasta de compartilhamento para ler e gravar em um arquivo de texto:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Para ver um exemplo mais completo, consulte o arquivo *iris_sklearn_shared_folder.py* no projeto de exemplo _Classificando a íris_.

Antes de usar esse recurso, você deve definir algumas configurações simples no arquivo *.compute* que representam o contexto de execução de destino na pasta *aml_config*. O caminho real para essa pasta pode variar conforme o destino de computação que você escolher e o valor configurado.

### <a name="configure-local-compute-context"></a>Configurar contexto de computação local

Para habilitar esse recurso em um contexto de computação local, basta adicionar a seguinte linha ao seu arquivo *.compute*, representando o ambiente _local_ (geralmente denominado *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

O path ~/.azureml/share é o caminho padrão da pasta base. Você pode alterá-lo para qualquer caminho absoluto local acessível pela execução do script. O nome da conta de experimentação, o nome do espaço de trabalho e o nome do projeto são acrescentados automaticamente ao nome do diretório base, que se torna o caminho completo do diretório compartilhado. Por exemplo, os arquivos podem ser gravados (e recuperados) no seguinte caminho se você usar o valor padrão anterior:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Configurar o contexto de computação do Docker (local ou remoto)
Para habilitar esse recurso em um contexto de computação do Docker, você precisa adicionar as duas linhas a seguir ao seu arquivo *.compute* local ou remoto do Docker.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>A propriedade **sharedVolumes** precisa ser definida como *true* quando você usa a variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` para acessar a pasta compartilhada. Caso contrário, a execução falhará.

O código em execução no contêiner do Docker sempre vê essa pasta compartilhada como */azureml-share/*. Esse caminho de pasta não é configurável do ponto de vista do contêiner do Docker. Não use esse nome de pasta no seu código. Em vez disso, use sempre o nome da variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` para referir-se a essa pasta. Ele é mapeado para uma pasta local no computador host ou no contexto de computação do Docker. O diretório base desta pasta local é o valor configurável da configuração `nativeSharedDirectory` no arquivo *.compute*. O caminho local da pasta compartilhada no computador host, se você usar o valor padrão, é o seguinte:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>O caminho da pasta compartilhada no disco local é o mesmo, seja um contexto de computação local ou um contexto de computação local do Docker. Isso significa que você pode, inclusive, compartilhar arquivos entre uma execução local e uma execução do Docker local.

Você pode colocar dados de entrada diretamente nessas pastas e esperar que as execuções do Docker ou locais nesse computador consigam coletá-los. Você também pode gravar arquivos nesta pasta das suas execuções do Docker ou locais, e esperar que os arquivos persistam naquela pasta, sobrevivendo ao ciclo de vida de execução.

Para obter mais informações, consulte [Arquivos de configuração de execução do Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).

>[!NOTE]
>A variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` não é compatível com um contexto de computação do HDInsight. No entanto, é muito fácil obter o mesmo resultado usando explicitamente um caminho absoluto do Armazenamento de Blobs do Azure para leitura e gravação no armazenamento de blobs conectado.

## <a name="option-3-use-external-durable-storage"></a>Opção 3: usar o armazenamento durável externo

É possível usar o armazenamento durável externo para manter o estado durante a execução. Essa opção é útil nos seguintes cenários:
- Seus dados de entrada já estão armazenados em um armazenamento durável que pode ser acessado do ambiente de computação de destino.
- Os arquivos não precisam fazer parte dos registros de histórico de execuções.
- Os arquivos precisam ser compartilhados por execuções entre diferentes ambientes de computação.
- Os arquivos precisam sobreviver ao contexto de computação em si.

Um exemplo dessa abordagem é usar o Armazenamento de Blobs do Azure do seu código Python ou PySpark. Veja este exemplo breve:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Este é um exemplo breve para anexar qualquer Armazenamento de Blobs do Azure arbitrário a um tempo de execução do HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusão
Como o Azure Machine Learning executa scripts copiando toda a pasta do projeto para o contexto de computação de destino, é preciso ter cuidado especial com arquivos grandes de entrada, saída e intermediários. Para transações de arquivos grandes, é possível usar a pasta outputs especial, a pasta compartilhada acessível por meio da variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` ou um armazenamento durável externo. 

## <a name="next-steps"></a>Próximas etapas
- Leia o artigo [Arquivos de configuração de execução do Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).
- Veja como o projeto tutorial [Classificando a íris](tutorial-classifying-iris-part-1.md) usa a pasta outputs para manter um modelo treinado.
