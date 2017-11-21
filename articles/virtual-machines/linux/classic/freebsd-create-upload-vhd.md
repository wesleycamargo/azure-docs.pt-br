---
title: Criar e carregar uma imagem de VM FreeBSD | Microsoft Docs
description: "Saiba como criar e carregar um VHD (disco rígido virtual) que contenha um sistema operacional FreeBSD para criar uma máquina virtual do Azure"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD FreeBSD para o Azure
Este artigo mostra como criar e carregar um VHD (disco rígido virtual) que contenha um sistema operacional FreeBSD. Depois de carregá-lo, você pode usá-lo como sua própria imagem para criar uma VM (máquina virtual) no Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como carregar um VHD usando o modelo do Resource Manager, veja [aqui](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

* **Uma assinatura do Azure**– Se não tiver uma conta, você poderá criar uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Ferramentas do Azure PowerShell**– O módulo do Azure PowerShell deve estar instalado e configurado para que a assinatura do Azure possa ser usada. Para baixar o módulo, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)(a página pode estar em inglês). Um tutorial que descreve como instalar e configurar o módulo está disponível aqui. Use o cmdlet [Downloads do Azure](https://azure.microsoft.com/downloads/) para carregar o VHD.
* **Sistema operacional FreeBSD instalado em um arquivo .vhd** – É necessário instalar um sistema operacional FreeBSD com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Por exemplo, você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções sobre como instalar e usar o Hyper-V, confira [Instalar o Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Além disso, há um [tutorial no MSDN sobre como usar o FreeBSD com o Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Esta tarefa inclui as quatro etapas a seguir:

## <a name="step-1-prepare-the-image-for-upload"></a>Etapa 1: preparar a imagem para upload
Na máquina virtual na qual o sistema operacional FreeBSD foi instalado, conclua os procedimentos a seguir:

1. Habilitar DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Habilitar SSH.

    Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Ele é habilitado por padrão após a instalação do disco FreeBSD. 
3. Instalar um console serial.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Instalar o sudo.

    A conta raiz está desabilitada no Azure. Isso significa que você precisa utilizar o sudo de um usuário sem privilégios para executar comandos com privilégios elevados.

        # pkg install sudo
   
5. Pré-requisitos para agente do Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Instalar o agente do Azure.

    A versão mais recente do agente do Azure sempre pode ser encontrada no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente dá suporte a FreeBSD 10 e 10.1 e a versão 2.1.4 + (incluindo 2.2.x) oficialmente dá suporte a FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Para o 2.0, vamos usar 2.0.16 como exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Para o 2.1, vamos usar 2.1.4 como exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Depois de instalar o agente do Azure, convém verificar se ele está funcionando:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Desprovisione o sistema.

    Desprovisione o sistema para limpá-lo e torná-lo adequado para o reprovisionamento. O comando a seguir também exclui a última conta de usuário provisionada e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora você pode desligar sua VM.

## <a name="step-2-prepare-the-connection-to-azure"></a>Etapa 2: preparar a conexão com o Azure
Verifique se você está usando a CLI do Azure no modelo de implantação clássico (`azure config mode asm`) e faça logon na sua conta:

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>Etapa 3: Carregar o arquivo .vhd

Você precisa de uma conta de armazenamento para carregar o arquivo do VHD. Você pode escolher uma conta de armazenamento existente ou [criar uma nova](../../../storage/common/storage-create-storage-account.md).

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar em seu Armazenamento de Blobs. Veja a seguir alguns termos que você usará ao carregar o arquivo:

* **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa2.
* **YourImagesFolder** é o contêiner dentro do Armazenamento de Blobs em que você deseja armazenar as imagens.
* **VHDName** é o rótulo que aparece no portal clássico do Azure para identificar o disco rígido virtual.
* **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.

Na janela PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Etapa 4: Criar uma VM com o arquivo .vhd carregado
Depois de carregar o arquivo .vhd, você pode adicioná-lo como uma imagem à lista de imagens personalizadas associadas à sua assinatura e criar uma máquina virtual com essa imagem personalizada.

1. Na janela PowerShell do Azure que você usou na etapa anterior, digite:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Use o Linux como o tipo de sistema operacional. A versão atual do Azure PowerShell aceita apenas "Linux" ou "Windows" como parâmetro.
   >
   >
2. Após concluir as etapas anteriores, a nova imagem será listada ao escolher a guia **Imagens** no portal clássico do Azure.  

    ![Escolher uma imagem](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Crie uma máquina virtual da galeria. Essa nova imagem agora está disponível em **Minhas imagens**.
4. Escolha a nova imagem. Em seguida, percorra as instruções para configurar um nome de host, senha, chave SSH etc.

    ![Imagem personalizada](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Após a conclusão do provisionamento, você verá sua VM do FreeBSD em execução no Azure.

    ![Imagem do FreeBSD no azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
