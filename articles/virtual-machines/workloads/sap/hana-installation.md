---
title: "Instalar SAP HANA em SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Como instalar SAP HANA em um SAP HANA no Azure (Instância Grande)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: e66d1e245d74c11e8bf36e307009910126551b0b
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
<a id="how-to-install-and-configure-sap-hana-large-instances-on-azure" class="xliff"></a>

# Como instalar e configurar SAP HANA (instâncias grandes) no Azure

A instalação do SAP HANA é sua responsabilidade e você pode iniciar a atividade após a entrega de um novo servidor SAP HANA no Azure (Instâncias Grandes) e depois de estabelecida a conectividade entre as VNets do Azure e as unidades de Instância Grande do HANA. Observe que, de acordo com a política do SAP, a instalação do SAP HANA deve ser executada por uma pessoa certificada a realizar instalações do SAP HANA – alguém que tenha sido aprovado no exame de certificação de Instalação Certified SAP Technology Associate – SAP HANA ou por um SI (integrador de sistemas) certificado pela SAP.

Verifique novamente, especialmente ao planejar a instalação do HANA 2.0, [Nota de Suporte da SAP #2235581 - SAP HANA: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E) para certificar-se de que o sistema operacional é compatível com a versão do SAP HANA que você decidiu instalar. Você perceberá que o sistema operacional com suporte para HANA 2.0 é mais restrito do que o sistema operacional com suporte para HANA 1.0. 

<a id="first-steps-after-receiving-the-hana-large-instance-units" class="xliff"></a>

## Primeiras etapas depois de receber as Unidades de Instância Grande do HANA

A **Primeira Etapa** depois de receber a Instância Grande do HANA e ter estabelecido o acesso e a conectividade com as instâncias é registrar o sistema operacional da instância no provedor do sistema operacional. Isso incluirá o registro do sistema operacional SUSE Linux em uma instância do SUSE SMT que você precisa ter implantada em uma VM no Azure à qual a unidade de Instância Grande do HANA pode se conectar. Ou o sistema operacional RedHat precisa ser registrado no Gerenciador de Assinaturas do Red Hat ao qual você precisa se conectar. Consulte também os comentários neste [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Essa etapa também é necessária para corrigir o sistema operacional no futuro. Uma tarefa que é responsabilidade do cliente. Para o SUSE, encontre a documentação para instalar e configurar o SMT [aqui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **Segunda Etapa** é verificar se há novos patches e correções da versão específica do sistema operacional. Verifique se o nível de patch da Instância Grande do HANA está no estado mais recente. Com base no tempo do patch e das versões do sistema operacional e das alterações na imagem, a Microsoft pode implantar nesse local. Pode haver casos em que os últimos patches não são incluídos. Portanto, depois de obter uma unidade de Instância Grande do HANA e registrar a instalação do sistema operacional no distribuidor do Linux, é uma etapa obrigatória verificar se, no momento, foram liberados patches relevantes para a segurança, funcionalidade, disponibilidade e o desempenho pelo fornecedor específico do Linux e se eles precisam ser aplicados.

A **Terceira Etapa** é verificar as Notas SAP relevantes para a instalação e configuração do SAP HANA na versão específica do sistema operacional. Devido à alteração de recomendações ou às alterações das Notas SAP ou de configurações que dependem de cenários de instalação individuais, a Microsoft nem sempre poderá ter uma unidade de Instância Grande do HANA configurada perfeitamente. Portanto, é obrigatório que você, como cliente, leia as Notas SAP (no mínimo, as listadas abaixo), verifique as configurações da versão do sistema operacional necessárias e aplique as definições de configurações nos casos em que elas não foram feitas.

Em particular, verifique os seguintes parâmetros e, eventualmente, ajuste-os para:

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

A **Quarta Etapa** é verificar a hora do sistema da Unidade de Instância Grande do HANA. As instâncias serão implantadas com um fuso horário do sistema que representa a localização da região do Azure em que o Carimbo da Instância Grande do HANA está localizado. Você tem a liberdade para alterar a hora do sistema ou o fuso horário das instâncias que possui. Ao fazer isso e solicitar mais instâncias para seu locatário, prepare-se, pois você precisará adaptar o fuso horário das instâncias recém-entregues. As operações da Microsoft não têm nenhuma informação sobre o fuso horário do sistema configurado com as instâncias após a entrega. Portanto, as instâncias recém-implantadas podem não estar definidas com o mesmo fuso horário para o qual você alterou. Como resultado, é sua responsabilidade como cliente verificar e, se necessário, adaptar o fuso horário das instâncias entregues. 

A **Quinta Etapa** é verificar etc/hosts. Conforme as folhas são entregues, elas têm diferentes endereços IP atribuídos para finalidades diferentes (consulte a próxima seção). Verifique etc/hosts. Nos casos em que as unidades são adicionadas a um locatário existente, não espere ter etc/hosts dos sistemas recém-implantados mantidos corretamente com os endereços IP dos sistemas entregues anteriormente. Portanto, como cliente, cabe a você verificar as configurações corretas, de forma que uma instância recém-implantada possa interagir e resolver os nomes das unidades implantadas anteriormente no locatário. 

<a id="networking" class="xliff"></a>

## Rede
Supomos que você seguiu as recomendações de design das VNets do Azure e de conexão delas com as Instâncias Grandes do HANA, conforme descrito nestes documentos:

- [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que vale a pena mencionar sobre a rede das unidades individuais. Cada unidade de Instância Grande do HANA é fornecida com dois ou três endereços IP atribuídos a dois ou três portas NIC da unidade. Três endereços IP são usados em configurações de expansão do HANA e no cenário de Replicação de Sistema do HANA. Um dos endereços IP atribuídos à NIC da unidade está fora do pool de IPs do Servidor descrito na [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

A distribuição de unidades com dois endereços IP atribuídos deve ser parecida com esta:

- eth0.xx deve ter um endereço IP atribuído que está fora do intervalo de endereços do Pool de IPs do Servidor enviado para a Microsoft. Esse endereço IP deve ser usado para ser mantido em /etc/hosts do sistema operacional.
- eth1.xx deve ter um endereço IP atribuído usado para a comunicação com o NFS. Portanto, esses endereços **NÃO** precisam ser mantidos em etc/hosts para permitir o tráfego entre instâncias no locatário.

Para casos de implantação de Replicação de Sistema do HANA ou de expansão do HANA, uma folha de configuração com dois endereços IP atribuídos não é adequada. Caso você tenha apenas dois endereços IP atribuídos e deseje implantar uma configuração como essa, contate o Gerenciamento de Serviços da Microsoft para obter um terceiro endereço IP em uma terceira VLAN atribuída. Para as unidades de Instância Grande do HANA que têm três endereços IP atribuídos em três portas NIC, o seguinte se aplica:

- eth0.xx deve ter um endereço IP atribuído que está fora do intervalo de endereços do Pool de IPs do Servidor enviado para a Microsoft. Portanto, esse endereço IP não deve ser usado para ser mantido em /etc/hosts do sistema operacional.
- eth1.xx deve ter um endereço IP atribuído usado para a comunicação com o armazenamento NFS. Portanto, esse tipo de endereço não deve ser mantido em etc/hosts.
- eth2.xx deve ser usado exclusivamente para ser mantido em etc/hosts para a comunicação entre instâncias diferentes. Esses também seriam os endereços IP que precisam ser mantidos em configurações em expansão do HANA como os endereços IP usados pelo HANA para a configuração entre nós.



<a id="storage" class="xliff"></a>

## Armazenamento

O layout de armazenamento do SAP HANA no Azure (Instâncias Grandes) é configurado pelo SAP HANA no Gerenciamento de Serviços do Azure por meio das práticas recomendadas do SAP. Confira o white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Conforme você lê o documento e examina uma unidade de Instância Grande do HANA, descobrirá que as unidades são fornecidas com um volume de disco bem generoso para HANA/data e que temos um HANA/log/backup. O motivo pelo qual dimensionamos HANA/data para um tamanho tão grande é que os instantâneos de armazenamento que oferecemos para você, como cliente, estão usando o mesmo volume de disco. Isso significa que quanto mais instantâneos de armazenamento você executar, mais espaço para eles será necessário. O volume de HANA/log/backup não é considerado como sendo o volume no qual os backups de banco de dados serão colocados, mas para utilizá-lo como volume de backup para o log de transações do HANA. Em versões futuras do armazenamento de instantâneos de autoatendimento, direcionaremos esse volume específico para que ele tenha instantâneos mais frequentes e, com essas replicações mais frequentes no site de recuperação de desastre, caso você deseje aceitar a funcionalidade de recuperação de desastre fornecida pela infraestrutura de Instância Grande do HANA. Consulte os detalhes em [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Além do armazenamento fornecido, você pode adquirir uma capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser incluído como novos volumes para Instâncias Grandes HANA.

Durante a integração com o Gerenciamento de Serviços da Microsoft, o cliente especifica uma UID (ID de Usuário) e a GID (ID de Grupo) para o grupo de usuários e sapsys do <SID>ADM (por exemplo: 1000,500). É necessário que, durante a instalação do sistema SAP HANA, esses mesmos valores sejam usados.

O controlador de armazenamento e os nós nos carimbos de Instância Grande são sincronizados com os servidores NTP. Quando você sincroniza SAP HANA em unidades do Azure (Instâncias Grandes) e VMs do Azure em relação a um servidor NTP, não deve haver um descompasso de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou carimbos de Instância Grande.

Para otimizar o SAP HANA para o armazenamento usado de forma subjacente, você deve também definir os seguintes parâmetros de configuração do SAP HANA:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Para as versões 1.0 do SAP HANA até SPS12, esses parâmetros podem ser definidos durante a instalação do banco de dados SAP HANA, conforme descrito em [Nota SAP nº 2267798 – Configuração do banco de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Você também pode configurar os parâmetros após a instalação do banco de dados SAP HANA usando a estrutura hdbparam. 

Com o SAP HANA 2.0, a estrutura hdbparam foi preterida. Como resultado, os parâmetros devem ser definidos com comandos SQL. Para obter detalhes, consulte [Nota SAP nº 2399079: Eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).


<a id="operating-system" class="xliff"></a>

## Sistema operacional

Espaço de troca da imagem do sistema operacional fornecida é definido como 2 GB de acordo com a [Nota de Suporte da SAP #1999997 - Perguntas Frequentes: memória do SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Qualquer configuração diferentes desejada deve ser definida por você como um cliente.

[SUSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/hana) é a distribuição do Linux instalada para SAP HANA no Azure (Instâncias Grandes). Essa distribuição particular fornece recursos específicos do SAP &quot;prontos para uso&quot; (incluindo parâmetros predefinidos para executar o SAP em SLES de forma eficaz).

Confira [Biblioteca de recursos/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SCN (Rede da Comunidade SAP) para obter vários recursos úteis relacionados à implantação do SAP HANA em SLES (incluindo configuração de alta disponibilidade, proteção de segurança específica para operações do SAP e muito mais).

Links adicionais e úteis relacionados a SAP no SUSE:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – proteção antivírus de SLES para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP).

Notas de suporte do SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Observação de suporte SAP 1944799 – diretrizes SAP HANA para instalação do sistema operacional SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Observação de suporte SAP 2205917 – configurações do SO recomendadas para o banco de dados do SAP HANA para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Observação de suporte SAP 1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para execução do SAP HANA em instâncias grandes de HANA. As versões do RHEL 6.7 e 7.2 estão disponíveis. 

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

<a id="time-synchronization" class="xliff"></a>

## Sincronização de horário

O SAP é muito sensível a diferenças de hora para os vários componentes que compõem o sistema SAP. Os despejos de memória curtos do SAP ABAP com o título de erro ZDATE\_LARGE\_TIME\_DIFF provavelmente são familiares se você trabalha com o SAP (Base) há muito tempo, pois esses despejos curtos aparecem quando a hora do sistema de servidores diferentes ou VMs se torna muito diferente.

Para SAP HANA no Azure (instâncias grandes), a sincronização de tempo feita no Azure não se aplica a unidades de computação nos carimbos de data/hora de Instâncias Grandes. Isso não é aplicável para executar aplicativos SAP de forma nativa no Azure (em VMs), pois o Azure garante que a hora do sistema seja sincronizada corretamente. Como resultado, deve ser configurado um servidor de tempo separado que possa ser usado pelos servidores de aplicativos SAP em execução em VMs do Azure e instâncias de banco de dados HANA SAP em execução em instâncias grandes HANA. A infraestrutura de armazenamento em carimbos de Instância Grande tem seu horário sincronizado com os servidores NTP.





