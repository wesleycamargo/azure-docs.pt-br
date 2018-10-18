---
title: Instalar SAP HANA em SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como instalar SAP HANA em um SAP HANA no Azure (Instância Grande).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162642"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar SAP HANA (instâncias grandes) no Azure

Antes de ler este artigo, familiarize-se com os [termos comuns do HANA em Instâncias Grandes](hana-know-terms.md) e os [SKUs do HANA em Instâncias Grandes](hana-available-skus.md).

A instalação do SAP HANA é responsabilidade sua e você pode começar a atividade após a entrega de um novo servidor do SAP HANA no Azure (Instâncias Grandes). E depois que a conectividade entre as VNets do Azure e as unidades de Instância Grande do HANA foi estabelecida. 

> [!Note]
> De acordo com a política da SAP, a instalação do SAP HANA deve ser feita por uma pessoa certificada para realizar instalações do SAP HANA. Uma pessoa, que foi aprovada no exame Certified SAP Technology Associate, o exame de certificação de Instalação do SAP HANA ou por um SI (integrador de sistemas) certificado pela SAP.

Verifique novamente, especialmente ao planejar a instalação do HANA 2.0, [Nota de Suporte da SAP #2235581 - SAP HANA: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E) para certificar-se de que o sistema operacional é compatível com a versão do SAP HANA que você decidiu instalar. Você perceberá que o sistema operacional com suporte para o HANA 2.0 é mais restrito do que o sistema operacional com suporte para o HANA 1.0. 

> [!IMPORTANT] 
> Para unidades do tipo II, somente a versão SLES 12 SP2 do sistema operacional tem suporte atualmente. 

Antes de começar a instalação do HANA, você deve validar o seguinte:
- [Validar as unidades HLI](#validate-the-hana-large-instance-units)
- [Configuração do sistema operacional](#operating-system)
- [Configuração de rede](#networking)
- [Configuração de armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar as unidade de Instância Grande do HANA

Depois de receber a unidade de Instância Grande do HANA da Microsoft, valide as configurações a seguir e ajuste conforme necessário.

A **Primeira Etapa** depois de receber a Instância Grande do HANA e ter estabelecido o acesso e a conectividade com as instâncias é registrar o sistema operacional da instância no provedor do sistema operacional. Essa etapa inclui o registro do sistema operacional SUSE Linux em uma instância do SUSE SMT que você precisa ter implantada em uma VM do Azure. A unidade de Instância Grande do HANA pode se conectar a essa instância SMT (consulte [Como configurar o servidor SMT para SUSE Linux](hana-setup-smt.md)). Ou o seu Red Hat OS precisa ser registrado com o Red Hat Subscription Manager que você precisa se conectar. Consulte também os comentários neste [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Essa etapa também é necessária para corrigir o sistema operacional. Uma tarefa que é responsabilidade do cliente. Para o SUSE, encontre a documentação para instalar e configurar o SMT [aqui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **Segunda Etapa** é verificar se há novos patches e correções da versão específica do sistema operacional. Verifique se o nível de patch da Instância Grande do HANA está no estado mais recente. Com base no tempo do patch e das versões do sistema operacional e das alterações na imagem, a Microsoft pode realizar a implantação. Pode haver casos em que os últimos patches não são incluídos. Portanto, depois de obter uma unidade de Instância Grande do HANA, é uma etapa obrigatória verificar se, no momento, foram liberados patches relevantes para a segurança, a funcionalidade, a disponibilidade e o desempenho pelo fornecedor específico do Linux e se eles precisam ser aplicados.

A **Terceira Etapa** é verificar as Notas SAP relevantes para a instalação e configuração do SAP HANA na versão específica do sistema operacional. Devido à alteração de recomendações ou às alterações das Notas SAP ou de configurações que dependem de cenários de instalação individuais, a Microsoft nem sempre poderá ter uma unidade de Instância Grande do HANA configurada perfeitamente. Portanto, é obrigatório que você, como cliente, leia as Observações da SAP relacionadas ao SAP HANA sobre a versão exata do Linux. Também verifique as configurações da versão do sistema operacional necessárias e aplique as definições de configuração nos locais em que ainda não foram aplicadas.

Em particular, verifique os seguintes parâmetros e, finalmente, ajuste-os para:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partir do SLES12 SP1 e RHEL 7.2, esses parâmetros devem ser definidos em um arquivo de configuração no diretório /etc/sysctl.d. Por exemplo, deve-se criar um arquivo de configuração com o nome 91-NetApp-HANA.conf. Para versões mais antigas do SLES e RHEL, esses parâmetros devem ser definidos como in/etc/sysctl.conf.

Para todas as versões do RHEL e a partir do SLES12, o 
- sunrpc.tcp_slot_table_entries = 128

parâmetro deve ser definido como in/etc/modprobe.d/sunrpc-local.conf. Se o arquivo não existir, primeiro ele deverá ser criado com a adição da seguinte entrada: 
- options sunrpc tcp_max_slot_table_entries=128

A **Quarta Etapa** é verificar a hora do sistema da Unidade de Instância Grande do HANA. As instâncias são implantadas com um fuso horário do sistema que representa a localização da região do Azure em que o Selo da Instância Grande do HANA está localizado. Você tem a liberdade para alterar a hora do sistema ou o fuso horário das instâncias que possui. Ao fazer isso e solicitar mais instâncias para seu locatário, prepare-se, pois você precisará adaptar o fuso horário das instâncias recém-entregues. As operações da Microsoft não têm nenhuma informação sobre o fuso horário do sistema configurado com as instâncias após a entrega. Portanto, as instâncias recém-implantadas podem não estar definidas com o mesmo fuso horário para o qual você alterou. Como resultado, é sua responsabilidade como cliente verificar e, se necessário, adaptar o fuso horário das instâncias entregues. 

A **Quinta Etapa** é verificar etc/hosts. Conforme as folhas são entregues, elas têm diferentes endereços IP atribuídos para finalidades diferentes (consulte a próxima seção). Verifique o arquivo /etc/hosts. Nos casos em que as unidades são adicionadas a um locatário existente, não espere ter etc/hosts dos sistemas recém-implantados mantidos corretamente com os endereços IP dos sistemas entregues anteriormente. Portanto, como cliente, cabe a você verificar as configurações corretas, de forma que uma instância recém-implantada possa interagir e resolver os nomes das unidades implantadas anteriormente no locatário. 

## <a name="operating-system"></a>Sistema operacional

> [!IMPORTANT] 
> Para unidades do tipo II, somente a versão SLES 12 SP2 do sistema operacional tem suporte atualmente. 

O espaço de troca da imagem do sistema operacional fornecida é definido como 2 GB, de acordo com a [Observação de Suporte da SAP nº 1999997 – Perguntas frequentes: Memória do SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Qualquer configuração diferentes desejada deve ser definida por você como um cliente.

[SUSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/hana) é a distribuição do Linux instalada para SAP HANA no Azure (Instâncias Grandes). Essa distribuição particular fornece recursos específicos do SAP &quot;prontos para uso&quot; (incluindo parâmetros predefinidos para executar o SAP em SLES de forma eficaz).

Confira [Biblioteca de recursos/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SCN (Rede da Comunidade SAP) para obter vários recursos úteis relacionados à implantação do SAP HANA em SLES (incluindo configuração de alta disponibilidade, proteção de segurança específica para operações do SAP e muito mais).

Links adicionais e úteis relacionados ao SAP no SUSE:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – proteção antivírus de SLES para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP).

Notas de suporte do SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Observação de suporte SAP 1944799 – diretrizes SAP HANA para instalação do sistema operacional SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Observação de suporte SAP 2205917 – configurações do SO recomendadas para o banco de dados do SAP HANA para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Observação de suporte SAP 1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para execução do SAP HANA em instâncias grandes de HANA. As versões do RHEL 6.7 e 7.2 estão disponíveis. Observe que, em oposição às máquinas virtuais do Azure, em que apenas RHEL 7.2 e as versões mais recentes têm suporte, as grandes instâncias HANA também oferecem suporte para RHEL 6.7. No entanto, é recomendável usar uma versão do RHEL 7.x.

Links adicionais e úteis relacionados a SAP no Red Hat:
- [SAP HANA no site Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notas de Suporte da SAP aplicáveis à implementação do SAP HANA no Red Hat:

- [Nota de Suporte da SAP #2009879 – Diretrizes SAP HANA para o sistema operacional RHEL (Red Hat Enterprise Linux)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Nota de Suporte da SAP #2292690 - Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Nota de Suporte da SAP #2247020 - Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070).
- [Nota de Suporte da SAP #2228351 - Linux: SAP HANA Database SPS 11 revisão 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Nota de Suporte da SAP #2397039 - Perguntas frequentes: SAP em RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Nota de Suporte da SAP #1496410 - Red Hat Enterprise Linux 6.x: instalação e atualização](https://launchpad.support.sap.com/#/notes/1496410).
- [Nota de Suporte da SAP #2002167 - Red Hat Enterprise Linux 7.x: instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167).

### <a name="time-synchronization"></a>Sincronização de horário

Os aplicativos SAP criados na arquitetura do SAP NetWeaver são sensíveis às diferenças de hora nos vários componentes que compõem o sistema SAP. Os despejos curtos do SAP ABAP com o título de erro ZDATE\_LARGE\_TIME\_DIFF provavelmente são familiares, pois esses despejos curtos são exibidos quando a hora do sistema de servidores ou VMs diferentes se desvia muito.

Para SAP HANA no Azure (instâncias grandes), a sincronização de tempo feita no Azure não se aplica a unidades de computação nos carimbos de data/hora de Instâncias Grandes. Essa sincronização não é aplicável à execução de aplicativos SAP em VMs nativas do Azure, pois o Azure garante que a hora do sistema é sincronizada corretamente. Como resultado, deve ser configurado um servidor de tempo separado que possa ser usado pelos servidores de aplicativos SAP em execução em VMs do Azure e instâncias de banco de dados HANA SAP em execução em instâncias grandes HANA. A infraestrutura de armazenamento em carimbos de Instância Grande tem seu horário sincronizado com os servidores NTP.


## <a name="networking"></a>Rede
Supomos que você seguiu as recomendações de design das VNets do Azure e de conexão dessas VNets com as Instâncias Grandes do HANA, conforme descrito nestes documentos:

- [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que vale a pena mencionar sobre a rede das unidades individuais. Cada unidade de Instância Grande do HANA é fornecida com dois ou três endereços IP atribuídos a dois ou três portas NIC da unidade. Três endereços IP são usados em configurações de expansão do HANA e no cenário de Replicação de Sistema do HANA. Um dos endereços IP atribuídos à NIC da unidade está fora do pool de IPs do Servidor descrito na [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Consulte [cenários com suporte HLI](hana-supported-scenario.md) para obter detalhes sobre a ethernet para a sua arquitetura.

## <a name="storage"></a>Armazenamento

O layout de armazenamento do SAP HANA no Azure (Instâncias Grandes) é configurado pelo Gerenciamento de Serviços do SAP HANA no Azure por meio das diretrizes recomendadas pela SAP, conforme documentado no white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Os tamanhos aproximados dos volumes diferentes com os diferentes SKUs de Instâncias Grandes do HANA foram documentados na [Visão geral e arquitetura do SAP HANA (Instância grande) no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomenclatura dos volumes de armazenamento são listadas na seguinte tabela:

| Uso de armazenamento | Nome da montagem | Nome do volume | 
| --- | --- | ---|
| Dados do HANA | /hana/data/SID/mnt0000<m> | IP de Armazenamento: /hana_data_SID_mnt00001_tenant_vol |
| Log do HANA | /hana/log/SID/mnt0000<m> | IP de Armazenamento: /hana_log_SID_mnt00001_tenant_vol |
| Backup de log do HANA | /hana/log/backups | IP de Armazenamento: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhado | /hana/shared/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Em que SID = a ID do Sistema da instância do HANA 

E locatário = uma enumeração interna das operações durante a implantação de um locatário.

Como você pode ver, o HANA compartilhado e usr/sap estão compartilhando o mesmo volume. A nomenclatura dos pontos de montagem inclui a ID do Sistema das instâncias do HANA, bem como o número de montagem. Em implantações escaláveis, há somente uma montagem, como mnt00001. Ao contrário da implantação escalável, em que você verá o mesmo número de montagens que os nós de trabalho e mestre. Para o ambiente escalável, os dados, o log e os volumes de backup de log são compartilhados e anexados a cada nó na configuração escalável. Para configurações que executam várias instâncias do SAP, um conjunto diferente de volumes é criado e anexado à unidade de Instância Grande do HANA. Consulte os [cenários suportados pela HLI](hana-supported-scenario.md) para obter detalhes do layout de armazenamento para seu cenário.

Conforme você lê o documento e examina uma unidade de Instância Grande do HANA, descobrirá que as unidades são fornecidas com um volume de disco bem generoso para HANA/data e que temos um HANA/log/backup. O motivo pelo qual dimensionamos HANA/data para um tamanho tão grande é que os instantâneos de armazenamento que oferecemos para você, como cliente, estão usando o mesmo volume de disco. Isso significa que quanto mais instantâneos de armazenamento são tirados, mais espaço é consumido pelos instantâneos nos volumes de armazenamento atribuídos. O volume de HANA/log/backup não é considerado como sendo o volume no qual colocar os backups de banco de dados. Ele é dimensionado para ser usado como volume de backup para os backups de log de transações do HANA. Consulte os detalhes em [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Além do armazenamento fornecido, você pode adquirir uma capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser incluído como novos volumes para Instâncias Grandes HANA.

Durante a integração com o Gerenciamento de Serviços do SAP HANA no Azure, o cliente especifica um UID (ID de Usuário) e um GID (ID de Grupo) para o grupo de usuários sidadm e sapsys (por exemplo: 1000, 500). É necessário que, durante a instalação do sistema SAP HANA, esses mesmos valores sejam usados. Conforme você deseja implantar várias instâncias do HANA em uma unidade, obtém vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implantação, é necessário definir:

- O SID das diferentes instâncias do HANA (sidadm é derivado dele).
- Tamanhos de memória das diferentes instâncias do HANA. Como o tamanho de memória por instância define o tamanho dos volumes em cada conjunto de volumes individual.

De acordo com as recomendações do provedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui o LUN de inicialização):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Esses pontos de montagem são configurados em /etc/fstab, conforme mostrado no seguinte gráfico:

![fstab de volumes montados na unidade de Instância Grande do HANA](./media/hana-installation/image1_fstab.PNG)

O resultado do comando df -h em uma unidade S72m de Instância Grande do HANA será parecido com este:

![fstab de volumes montados na unidade de Instância Grande do HANA](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos carimbos de Instância Grande são sincronizados com os servidores NTP. Quando você sincroniza SAP HANA em unidades do Azure (Instâncias Grandes) e VMs do Azure em relação a um servidor NTP, não deve haver um descompasso de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou carimbos de Instância Grande.

Para otimizar o SAP HANA para o armazenamento usado de forma subjacente, você deve também definir os seguintes parâmetros de configuração do SAP HANA:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Para as versões 1.0 do SAP HANA até SPS12, esses parâmetros podem ser definidos durante a instalação do banco de dados SAP HANA, conforme descrito em [Nota SAP nº 2267798 – Configuração do banco de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Você também pode configurar os parâmetros após a instalação do banco de dados SAP HANA usando a estrutura hdbparam. 

Com o SAP HANA 2.0, a estrutura hdbparam foi preterida. Como resultado, os parâmetros devem ser definidos com comandos SQL. Para obter detalhes, consulte [Nota SAP nº 2399079: Eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte os [ cenários suportados pelo HLI ](hana-supported-scenario.md) para aprender o layout de armazenamento para sua arquitetura.


**Próximas etapas**

- Veja [Instalação do HANA em HLI](hana-example-installation.md)










































 







 




