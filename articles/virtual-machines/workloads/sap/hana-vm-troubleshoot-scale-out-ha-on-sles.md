---
title: Solucionar problemas de instalação do SAP HANA 2.0 expansão HSR Pacemaker com SLES 12 SP3 em máquinas virtuais do Azure | Microsoft Docs
description: Guia para verificar e solucionar problemas de uma configuração de alta disponibilidade de expansão do SAP HANA complexa com base em SAP HSR (HANA System Replication) e Pacemaker em SLES 12 SP3 em execução em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184470"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verificar e solucionar problemas de instalação de alta disponibilidade de expansão do SAP HANA no SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Este artigo foi escrito para ajudar a verificar a configuração de cluster do Pacemaker para expansão do SAP HANA em execução em máquinas virtuais do Azure. A configuração do cluster foi realizada em combinação com SAP HSR (HANA System Replication) e SAPHanaSR-ScaleOut do pacote RPM SUSE. Todos os testes foram realizados em SUSE SLES 12 SP3 apenas. Há várias seções que abrangem áreas diferentes e incluem comandos de exemplo e trechos de arquivos de configuração. Esses exemplos são recomendados como um método para verificar e examinar a configuração do cluster inteiro.



## <a name="important-notes"></a>Observações importantes

Todos os testes para expansão do SAP HANA em combinação com a replicação de sistema do SAP HANA e o Pacemaker foi feita somente com o SAP HANA 2.0. A versão do sistema operacional era SUSE Linux Enterprise Server 12 SP3 para aplicativos SAP. Além disso, o SAPHanaSR-ScaleOut do pacote RPM mais recente do SUSE foi usado para configurar o cluster do pacemaker.
O SUSE publicou uma descrição detalhada dessa configuração otimizada para desempenho, que pode ser encontrada [aqui][sles-hana-scale-out-ha-paper]

Para tipos de máquina virtual que têm suporte para expansão do SAP HANA, confira o [diretório IaaS certificado do SAP HANA][sap-hana-iaas-list]

Houve um problema técnico com expansão do SAP HANA em combinação com várias sub-redes e vNICs e configuração de HSR. É obrigatório usar os patches mais recentes do SAP HANA 2.0, em que esse problema foi corrigido. Há suporte para as seguintes versões do SAP HANA: 

**rev2.00.024.04 ou superior e rev2.00.032 ou superior.**

Caso haja uma situação que exija suporte do SUSE, siga este [guia][suse-pacemaker-support-log-files]. Colete todas as informações sobre o cluster de alta disponibilidade do SAP HANA conforme descrito no artigo. Suporte do SUSE precisa dessas informações para análise posterior.

Durante o teste interno, a configuração do cluster foi confundida por um desligamento normal da VM por meio do portal do Azure. Portanto, é recomendável testar um failover de cluster por outros métodos. Use métodos como forçar um pânico de kernel, desligar as redes ou migrar os recursos **msl** (consulte os detalhes nas seções a seguir). A suposição é que um desligamento padrão acontece de modo intencional. O melhor exemplo de um desligamento intencional é manutenção (consulte detalhes na seção sobre a manutenção planejada).

Durante o teste interno, a instalação do cluster ficou confusa após uma tomada de controle manual do SAP HANA enquanto o cluster estava no modo de manutenção. É recomendável mudá-la de volta manualmente outra vez, antes de encerrar o modo de manutenção do cluster. Outra opção é disparar um failover antes de colocar o cluster em modo de manutenção (consulte a seção sobre a manutenção planejada para obter mais detalhes). A documentação do SUSE descreve como redefinir o cluster em relação nesse sentido usando o comando de crm. Mas a abordagem mencionada antes parecia ser robusta durante o teste interno e nunca mostrou nenhum efeito colaterais inesperados.

Ao usar o comando crm migrate, não deixe de limpar a configuração do cluster. Isso adiciona restrições de local das quais você pode não estar ciente. Essas restrições têm um impacto sobre o comportamento do cluster (veja mais detalhes na seção sobre a manutenção planejada).



## <a name="test-system-description"></a>Descrição do sistema de teste

Para verificação e certificação de alta disponibilidade de expansão do SAP HANA, foi usada uma configuração consistindo em dois sistemas com três nós do SAP HANA cada, um mestre e dois trabalhadores. Aqui está a lista de nomes de VM e os endereços IP internos. Todos os exemplos de verificação mais adiante foram feitos nessas VMs. Usar esses nomes de VM e endereços IP nos exemplos de comando deve ajudar a entender melhor os comandos e suas saídas.


