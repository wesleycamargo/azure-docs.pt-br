---
title: "Início Rápido do Azure – Criar Portal da VM | Microsoft Docs"
description: "Início Rápido do Azure – Criar Portal da VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a9a2e2ace4c4b6867e5d8b318c5d80a9df124ecc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Criar uma máquina virtual Linux com o Portal do Azure

Máquinas virtuais do Azure podem ser criadas por meio do Portal do Azure. Esse método fornece uma interface do usuário baseada em navegador para a criação e configuração de máquinas virtuais e todos os recursos relacionados. Este início rápido explica como criar uma máquina virtual e instalar um servidor Web na VM.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chave SSH, essa etapa pode ser ignorada.

Em um shell Bash, execute este comando e siga as orientações da tela. A saída do comando inclui o nome do arquivo da chave pública. Copie o conteúdo do arquivo de chave pública (`cat ~/.ssh/id_rsa.pub`) para a área de transferência. Se usar o Subsistema do Windows para Linux, verifique se não copiou caracteres de quebra de linha da saída. Observe o nome do arquivo de chave privada para uso posterior.

```bash
ssh-keygen -t rsa -b 2048
```

Você pode encontrar informações mais detalhadas sobre esse processo [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**. 

3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH**. Ao colar na sua chave pública SSH, tome cuidado para remover qualquer espaço em branco à direita ou à esquerda. Ao concluir, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. 

    ![A captura de tela que mostra os tamanhos da VM](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. Em **configurações**, mantenha os padrões e clique em **OK**.

6. Na página de resumo, clique em **OK** para iniciar a implantação da máquina virtual.

7. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, a folha de resumo da VM abrirá automaticamente.


## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Crie uma conexão SSH com a máquina virtual.

1. Clique no botão **Conectar** nas propriedades da máquina virtual. O botão conectar exibe uma cadeia de conexão SSH que pode ser usada para conectar-se à máquina virtual.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Execute o seguinte comando para criar uma sessão SSH. Substitua a cadeia de conexão com aquela que você copiou do Portal do Azure.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Instalar o NGINX

Use o seguinte script bash para atualizar fontes de pacote e instalar o pacote mais recente do NGINX. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, saia da sessão SSH e retorne as propriedades da VM no portal do Azure.


## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web 

Um Grupo de Segurança de Rede (NSG) protege o tráfego de entrada e saída. Quando uma VM é criada no portal do Azure, uma regra de entrada é criada na porta 22 para conexões SSH. Como essa VM hospeda um servidor Web, uma regra NSG precisa ser criada para a porta 80.

1. Na máquina virtual, clique no nome do **Grupo de recursos**.
2. Selecione **o grupo de segurança de rede**. O NSG pode ser identificado usando a coluna **Tipo**. 
3. No menu à esquerda, em configurações, clique em **Regras de segurança de entrada**.
4. Clique em **Adicionar**.
5. Em **Nome**, digite **http**. Verifique se o **intervalo da Porta de Origem** está definido como `*`, **o intervalo da Porta de Destino** está como *80*, e a **Ação** está como *Permitir*. 
6. Clique em **OK**.


## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 aberta para a sua VM, o servidor Web agora pode ser acessada da Internet. Abra o navegador Web e insira o endereço IP público da VM. O endereço IP público pode ser encontrado nas propriedades da VM no Portal do Azure.

![Site padrão NGINX](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
