---
title: Movendo arquivos de e para uma VM Linux usando o SCP | Microsoft Docs
description: "Movendo arquivos de e para uma VM Linux com segurança usando o SCP e um par de chaves SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 5ca865475b82b73e5c7e1ab21f098a87548605d9
ms.openlocfilehash: fe07ea13f202f19e399a8ede163fcba932490418


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>Movendo arquivos de e para uma VM Linux usando o SCP

Este artigo mostra como mover arquivos da estação de trabalho para uma VM Linux do Azure ou de uma VM Linux do Azure para a estação de trabalho, usando o SCP (Cópia Segura).  Por exemplo, estamos movendo arquivos de configuração do Azure para uma VM Linux e puxando um diretório de arquivos de log, usando o SCP e chaves SSH.   

Par este artigo, os requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [arquivos de chave SSH pública e privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos

Copiar um arquivo para a VM Linux

```bash
scp file user@host:directory/targetfile
```

Copiar um arquivo da VM Linux

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Mover arquivos entre a estação de trabalho e uma VM Linux, de forma rápida e segura, é uma parte crítica do gerenciamento da infraestrutura do Azure.  Para este artigo, apresentaremos o uso do SCP, uma ferramenta criada com base no SSH e incluída no shell Bash padrão do Linux, Mac e Windows.

## <a name="ssh-key-pair-authentication"></a>Autenticação de par de chaves SSH

O SCP usa o SSH para a camada de transporte.  Usando o SSH para o transporte, o SSH manipula a autenticação no host de destino movendo também o arquivo em um túnel criptografado fornecido por padrão com o SSH.  Para a autenticação do SSH, nomes de usuário e senhas podem ser usados, mas é altamente recomendável a autenticação de chaves SSH pública e privada como uma prática recomendada de segurança. Depois de o SSH autenticar a conexão, o SCP inicia o processo de cópia do arquivo.  Usando `~/.ssh/config` e chaves SSH pública e privada configuradas corretamente, a conexão do SCP pode ser estabelecida sem o uso de um nome de usuário, com apenas um nome do servidor.  Se você tiver apenas uma chave SSH, o SCP a procurará no diretório `~/.ssh/` e a usará por padrão para fazer logon na VM.

Para obter mais informações sobre como configurar o `~/.ssh/config` e chaves SSH pública e privada, siga o artigo [Create SSH keys](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Criar chaves SSH).

## <a name="scp-a-file-to-a-linux-vm"></a>Usar o SCP para copiar um arquivo para uma VM Linux

Para o primeiro exemplo, estamos copiando um arquivo de credenciais do Azure para uma VM Linux usada para implantar a automação.  Como esse arquivo contém credenciais de API do Azure, que incluem segredos, a segurança é importante e o túnel criptografado fornecido pelo SSH protege o conteúdo do arquivo.

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Usar o SCP para copiar um diretório de uma VM Linux

Neste exemplo, estamos copiando um diretório repleto de arquivos de log da VM Linux para a estação de trabalho.  Um arquivo de log pode ou não conter dados confidenciais ou secretos e o uso do SCP garante que o conteúdo dos arquivos de log é criptografado.  Usar o SCP para transferir os arquivos com segurança é a maneira mais fácil de enviar o diretório de log e os arquivos para a estação de trabalho e, ao mesmo tempo, permanecer seguro.

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

O sinalizador `-r` da CLI instrui o SCP a copiar os arquivos e diretórios recursivamente do ponto do diretório listado no comando.  Observe também que a sintaxe de linha de comando é semelhante a um comando de cópia `cp`.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Desabilitar senhas SSH na sua VM Linux configurando o SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