| Tipo de nó | Nome da VM | Endereço IP |
| --- | --- | --- |
| Nó mestre no site 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Nó de trabalho 1 no site 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Nó de trabalho 2 no site 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Nó mestre no site 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Nó de trabalho 1 no site 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Nó de trabalho 2 no site 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Nó do criador da maioria | hso-hana-dm | 10.0.0.13 |
| Servidor do dispositivo SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| Servidor NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Servidor NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Várias sub-redes e vNICs

Seguindo as recomendações de rede do SAP HANA, três sub-redes foram criadas em uma rede virtual do Azure. A expansão do SAP HANA no Azure deve ser instalada no modo não compartilhado, o que significa que todos os nós usam volumes de disco local para **hana/data** e **hana/log**. Devido ao uso apenas de volumes de disco local, não é necessário definir uma sub-rede separada para o armazenamento:

- 10.0.2.0/24 para a comunicação entre nós do SAP HANA
- 10.0.1.0/24 para HSR de Replicação de Sistema do SAP HANA
- 10.0.0.0/24 para todo o resto

Para obter informações sobre a configuração do SAP HANA relacionada ao uso de várias redes, veja a seção **global.ini** mais adiante.

Correspondendo ao número de sub-redes, todas as VMs no cluster têm três vNICs. [Este][azure-linux-multiple-nics] artigo descreve um possível problema de roteamento no Azure ao implantar uma VM do Linux. Este tópico de roteamento específico se aplica somente ao uso de vários vNICs. O problema é resolvido pelo SUSE, por padrão, no SLES 12 SP3. O artigo do SUSE sobre este tópico pode ser encontrado [aqui][suse-cloud-netconfig].


Como uma verificação básica para determinar se o SAP HANA está configurado corretamente para usar várias redes, basta executar os comandos a seguir. A primeira etapa é simplesmente verificar, no nível do sistema operacional, se todos os três endereços IP internos para todas as três sub-redes estão ativos. Caso você tenha definido sub-redes com diferentes intervalos de endereços IP, deverá adaptar os comandos:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Aqui está um exemplo de saída do segundo nó de trabalho no site 2. Você pode ver três endereços IP internos diferentes de eth0, eth1 e eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


A segunda etapa é a verificação das portas do SAP HANA para o servidor de nomes e HSR. O SAP HANA deve escutar nas sub-redes correspondentes. Dependendo do número de instância do SAP HANA, você precisa adaptar os comandos. Para o sistema de teste, o número da instância era **00**. Há diferentes maneiras de descobrir quais portas são usadas. 

Abaixo, você verá uma instrução SQL que retorna a ID da instância e o número de instância, entre outras informações:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Para localizar os números de porta corretos, você pode examinar, por exemplo, no HANA Studio em "**Configuração**" ou por meio de uma instrução SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Para localizar todas as portas usadas na pilha de software SAP, incluindo o SAP HANA, pesquise [aqui][sap-list-port-numbers].

Dado o número de instância **00** no sistema de teste SAP HANA 2.0, o número da porta para o servidor de nomes é **30001**. O número da porta para comunicação de metadados HSR é **40002**. Uma opção é entrar um nó de trabalho e, em seguida, verificar os serviços do nó mestre. Aqui, a verificação foi feita no nó de trabalho 2 no site 2 tentando se conectar ao nó mestre no site 2.

Verifique a porta do servidor de nomes:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

O resultado deve ser semelhante à saída de exemplo a seguir para provar que a comunicação entre nós está usando a sub-rede **10.0.2.0/24**.
Apenas conexão por meio da sub-rede **10.0.2.0/24** deve ter êxito:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Agora verifique a porta HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

O resultado deve ser semelhante à saída de exemplo a seguir para provar que a comunicação HSR está usando a sub-rede **10.0.1.0/24**.
Apenas conexão por meio da sub-rede **10.0.1.0/24** deve ter êxito:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


O arquivo de configuração do corosync precisa estar correto em todos os nós no cluster, incluindo o nó do criador da maioria. Caso a associação de cluster de um nó não tenha funcionado conforme esperado, crie e/ou copie **/etc/corosync/corosync.conf** manualmente em/para todos os nós e reinicie o serviço.

Aqui está o conteúdo do **corosync.conf** do sistema de teste como um exemplo.

