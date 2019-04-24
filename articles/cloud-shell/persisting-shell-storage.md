---
title: Manter arquivos para Bash no Azure Cloud Shell | Microsoft Docs
description: Passo a passo de como o Bash no Azure Cloud Shell mantém arquivos.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 0aa00af543a3d21db9b8ad0ed808a8bff0b534e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200114"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Como funciona o armazenamento do Cloud Shell 
O Cloud Shell persiste arquivos usando os seguintes métodos: 
* Criando uma imagem de disco do seu diretório `$Home` para persistir todo o conteúdo do diretório. A imagem de disco é salva no compartilhamento de arquivos especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e sincroniza as alterações automaticamente. 
* Montando o compartilhamento de arquivos especificado como `clouddrive` no diretório `$Home` para que haja interação direta com o compartilhamento de arquivos. `/Home/<User>/clouddrive` é mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os arquivos em seu diretório `$Home` como chaves SSH são persistidos em sua imagem de disco do usuário, que é armazenada no compartilhamento de arquivos montado. Aplique as práticas recomendadas ao persistir informações em seu diretório `$Home` e no compartilhamento de arquivos montado.

## <a name="bash-specific-commands"></a>Comandos específicos do bash

### <a name="use-the-clouddrive-command"></a>Use o comando `clouddrive`
Com o Bash no Cloud Shell, você pode executar um comando denominado `clouddrive`, que permite a atualização manual do compartilhamento de arquivos que está montado no Cloud Shell.
![Usando o comando “clouddrive”](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Montar um novo clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
Você pode atualizar o compartilhamento de arquivos associado ao Cloud Shell usando o comando `clouddrive mount`.

Se estiver montando um compartilhamento de arquivos existente, as contas de armazenamento deverão estar localizadas na sua região selecionada do Cloud Shell. Recupere o local executando `env` do Bash e verificando o `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>O comando `clouddrive mount`

> [!NOTE]
> Se você estiver montando um novo compartilhamento de arquivo, uma nova imagem de usuário será criada para o diretório `$Home`. A imagem `$Home` anterior é mantida em seu compartilhamento de arquivo anterior.

Execute o comando `clouddrive mount` com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para exibir mais detalhes, execute `clouddrive mount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmontar clouddrive
Você pode desmontar um compartilhamento de arquivos que esteja montado no Cloud Shell a qualquer momento. Como o Cloud Shell requer o uso de compartilhamento de arquivos montado, será solicitado que você crie e monte outro compartilhamento de arquivos na sessão seguinte.

1. Execute `clouddrive unmount`.
2. Reconheça e confirme os prompts.

Seu compartilhamento de arquivos continuará existindo se você não o excluir manualmente. O Cloud Shell deixará de procurar por esse compartilhamento de arquivos em sessões subsequentes. Para exibir mais detalhes, execute `clouddrive unmount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora a execução desse comando não exclua todos os recursos, a exclusão manual de um grupo de recursos, de uma conta de armazenamento, ou de um compartilhamento de arquivos mapeado para o Cloud Shell apaga a imagem do disco `$Home` e todos os arquivos em seu compartilhamento de arquivos. Essa ação não pode ser desfeita.

### <a name="list-clouddrive"></a>Lista `clouddrive`
Para descobrir qual compartilhamento de arquivos está montado como `clouddrive`, execute o comando `df`. 

O caminho de arquivo para a unidade de nuvem mostra o nome da conta de armazenamento e o compartilhamento de arquivos na URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>Comandos específicos do PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Compartilhamentos de Arquivos do Azure
O cmdlet `Get-CloudDrive` recupera as informações de compartilhamento de arquivos do Azure montadas no momento por `clouddrive` no Cloud Shell. <br>
![Executando Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar o `clouddrive`
Você pode desmontar um compartilhamento de arquivos do Azure que esteja montado no Cloud Shell a qualquer momento. Se o compartilhamento de arquivos do Azure tiver sido removido, será solicitado que você crie e monte um novo compartilhamento de arquivos do Azure na sessão seguinte.

O cmdlet `Dismount-CloudDrive` desmonta um compartilhamento de arquivos do Azure da conta de armazenamento atual. Desmontar `clouddrive` encerra a sessão atual. O usuário precisará criar e montar um novo compartilhamento de arquivos do Azure durante a próxima sessão.
![Executando Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Observação: Se for necessário definir uma função em um arquivo e chamá-la a partir de cmdlets do PowerShell, o operador de ponto deve ser incluído. Por exemplo: . .\MyFunctions.ps1

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início Rápido do PowerShell no Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre armazenamento de Arquivos do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
