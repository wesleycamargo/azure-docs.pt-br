---
title: "Persistir arquivos no Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: Passo a passo de como o Azure Cloud Shell persiste arquivos.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 26428ad0d3acda959235ffa780294154ba61bca5
ms.contentlocale: pt-br
ms.lasthandoff: 08/08/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Persistir arquivos no Azure Cloud Shell
O Cloud Shell faz uso do Armazenamento de Arquivos do Azure para persistir os arquivos entre as sessões.

## <a name="set-up-a-clouddrive-file-share"></a>Configurar um compartilhamento de arquivos `clouddrive`
No primeiro início, o Cloud Shell solicita a associação de um compartilhamento de arquivos novo ou existente para persistir arquivos entre as sessões.

### <a name="create-new-storage"></a>Criar novo armazenamento

Ao usar as configurações básicas e seleciona apenas uma assinatura, o Cloud Shell criará três recursos em seu nome na região com suporte mais próxima de você:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs-uniqueGuid`
* Compartilhamento de arquivos:`cs-<user>-<domain>-com-uniqueGuid`

![A configuração da assinatura](media/basic-storage.png)

O compartilhamento de arquivos é montado como `clouddrive` no seu diretório `$Home`. O compartilhamento de arquivos também é usado para armazenar uma imagem de 5 GB criada para você que atualiza e persiste automaticamente seu diretório `$Home`. Isso é uma ação única e o compartilhamento de arquivos será montado automaticamente nas sessões subsequentes.

### <a name="use-existing-resources"></a>Usar recursos existentes

Usando a opção avançada, você pode associar recursos existentes. Quando aparecer o prompt de instalação de armazenamento, selecione **Mostrar configurações avançadas** para exibir opções adicionais. Compartilhamentos de arquivos existentes recebem uma imagem do usuário de 5 GB para persistir seu diretório `$Home`. Os menus suspensos são filtrados para sua região do Cloud Shell, o armazenamento redundante localmente e as contas de armazenamento com redundância geográfica atribuídos.

![A configuração do grupo de recursos](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento criadas no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se você deseja impedir que os usuários criem contas de armazenamento no Cloud Shell, crie uma [Política de recursos do Azure para marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) que seja disparada por essa marca específica.

## <a name="how-cloud-shell-works"></a>Como funciona o Cloud Shell
O Cloud Shell persiste arquivos usando os seguintes métodos:
* Criando uma imagem de disco do seu diretório `$Home` para persistir todo o conteúdo do diretório. A imagem de disco é salva no compartilhamento de arquivos especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` e sincroniza as alterações automaticamente.

* Montando o compartilhamento de arquivos especificado como `clouddrive` no diretório `$Home` para que haja interação direta com o compartilhamento de arquivos. `/Home/<User>/clouddrive` é mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os arquivos em seu diretório `$Home` como chaves SSH são persistidos em sua imagem de disco do usuário, que é armazenada no compartilhamento de arquivos montado. Aplique as práticas recomendadas ao persistir informações em seu diretório `$Home` e no compartilhamento de arquivos montado.

## <a name="use-the-clouddrive-command"></a>Use o comando `clouddrive`
Com o Cloud Shell, você pode executar um comando denominado `clouddrive`, que permite a atualização manual do compartilhamento de arquivos que está montado no Cloud Shell.
![Usando o comando “clouddrive”](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montar um novo`clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
Você pode atualizar o compartilhamento de arquivos associado ao Cloud Shell usando o comando `clouddrive mount`.

Se estiver montando um compartilhamento de arquivos existente, as contas de armazenamento deverão atender ao seguinte:
* Armazenamento redundante localmente ou armazenamento com redundância geográfica para dar suporte a compartilhamentos de arquivos.
* Localizadas na região atribuída a você. Ao fazer a integração, a região atribuída a você será listada no nome do grupo de recursos `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
Os arquivos do Azure devem residir na mesma região que o computador do Cloud Shell em que você estiver montando. Há computadores do Cloud Shell nas regiões a seguir:
|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Índia Central, Sudeste Asiático|

### <a name="the-clouddrive-mount-command"></a>O comando `clouddrive mount`

> [!NOTE]
> Se você estiver montando um novo compartilhamento de arquivos, uma nova imagem de usuário será criada para o diretório `$Home`, pois a imagem de `$Home` anterior é mantida no compartilhamento de arquivos anterior.

Execute o comando `clouddrive mount` com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para exibir mais detalhes, execute `clouddrive mount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive mount'](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmontar o `clouddrive`
Você pode desmontar um compartilhamento de arquivos que esteja montado no Cloud Shell a qualquer momento. No entanto, como o Cloud Shell requer um compartilhamento de arquivos montado, será solicitado que você crie e monte um novo compartilhamento de arquivos na sessão seguinte se ele for removido.

Para remover um compartilhamento de arquivos do Cloud Shell:
1. Execute `clouddrive unmount`.
2. Reconheça e confirme os prompts.

Seu compartilhamento de arquivos continuará existindo se você não o excluir manualmente. O Cloud Shell deixará de procurar por esse compartilhamento de arquivos em sessões subsequentes.

Para exibir mais detalhes, execute `clouddrive unmount -h` conforme mostrado aqui:

![Executando o comando ' clouddrive unmount'](media/unmount-h.png)

> [!WARNING]
> Embora a execução desse comando não exclua todos os recursos, a exclusão manual de um grupo de recursos, de uma conta de armazenamento, ou de um compartilhamento de arquivos mapeado para o Cloud Shell apagará a imagem do disco `$Home` e todos os arquivos em seu compartilhamento de arquivos. Essa ação não pode ser desfeita.

## <a name="list-clouddrive-file-shares"></a>Listar compartilhamentos de arquivos do `clouddrive`
Para descobrir qual compartilhamento de arquivos está montado como `clouddrive`, execute o comando `df` a seguir. 

O caminho de arquivo para o clouddrive mostrará o nome da conta de armazenamento e o compartilhamento de arquivos na URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

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
O diretório `clouddrive` é sincronizado com a folha de armazenamento do portal do Azure. Use essa folha para transferir arquivos locais de ou para o compartilhamento de arquivos. A atualização dos arquivos a partir do Cloud Shell é refletida na GUI do armazenamento de arquivos quando você atualiza a folha.

### <a name="download-files"></a>Baixar arquivos

![Lista de arquivos locais](media/download.png)
1. No portal do Azure, vá para o compartilhamento de arquivos montado.
2. Selecione o arquivo de destino.
3. Selecione o botão **Baixar** .

### <a name="upload-files"></a>Carregar arquivos

![Arquivos locais a serem carregados](media/upload.png)
1. Vá para o compartilhamento de arquivos montado.
2. Selecione o botão **Carregar**.
3. Selecione os arquivos que você deseja carregar.
4. Confirme o upload.

Agora você deve ver os arquivos que estão acessíveis em seu diretório do `clouddrive` no Cloud Shell.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md) <br>
[Saiba mais sobre o armazenamento de Arquivos do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

