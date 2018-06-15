---
title: Criar e usar um par de chaves SSH para VMs Linux no Azure | Microsoft Docs
description: Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure para melhorar a segurança do processo de autenticação.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
ms.locfileid: "31602623"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Etapas rápidas: criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure
Com um par de chaves SSH (secure shell), você pode criar VMs (máquinas virtuais) no Azure que usam chaves SSH para autenticação, eliminando a necessidade de senhas para fazer logon. Este artigo mostra como gerar e usar rapidamente um par de arquivos de chaves SSH pública e privada para VMs Linux. É possível concluir essas etapas com o Azure Cloud Shell, um host do macOS ou Linux, o Subsistema do Windows para Linux e outras ferramentas compatíveis com OpenSSH. 

Para obter mais informações e exemplos, consulte [etapas detalhadas para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para ver outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [Como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Use o comando `ssh-keygen` para gerar arquivos de chave SSH pública e privada que, por padrão, são criados no diretório `~/.ssh`. Especifique outro local e uma frase secreta adicional (uma senha para acessar o arquivo de chave privada) quando solicitado. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos.

```bash
ssh-keygen -t rsa -b 2048
```

Se você usar o [CLI do Azure 2.0](/cli/azure) para criar sua VM, opcionalmente, poderá gerar arquivos de chave SSH pública e privada executando o comando [az vm create](/cli/azure/vm#az_vm_create) com a opção `--generate-ssh-keys`. As chaves são armazenadas no diretório ~/.ssh. Observe que essa opção de comando não substitui as chaves se elas já existem no local.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Forneça a chave pública SSH ao implantar uma VM
Para criar uma VM Linux que usa chaves SSH para autenticação, especifique sua chave SSH pública ao criar a VM usando o Portal do Azure, a CLI, modelos do Resource Manager ou outros métodos:

* [Criar uma máquina virtual Linux com o Portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se você não estiver familiarizado com o formato de uma chaves públicas SSH, veja sua chave pública executando `cat` da seguinte forma, substituindo `~/.ssh/id_rsa.pub` por seu próprio local de arquivo de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

Se você copiar e colar o conteúdo do arquivo de chave pública para usar no Portal do Azure ou em um modelo do Resource Manager, não copie um espaço em branco adicional. Por exemplo, se você usar o macOS, direcione o arquivo de chave pública (por padrão, `~/.ssh/id_rsa.pub`) para **pbcopy** para copiar o conteúdo (há outros programas Linux que fazem a mesma coisa, como o **xclip**).

A chave pública colocada em sua VM Linux no Azure é armazenada por padrão em `~/.ssh/id_rsa.pub`, a menos que você altere o local quando criar as chaves. Se você usar o [CLI do Azure 2.0](/cli/azure) para criar sua VM com uma chave pública existente, especifique o valor ou o local dessa chave pública executando o comando [az vm create](/cli/azure/vm#az_vm_create) com a opção `--ssh-key-value`. 

## <a name="ssh-to-your-vm"></a>SSH para sua VM
Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, realize SSH para sua VM usando o endereço IP ou nome DNS da VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no comando a seguir pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você forneceu uma senha quando criou o par de chaves, insira a senha quando receber a solicitação durante o processo de logon. (O servidor é adicionado à sua pasta `~/.ssh/known_hosts` e você não receberá uma solicitação para se conectar novamente até que a chave pública em sua VM do Azure mude ou o nome do servidor seja removido do `~/.ssh/known_hosts`.)

As VMs criadas com chaves SSH são, por padrão, configuradas com as senhas desabilitadas, para tornar as tentativas de adivinhação por força bruta muito mais caras e difíceis. 

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como criar um par de chaves SSH simples para uso rápido. 

* Se precisar de mais ajuda para trabalhar com o par de chaves SSH, consulte [Etapas detalhadas para criar e gerenciar pares de chaves SSH](create-ssh-keys-detailed.md).

* Se você tiver problemas com conexões SSH com uma VM no Azure, consulte [Solucionar problemas de conexão SSH para uma VM Linux no Azure](troubleshoot-ssh-connection.md).