A primeira seção é **totem**, conforme descrito nesta [documentação][sles-pacemaker-ha-guide] (etapa de instalação de cluster da seção 11). Você pode ignorar o valor para **mcastaddr**. Apenas mantenha a entrada existente. As entradas para **token** e **consenso** devem ser definidas de acordo com a documentação do SAP HANA no Microsoft Azure, que pode ser encontrada [aqui][sles-pacemaker-ha-guide]

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

A segunda seção de **registro em log** não foi alterada em relação aos padrões determinados:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

A terceira seção mostra a **nodelist**. Todos os nós do cluster devem aparecer com sua ID de nó:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

No **quórum** da última seção, é importante definir o valor de **expected_votes** corretamente. Deve ser o número de nós, incluindo o nó do criador da maioria. E o valor para **two_node** deve ser **0**. Não remova a entrada completamente. Basta definir o valor como **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Reinicie o serviço por meio de **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Dispositivo SBD

A documentação sobre como configurar um dispositivo SBD em uma VM do Azure é descrita [aqui][sles-pacemaker-ha-guide] (isolamento sbd de seção).

O primeiro ponto a verificar é, na VM do servidor SBD, se há entradas ACL para cada nó no cluster. Execute o seguinte comando na VM do servidor SBD:


<pre><code>
targetcli ls
</code></pre>


No sistema de teste, a saída do comando se parecia com o exemplo a seguir. Os nomes de ACL como **iqn.2006-04.hso-db-0.local:hso-db-0** devem ser inseridos como o nome do iniciador correspondente nas VMs. Cada VM precisa de um diferente.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Depois disso, verifique se os nomes do iniciador em todas as VMs são diferentes e correspondem às entradas mostradas acima. Aqui está um exemplo de nó de trabalho 1 no site 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A saída tem a aparência do exemplo a seguir:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Em seguida, verifique se **descoberta** funciona corretamente e execute o seguinte comando em cada nó de cluster usando o endereço IP da VM do servidor SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A saída deve ser semelhante ao exemplo a seguir:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

O próximo ponto de prova é verificar se o nó vê o dispositivo SDB. Verifique-o em cada nó, incluindo o nó do criador da maioria:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A saída deve ser semelhante ao exemplo a seguir. Lembre-se de que os nomes podem ser diferentes (o nome do dispositivo também pode mudar após a reinicialização da VM):

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Dependendo do status do sistema, às vezes pode ajudar reiniciar os serviços iscsi para resolver problemas. Em seguida, execute os comandos a seguir:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


De qualquer nó, você pode verificar se todos os nós estão **limpos**. Apenas fique atento para usar o nome correto do dispositivo em um nó específico:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A saída deve mostrar **limpo** para cada nó no cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Outra verificação de SBD é a opção de **despejo** do comando sbd. Aqui está um exemplo de comando e saída do nó do criador da maioria no qual o nome do dispositivo não era **sdm**, mas **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A saída (além do nome do dispositivo) deve ser a mesma em todos os nós:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Mais uma verificação de SBD é a possibilidade de enviar uma mensagem para outro nó. Execute o seguinte comando no nó 1 do trabalho no site 2 para enviar uma mensagem para o nó de trabalho 2 no site 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

No lado da VM de destino, que era **hso-hana-vm-s2-2** neste exemplo, você pode encontrar a seguinte entrada em **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verifique se as entradas no **/etc/sysconfig/sbd** correspondem à descrição da nossa [documentação][sles-pacemaker-ha-guide] (isolamento sbd da seção). Verifique se a configuração de inicialização em **/etc/iscsi/iscsid.conf** está definida como automática.

Entradas importantes em **/etc/sysconfig/sbd** (adapte o valor de ID, se necessário):

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Outro item a verificar é a configuração de inicialização em **/etc/iscsi/iscsid.conf**. A configuração necessária deveria ter ocorrido pelo comando **iscsiadm** mostrado a seguir, descrito na documentação. Faz sentido verificá-lo e talvez adaptá-lo manualmente com **vi** caso seja diferente.

Comando para definir o comportamento de inicialização:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Entrada em **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Durante o teste e verificação, houve ocorrências em que, após a reinicialização de uma VM, o dispositivo SBD não estava mais visível. Havia uma discrepância entre a configuração de inicialização e o que yast2 mostrava. Para verificar as configurações, execute as etapas abaixo:

