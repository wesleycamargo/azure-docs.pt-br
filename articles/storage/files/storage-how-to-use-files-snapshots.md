---
title: "Trabalhar com instantâneos de compartilhamento (versão prévia) | Microsoft Docs"
description: "Um instantâneo de compartilhamento é uma versão somente leitura de um compartilhamento dos Arquivos do Azure feito em determinado momento, como uma maneira de fazer backup do compartilhamento."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>Trabalhar com instantâneos de compartilhamento (versão prévia)
O instantâneo de compartilhamento (versão prévia) é uma versão somente leitura de um compartilhamento dos Arquivos do Azure feita em determinado momento. Quando um instantâneo de compartilhamento é criado, ele pode ser lido, copiado ou excluído, mas não modificado. Um instantâneo de compartilhamento fornece uma maneira de fazer backup do compartilhamento da maneira como ele aparece em um momento específico. 

Neste artigo, você aprenderá como criar, gerenciar e excluir instantâneos de compartilhamento. Para obter mais informações, consulte [visão geral de instantâneo de compartilhamento](storage-snapshots-files.md) ou sobre [FAQ de instantâneo](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de compartilhamento

Você pode criar um instantâneo de compartilhamento usando o portal do Azure, o PowerShell, a CLI do Azure, a API REST ou qualquer SDK de Armazenamento do Azure. As seções a seguir descrevem como criar um instantâneo de compartilhamento usando o portal, a CLI e o PowerShell. 

Você pode fazer um instantâneo de compartilhamento de um compartilhamento de arquivos enquanto ele está em uso. No entanto, instantâneos de compartilhamento só capturam dados que já foram gravados em um compartilhamento de arquivos no momento em que o comando de instantâneo de compartilhamento é emitido. Isso pode excluir quaisquer dados armazenados em cache por quaisquer aplicativos ou pelo sistema operacional.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Criar um instantâneo de compartilhamento usando o portal  
Para criar um instantâneo de compartilhamento pontual, vá até seu compartilhamento de arquivos no portal e selecione **Criar um instantâneo**.

>   ![Menu Instantâneo no portal](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Criar um instantâneo de compartilhamento usando a CLI do Azure 2.0
Você pode criar um instantâneo de compartilhamento usando o comando `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Saída de exemplo:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>Criar um instantâneo de compartilhamento usando o PowerShell
Você pode criar um instantâneo de compartilhamento usando o comando `$share.Snapshot()`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Realizar operações comuns de instantâneo de compartilhamento

Você pode enumerar os instantâneos de compartilhamento associados com seu compartilhamento de arquivos usando a guia **Versões Anteriores** no Windows, por meio da REST, da biblioteca de cliente, do PowerShell e do portal. Depois que o compartilhamento de arquivos for montado, você poderá exibir todas as versões anteriores do arquivo usando a guia **Versões Anteriores** no Windows. 

As seções a seguir descrevem como usar o Portal do Azure, o Windows e a CLI do Azure 2.0 para listar, navegar até e restaurar instantâneos de compartilhamento.

### <a name="share-snapshot-operations-in-the-portal"></a>Operações de instantâneo de compartilhamento no portal

Você pode examinar todos os seus instantâneos de compartilhamento de um compartilhamento de arquivos no portal e navegar até um instantâneo de compartilhamento para exibir seu conteúdo.

#### <a name="view-a-share-snapshot"></a>Exibir um instantâneo de compartilhamento
Em seu compartilhamento de arquivos, em **Instantâneo**, selecione **Exibir instantâneos**.

![Comando "Exibir instantâneos" no portal](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Listar e navegar até o conteúdo do instantâneo de compartilhamento
Exiba a lista de instantâneos de compartilhamento e depois navegue diretamente até o conteúdo de um instantâneo selecionando o carimbo de data/hora desejado.

![Instantâneo selecionado na lista de carimbos de data/hora](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Você também pode selecionar o botão **Conectar** na sua exibição de lista de instantâneos para obter o comando `net use` e o caminho do diretório para um instantâneo de compartilhamento específico. Depois você pode navegar diretamente até esse instantâneo.


![Painel Conectar com caixa de comando](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Baixar ou restaurar de um instantâneo de compartilhamento
No portal, baixe ou restaure um arquivo de um instantâneo usando os botões **Baixar** ou **Restaurar** respectivamente.

![Botões Baixar e Restaurar](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Operações de instantâneo de compartilhamento no Windows
Quando você já tiver tirado instantâneos de compartilhamento do seu compartilhamento de arquivos, você poderá exibir versões anteriores de um compartilhamento, diretório ou um arquivo específico a partir do seu compartilhamento de arquivos montado no Windows. Por exemplo, eis aqui como você pode usar o recurso Versões Anteriores para exibir e restaurar uma versão anterior de um diretório no Windows.

> [!Note]  
> Você pode executar as mesmas operações em nível de compartilhamento e no nível de arquivo. Somente a versão que contém alterações para esse diretório ou arquivo é mostrada na lista. Se um arquivo ou diretório específico não foi alterado entre dois instantâneos de compartilhamento, o instantâneo de compartilhamento aparece na lista de versões anteriores do nível de compartilhamento, mas não na lista de versões anteriores do diretório ou do arquivo.

#### <a name="mount-a-file-share"></a>Montar um compartilhamento de arquivos
Primeiro, monte o compartilhamento de arquivos usando o comando `net use`.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Abrir um compartilhamento montado no Explorador de Arquivos
Vá para o Explorador de Arquivos e localize o compartilhamento montado.

![Compartilhamento montado no Explorador de Arquivos](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Listar versões anteriores
Navegue até o item ou o item pai que precisa ser restaurado. Clique duas vezes para ir até o diretório desejado. Clique com o botão direito do mouse e selecione **Propriedades** no menu.

![Clique com o botão direito do mouse no menu para um diretório selecionado](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Selecione **Versões Anteriores** para ver a lista de instantâneos de compartilhamento para esse diretório. A lista pode levar alguns segundos para carregar, dependendo da velocidade da rede e do número de instantâneos de compartilhamento no diretório.

![Guia Versões Anteriores](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Você pode selecionar **Abrir** para abrir um instantâneo específico. 

![Instantâneo aberto](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior
Selecione **Restaurar** para copiar o conteúdo do diretório inteiro recursivamente no momento da criação do instantâneo de compartilhamento para o local original.
 ![Botão Restaurar em mensagem de aviso](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Operações de instantâneo de compartilhamento na CLI do Azure 2.0
Você pode usar a CLI do Azure 2.0 para executar operações referentes aos instantâneos de compartilhamento como: listá-los, navegar até seu conteúdo, restaurar ou baixar arquivos deles ou excluí-los.

#### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento

Você pode listar instantâneos de um determinado compartilhamento usando [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) com `--include-snapshots`:

```azurecli-interactive 
az storage share list --include-snapshots
```

O comando fornece a você uma lista de compartilhamento de instantâneos, juntamente com todas as suas propriedades associadas. A saída a seguir é um exemplo:

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>Navegar até um instantâneo de compartilhamento
Você também pode navegar até um determinado instantâneo de compartilhamento e exibir seu conteúdo usando [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Especifique o nome do compartilhamento e o carimbo de data/hora até os quais você deseja navegar, conforme mostrado no exemplo a seguir:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

Na saída, você verá que o conteúdo do instantâneo de compartilhamento é idêntico ao conteúdo do compartilhamento do momento em que esse instantâneo de compartilhamento foi criado:

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento

Você pode restaurar um arquivo copiando-o ou baixando-o do instantâneo de compartilhamento. Use o comando `az storage file download`, conforme mostrado no exemplo a seguir:

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

Na saída, você verá que o conteúdo do arquivo baixado e suas propriedades são idênticos ao conteúdo e às propriedades do momento em que o instantâneo de compartilhamento foi criado:

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento

Você pode excluir instantâneos de compartilhamento usando o Portal do Azure, o PowerShell, a CLI, a API REST ou qualquer SDK de Armazenamento. As seções a seguir descrevem como excluir instantâneos de compartilhamento usando o portal do Azure, a CLI e o PowerShell.

Você pode navegar até os instantâneos de compartilhamento e exibir as diferenças entre dois instantâneos usando qualquer ferramenta de comparação. Depois você pode determinar qual instantâneo de compartilhamento deseja excluir. 

Você não pode excluir um compartilhamento que tem um instantâneo de compartilhamento. Para poder excluir o compartilhamento, primeiro você deve excluir todos os seus instantâneos de compartilhamento.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Excluir um instantâneo de compartilhamento usando o portal  
No portal, você pode ir até sua folha de compartilhamento de arquivos e usar o botão **Excluir** para excluir um ou mais instantâneos de compartilhamento.

>   ![Botão Excluir](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Excluir um instantâneo de compartilhamento usando a CLI do Azure 2.0
Você pode excluir um instantâneo de compartilhamento usando o comando `[az storage share delete]`. Use seu carimbo de data/hora do instantâneo de compartilhamento para o parâmetro `--snapshot '2017-10-04T23:28:35.0000000Z' ` no exemplo a seguir:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Saída de exemplo:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Excluir um instantâneo de compartilhamento usando o PowerShell
Você pode excluir um instantâneo de compartilhamento usando o comando `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral do instantâneo](storage-snapshots-files.md)
* [Perguntas Frequentes do Instantâneo](storage-files-faq.md)
