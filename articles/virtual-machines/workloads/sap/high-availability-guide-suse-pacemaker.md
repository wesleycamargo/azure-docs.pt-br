---
title: Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure | Microsoft Docs
description: Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 9ce95bcf15d0186c1baea3df407d0fc0c4200f45
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115469"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

Há duas opções para configurar um cluster Pacemaker no Azure. Você pode usar um agente de isolamento que cuida da reinicialização de um nó com falha por meio das APIs do Azure ou você pode usar um dispositivo SBD.

O dispositivo SBD requer uma máquina virtual adicional que atue como um servidor de destino iSCSI e forneça um dispositivo SBD. Este servidor de destino iSCSI, pode, entretante, ser compartilhado com outros clusters do Pacemaker. A vantagem de usar um dispositivo SBD é um tempo de failover mais rápido e, se você estiver usando dispositivos SBD locais, não precisará de quais alterações ao operar o cluster do pacemaker. O isolamento do SBD ainda pode usar o agente de limite do Azure como u, mecanismo de isolamento de backup, caso o servidor de destino iSCSI não esteja disponível.

Se você não quiser investir em uma máquina virtual adicional, também poderá usar o agente do Azure Fence. A desvantagem é que um failover pode levar entre 10 a 15 minutos se uma parada de recurso falhar ou se os nós do cluster não puderem mais se comunicar uns com os outros.