1. Iniciar o yast2
2. Selecione **Serviços de Rede** no lado esquerdo
3. Role para baixo no lado direito para **Iniciador iSCSI** e selecione-o
4. Na próxima tela, na guia **Serviço**, você deverá ver o nome do iniciador exclusivo para o nó
5. Acima do nome do iniciador, verifique se o valor **Início do Serviço** está definido como **Ao Inicializar**
6. Se não for o caso, defina-o como **Ao Inicializar**, em vez de **Manualmente**
7. Em seguida, mude a guia superior para **Destinos Conectados**
8. Na tela de destinos conectados, você deverá ver uma entrada para o dispositivo SBD, como neste exemplo: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**
9. Verifique se o valor de Inicialização está definido como "**onboot**"
10. Se não estiver, escolha **Editar** e altere-o
11. Salve as alterações e saia do yast2



## <a name="pacemaker"></a>Marca-Passo

Depois que tudo estiver configurado corretamente, você poderá executar o seguinte comando em todos os nós para verificar o status do serviço do pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

A parte superior da saída deve ser semelhante ao exemplo a seguir. É importante que o status após **Ativo** seja mostrado como **carregado** e **ativo (em execução)**. O status após "Carregado" deve ser mostrado como **habilitado**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Caso a configuração ainda esteja **desabilitada**, execute o seguinte comando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Para ver todos os recursos configurados no pacemaker, execute o seguinte comando:

<pre><code>
crm status
</code></pre>

A saída deve ser semelhante ao exemplo a seguir. Não tem problema os recursos cln e msl serem mostrados como parados na VM do pacemaker da maioria (**hso-hana-dm**). Não há nenhuma instalação do SAP HANA no nó do criador da maioria. Portanto, os recursos **cln** e **msl** são mostrados como parados. É importante que seja mostrado o número correto de total de VMs (**7**). Todas as VMs que fazem parte do cluster devem estar listadas com o status **Online**. O nó mestre primário atual deve ser reconhecido corretamente (neste exemplo, é **hso-hana-vm-s1-0**).

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Um recurso importante do pacemaker é colocá-lo no modo de manutenção. Esse modo permite fazer modificações (por exemplo, uma reinicialização da VM) sem provocar uma ação imediata do cluster. Um caso de uso típico seria a manutenção planejada de infraestrutura de sistema operacional ou do Azure (consulte também uma seção separada sobre a manutenção planejada). Use o seguinte comando para colocar o pacemaker no modo de manutenção:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Durante a verificação com **crm status**, você pode observar, na saída, que todos os recursos estão marcados como **não gerenciados**. Nesse estado, o cluster não reage em nenhuma alteração, como iniciar/parar SAP HANA.
Aqui está uma saída de exemplo do comando **crm status** enquanto o cluster está no modo de manutenção:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


No exemplo de comando abaixo, você verá como encerrar o modo de manutenção do cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Outro comando crm permite obter a configuração de cluster completa em um editor com a possibilidade de editar. Depois de salvar as alterações, o cluster inicia as ações apropriadas:

<pre><code>
crm configure edit
</code></pre>

Para ver apenas a configuração completa do cluster, use a opção crm **show**:

<pre><code>
crm configure show
</code></pre>



Após falhas de recursos de cluster, o comando **crm status** mostra uma lista de **Ações com Falha**. Veja um exemplo para essa saída abaixo:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

É necessário fazer uma limpeza do cluster após falhas. Basta usar o comando crm novamente e usar a opção de comando **limpeza** se livrar-se dessas entradas de ação com falha dando ao recurso de cluster o nome correspondente mostrado abaixo:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

O comando deve retornar uma saída, que se parece com o exemplo a seguir:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover--takeover"></a>Failover/tomada de controle

Como já mencionado na primeira seção com observações importantes, você não deve usar um desligamento normal padrão para testar o failover de cluster ou a tomada de controle do SAP HANA HSR. Em vez disso, é recomendável disparar, por exemplo, um pânico de kernel, forçar uma migração de recursos ou talvez desligar todas as redes no nível do sistema operacional de uma VM. Outro método seria a o comando **crm \<node\> standby**. Também consulte o documento SUSE, que pode ser encontrado [aqui][sles-12-ha-paper]. Abaixo você pode ver três comandos de exemplo para forçar um failover de cluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Como também descrito na seção sobre a manutenção planejada, uma boa maneira de monitorar as atividades de cluster é executar **SAPHanaSR showAttr** com o comando **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Além disso, ajuda a examinar o status de paisagem do SAP HANA proveniente de um script em python do SAP. Esse valor de status é aquele que a configuração do cluster está procurando. Fica claro ao pensar sobre uma falha de nó de trabalho. Se um nó de trabalho ficar inativo, o SAP HANA não retornará imediatamente um erro para a integridade de todo o sistema de expansão. Há algumas tentativas para evitar failovers desnecessários. O cluster reagirá somente se o status mudar de OK (valor retornado 4) para erro (valor retornado 1). Portanto estará correto se a saída do **SAPHanaSR showAttr** mostrar uma VM com o estado **offline**, mas não houver nenhuma atividade ainda para mudar de primário para secundário. Nenhuma atividade do cluster é disparada enquanto o SAP HANA não retornar um erro.

