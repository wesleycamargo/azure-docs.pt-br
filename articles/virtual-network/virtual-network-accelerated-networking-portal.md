---
title: "Rede acelerada para uma máquina virtual – Portal | Microsoft Docs"
description: "Saiba como configurar a Rede Acelerada para uma máquina virtual do Azure usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: a2e8e0dc40a63c363f295149e35f9823c8e90fa8
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Rede acelerada para uma máquina virtual usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

Rede Acelerada permite SR-IOV (Virtualização de E/S de Raiz Única) para uma VM (máquina virtual), melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos VM com suporte. Esse artigo explica como usar o Portal do Azure para configurar a Rede Acelerada no modelo de implantação do Azure Resource Manager. Você também pode criar uma VM com Rede Acelerada usando o Azure PowerShell. Para saber como fazer isso, clique na caixa do PowerShell na parte superior deste artigo.

A figura abaixo mostra a comunicação entre duas VMs (máquinas virtuais) com e sem Rede Acelerada:

![Comparação](./media/virtual-network-accelerated-networking-portal/image1.png)

Sem Rede Acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais, leia o artigo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualização de Rede Hyper-V e comutador virtual).

Com Rede Acelerada, o tráfego de rede chega à NIC (placa de rede) e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica sem Rede Acelerada são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios de Rede Acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma rede virtual. Ao se comunicar entre redes virtuais ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="limitations"></a>Limitações
Existem as seguintes limitações ao usar essa funcionalidade:

* **Criação de adaptador de rede:** rede acelerada só pode ser habilitada para um novo adaptador de rede.  Não pode ser habilitada em um adaptador de rede existente.
* **Criação da VM:** um adaptador de rede com rede acelerada habilitada somente poderá ser conectado a uma VM quando a VM for criada. O adaptador de rede não pode ser conectado a uma VM existente.
* **Regiões:** oferecida apenas nas regiões do Azure do Centro-Oeste dos EUA e da Europa Ocidental. O conjunto de regiões será expandido no futuro.
* **Sistema de operacional com suporte:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Suporte a Linux e Windows Server 2012 será adicionado em breve.
* **Tamanho da VM:** Standard_D15_v2 e Standard_DS15_v2 são os únicos tamanhos de instância de VM com suporte. Para obter mais informações, consulte o artigo [Tamanhos das máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . O conjunto de tamanhos de instância VM com suporte será expandido no futuro.

Alterações feitas nessas limitações serão anunciadas na página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar uma VM do Windows com rede acelerada
1. Registre-se para a preview enviando um email para [Assinaturas de rede acelerada](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não conclua as etapas restantes antes de receber um email notificando que você foi aceito para a visualização.
2. Faça logon no Portal do Azure em http://portal.azure.com.
3. Criar uma VM seguindo as etapas do artigo [Criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) selecionando as opções a seguir:
   
   * Selecione um sistema operacional listado na seção limitações deste artigo.
   * Selecione um local (região) listado na seção limitações deste artigo.
   * Selecione um tamanho de VM listado na seção limitações deste artigo. Se um dos tamanhos compatíveis não estiver listado, clique em **Exibir todos** na folha **Escolher um tamanho**, para selecionar um tamanho de uma lista expandida.
   * Na folha **configurações**, clique em *Habilitado* para **Rede acelerada**, conforme mostrado na figura a seguir:
     
       ![Configurações](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > A opção de Rede Acelerada só estará visível se você:
     > 
     > * For aceito para a visualização
     > * Selecionar o sistema operacional, o local e os tamanhos de VM compatíveis mencionados na seção limitações deste artigo.
     > 
     > 
4. Depois de criar a VM, baixe o [driver de Rede Acelerada](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conecte-se à VM e faça logon e execute o instalador de driver dentro da VM.
5. Clique com o botão direito do mouse em Windows e clique em **Gerenciador de Dispositivos**. Verifique se **Adaptador Ethernet de Função Virtual Mellanox ConnectX-3** aparece na opção **Rede** quando expandida, conforme mostra a figura a seguir:
   
    ![Gerenciador de Dispositivos](./media/virtual-network-accelerated-networking-portal/image2.png)