![Pacemaker na visão geral do SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Ao planejar e implantar nós de cluster do Linux Pacemaker e dispositivos SBD, é essencial para a confiabilidade geral da configuração completa do cluster que o roteamento entre as VMs envolvidas e a(s) VM(s) que hospeda(m) o(s) dispositivo(s) SBD não esteja passando por nenhum outro dispositivos como [NVAs](https://azure.microsoft.com/solutions/network-appliances/). Caso contrário, problemas e eventos de manutenção com o NVA podem ter um impacto negativo na estabilidade e confiabilidade da configuração geral do cluster. Para evitar esses obstáculos, não defina regras de roteamento de NVAs ou [regras de Roteamento Definido pelo Usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) que roteiem o tráfego entre nós de cluster e dispositivos SBD por meio de NVAs e dispositivos semelhantes ao planejar e implantar nós de cluster do Linux Pacemaker e dispositivos SBD. 
>


## <a name="sbd-fencing"></a>Isolamento de SBD

Siga etapas a seguir se você quiser usar um dispositivo SBD para isolamento.

### <a name="set-up-an-iscsi-target-server"></a>Configurar um servidor de destino iSCSI

Você precisa primeiro criar uma máquina virtual de destino iSCSI, se você não tiver uma. Os servidores de destino iSCSI podem ser compartilhados com vários clusters Pacemaker.

1. Implante um novo SLES 12 SP1 ou máquina virtual superior e conecte-se ao computador por meio do ssh. A máquina não precisa ser grande. Um tamanho de máquina virtual, como Standard_E2s_v3 ou Standard_D2s_v3 é suficiente.

1. Atualizar o SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Remover pacotes

   Para evitar um problema conhecido com targetcli e SLES 12 SP3, desinstale os pacotes a seguir. Você pode ignorar erros sobre os pacotes que não podem ser encontrados
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Instale os pacotes de destino iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Habilite o serviço de destino iSCSI

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Crie o dispositivo iSCSI no servidor de destino iSCSI

Anexe um novo disco de dados à máquina virtual de destino iSCSI que pode ser usada para este cluster. O disco de dados pode ser menor que 1 GB e deve ser colocado em uma Conta de Armazenamento Premium ou um Disco Gerenciado Premium para aproveitar o [único SLA da VM](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Execute o seguinte comando na **VM de destino iSCSI** para criar um disco iSCSI para o novo cluster. No exemplo a seguir, **cl1** é usado para identificar o cluster novo e **prod-cl1-0** e **prod-cl1-1** são os nomes do host de nós do cluster. Substitua-os pelos nomes do hoste do seus nós de cluster.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>Configurar o dispositivo SBD

Conecte-se ao dispositivo iSCSI que foi criado na última etapa do cluster.
Execute os seguintes comandos nos nós do novo cluster que você deseja criar.
Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]**  Conecte aos dispositivos iSCSI

   Primeiro, habilite o iSCSI e os serviços SBD.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Altere o nome do iniciador no primeiro nó

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do arquivo para corresponder as ACLs que você usou ao criar o dispositivo iSCSI no servidor de destino iSCSI

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Altere o nome do iniciador no segundo nó

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Altere o conteúdo do arquivo para corresponder as ACLs que você usou ao criar o dispositivo iSCSI no servidor de destino iSCSI

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** Reiniciar o serviço iSCSI

   Agora, reinicie o serviço iSCSI para aplicar a alteração
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Conecte aos dispositivos iSCSI. No exemplo a seguir, 10.0.0.17 é o endereço IP do servidor de destino iSCSI e 3260 é a porta padrão. <b>iqn.2006 04.cl1.local:cl1</b> é o nome do destino que será listado quando você executar o primeiro comando.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Certifique-se de que o dispositivo iSCSI está disponível e observe o nome do dispositivo (no seguinte exemplo/des/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Agora, recupere a ID do dispositivo iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   O comando lista três IDs de dispositivo. É recomendável usar a ID que começa com scsi-3, no exemplo acima é
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** Criar o dispositivo SBD

   Use a ID do dispositivo do dispositivo iSCSI para criar um novo dispositivo SBD no primeiro nó do cluster.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Adaptar a configuração SBD

   Abra o arquivo de configuração SBD

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Altere a propriedade do dispositivo SBD, habilite a integração do pacemaker e altere o modo de início de SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Crie o arquivo de configuração do softdog

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Agora, carregue o módulo

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalação do Cluster

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Atualizar o SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Habilitar o acesso ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** Habilitar o acesso ssh

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Habilitar o acesso ssh

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Instalar Agentes de isolamento
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]** Configurar a resolução de nome do host   

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir. A vantagem de usar /etc/hosts é que o cluster se torna independente do DNS, o que também pode ser um ponto único de falhas.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instalar o Cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Adicionar nó ao cluster
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Alterar a senha hacluster para a mesma senha

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Configurar corosync para usar outro transporte e adicione nodelist. Caso contrário, o cluster não funcionará.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Adicione o seguinte conteúdo em negrito para o arquivo se os valores não forem diferentes ou estiverem ausentes. Altere o token para 30.000 para permitir a manutenção da preservação da Memória. Confira [este artigo para o Linux][virtual-machines-linux-maintenance] ou o [Windows][virtual-machines-windows-maintenance] para obter mais detalhes.
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Em seguida, reinicie o serviço corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Alterar as configurações padrão do pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH usa uma Entidade de Serviço para autorização no Microsoft Azure. Siga estas etapas para criar uma Entidade de Serviço.

1. Acesse <https://portal.azure.com>
1. Abra a folha Azure Active Directory  
   Vá para Propriedades e anote a ID do Diretório. Essa é a **ID de locatário**.
1. Clique em Registros do Aplicativo
1. Clique em Adicionar
1. Digite um Nome, selecione Tipo de Aplicativo "Aplicativo Web/API", insira uma URL de logon (por exemplo, http://localhost) e clique em Criar
1. A URL de logon não é usada e pode ser qualquer URL válida
1. Selecione o novo Aplicativo e clique em Chaves na guia Configurações
1. Insira uma descrição para uma nova chave, selecione "Nunca expira" e clique em Salvar
1. Anote o Valor. Ele é usado como **senha** da Entidade de Serviço
1. Anote a ID do Aplicativo. Ela é usada como nome de usuário (**ID de logon** nas etapas abaixo) da Entidade de Serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Criar uma função personalizada para o agente de isolamento

A Entidade de Serviço não tem permissões para acessar os recursos do Azure por padrão. Você precisa fornecer as permissões da Entidade de Serviço para iniciar e parar (desalocar) todas as máquinas virtuais do cluster. Se você ainda não tiver criado a função personalizada, você pode criá-la usando o [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) ou [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

Use o seguinte conteúdo para o arquivo de entrada. Você precisa adaptar o conteúdo às suas assinaturas, ou seja, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 pelas IDs da sua assinatura. Se você tiver apenas uma assinatura, remova a segunda entrada em AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Atribuir a função personalizada à Entidade de Serviço

Atribua a função personalizada “Função do Agente de Isolamento Linux" que foi criada no último capítulo para a Entidade de Serviço. Não mais use a função de proprietário!

1. Acesse https://portal.azure.com
1. Abra a folha Todos os recursos
1. Selecione a máquina virtual do primeiro nó do cluster
1. Clique em Controle de acesso (IAM)
1. Clique em Adicionar
1. Selecione a função "Função do Agente de Isolamento do Linux”
1. Digite o nome do aplicativo criado acima
1. Clique em OK

Repita as etapas acima para o segundo nó do cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Criar os dispositivos STONITH

Depois de editar as permissões das máquinas virtuais, você pode configurar os dispositivos STONITH no cluster.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Criar topologia de limite de isolamento SBD

Se você quiser usar um dispositivo SBD, ainda é recomendável usar um agente de isolamento do Azure como backup caso o servidor de destino iSCSI não esteja disponível.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** Habilitar o uso de um dispositivo STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Próximas etapas
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
