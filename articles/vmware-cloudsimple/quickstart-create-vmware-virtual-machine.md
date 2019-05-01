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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577714"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guia de início rápido - consuma a VMs do VMware no Azure

Para criar uma máquina virtual no portal do Azure, use modelos de máquina Virtual que o administrador CloudSimple tenha habilitada para sua assinatura. Esses modelos VM são encontrados na infra-estrutura do VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Criação da VM de CloudSimple no Azure requer um modelo de VM

Crie uma máquina virtual em sua nuvem privada da interface do usuário do vCenter. Para criar um modelo, siga as instruções em [clonar uma máquina Virtual a um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store o modelo de VM em sua nuvem privada vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. No menu à esquerda, clique em **+** ou **criar um recurso**.

2. No menu à esquerda, clique em **Compute**e, em seguida, clique em **Máquina Virtual de CloudSimple**.

3. Clique em **confirmar** para verificar que você deseja criar uma nova VM.

4. Defina a configuração básica, conforme descrito na tabela a seguir e, em seguida, clique em **Avançar: Tamanho**.

    | Campo | DESCRIÇÃO |
    | ------------ | ------------- |
    | Assinatura | Assinatura do Azure associada à sua implantação de nuvem privada.  |
    | Grupo de recursos | Grupo de implantação para o qual a VM será atribuída. Você pode selecionar um grupo existente ou criar um novo. |
    | NOME | Nome para identificar a máquina virtual.  |
    | Local padrão | Região do Azure no qual essa VM é hospedada.  |
    | Pool de Recursos | Recursos físicos para a VM. Selecione os pools de recursos disponíveis. |
    | Modelo do vSphere | Tipo de modelo do sistema operacional da VM.  |
    | Nome de usuário | Nome de usuário do administrador de VM. |
    | Confirmar senha | Senha para o administrador da VM.  |

5. Selecione o número de núcleos e a capacidade de memória para a VM.

6. (Opcional) Se você deseja expor a virtualização total da CPU para o sistema operacional convidado, selecione a **expor ao sistema operacional convidado** caixa de seleção.
Esta seleção permite que os aplicativos que exigem virtualização de hardware para execução em máquinas virtuais sem conversão binária ou de paravirtualização. Para obter mais informações, consulte o artigo de VMware [expor Hardware assistido por virtualização do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Clique em **Avançar: Configuração**.

8. Configure adaptadores de rede e discos, conforme descrito nas tabelas a seguir.

    Para interfaces de rede, clique em **interface de rede Add** e defina as seguintes configurações.

    | Controle | DESCRIÇÃO |
    | ------------ | ------------- |
    | NOME | Insira um nome para identificar a interface.  |
    | Rede | Selecione na lista de redes configuradas no vSphere sua nuvem privada.  |
    | Adaptador | Selecione um adaptador do vSphere na lista de tipos disponíveis configurado para a VM. Para obter mais informações, consulte o artigo da base de dados de Conhecimento de VMware [escolher um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se deseja habilitar o hardware da NIC quando a VM for inicializada. O padrão é **habilitar**. |

    Para discos, clique em **adicionar disco** e defina as seguintes configurações.

    | item | DESCRIÇÃO |
    | ------------ | ------------- |
    | NOME | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para diferentes sistemas operacionais com suporte.  |
    | Mode | Determina como o disco participa de instantâneos. Escolha uma destas opções: <br> -Independente persistente: Todos os dados gravados no disco são gravados permanentemente.<br> -Independente não persistente: As alterações gravadas no disco são descartadas quando você desligar ou redefinir a máquina virtual.  Modo de não persistentes independente permite que você sempre reiniciar a máquina virtual no mesmo estado. Para obter mais informações, consulte o [documentação do VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Examine as configurações. Para fazer alterações, clique nas guias na parte superior.

10. Clique em **criar** para salvar as configurações e criar a VM.

## <a name="next-steps"></a>Próximas etapas

* [Exibir uma lista de máquinas virtuais de CloudSimple](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Gerenciar CloudSimple máquina de virtual do Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)