Você pode monitorar o status de integridade de paisagem do SAP HANA como usuário \<SID HANA\>adm chamando o script em python do SAP da seguinte maneira (talvez seja necessário adaptar o caminho):

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A saída desse comando deve ter a aparência do exemplo abaixo. É importante a coluna **Status do Host**, bem como o status **host geral**. A saída real é, na verdade, mais larga e com colunas adicionais.
Para tornar a tabela de saída mais legível neste documento, a maioria das colunas no lado direito foi retirada:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Há outro comando para verificar as atividades do cluster atual. Veja abaixo o comando e a parte final da saída depois que o nó mestre do site primário foi interrompido. Você pode ver a lista de ações de transição, como **promovendo** o antigo nó mestre secundário (**hso-hana-vm-s2-0**) como o novo mestre primário. Se tudo estiver OK e todas as atividades estiverem concluídas, essa lista de **Resumo de Transição** deverá estar vazia.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Manutenção planejada 

Há diferentes casos de uso que dizem respeito à manutenção planejada. Uma pergunta é, por exemplo, se será feita apenas manutenção de infraestrutura, como alterações no nível do sistema operacional e configuração de disco ou uma atualização do HANA.
Você pode encontrar informações adicionais em documentos do SUSE, como [aqui][sles-zero-downtime-paper] ou [outro aqui][sles-12-for-sap]. Esses documentos também incluem exemplos de como migrar manualmente um primário.

Intensos testes internos foram feitos para verificar o caso de uso de manutenção de infraestrutura. Para evitar qualquer tipo de problema relacionado à migração primária, foi tomada a decisão de sempre migrar um primário antes de colocar um cluster em modo de manutenção. Dessa maneira não é necessário fazer com que o cluster se esqueça da situação anterior (qual lado era primário, qual lado era secundário).

Há duas situações diferentes nesse sentido:

1. Manutenção planejada do secundário atual. 
   Neste caso, basta colocar o cluster no modo de manutenção e fazer o trabalho na réplica secundária sem afetar o cluster

2. Manutenção planejada do primário atual. 
   Para permitir que os usuários continuem trabalhando durante a manutenção, é necessário forçar um failover. Com essa abordagem, você deve disparar o failover de cluster por pacemaker, e não apenas no nível do SAP HANA HSR. A configuração do pacemaker automaticamente aciona a tomada de controle do SAP HANA. Além disso, é necessário realizar o failover antes de colocar o cluster em modo de manutenção.

O procedimento para manutenção no site secundário atual gostaria que de realizar as etapas abaixo:

1. Colocar o cluster no modo de manutenção
2. Realizar o trabalho no site secundário 
3. Encerrar o modo de manutenção do cluster

O procedimento para manutenção no site primário atual é mais complexo:

1. Disparar um failover manualmente/tomada de controle do SAP HANA por meio da migração de recursos do Marca-Passo (veja os detalhes abaixo)
2. O SAP HANA no site primário antigo está sendo encerrado pela configuração do cluster
3. Colocar o cluster no modo de manutenção
4. Depois que o trabalho de manutenção estiver concluído, registre o antigo primário como o novo site secundário
5. Limpe configuração do cluster (veja os detalhes abaixo)
6. Encerrar o modo de manutenção do cluster


Migrar um recurso (por exemplo, para forçar um failover) adiciona uma entrada para a configuração do cluster. Você precisa limpar essas entradas antes de encerrar o modo de manutenção. Aqui está um exemplo:

A primeira etapa é forçar um failover de cluster migrando o recurso de msl para o nó mestre secundário atual. O comando a seguir enviará um aviso se uma "restrição de mover" tiver sido criada.

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Verifique o processo de failover por meio do comando **SAPHanaSR showAttr**. O que ajuda a monitorar o status do cluster é abrir uma janela do shell dedicada e iniciar o comando com **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

O resultado deve refletir o failover manual. O antigo nó mestre secundário foi **promovido** (neste exemplo **hso-hana-vm-s2-0**) e o site primário antigo foi interrompido (**lss** valor **1** para o antigo nó mestre primário **hso-hana-vm-s1-0**): 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Após o failover de cluster e a tomada de controle do SAP HANA, coloque o cluster no modo de manutenção conforme descrito na seção do pacemaker.

