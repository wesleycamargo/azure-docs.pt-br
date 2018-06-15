---
title: Usar chaves SSH com Windows para VMs Linux | Microsoft Docs
description: Saiba como gerar e usar chaves SSH em um computador Windows para se conectar a uma máquina virtual Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
ms.locfileid: "31601015"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar chaves SSH com o Windows no Azure

Este artigo apresenta maneiras de gerar e usar chaves SSH (Secure Shell) em um computador Windows para criar e conectar-se a uma VM (máquina virtual) do Linux no Azure. Para usar as chaves SSH de um cliente Linux ou macOS, consulte as diretrizes [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Você se conecta às VMs Linux e as gerencia no Azure usando um *cliente SSH*. Computadores que executam o Linux ou macOS geralmente têm um pacote de comandos SSH para gerar e gerenciar chaves SSH e estabelecer conexões SSH. 

Computadores Windows nem sempre têm comandos SSH equivalentes instalados. As versões do Windows 10 que incluem o [Subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/about) permitem executar e acessar utilitários como um cliente SSH nativamente dentro de um shell Bash. 

Se quiser usar algo diferente do Bash for Windows, alguns clientes SSH do Windows comuns poderão ser instalados localmente, e eles estão incluídos nos seguintes pacotes:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git for Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Outra opção é usar os utilitários SSH disponíveis no Bash no [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Acesse o Cloud Shell no seu navegador da Web em [https://shell.azure.com](https://shell.azure.com) ou no [Portal do Azure](https://portal.azure.com). 
* Acesse o Cloud Shell como um terminal de dentro do Visual Studio Code instalando a [extensão de Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Esta seção mostra duas opções para criar um par de chaves SSH no Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Criar chaves SSH com ssh-keygen

Se for possível executar um comando shell como Bash para Windows ou GitBash (ou Bash no Azure Cloud Shell), crie um par de chaves SSH usando o comando `ssh-keygen`. Digite o comando a seguir e responda aos prompts. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte as etapas [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) para criar as chaves com `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Criar chaves SSH com PuttyGen

Se você preferir usar uma ferramenta baseada em GUI para criar chaves SSH, use o gerador de chaves PuttyGen, incluído no [pacote de download do Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para criar um par de chaves SSH RSA com o PuttyGen:

1. Inicie o PuttyGen.

2. Clique em **Gerar**. Por padrão, o PuttyGen gera uma chave SSH-2 RSA de 2048 bits.

4. Passe o mouse sobre a área em branco para gerar certa aleatoriedade para a chave.

5. Depois que a chave pública for gerada, opcionalmente, insira e confirme uma frase secreta. Será solicitado que você insira a frase secreta ao autenticar-se na VM com a sua chave SSH. Sem uma frase secreta, se um indivíduo obtiver a chave privada, ele poderá fazer logon em qualquer VM ou serviço que use essa chave. É recomendável criar uma frase secreta. No entanto, se você esquecer a senha, não é possível recuperá-la.

6. A chave pública é exibida na parte superior da janela. Copie e cole essa chave pública de uma linha no Portal do Azure ou no modelo do Azure Resource Manager ao criar uma VM Linux. Você também pode clicar em **Salvar chave pública** para salvar uma cópia no computador:

    ![Salvar o arquivo de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcionalmente, para salvar a chave privada no formato chave privada do PuTTy (arquivo .ppk), clique em **Salva chave privada**. Será necessário ter arquivo .ppk com o qual você deseja usar o PuTTY posteriormente para estabelecer uma conexão SSH com a VM.

    ![Salvar o arquivo de chave privada do PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se você desejar salvar a chave privada no formato OpenSSH, o formato de chave privada usado por muitos clientes SSH, clique em **Conversões** > **Exportar chave OpenSSH**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Forneça a chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que usa chaves SSH para autenticação, forneça sua chave SSH pública ao criar a VM usando o Portal do Azure ou outros métodos.

O exemplo a seguir mostra como você pode copiar e colar essa chave pública no portal do Azure ao criar uma VM Linux. A chave pública normalmente é armazenada no `~/.ssh/authorized_keys` na sua nova VM.

   ![Usar chave pública quando você cria uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Uma maneira de criar uma conexão SSH do Windows para a VM Linux é usar um cliente SSH. Este é o método preferencial se você tiver um cliente SSH instalado no seu sistema Windows ou usar ferramentas SSH no Bash no Azure Cloud Shell. Se você preferir uma ferramenta baseada em GUI, conecte-se com o PuTTY.  

### <a name="use-an-ssh-client"></a>Usar um cliente SSH
Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, realize SSH para sua VM usando o endereço IP ou nome DNS da VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no comando a seguir pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você configurou uma frase secreta quando criou o par de chaves, insira-a quando solicitado durante o processo de logon.

### <a name="connect-with-putty"></a>Conectar com o PuTTY

Se tiver instalado o [pacote de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e gerado anteriormente uma chave privada PuTTY (arquivo .ppk), você poderá se conectar à VM do Linux com o PuTTY.

1. Inicie o PuTTy.

2. Preencha o nome do host ou o endereço IP da sua VM no portal do Azure:

    ![Abrir nova conexão PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Antes de selecionar **Abrir**, clique em **Conexão** > **SSH** > **guia Autenticação**. Procure e selecione a chave privada do PuTTY (arquivo .ppk):

    ![Selecionar a chave privada do PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique em **Abrir** para se conectar à VM.

## <a name="next-steps"></a>Próximas etapas

* Para ver etapas detalhadas, opções e exemplos avançados para trabalhar com chaves SSH, consulte [etapas detalhadas para criar pares de chave SSH](create-ssh-keys-detailed.md).

* Você também pode usar o PowerShell no Azure Cloud Shell para gerar as chaves SSH e estabelecer conexões SSH com VMs Linux. Consulte o [Início rápido do PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se você tiver problemas ao usar o SSH para se conectar às suas VMs Linux, confira [Solucionar problemas de conexão SSH com uma VM do Linux do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
