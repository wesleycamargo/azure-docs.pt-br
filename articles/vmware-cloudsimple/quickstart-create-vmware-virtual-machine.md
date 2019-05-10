---
title: Solução de VMware do Azure por CloudSimple Quickstart - consumir a VMs do VMware no Azure
description: Saiba como configurar e consumir as VMs VMware no portal do Azure usando a solução CloudSimple do VMware do Azure
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0bb8d7a5a1ea30b704b44c9337cd28043597ff7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209518"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guia de início rápido - consuma a VMs do VMware no Azure

Para criar uma máquina virtual no portal do Azure, use modelos de máquina Virtual que o administrador CloudSimple tenha habilitada para sua assinatura. Esses modelos VM são encontrados na infra-estrutura do VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Criação da VM de CloudSimple no Azure requer um modelo de VM

Crie uma máquina virtual em sua nuvem privada da interface do usuário do vCenter. Para criar um modelo, siga as instruções em [clonar uma máquina Virtual a um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store o modelo de VM em sua nuvem privada vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Pesquise **máquinas virtuais de CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual de CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Insira as informações básicas, clique **Next: tamanho**.

    ![Criar máquina virtual de CloudSimple - Noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Assinatura | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de recursos | Grupo de recursos ao qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | NOME | Nome para identificar a máquina virtual.  |
    | Local padrão | Região do Azure no qual essa VM é hospedada.  |
    | Nuvem privada | Nuvem privada CloudSimple onde você deseja criar a máquina virtual. |
    | Pool de Recursos | Pool de recursos mapeada para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo do vSphere | modelo do vSphere para a VM.  |
    | Nome de usuário | Nome de usuário do administrador de VM (para modelos do Windows)|
    | Senha |  Senha para o administrador VM (para modelos do Windows). |
    | Confirmar senha | Confirme a senha |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Next: configurações**. Selecione a caixa de seleção se você quiser expor a virtualização total da CPU para o sistema operacional convidado. Aplicativos que exigem virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou de paravirtualização. Para obter mais informações, consulte o artigo de VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">expor Hardware assistido por virtualização do VMware</a>.

    ![Criar a máquina virtual de CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configurar discos e adaptadores de rede, conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual de CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **interface de rede Add** e defina as seguintes configurações.
    
    | Controle | Descrição |
    | ------------ | ------------- |
    | NOME | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de grupo distribuído de porta configurada no vSphere sua nuvem privada.  |
    | Adaptador | Selecione um adaptador do vSphere na lista de tipos disponíveis configurado para a VM. Para obter mais informações, consulte o artigo da base de dados de Conhecimento de VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolher um adaptador de rede para sua máquina virtual</a>. |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware da NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as seguintes configurações.

    | item | Descrição | 
    | ------------ | ------------- | 
    | NOME | Insira um nome para identificar o disco.  | 
    | Tamanho | Selecione um dos tamanhos disponíveis.  | 
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa de instantâneos. Escolha uma destas opções: <br> -Independente persistente: Todos os dados gravados no disco são gravados permanentemente.<br> -Independente não persistente: As alterações gravadas no disco são descartadas quando você desligar ou redefinir a máquina virtual.  Modo de não persistentes independente permite que você sempre reiniciar a máquina virtual no mesmo estado. Para obter mais informações, consulte o <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação do VMware</a>.

7. Depois que a validação é concluída, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual de CloudSimple - examine](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Próximas etapas

* [Exibir uma lista de máquinas virtuais de CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gerenciar CloudSimple máquina de virtual do Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