O comando **SAPHanaSR-showAttr** ou **crm status** não indica nada sobre as restrições criadas pela migração de recursos. Uma opção para tornar essas restrições visíveis é mostrar a configuração do recurso de cluster completa com o seguinte comando:

<pre><code>
crm configure show
</code></pre>

Dentro da configuração de cluster, você deve encontrar uma nova restrição de local causada pela antiga migração de recurso manual. Aqui está um exemplo (entrada iniciando com **location cli-**):

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Infelizmente, essas restrições podem ter um impacto sobre o comportamento geral do cluster. Portanto, é obrigatório removê-las novamente antes de reativar todo o sistema novamente. Com o comando **unmigrate**, é possível limpar as restrições de local criadas antes. A nomenclatura pode ser um pouco confusa. Isso não significa que ele tentaria migrar o recurso de volta para sua VM original da qual ele foi migrado. Apenas remove as restrições de local e também retorna informações correspondentes ao executar o comando:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

No final do trabalho de manutenção, você interrompe o modo de manutenção do cluster conforme mostrado na seção do pacemaker.



## <a name="hbreport-to-collect-log-files"></a>hb_report para coletar arquivos de log

Para analisar problemas de cluster do pacemaker, é útil e também solicitado pelo suporte do SUSE executar o utilitário **hb_report**. Ele coleta todos os arquivos de log importantes que permitem a análise do que aconteceu. Aqui está uma chamada de exemplo usando uma hora de início e de término em que um incidente específico ocorreu (também consulte primeiro a seção sobre observações importantes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

O comando vaza no local em que ele colocou os arquivos de log compactados:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Você pode então extrair os arquivos individuais por meio do comando **tar** padrão:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Examinando os arquivos extraídos, você encontra todos os arquivos de log. A maioria deles foi colocada em diretórios separados para cada nó no cluster:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Dentro do intervalo de tempo especificado, o nó mestre atual **hso-hana-vm-s1-0** foi encerrado. No **journal.log**, você encontrará entradas relacionadas a este evento:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Outro exemplo é o arquivo de log do pacemaker no mestre secundário, que se tornou o novo mestre primário. Aqui está um trecho que mostra que o status do nó mestre primário eliminado foi definido como **offline**.

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>global.ini do SAP HANA


Abaixo, você vê trechos do arquivo global.ini do SAP HANA no site de cluster 2 como um exemplo para mostrar as entradas de resolução de nome de host para usar diferentes redes para comunicação entre nós do SAP HANA e o HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>HAWK

A solução de cluster também fornece uma interface de navegador, que oferece uma GUI interessante para pessoas que preferem menus e elementos gráficos em vez de todos os comandos no nível do shell.
Para usar a interface do navegador, pegue a URL mostrada abaixo e substitua **\<nó\>** por um nó real do SAP HANA e, em seguida, insira as credenciais do cluster (usuário **hacluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

A captura de tela a seguir mostra o painel do cluster:


![Painel do cluster do HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Na segunda captura de tela, você pode ver um exemplo das restrições de local causadas por uma migração de recursos do cluster conforme explicado na seção de manutenção planejada:


![Restrições de lista do HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Outro recurso interessante é a possibilidade de carregar uma saída **hb_report** (consulte a seção **hb_report**) no **HAWK** sob **histórico**, como mostrado na seguinte captura de tela:

![Saída hb_report de upload do HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

O **Gerenciador de Histórico** então permite percorrer todas as transições de cluster incluídas na saída **hb_report**:

![Visão do HAWK das transições dentro de saída hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Na última captura de tela, você pode ver a seção de detalhes de uma única transição, que mostra que o cluster reagiu em uma falha de nó mestre primário (nó **hso-hana-vm-s1-0**) e agora está promovendo o nó secundário como o novo mestre (**hso-hana-vm-s2-0**):

![Visão do HAWK da transição única](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Próximas etapas

Este guia de solução de problemas é sobre a alta disponibilidade para SAP HANA em uma configuração de expansão. Outro componente importante em um cenário SAP, além do banco de dados, é a pilha do SAP NetWeaver. Em seguida, você deve ler sobre a alta disponibilidade para SAP NetWeaver em máquinas virtuais do Azure usando SUSE Enterprise Linux Server [neste][sap-nw-ha-guide-sles] artigo.

