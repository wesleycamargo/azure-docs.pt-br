---
title: "Usando a CLI do Azure 2.0 (Visualização) com o Armazenamento do Azure | Microsoft Docs"
description: "Saiba como usar a Interface de Linha de Comando do Azure (CLI do Azure) 2.0 (Visualização) com o Armazenamento do Azure para criar e gerenciar contas de armazenamento e trabalhar com blobs e arquivos do Azure. A CLI do Azure 2.0 é uma ferramenta de plataforma cruzada."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 1e2569a6db330c908fe4087c1bc5a475d3e8a575
ms.openlocfilehash: 0e1f7719c993dda419e3ebae7d50b91bd5effbc2


---
# <a name="using-the-azure-cli-20-preview-with-azure-storage"></a>Usando a CLI do Azure 2.0 (Visualização) com o Armazenamento do Azure

## <a name="overview"></a>Visão geral

A CLI do Azure 2.0 (Visualização) de software livre e plataforma cruzada fornece um conjunto de comandos para trabalhar com a Plataforma Azure. Ela fornece grande parte das mesmas funcionalidades encontradas no [Portal do Azure](https://portal.azure.com), incluindo acesso a dados avançado.

Neste guia, mostraremos como usar a [CLI do Azure 2.0 (Visualização)](/cli/azure/get-started-with-az-cli2) para executar várias tarefas ao trabalhar com recursos na sua conta de Armazenamento do Azure. É recomendável baixar e instalar ou atualizar para a versão mais recente da CLI 2.0 antes usar este guia.

Os exemplos neste guia supõem o uso do shell Bash no Ubuntu, mas outras plataformas devem funcionar de forma semelhante. 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que você conhece os conceitos básicos do Armazenamento do Azure. Ele também pressupõe que você é capaz de satisfazer os requisitos de criação de conta especificados abaixo para o serviço do Azure e de Armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/).
* **Conta de armazenamento**: veja [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

### <a name="install-the-azure-cli-20-preview"></a>Instalar a CLI do Azure 2.0 (Visualização)

Baixe e instale a CLI do Azure 2.0 (Visualização) seguindo as instruções descritas em [Instalar a CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2).

> [!TIP]
> Se você tiver problemas com a instalação, confira a seção [Solucionando problemas de instalação](/cli/azure/install-az-cli2#installation-troubleshooting) do artigo e o guia [Solucionar Problemas de Instalação](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) no GitHub.
>

## <a name="working-with-the-cli"></a>Trabalhando com a CLI

Depois de instalar a CLI, você poderá usar o comando `az` na sua interface de linha de comando (Bash, Terminal e Prompt de Comando) para acessar os comandos da CLI do Azure. Digite o comando `az` será apresentada uma saída semelhante a:

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 (Preview) components.
    configure : Configure Azure CLI 2.0 Preview or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    context   : Manage contexts.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove accesses to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

Na sua interface de linha de comando, execute o comando `az storage -h` para listar os comandos do grupo `storage` e seus subgrupos. As descrições dos subgrupos fornecem uma visão geral da funcionalidade que a CLI do Azure fornece para trabalhar com seus recursos de armazenamento.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à assinatura do Azure

Para trabalhar com os recursos na sua assinatura do Azure, você deve primeiro fazer logon na conta do Azure com `az login`. Há várias maneiras de fazer logon:

* **Logon Interativo**: `az login`
* **Faça logon com o nome de usuário e senha**: `az login -u johndoe@contoso.com -p VerySecret`
  * Isso não funciona com contas da Microsoft ou contas que usam autenticação multifator.
* **Faça logon com uma entidade de serviço**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Script de exemplo da CLI do Azure 2.0

Em seguida, trabalharemos com um pequeno script shell que emite alguns comandos básicos da CLI do Azure 2.0 para interagir com recursos do Armazenamento do Azure. Primeiro, o script cria um novo contêiner na sua conta de armazenamento e carrega um arquivo existente (como um blob) para esse contêiner. Ele lista todos os blobs no contêiner e, por fim, baixa o arquivo para um destino no computador local que você especificar.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

echo "Done"
```

**Configurar e executar o script**

1. Abra o editor de texto de sua preferência, e copie e cole o script anterior no editor.

2. Em seguida, atualize as variáveis do script para refletir suas configurações. Substitua os seguintes valores conforme especificado:

   * **\<storage_account_name\>** O nome da sua conta de armazenamento.
   * **\<storage_account_key\>** A tecla de acesso primária ou secundária para sua conta de armazenamento.
   * **\<container_name\>** Um nome para o novo contêiner que será criado, como “azure-cli-sample-container”.
   * **\<blob_name\>** Um nome para o blob de destino no contêiner.
   * **\<file_to_upload\>** O caminho para o pequeno arquivo no seu computador local, tal como “~/images/HelloWorld.png”.
   * **\<destination_file\>** O caminho do arquivo de destino, como “~/downloadedImage.png”.

3. Depois de atualizar as variáveis necessárias, salve o script e sair do editor. As próximas etapas pressupõem que você nomeou seu script como **my_storage_sample.sh**.

4. Marque o script como executável, se necessário: `chmod +x my_storage_sample.sh`

5. Execute o script. Por exemplo, no Bash:`./my_storage_sample.sh`

Você deverá ver uma saída semelhante à seguinte e o **\<destination_file\>** especificado no script deve aparecer no computador local.

```
Creating the container...
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!NOTE]
> A saída anterior está no formato de **tabela**. Você pode especificar qual formato de saída deve ser usado definindo o argumento `--output` nos comandos da sua CLI ou defina-o globalmente usando `az configure`.
>

## <a name="manage-storage-accounts"></a>Gerenciar contas de armazenamento

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para usar o Armazenamento do Azure, você precisa de uma conta de armazenamento. Depois de configurar seu computador para se [conectar à sua assinatura](#connect-to-your-azure-subscription), você pode criar uma nova conta de Armazenamento do Azure.

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` [Obrigatório]: local. Por exemplo, “Oeste dos EUA”.
* `-n` [Obrigatório]: o nome da conta de armazenamento. O nome deve conter entre 3 e 24 caracteres, usando apenas alfanuméricos minúsculo.
* `-g` [Obrigatório]: nome do grupo de recursos.
* `--sku` [Obrigatório]: o SKU da conta de armazenamento. Valores permitidos:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Definir as variáveis de ambiente da conta de armazenamento padrão do Azure
Você pode ter várias contas de armazenamento na sua assinatura do Azure. Para selecionar uma delas para usar em todos os comandos de armazenamento posteriores, você pode definir essas variáveis de ambiente:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta de armazenamento padrão é usar a cadeia de conexão. Em primeiro lugar, obtenha a cadeia de conexão com o comando `show-connection-string`:

```azurecli
az storage account show-connection-string -n <account_name> -g <resource_group>
```

Em seguida, copie a cadeia de conexão de saída e defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` (pode ser necessário circunscrever a cadeia de conexão entre aspas):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> Todos os exemplos nas seções a seguir deste artigo pressupõem que você definiu as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Criar e gerenciar blobs
O Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Esta seção pressupõe que você esteja familiarizado com o conceitos de Armazenamento de Blobs do Azure. Para obter informações detalhadas, confira [Introdução ao Armazenamento de Blobs do Azure usando o .NET](storage-dotnet-how-to-use-blobs.md) e [Blob Service Concepts](/rest/api/storageservices/fileservices/blob-service-concepts) (Conceitos do serviço Blob).

### <a name="create-a-container"></a>Criar um contêiner
Todos os blobs no armazenamento do Azure devem residir em um contêiner. Você pode criar um contêiner usando o comando `az storage container create`:

```azurecli
az storage container create -n <container_name>
```

Você pode definir um dos três níveis de acesso de leitura para um novo contêiner especificando o argumento `--public-access` opcional:

* `off` (padrão): os dados do contêiner são privados para o proprietário da conta.
* `blob`: acesso de leitura público para blobs.
* `container`: acesso de leitura e listagem público para todo o contêiner.

Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura aos contêineres e blobs](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Carregar um blob para um contêiner
O Armazenamento de Blobs do Azure dá suporte a blobs de blocos, anexos e páginas. Carregue os blobs para um contêiner usando o comando `blob upload`:

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 Por padrão, o comando `blob upload` carrega os arquivos de *.vhd para blobs de página ou para blobs de blocos. Para especificar outro tipo ao carregar um blob, você pode usar o argumento `--type`; os valores permitido são `append`, `block` e `page`.

 Para obter mais informações sobre os blobs, consulte [Compreendendo os Blobs de Blocos, Anexos e Páginas](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="download-blobs-from-a-container"></a>Baixar blobs de um contêiner
Este exemplo demonstra como baixar blobs de um contêiner:

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>Copiar blobs
É possível copiar blobs em ou entre regiões e contas de armazenamento de modo assíncrono.

O exemplo a seguir demonstra como copiar blobs de uma conta de armazenamento para outra. Primeiro criamos um contêiner em outra conta, especificando que seus blobs podem ser acessados publicamente e anonimamente. Em seguida, podemos carregar um arquivo para o contêiner e, por fim, copiar o blob do contêiner para o contêiner **mycontainer** na conta atual.

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

A URL do blob de origem (especificada por `-u`) deve ser acessível publicamente ou incluir um token SAS (Assinatura de Acesso Compartilhado).

### <a name="delete-a-blob"></a>Excluir um blob
Para excluir um blob, use o comando `blob delete`:

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Criar e gerenciar compartilhamentos de arquivos
O Armazenamento de Arquivos do Azure oferece armazenamento compartilhado para aplicativos que usam o protocolo SMB. As máquinas virtuais e os serviços de nuvem do Microsoft Azure, bem como aplicativos locais, podem compartilhar dados de arquivos por meio de compartilhamentos montados. Você pode gerenciar compartilhamentos de arquivos e dados de arquivos por meio da CLI do Azure. Para obter mais informações sobre o Armazenamento de Arquivos do Azure, confira [Introdução ao Armazenamento de Arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md) ou [Como usar o Armazenamento de Arquivos do Azure com o Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Um compartilhamento de Arquivos do Azure é um compartilhamento de arquivos do SMB no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento de arquivos. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. O exemplo a seguir cria um compartilhamento de arquivos denominado **myshare**.

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica opcional para um compartilhamento de arquivos do Azure. O exemplo a seguir cria um diretório denominado **myDir** no compartilhamento de arquivos.

```azurecli
az storage directory create -n myDir -s myshare
```

Observe que esse caminho de diretório pode incluir vários níveis, *por exemplo*: **a/b**. No entanto, você deve garantir a existência de todos os diretórios pai. Por exemplo, para o caminho **a/b**, você deve criar o diretório **a** primeiro e, em seguida, o diretório **b**.

### <a name="upload-a-local-file-to-a-share"></a>Carregar um arquivo local para um compartilhamento
O exemplo a seguir carrega um arquivo de **~/temp/samplefile.txt** para a raiz do compartilhamento de arquivos **myshare**. O argumento `--source` especifica o arquivo local existente para o upload.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Assim como na criação do diretório, você pode especificar um caminho de diretório no compartilhamento para carregar o arquivo para um diretório existente dentro do compartilhamento:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Um arquivo no compartilhamento pode ter até 1 TB.

### <a name="list-the-files-in-a-share"></a>Listar os arquivos em um compartilhamento
Você pode listar arquivos e diretórios em um compartilhamento usando o comando `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
```

### <a name="copy-files"></a>Copiar arquivos      
Você pode copiar um arquivo para outro arquivo, um arquivo para um blob ou um blob para um arquivo. Por exemplo, para copiar um arquivo para um diretório em um compartilhamento diferente:        
        
```azurecli
# Get the URL for the source file you want to copy
az storage file url -s myshare -p /mydir/image.png

# Copy the file to another share
az storage file copy start \
    --source-uri https://mystorageaccount.file.core.windows.net/myshare/mydir/image.png \   
    --destination-share myshare2 --destination-path mydir2/image.png        
```

> [!NOTE]
> Há um problema conhecido na CLI 2.0 (Visualização) que impede o uso de `--source-share` e `--source-path`. Você pode usar o argumento `--source-uri` como uma solução alternativa até que esse problema seja resolvido.
>

## <a name="next-steps"></a>Próximas etapas
Veja alguns recursos adicionais para aprender mais sobre como trabalhar com a CLI do Azure 2.0 (Visualização).

* [Introdução às CLI do Azure 2.0 (Visualização)](/cli/azure/get-started-with-az-cli2)
* [Referência de comando da CLI do Azure 2.0 (Visualização)](/cli/azure)
* [CLI do Azure 2.0 (Visualização) no GitHub](https://github.com/Azure/azure-cli)



<!--HONumber=Jan17_HO1-->


