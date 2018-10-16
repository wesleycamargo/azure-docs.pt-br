---
title: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP | Microsoft Docs
description: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
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
ms.date: 09/06/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e46503f8dc97f58db1cd5acfd2122e2895fb15b0
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162301"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[planning-guide]:planning-guide.md 

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implantar o software SAP no Microsoft Azure. Antes de ler este guia, leia o [Guia de planejamento e implementação][planning-guide]. Este documento aborda os aspectos de implantação genérica dos sistemas DBMS relacionados ao SAP em Máquinas Virtuais do Microsoft Azure (VMs), usando a infraestrutura do Azure como recursos de um serviço (IaaS).

O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os principais recursos para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são abordados neste documento, após este documento.

## <a name="definitions-upfront"></a>Definições prévias
No decorrer do documento, os termos a seguir são usados:

* IaaS: infraestrutura como serviço.
* PaaS: plataforma como serviço.
* SaaS: software como serviço.
* Componente SAP: um aplicativo SAP individual como ECC, BW, Solution Manager ou EP.  Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como Desenvolvimento, QAS, Treinamento, DR ou Produção.
* Estrutura da SAP: Este termo refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
* Sistema SAP: a combinação de camada DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção SAP CRM etc. Em implantações do Azure não há suporte para dividir essas duas camadas entre local e Azure. Isso significa que um sistema SAP é implantado localmente ou no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou de forma local. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, mas o sistema de produção CRM SAP localmente.
* Entre instalações: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os datacenters locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo para a conexão é estender domínios locais, Active Directory local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com esta extensão, as VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e resolução de nomes entre VMs implantadas de forma local e VMs implantadas no Azure são possíveis. Este é o cenário mais comum para a implantação de ativos SAP no Azure. Para obter mais informações, consulte [Planejamento e design para gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Implantações entre instalações de sistemas SAP em que máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local têm suporte para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura da SAP completa no Azure requer que essas VMs sejam parte do domínio local e AD/LDAP. Em versões anteriores da documentação, cenários de TI Híbrida foram mencionados, em que o termo *Híbrido* está enraizado no fato de que há uma conectividade entre instalações entre o local e o Azure. Nesse caso, *Híbrido* também significa que as VMs no Azure fazem parte do Active Directory local.
> 
> 

Alguma documentação da Microsoft descreve cenários entre instalações de modo um pouco diferente, especialmente para configurações de HA do DBMS. No caso de documentos relacionados ao SAP, o cenário entre locais se resume a ter uma conectividade de site a site ou privada [ExpressRoute](https://azure.microsoft.com/services/expressroute/) e ao fato de que a estrutura da SAP é distribuída entre os locais e o Azure.

## <a name="resources"></a>Recursos
Há vários artigos na carga de trabalho do SAP no Azure.  É recomendável iniciar em [carga de trabalho do SAP no Azure – Introdução](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e, em seguida, escolha a área de interesses

As seguintes notas SAP estão relacionadas ao SAP no Azure em relação à área de abordados neste documento:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: tipos de VM do Azure e produtos com suporte |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento avançado |
| [2191498] |SAP no Linux com o Azure: monitoramento avançado |
| [2039619] |Aplicativos SAP no Microsoft Azure usando o Banco de Dados Oracle: versões e produtos com suporte |
| [2233094] |DB6: Aplicativos SAP no Azure usando o IBM DB2 para Linux, UNIX e Windows - informações adicionais |
| [2243692] |Linux na VM do Microsoft Azure (IaaS): problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Oracle Database 12c – suporte do sistema de arquivos em Linux |
| [1114181] |Oracle Database 11g – suporte do sistema de arquivos em Linux |


Leia também a [Wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), que contém todas as Notas SAP para Linux.

Você deve ter um conhecimento prático sobre a arquitetura do Microsoft Azure e como as Máquinas Virtuais do Microsoft Azure são implantadas e operadas. Você pode encontrar mais informações em [Documentação do Azure](https://docs.microsoft.com/azure/).

Embora estejamos discutindo IaaS, em geral, as instalação e a configuração do Windows, do Linux e do DBMS são essencialmente as mesmas que em qualquer máquina virtual ou computador bare metal que você instalaria localmente. No entanto, há algumas decisões de implementação de gerenciamento de sistema e arquitetura que são diferentes ao utilizar IaaS do Azure. A finalidade deste documento é explicar as diferenças de gerenciamento de sistema e de arquitetura específicas para as quais você deve estar preparado ao usar o IaaS do Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implantações de RDBMS
Para seguir este capítulo, é necessário compreender o que foi apresentado [neste][deployment-guide-3] capítulo do [Guia de Implantação][deployment-guide]. O conhecimento sobre as diferentes séries de VM e suas diferenças e as diferenças do [Armazenamento Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) e Standard do Azure deve ser compreendido e conhecido antes de ler este capítulo.

Em termos de Armazenamento do Azure para VMs do Azure, você deve estar familiarizado com os artigos:

- [Sobre armazenamento de discos para VMs do Windows do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [Sobre armazenamento de discos para VMs do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds) 

Numa configuração básica, nós normalmente recomendamos uma estrutura de implantação na qual o sistema operacional, o DBMS e eventuais binários SAP são separados dos arquivos de banco de dados. Portanto, recomendamos que os sistemas SAP em execução em Máquinas Virtuais do Azure tenham a VHD base (ou disco) instalada com o sistema operacional, os executáveis do sistema de gerenciamento de banco de dados e os executáveis SAP. Os arquivos de dados e de log do DBMS são armazenados no Armazenamento do Azure (Armazenamento Standard ou Premium) em discos separados e anexados como discos lógicos à VM da imagem do sistema operacional do Azure original. Especialmente em implantações do Linux, pode haver diferentes recomendações documentadas. Especialmente em torno do SAP HANA.  

Ao planejar seu layout de disco, você precisa encontrar o melhor equilíbrio entre os seguintes itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* A taxa de transferência geral do armazenamento que uma VM pode fornecer.
* A latência que diferentes tipos de Armazenamento do Azure podem fornecer.
* SLAs VM

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no Armazenamento Standard e no Armazenamento Premium do Azure. Latência de e/s também é diferente entre os dois tipos de armazenamento.  Com o Armazenamento Premium fornecendo fatores de melhor latência de e/s. Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que você pode anexar. Outra restrição é que somente determinados tipos de VM podem aproveitar o Armazenamento Premium do Azure. Como resultado, a decisão para um determinado tipo de VM pode ser orientada não apenas pelos requisitos de CPU e memória, mas também pelo IOPS, pelos requisitos de taxa de transferência do disco e pela latência, que normalmente são dimensionados conforme o número de discos ou o tipo de discos de Armazenamento Premium. Especialmente com o Armazenamento Premium, o tamanho de um disco também pode ser determinado pelo número de IOPS e pela taxa de transferência que precisam ser atingidos em cada disco.

> [!NOTE]
> Para implantações de DBMS, o uso do Armazenamento Premium para quaisquer dados, log de transações ou arquivos de restauração é altamente recomendável. Assim, não importa se você deseja implantar os sistemas de não produção ou de produção.

> [!NOTE]
> Para se beneficiar do Azure exclusivo do [SLA de única VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) todos os discos anexados precisam ser do tipo de Armazenamento Premium do Azure, incluindo o VHD de base.
>

O posicionamento de arquivos de banco de dados e arquivos de log/refazer e o tipo de Armazenamento do Azure usado devem ser definidos por requisitos de taxa de transferência, latência e IOPS. Para ter IOPS suficiente, você poderá ser forçado a aproveitar vários discos ou usar um disco de Armazenamento Premium maior. No caso de usar vários discos, você criaria uma distribuição de software entre discos, que contêm os arquivos de dados ou arquivos de log/refazer. Nesses casos, o IOPS e SLAs de discos do Armazenamento Premium subjacentes ou o máximo de discos de IOPS de armazenamento Standard do Azure que podem ser obtido da taxa de transferência de disco são cumulativos para o conjunto resultante de distribuição. 

Como já foi mencionado, se seus requisitos de IOPS excedem o que pode fornecer um único VHD, você precisa equilibrar o número de IOPS necessários para os arquivos de banco de dados entre um número de VHDs. A maneira mais fácil de distribuir a carga de IOPS em discos é criar uma faixa de software nos diferentes discos. Em seguida, colocar um número de arquivos de dados do SAP DBMS no LUNS retirado da faixa de software. o número de discos na distribuição é orientado pelas demandas, demandas de taxa de transferência de disco e volume de IOPs demandas. 


- - -
> ![Windows][Logo_Windows] Windows
> 
> É recomendável usar espaços de armazenamento do Windows para criar esses conjuntos de distribuição entre vários VHDs do Azure. É recomendável usar pelo menos Windows Server 2012 R2 ou Windows Server 2016.
> 
> ![Linux][Logo_Linux] Linux
> 
> Há suporte apenas para MDADM e LVM (Logical Volume Manager) para criar um RAID de software no Linux. Para obter mais informações, leia os seguintes artigos:
> 
> - [Configurar RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurar o LVM em uma VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
> 
> 

- - -
 
> [!NOTE]
> Já que o Armazenamento do Azure está mantendo três imagens dos VHDs, não faz sentido configurar uma redundância enquanto estiver distribuindo. Você só precisa configurar a distribuição, portanto, se as E/Ss estão sendo distribuídas nos diferentes VHDs.
>

### <a name="managed-or-non-managed-disks"></a>Discos não gerenciados ou gerenciados
Uma Conta de Armazenamento do Azure não é apenas um constructo administrativo, mas também uma entidade de limitações. As limitações são diferentes entre a Conta de Armazenamento Standard do Azure e as Contas de Armazenamento Premium do Azure. As funcionalidades e limitações exatas são listadas no artigo [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Para o Armazenamento Standard do Azure, é importante lembrar que há um limite no IOPS por conta de armazenamento (linha contendo **Taxa de Solicitação Total** no artigo [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). Além disso, há um limite inicial do número de contas de armazenamento por assinatura do Azure. Portanto, você precisará balancear VHDs para uma estrutura SAP maior entre diferentes contas de armazenamento para evitar atingir os limites dessas contas de armazenamento. Um trabalho tedioso quando você estiver falando de algumas centenas de computadores virtuais com mais de mil VHDs. 

Uma vez que não é recomendável usar o armazenamento Standard do Azure para implantações de DBMS em conjunto com carga de trabalho do SAP, referências e recomendações para o armazenamento Standard do Azure são limitadas a este curto [artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planejamento e implantação de VHDs em diferentes contas de armazenamento do Azure, a Microsoft introduziu o que é chamado de [Discos Gerenciados](https://azure.microsoft.com/services/managed-disks/) em 2017. Discos gerenciados estão disponíveis para Armazenamento Standard do Azure, bem como o armazenamento Premium do Azure. As principais vantagens de Discos Gerenciados em comparação com a lista de discos não gerenciados, como:

- Para Discos Gerenciados, o Azure distribui diferentes VHDs em diferentes contas de armazenamento automaticamente no momento da implantação e, assim, evita atingindo os limites de uma conta de Armazenamento do Azure em termos de volume de dados, taxa de transferência de e/s e IOPS.
- Usando Discos Gerenciados, o Armazenamento do Azure está honrando os conceitos dos Conjuntos de Disponibilidade do Azure e com isso implanta o VHD base e o disco conectado de uma VM em diferentes domínios de falha e atualização se a VM fizer parte de um Conjunto de Disponibilidade do Azure.


> [!IMPORTANT]
> Considerando as vantagens dos Discos Gerenciados do Azure, é altamente recomendável usar os Discos Gerenciados do Azure para suas implantações de DBMS e implantações do SAP em geral.
>

Para converter de não gerenciados para discos gerenciados, consulte os artigos:

- [Converter uma máquina virtual do Windows de discos não gerenciados em Discos Gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Converter uma máquina virtual Linux de discos não gerenciados em Discos Gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e discos de dados
Quando você monta discos carregados em VMs, pode escolher se o tráfego de E/S entre a VM e os discos no Armazenamento do Azure são armazenadas em cache. O Armazenamento Standard e Premium do Azure usam duas tecnologias diferentes para esse tipo de cache. 

As recomendações abaixo pressupõem que as características de E/S para o DBMS Standard:

- É principalmente lido carga de trabalho contra arquivos de dados de um banco de dados. Essas leituras são de desempenho crítico para o sistema DBMS
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de verificação ou num fluxo constante. No entanto, uma média ao longo do dia, as gravações são menores do que as leituras. Em vez de leituras de arquivos de dados, essas gravações são assíncronas e estão obstruindo nenhuma transação de usuário.
- Praticamente, não há nenhuma leitura dos arquivos de refazer ou log de transação. As exceções são grandes E/Ss ao executar backups de log de transações. 
- A carga principal em relação a arquivos de log de transação ou refazer são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB.
- Todas as gravações precisam ser persistidas em disco de forma confiável

Para o Armazenamento Standard do Azure, os possíveis tipos de cache são:

* Nenhum
* Ler
* Leitura/Gravação

Para obter um desempenho consistente e determinístico, você deve definir o cache no Armazenamento Standard do Azure para todos os discos contendo **Arquivos de dados relacionados ao DBMS, arquivos de log/refazer e espaço de tabela como ‘NONE’**. O caching de base VHD pode permanecer com o padrão.

Para o Armazenamento Premium do Azure, existem as seguintes opções de caching:

* Nenhum
* Ler 
* Leitura/gravação 
* Nenhum + Acelerador de Gravação (somente para VMs da série M do Azure)
* Leitura + Acelerador de Gravação (somente para VMs da série M do Azure)

A recomendação para o Armazenamento Premium do Azure é aproveitar o **Cache de leitura para arquivos de dados** do banco de dados SAP e escolher **Sem cache para discos de arquivos de log**.

Para implantações da série M, é altamente recomendável usar o Acelerador de gravação do Azure para sua implantação do DBMS. Para obter detalhes, restrições e implantação do Acelerador de Gravação do Azure consulte o documento [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator). 


### <a name="azure-non-persistent-disks"></a>Discos do Azure não persistente
As VMs do Azure oferecem discos não persistentes depois que uma VM é implantada. No caso de uma reinicialização da VM, todo o conteúdo nessas unidades será apagado. Portanto, é um dado que arquivos de dados e arquivos de log/restauração de bancos de dados sob nenhuma circunstância encontra-se nessas unidades são persistentes. Pode haver exceções para alguns dos bancos de dados, onde essas unidades não persistentes poderiam ser adequadas para espaços de tabela temporários e tempdb. No entanto, evite usar essas unidades VMs da série A, pois essas unidades não persistentes são limitadas na taxa de transferência dessa família VM. Para obter mais detalhes, leia o artigo [Noções básicas sobre a unidade temporária em máquinas virtuais do Windows Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> A unidade D:\ em uma VM do Azure é uma unidade não persistente que é apoiada por alguns discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo na unidade D:\ são perdidas quando a VM é reiniciada. Por “alterações”, como arquivos armazenados, diretórios criados, aplicativos instalados etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> As VMs do Azure do Linux montam automaticamente uma unidade em /mnt/resource, que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo em /mnt/resource são perdidas quando a VM é reinicializada. Por alterações, como arquivos armazenados, diretórios criados, aplicativos instalados etc.
> 
> 

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento do Microsoft Azure
O Armazenamento do Microsoft Azure armazena a VHD de base (com o SO) e os discos anexados ou BLOBs em pelo menos três nós de armazenamento separados. Esse fato é chamado de Armazenamento com Redundância Local (LRS). O LRS é o padrão para todos os tipos de armazenamento no Azure. 

Há vários outros métodos de redundância, que são descritos no artigo [replicação de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Para o Armazenamento Premium do Azure, que é o tipo recomendado de armazenamento para VMs de DBMS e os discos que armazenam arquivos de banco de dados e a restauração do log, o método somente disponível é LRS. Como resultado, você precisará configurar métodos de banco de dados, como o SQL Server Always On, o Oracle Data Guard ou HANA System Replication para habilitar a replicação de dados do banco de dados em outra região do Azure ou em outra zona de disponibilidade do Azure.


> [!NOTE]
> Para implantações de DBMS, o uso de Armazenamento com Redundância Geográfica como disponíveis com o Armazenamento Standard do Azure não é recomendável, pois ele tem um sério impacto no desempenho e não honra a ordem de gravação entre diferentes VHDs anexados a uma VM. O fato de não honrar a ordem de gravação em diferentes VHDs tem um alto potencial para acabar em bancos de dados inconsistentes no lado do destino de replicação se os arquivos de banco de dados e log/redo forem distribuídos por vários VHDs (como na maioria das vezes) no lado da VM de origem.

 

## <a name="vm-node-resiliency"></a>Resiliência de nó de VM
A plataforma Azure oferece vários SLAs diferentes para as VMs. Os detalhes exatos podem ser encontrados na versão mais recente do [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada de DBMS geralmente é uma parte crítica da disponibilidade de um sistema SAP, você precisa ficar familiarizado com os conceitos de eventos de manutenção, as zonas de disponibilidade e conjuntos de disponibilidade. Os artigos que descrevem todos esses conceitos são [Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gerenciar a disponibilidade de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).  

Recomendação mínima para cenários de DBMS de produção com carga de trabalho do SAP é:

- Implante duas VMs em um conjunto de disponibilidade separado na mesma região do Azure.
- Essas duas VMs executariam na mesma VNet do Azure e teriam NICs anexadas das mesmas sub-redes.
- Use os métodos de banco de dados para manter um modo de espera ativo com a segunda VM. Métodos podem ser SQL Server Always On, o Oracle Data Guard ou HANA System Replication.

Além disso, você pode implantar uma terceira VM em outra região do Azure e usar os mesmos métodos de banco de dados para fornecer uma réplica assíncrona em outra região do Azure.

A maneira de configurar conjuntos de disponibilidade do Azure é demonstrada neste [tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações sobre Rede do Azure 
Em implantações do SAP em larga escala, a recomendação é que você esteja usando o plano gráfico de [Datacenter Virtual do Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) de suas atribuições de configuração e as permissões e a função de rede virtual para diferentes partes da sua organização.

Há várias práticas recomendadas, resultando em centenas de implantações de clientes:

- A(s) VNet(s) a aplicação SAP está implementada, não tem acesso à Internet.
- As máquinas virtuais de banco de dados estão em execução na mesma rede virtual como a camada de aplicativo.
- As VMs na VNet têm uma alocação estática do endereço IP privado. Veja o artigo [Tipos de endereço IP e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) como referência.
- Restrições de roteamento de e para as VMs de DBMS **não** são definidas com firewalls instalados nas VMs DBMS locais. Em vez disso, o roteamento de tráfego é definido com [grupos de segurança de rede (NSG) do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Para fins de separação e isolar o tráfego para a VM de DBMS, você pode atribuir diferentes NICs à VM. Em que cada NIC tem um endereço IP diferente e cada NIC é um valor atribuído a uma sub-rede de rede virtual diferente, que novamente tem diferentes regras NSG. Tenha em mente que o isolamento ou a separação do tráfego de rede é apenas uma medida para o roteamento e não permite definir cotas para taxa de transferência de rede.

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do SO convidado para um vNIC. Alguns serviços do Azure, como o serviço de Backup do Azure, dependem do fato de que pelo menos o vNIC primário esteja definido como DHCP, e não para endereços IP estáticos. Veja também o documento [Solucionar problemas de backup da máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se você precisar atribuir vários endereços IP estáticos a uma VM, precisará atribuir vários vNICs a uma VM.
>
>

Usando duas VMs para sua implantação de DBMS dentro de um conjunto de disponibilidade do Azure além de um roteamento separado para a camada de aplicativo SAP e o tráfego de gerenciamento e operações para as duas VMs de DBMS em produção, o diagrama aproximado pareceria com:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Balanceador de carga do Azure para redirecionar o tráfego
O uso de endereços IP privados virtuais usados em funcionalidades como a replicação do SQL Server Always On ou sistema HANA requer a configuração do balanceador de carga do Azure. O balanceador de carga do Azure é capaz de por meio de portas de investigação determinar o nó ativo do DBMS e rotear o tráfego exclusivamente para esse nó do banco de dados ativo. Caso ocorra um failover do nó banco de dados, não é necessário para o aplicativo SAP reconfigurar. Em vez disso, as arquiteturas de aplicativos SAP mais comuns se reconectarão contra o endereço IP virtual. Enquanto isso o balanceador de carga do Azure reagiu no failover de nó ao redirecionar o tráfego contra o endereço IP virtual para o segundo nó.

O Azure oferece duas diferentes [SKUs do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Um SKU básico e um SKU standard. A menos que você deseje implantar em zonas de disponibilidade do Azure, o SKU do balanceador de carga básico faz bem. 

O tráfego entre as VMs de DBMS e camada do aplicativo SAP é sempre roteado por meio do balanceador de carga do Azure o tempo todo? A resposta depende de como você configura o balanceador de carga. Neste momento, o tráfego de entrada para a VM de DBMS sempre será roteado por meio do balanceador de carga do Azure. A rota de tráfego de saída da VM do DBMS para a VM da camada de aplicativo depende da configuração do balanceador de carga do Azure. O balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado de VM de DBMS para a camada de aplicativo SAP **não** será roteado por meio do balanceador de carga do Azure. Em vez disso, ele irá diretamente para a camada de aplicativo. Se DirectServerReturn não estiver configurado, o tráfego de retorno para a camada de aplicativo SAP é roteado por meio do balanceador de carga do Azure

É recomendável configurar DirectServerReturn em combinação com os balanceadores de carga do Azure são posicionados entre a camada de aplicativo SAP e a camada de DBMS para reduzir a latência de rede entre as duas camadas

Um exemplo de como configurar essa configuração é publicado em torno do AlwaysOn do SQL server em [este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se você optar por usar modelos JSON do github publicados como referência para suas implantações de infraestrutura do SAP no Azure, você deve estudar este [modelo para um sistema de 3 camadas do SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, você também pode estudar as configurações corretas do balanceador de carga do Azure.

### <a name="azure-accelerated-networking"></a>Rede Acelerada do Azure
Para reduzir ainda mais a latência de rede entre VMs do Azure, é altamente recomendável escolher a opção de [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) durante a implantação de VMs do Azure para carga de trabalho do SAP. Especialmente para a camada de aplicativo SAP e a camada de DBMS SAP. 

> [!NOTE]
> Nem todos os tipos VM estão dando suporte à Rede Acelerada. O artigo mencionado está listando os tipos de VM que dão suporte à Rede Acelerada. 
>  

- - -
> ![Windows][Logo_Windows] Windows
> 
> Para Windows, confira o artigo [Criar uma máquina virtual do Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) para entender os conceitos e a maneira de como implantar VMs com Rede Acelerada
> 
> ![Linux][Logo_Linux] Linux
> 
> Para Linux, leia o artigo [Criar uma máquina virtual Linux com Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter detalhes sobre a distribuição do Linux. 
> 
> 

- - -

> [!NOTE]
> No caso do SUSE, Red Hat e Oracle Linux, o serviço de Rede Acelerada de mensagens é compatível com versões recentes. Versões mais antigas, como o SLES 12 SP2 ou RHEL 7.2 não dão suporte à Rede Acelerada do Azure 
>  


## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para o uso produtivo de aplicativos SAP nas máquinas virtuais do Azure, o SAP requer a capacidade de obter dados de monitoramento dos hosts físicos executando as máquinas virtuais do Azure. É necessário um nível de patch do Agente de Host SAP específico que habilite essa funcionalidade no SAPOSCOL e no Agente de Host SAP. O nível de patch exato está documentado na Nota SAP [1409604].

Para obter detalhes sobre a implantação de componentes que fornecem dados de host ao SAPOSCOL e ao Agente de Host SAP, bem como sobre o gerenciamento de ciclo de vida desses componentes, consulte o [Guia de implantação][deployment-guide]


## <a name="next-steps"></a>Próximas etapas
Para obter documentação sobre o DBMS específico, consulte estes artigos:

- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP](dbms_guide_sqlserver.md)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](dbms_guide_oracle.md)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](dbms_guide_ibm.md)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](dbms_guide_sapase.md)
- [SAP maxDB, live Cache e implantação do Servidor de Conteúdo no Azure](dbms_guide_maxdb.md)
- [SAP HANA no guia de operações do Azure](hana-vm-operations.md)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md)

