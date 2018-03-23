---
title: Disponibilidade do SAP HANA em uma Região do Azure | Microsoft Docs
description: Operações do SAP HANA em VMs nativas do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade do SAP HANA em uma região do Azure
Nesta seção são apresentados vários cenários que descrevem os cenários de disponibilidade em uma Região do Azure. O Azure tem várias regiões, que são distribuídas pelo mundo. Para obter a lista de regiões do Azure, consulte o artigo [Regiões do Azure](https://azure.microsoft.com/regions/). Implantando SAP HANA em VMs em uma Região do Azure, a Microsoft oferece a implantação de uma única VM com uma instância do HANA. Ou, para maior disponibilidade, você pode implantar duas VMs com duas instâncias do HANA dentro de um [Conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que estiver usando Replicação de sistema do HANA para fins de disponibilidade. O Azure tem uma versão prévia pública das [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Essas Zonas de Disponibilidade não serão discutidas em detalhes ainda. Exceto algumas ideias gerais sobre o uso de Conjuntos de Disponibilidade versus Zonas de Disponibilidade.

Qual é a diferença entre Zonas de Disponibilidade e Conjuntos de Disponibilidade do Azure? Para Regiões do Azure onde Zonas de Disponibilidade forem oferecidas, as regiões têm vários data centers, que são independentes no fornecimento de fonte de alimentação, resfriamento e rede. O motivo para a oferta de diferentes regiões dentro de uma única região do Azure é para que você possa implantar aplicativos em duas ou três Zonas de Disponibilidade oferecidas. Supondo que os problemas em fontes de alimentação e/ou rede afetem apenas uma infraestrutura de Zona de Disponibilidade, a implantação do aplicativo dentro de uma região do Azure permanece totalmente funcional. Eventualmente com alguma capacidade reduzida, pois algumas máquinas virtuais em uma região podem ser perdidas. Mas as VMs nas outras duas zonas ainda estão em execução. 
 
Ao passo que um Conjunto de disponibilidade do Azure é uma funcionalidade de agrupamento lógico que você pode usar no Azure para garantir que os recursos da VM colocados nele sejam isolados uns dos outros em relação a falhas quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um Conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Ou, como em outras documentações do Azure, ela é considerada posicionamentos em diferentes [Domínios de atualização e de falha](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Esses posicionamentos normalmente estão dentro de um datacenter do Azure. Supondo que os problemas em fontes de alimentação e/ou rede afetem o datacenter que você implantou, toda a sua capacidade em uma Região do Azure será afetada.

O posicionamento dos data centers que representam as Zonas de Disponibilidade do Azure é um meio-termo entre a entrega de latência de rede entre serviços implantados em diferentes regiões aceitáveis para a maioria dos aplicativos e uma certa distância entre os data centers. De modo que catástrofes naturais idealmente não afetariam o fornecimento e a infraestrutura de energia e rede para todas as Zonas de Disponibilidade nesta região. No entanto, como mostrado por catástrofes naturais monumentais, as Zonas de Disponibilidade nem sempre podem fornecer a disponibilidade dentro de uma região conforme desejado. Considere o furacão Maria que atingiu a ilha de Porto Rico em 20/08/2017 e basicamente causou um blecaute quase total nos 144 km da ilha.   
  


## <a name="single-vm-scenario"></a>Cenário de VM única
Neste cenário, você criou uma Máquina Virtual do Azure para a instância do SAP HANA. Você usou o Armazenamento Premium do Azure para hospedar o disco do sistema operacional e todos os discos de dados. O SLA de tempo de atividade de 99,9% pelo Azure e os SLAs de outros componentes do Azure são suficientes para que você possa cumprir seu SLAs de disponibilidade para seus clientes. Nesse cenário, você não precisa utilizar um Conjunto de Disponibilidade do Azure para VMs que executem a camada de DBMS. Neste cenário você conta com dois recursos diferentes:

- Reinicialização automática de VM do Azure (também conhecida como Recuperação de serviço do Azure)
- Reinicialização automática de SAP HANA

A Reinicialização automática de VM do Azure ou 'recuperação de serviço' é uma funcionalidade no Azure que funciona em dois níveis:

- O host do servidor do Azure verificando a integridade de uma VM hospedada no host do servidor
- O Controlador de malha do Azure monitorando a integridade e a disponibilidade do host do servidor

Para cada VM hospedada em um host de servidor do Azure, uma funcionalidade de verificação de integridade monitora a integridade da(s) VM(s) hospedada(s). Caso VMs entrem em um estado não íntegro, uma reinicialização da VM pode ser iniciada pelo agente de host do Azure que verifica a integridade da VM. O Controlador de Malha do Azure verifica a integridade do host ao verificar muitos parâmetros diferentes que indicam problemas com o hardware do host, mas também verifica na acessibilidade do host por meio da rede. Uma indicação de problemas com o host pode levar a ações como:

- Reinicialização do host e reinicialização das VMs que estavam em execução no host se o host sinalizar o estado de integridade ruim
- Reinicialização do host e reinicialização da(s) VM(s) que foram hospedadas originalmente no host em um host íntegro, caso o host não esteja em um estado íntegro após a reinicialização. Nesse caso, o host será marcado como não íntegro e não será usado para outras implantações até que seja limpo ou substituído.
- Reinicialização imediata das VMs em um host íntegro quando o host não íntegro tem problemas no processo de reinicialização. 

Com o monitoramento do host e da VM fornecido pelo Azure, as VMs do Azure que apresentam problemas de host são automaticamente reiniciadas em um host íntegro do Azure 

O segundo recurso com o qual você pode contar nesse cenário é o fato de que seu serviço HANA que é executado nessa VM reinicializada está sendo iniciado automaticamente após a reinicialização da VM. A [Reinicialização automática do Serviço HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) pode ser configurada por meio dos serviços de watchdog dos diferentes serviços HANA.

Esse cenário de VM único poderia ser melhorado pela adição de um nó de failover passivo para uma configuração de SAP HANA. Ou, como é indicado na documentação do SAP HANA como [Failover automático do Host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Essa configuração pode fazer sentido em situações de implantação local onde o hardware do servidor é limitado e um nó de servidor único é dedicado como nó de Failover automático do Host para um conjunto de hosts de produção. No entanto, em situações como quando a infraestrutura subjacente do Azure está fornecendo um servidor de destino íntegro para uma reinicialização bem-sucedida de uma VM, o cenário de implantação de Failover automático do host do SAP HANA não faz sentido. 

Como resultado, não temos nenhuma arquitetura de referência que preveja um nó em espera para o Failover automático do host do HANA. Isso também se aplica a configurações de expansão do SAP HANA.


## <a name="availability-scenarios-involving-two-different-vms"></a>Cenários de disponibilidade que envolvem duas VMs diferentes
O uso de duas VMs do Azure em Conjuntos de Disponibilidade do Azure permite que você aumente o tempo de atividade entre essas duas VMs se essas VMs forem colocadas em um Conjunto de disponibilidade do Azure dentro de uma região do Azure. A configuração básica no Azure seria semelhante aos gráficos mostrados aqui: ![Duas VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas acima são cortadas e os gráficos limitados às camadas de VMs, hosts, Conjuntos de disponibilidade e Regiões do Azure. As VNets, os Grupos de recursos e as assinaturas do Azure não desempenham um papel nos cenários descritos.

### <a name="replicating-backups-to-second-virtual-machine"></a>Replicação de backups para uma segunda máquina virtual
Uma das configurações mais rudimentares é ter backups, especialmente backups de log de transação enviados de uma VM a outra máquina virtual do Azure. Você tem a opção de qualquer tipo de Armazenamento do Microsoft Azure. Você seria responsável por gerar script da cópia dos backups agendados realizados na primeira VM para a segunda VM. Se o uso exigir as instâncias da segunda VM, você precisaria restaurar os backups totais, incrementais/diferenciais e de log de transação até o ponto que você precisa. A arquitetura seria semelhante a: ![Duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Essa configuração não é muito adequada para a obtenção de tempos RPO e RTO excelentes. Os tempos RTO seriam especialmente afetados devido à necessidade de restaurar completamente todo o banco de dados com os backups copiados. No entanto, essa configuração é utilizável para recuperação após a exclusão não intencional de dados nas instâncias principais. com essa configuração, é possível a qualquer momento restaurar para um determinado ponto no tempo, extrair os dados e importar os dados excluídos para sua instância principal. Portanto, pode fazer sentido usar esse método de cópia de backup em combinação com outros recursos de alta disponibilidade. Durante o tempo em que apenas os backups são copiados, você poderá utilizar uma VM menor do que a VM na qual as instâncias do SAP HANA principal estão sendo executadas. Mas tenha em mente que VMs menores têm um menor número de VHDs que podem ser conectados. Verifique [Tamanhos de máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) para saber os limites de tipos de VM individuais.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Usando a replicação de sistema do SAP HANA sem failover automático
Para os seguintes cenários, você usará replicação de sistema do SAP HANA. A documentação emitida pelo SAP pode ser encontrada a partir do artigo [Replicação do sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Entre duas VMs do Azure em uma única região do Azure, há duas configurações diferentes que têm algumas diferenças no Objetivo de Tempo de Recuperação. Em geral, os cenários sem failover automático podem não ser muito relevantes para cenários de dentro de uma região do Azure. A razão para isso é que na maioria das situações de falha na infraestrutura do Azure, a recuperação do Serviço do Azure reinicia a máquina virtual primária em outro host. Há apenas alguns casos extremos em que essa configuração pode ajudar em termos de cenários de falha. Ou alguns casos que você, como cliente, deseja entender, especialmente a respeito da eficiência.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Usando a Replicação de sistema do HANA sem failover automático e sem pré-carregamento de dados 
Esse é um cenário no qual você usa a Replicação de sistema do SAP HANA para fins de movimentação de dados de forma síncrona para atingir um Objetivo de Ponto de Recuperação (RPO) de 0. No outro lado, você tem Objetivo de Tempo de Recuperação (RTO) suficiente para não precisar de failover ou de pré-carregamento dos dados ao cache de instância do HANA. Nesse caso, você tem a possibilidade de gerar mais economia em sua configuração da seguinte maneira:

- Você pode executar outra instância do SAP HANA na segunda VM que usa a maior parte da memória da máquina virtual. Geralmente, essa instância seria uma instância que pode ser desligada em caso de um failover para a segunda VM. De forma que os dados replicados possam ser carregados no cache da instância do HANA de destino na segunda VM.
- Você pode usar um tamanho menor de VM como segunda VM. No caso de um failover, você teria uma etapa antes do failover manual na qual você redimensionaria a VM ao tamanho da VM de origem. Esse cenário é da seguinte maneira:

![Duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo sem pré-carregamento de dados no destino de Replicação de Sistema do HANA, será necessário pelo menos 64 GB de memória, além de memória suficiente para manter os dados rowstore em memória da instância de destino.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Usando a Replicação de sistema do HANA sem failover automático e com pré-carregamento de dados
A diferença no cenário introduzido antes é que os dados, que são replicados para a instância do HANA na segunda VM, são pré-carregados. Isso elimina as duas vantagens que você pode ter com o cenário de não pré-carregar dados. Nesse caso, você não pode executar outro sistema SAP HANA na segunda VM. Nem pode usar uma VM de tamanho menor. Portanto, esse é um cenário que dificilmente é implementado com os clientes


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Usando a replicação de sistema do SAP HANA com failover automático

A configuração padrão de disponibilidade dentro de uma região do Azure, a maioria dos clientes implementando com SAP HANA, é uma configuração em que as duas máquinas virtuais do Azure executando o Linux SLES têm um cluster de failover definido. O cluster do Linux do SLES é baseado na estrutura [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) em conjunto com um dispositivo [STONITH](http://linux-ha.org/wiki/STONITH). Do ponto de vista do SAP HANA, o modo de replicação usado é sincronizado e um failover automático é configurado. A segunda VM, a instância do SAP HANA age como um nó em espera ativo, que recebe uma transmissão síncrona de registros de alteração da instância do SAP HANA primária. Como as transações são confirmadas pelo aplicativo no nó primário do HANA, o nó primário do HANA espera para confirmar para o aplicativo até a confirmação de que o nó secundário do SAP HANA recebeu o registro de confirmação. Dois modos diferentes de replicação síncrona do SAP HANA. Para obter detalhes e diferenças nesses dois modos de replicação síncrona, leia o artigo [Modos de replicação para a replicação do sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)

A configuração geral é da seguinte maneira

![Duas VMs com a replicação de armazenamento e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Essa solução é escolhida porque ela permite que você alcance um RPO = 0 e um RTO extremamente baixo. Configure a conectividade de cliente do SAP HANA de modo que os clientes do SAP HANA usem o endereço IP virtual para conectar-se à configuração de Replicação de sistema do HANA. Isso elimina a necessidade de reconfigurar o aplicativo caso ocorra um failover para o nó secundário. Nessa solução, os SKUs de VM do Azure para o primário ou secundário deve ser o mesmo.  


## <a name="next-steps"></a>Próximas etapas
Se você precisar de diretrizes passo a passo sobre como definir essa configuração no Azure, leia os artigos:

- [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [SAP no Azure – Parte 4 – Alta disponibilidade para SAP HANA usando Replicação de Sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Se você precisar de mais informações sobre a disponibilidade do SAP HANA em regiões do Azure, leia:

- [Disponibilidade do SAP HANA entre regiões do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

