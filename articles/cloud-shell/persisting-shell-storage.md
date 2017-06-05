---
title: "Persistindo arquivos no Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: Passo a passo de como o Azure Cloud Shell persiste arquivos.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 540cd10066e055e2dc132445b9adba5a4112d63a
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Persistindo arquivos no Azure Cloud Shell
Na inicialização, o Azure Cloud Shell solicita sua assinatura para criar uma conta de armazenamento de LRS e um compartilhamento arquivos do Azure para você.

![](media/storage-prompt.png)

### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>Três recursos serão criados em seu nome em uma região com suporte mais próxima a você:
1. Um Grupo de Recursos chamado: `cloud-shell-storage-<region>`
2. Uma Conta de Armazenamento chamada: `cs-uniqueGuid`
3. Um Compartilhamento de Arquivos chamado: `cs-<user>-<domain>-com-uniqueGuid`

Esse compartilhamento de arquivos será montado como `clouddrive` em seu diretório $Home. Esse compartilhamento de arquivos também é usado para armazenar uma imagem de 5 GB criada para você que atualiza e persiste automaticamente seu diretório $Home. Trata-se de uma ação única que é montada automaticamente nas sessões posteriores.

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>O Cloud Shell persiste arquivos com os dois métodos abaixo:
1. Crie uma imagem de disco de seu diretório $Home para persistir arquivos em $Home. Essa imagem de disco é salva no compartilhamento de arquivos especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`

2. Monte o compartilhamento de arquivos especificado como `clouddrive` no diretório $Home para que haja interação direta com o compartilhamento de arquivos. 
`/Home/<User>/clouddrive` é mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!Note]
> Todos os arquivos em seu diretório $Home como chaves SSH são mantidos em sua imagem de disco do usuário armazenada no compartilhamento de arquivo montados. Aplique as práticas recomendadas ao manter informações em seu diretório $Home e no compartilhamento de arquivos montado.

## <a name="using-clouddrive"></a>Usando clouddrive
O Cloud Shell permite que os usuários executem um comando chamado `clouddrive` que permite atualizar manualmente o compartilhamento de arquivos montado no Cloud Shell.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montar um novo clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
O Cloud Shell criará um compartilhamento de arquivos e uma conta de armazenamento para você na primeira inicialização, no entanto, você pode atualizar o compartilhamento de arquivos com o comando `clouddrive mount`.

Se estiver montando um compartilhamento de arquivos existente, as contas de armazenamento devem ser:
1. LRS ou GRS para dar suporte a compartilhamentos de arquivos.
2. Localizadas na região atribuída a você. No momento da integração, a região atribuída a você é listada no nome do grupo de recursos `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
Arquivos do Azure devem residir na mesma região que o computador que está sendo montado. Há máquinas do Cloud Shell nas regiões abaixo:
|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Índia Central, Sudeste Asiático|

### <a name="mount-command"></a>Comando de montagem

> [!NOTE]
> Se estiver montando um novo compartilhamento de arquivos, uma nova imagem do usuário será criada para seu diretório $Home, pois sua imagem $Home anterior é mantida no compartilhamento de arquivos anterior.

1. Execute `clouddrive mount` com os seguintes parâmetros <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver mais detalhes, execute `clouddrive mount -h`: <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmontar clouddrive
Você pode desmontar um compartilhamento de arquivos montado no Cloud Shell a qualquer momento. No entanto, o Cloud Shell requer um compartilhamento de arquivos montado, portanto, será solicitado que você crie e monte um novo compartilhamento de arquivos na sessão seguinte se ele for removido.

Para desanexar um compartilhamento de arquivos do Cloud Shell:
1. Execute o `clouddrive unmount`
2. Reconheça e confirme os prompts

Seu compartilhamento de arquivos continuará existindo, a menos que seja excluído manualmente. O Cloud Shell deixará de procurar por esse compartilhamento de arquivos em sessões subsequentes.

Para ver mais detalhes, execute `clouddrive mount -h`: <br>
![](media/unmount-h.png)

> [!WARNING]
> Esse comando não excluirá nenhum recurso. No entanto, excluir manualmente o grupo de recursos, conta de armazenamento ou compartilhamento de arquivos mapeado para o Cloud Shell apagará sua imagem de disco do diretório $Home e todos os arquivos no compartilhamento de arquivos. Isso não pode ser desfeito.

## <a name="list-clouddrive"></a>Listas clouddrive
Para descobrir qual compartilhamento de arquivos está montado como `clouddrive`:
1. Execute o `df` 

O caminho de arquivo para clouddrive mostrará o nome da conta de armazenamento e o compartilhamento de arquivos na URL.

`//storageaccountname.file.core.windows.net/filesharename`

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

## <a name="transfer-local-files-to-cloud-shell"></a>Transferir arquivos locais para o Cloud Shell
O diretório `clouddrive` é sincronizado com a folha de armazenamento do portal do Azure. Use isto para transferir arquivos locais de ou para o compartilhamento de arquivo. A atualização de arquivos de dentro do Cloud Shell é refletida na GUI do Armazenamento de Arquivos após a atualização de folha.

### <a name="download-files"></a>Baixar arquivos
![](media/download.gif)
1. Navegue até o compartilhamento de arquivos montado
2. Selecione o arquivo de destino no Portal
3. Clique em "Baixar"

### <a name="upload-files"></a>Carregar arquivos
![](media/upload.gif)
1. Navegue até o compartilhamento de arquivos montado
2. Escolha "Carregar"
3. Escolha o arquivo que deseja carregar
4. Confirme o upload

Agora você deve ver o arquivo acessível em seu diretório do clouddrive no Cloud Shell.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md) 
[Saiba mais sobre o Armazenamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 
[Saiba mais sobre marcações de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 
