---
title: Início Rápido - Criar uma VM do Linux no Portal do Azure | Microsoft Docs
description: Neste início rápido, você aprende a usar o portal do Azure para criar uma máquina virtual Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fcc9f338ad69322091199ce9d5d2d1d6f9f2165e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227275"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Início Rápido: criar uma máquina virtual do Linux no portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface de usuário baseada em navegador para criar as VMS seus recursos relacionados. Este início rápido mostra como usar o Portal do Azure para implantar uma VM (máquina virtual) Linux no Azure que executa o Ubuntu. Para ver a VM em ação, você habilita o SSH na VM e instala o servidor Web do NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chave SSH, essa etapa pode ser ignorada.

Para criar um par de chaves SSH e realizar o logon em VMs do Linux, execute o seguinte comando a partir do shell do Bash e siga as direções na tela. Por exemplo, você pode usar o [Azure Cloud Shell](../../cloud-shell/overview.md) ou [Windows Substem para Linux](/windows/wsl/install-win10). A saída do comando inclui o nome do arquivo da chave pública. Copie o conteúdo do arquivo de chave pública (`cat ~/.ssh/id_rsa.pub`) para a área de transferência.

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações sobre como criar pares de chave SSH, incluindo o uso de PuTTy, consulte [Como usar chaves SSH com o Windows](ssh-from-windows.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** por Canonical, escolha **Criar**.

1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, escolha **Criar** em **Grupo de recursos**. Na janela pop-up, digite *myResourceGroup* no nome do grupo de recursos e, em seguida, escolha **OK*. 

    ![Criar um grupo de recursos para sua VM](./media/quick-create-portal/project-details.png)

1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Região**. Deixe os outros padrões.

    ![Seção de detalhes da instância](./media/quick-create-portal/instance-details.png)

1. Em **Conta de administrador**, selecione **Chave pública SSH**, digite seu nome de usuário e, em seguida, cole sua chave pública na caixa de texto. Remova os espaços em branco à esquerda ou à direita em sua chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **Regras de porta de entrada** > **Portas de entrada públicas**, escolha **Permitir portas selecionadas** e, em seguida, selecione **SSH (22)** e **HTTP (80)** na lista suspensa. 

    ![Abrir portas para RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.

    
## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Crie uma conexão SSH com a VM.

1. Selecione o botão **Conectar** na página visão geral da sua VM. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para conectar-se ao nome DNS pela porta 22. Em **Logon usando a conta local de VM** um comando de conexão é mostrado. Clique no botão para copiar o comando. O exemplo a seguir mostra a aparência do comando de conexão SSH:

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. Cole o comando de conexão SSH em um shell, como o Azure Cloud Shell ou Bash no Ubuntu no Windows para criar a conexão. 

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do NGINX. Para atualizar fontes de pacote e instalar o último pacote do NGINX, execute os seguintes comandos na sessão do SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, `exit` da sessão SSH e retorne as propriedades da VM no portal do Azure.


## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Com o NGINX instalado e a porta 80 aberta para a sua VM, o servidor Web agora pode ser acessado através da Internet. Abra o navegador Web e insira o endereço IP público da VM. O endereço IP público pode ser encontrado na página de visão geral de VM, ou na parte superior da *Rede* em que você adiciona a regra de porta de entrada.

![Site padrão NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione **Excluir**, em seguida, confirme o nome do grupo de recursos para excluir.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
