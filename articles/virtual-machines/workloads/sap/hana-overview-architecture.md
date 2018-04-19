---
title: Visão geral e arquitetura do SAP HANA do Azure (Instâncias Grandes) | Microsoft Docs
description: Visão geral da arquitetura de como implantar o SAP HANA do Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ceeec6991aaac64211301313c1bb8dc5f5faa1c0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Visão geral e arquitetura do SAP HANA (Instâncias Grandes) no Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA no Azure (Instâncias Grandes)?

SAP HANA do Azure (Instâncias Grandes) é uma solução exclusiva para o Azure. Além de fornecer máquinas virtuais para implantar e executar o SAP HANA, o Azure oferece a possibilidade de executar e implantar o SAP HANA em servidores bare-metal dedicados a você. A solução do SAP HANA do Azure (Instâncias Grandes) baseia-se no hardware bare-metal de host/servidor não compartilhado que é atribuído a você. O hardware de servidor é inserido em selos maiores que contêm infraestrutura de computação/servidor, de rede e de armazenamento. Como uma combinação, é a TDI (integração de data center personalizada) certificada do HANA. O SAP HANA do Azure (Instâncias Grandes) oferece diferentes tamanhos ou SKUs de servidor. Unidades podem ter 72 CPUs e 768 GB de memória e aumentar para unidades com 960 CPUs e 20 TB de memória.

O isolamento do cliente dentro do carimbo de infraestrutura é executado em locatários, o que é semelhante a:

- **Rede**: isolamento dos clientes dentro da pilha de infraestrutura por meio de redes virtuais por locatário atribuído pelo cliente. Um locatário é atribuído a um único cliente. Um cliente pode ter vários locatários. O isolamento de rede dos locatários proíbe a comunicação de rede entre locatários no nível do carimbo de infraestrutura, mesmo se os locatários pertencerem ao mesmo cliente.
- **Componentes de armazenamento**: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos a elas. Volumes de armazenamento podem ser atribuídos a somente uma máquina virtual de armazenamento. Uma máquina virtual de armazenamento é atribuída exclusivamente a um único locatário na pilha de infraestrutura certificada pela TDI do SAP HANA. Como resultado, os volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acessados apenas em um locatário específico e relacionado. Eles não são visíveis entre os diferentes locatários implantados.
- **Servidor ou host**: uma unidade de host ou servidor não é compartilhada entre os clientes ou locatários. Um servidor ou host, implantado para um cliente, é uma unidade de computação bare-metal atômica que é atribuída a um único locatário. *Nenhum* particionamento de hardware ou particionamento flexível é usado, o que pode resultar no compartilhamento de um host ou de um servidor com outro cliente. Volumes de armazenamento que são atribuídos à máquina virtual de armazenamento do locatário específico são montados para um servidor desse tipo. Um locatário pode ter unidades de servidor de um para muitos de diferentes SKUs atribuídos de modo exclusivo.
- Em um carimbo de infraestrutura do SAP HANA do Azure (Instâncias Grandes), muitos locatários diferentes são implantados e isolados uns em relação a outros por meio dos conceitos de locatário no nível de computação, armazenamento e rede. 


Essas unidades de servidor bare-metal têm suporte para executar apenas o SAP HANA. A camada de aplicativo do SAP ou a camada de middleware de carga de trabalho executa em máquinas virtuais. Os carimbos de infraestrutura que executam o SAP HANA em unidades do Azure (Instâncias Grandes) são conectados aos backbones de serviços de rede do Azure. Dessa maneira, é fornecida uma conectividade de baixa latência entre as unidades do SAP HANA do Azure (Instâncias Grandes) e as máquinas virtuais.

Este documento é um dos vários documentos que cobrem o SAP HANA do Azure (Instâncias Grandes). Este documento apresenta a arquitetura básica, as responsabilidades e os serviços fornecidos pela solução. Recursos de alto nível da solução também são abordados. Para a maioria das outras áreas, como rede e conectividade, quatro outros documentos cobrem detalhes e informações detalhadas. A documentação do SAP HANA do Azure (Instâncias Grandes) não abrange aspectos da instalação ou das implantações do SAP NetWeaver do SAP NetWeaver nas VMs. O SAP NetWeaver no Azure é abordado em documentos separados, localizados no mesmo contêiner de documentação do Azure. 


Os diferentes documentos de diretrizes de Instância grande do HANA abordam as seguintes áreas:

- [Visão geral e arquitetura do SAP HANA (Instâncias Grandes) do Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalar e configurar o SAP HANA (Instâncias Grandes) do Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidade e recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Solução de problemas e monitoramento do SAP HANA (Instâncias Grandes) do Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configuração de alta disponibilidade no SUSE usando STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Backup e restauração do sistema operacional para SKUs do tipo II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definições

Várias definições comuns são amplamente usadas no guia de implantação técnica e arquitetura. Observe os seguintes termos e seus significados:

- **IaaS**: infraestrutura como serviço.
- **PaaS:** plataforma como serviço.
- **SaaS**: software como serviço.
- **Componente do SAP**: um aplicativo do SAP individual, como ECC (Componente Central ERP), BW (Business Warehouse), Solution Manager ou EP (Portal Empresarial). Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
- **Ambiente do SAP**: um ou mais componentes do SAP agrupados logicamente para executar uma função de negócios como desenvolvimento, 	garantia de qualidade, treinamento, recuperação de desastre ou produção.
- **Paisagem do SAP:** refere-se a todos os ativos do SAP no cenário de TI. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
- **SAP system**: a combinação da camada do DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP BW e um sistema de produção SAP CRM. As implantações do Azure não dão suporte à divisão dessas duas camadas entre local e Azure. Um sistema SAP é implantado no local ou é implantado no Azure. É possível implantar os diferentes sistemas de uma paisagem SAP no local ou no Azure. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste do SAP CRM no Azure enquanto implanta o sistema de produção do SAP CRM no local. Para SAP HANA do Azure (Instâncias Grandes), pretende-se que você hospede a camada de aplicativo do SAP dos sistemas SAP em VMs e a instância do SAP HANA relacionada em uma unidade no carimbo do SAP HANA do Azure (Instâncias Grandes).
- **Carimbo da Instância Grande**: uma pilha de infraestrutura de hardware com certificação TDI do SAP HANA e dedicada para executar instâncias do SAP HANA no Azure.
- **SAP HANA do Azure (Instâncias Grandes):** nome oficial para a oferta no Azure para executar instâncias do HANA em hardware certificado por TDI do SAP HANA implantado em carimbos de Instância Grande em diferentes regiões do Azure. O termo relacionado *HANA em Instâncias Grandes* é a abreviação de *SAP HANA do Azure (Instâncias Grandes)* e é amplamente usado neste guia de implantação técnica.
- **Entre instalações**: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo da conexão é estender os domínios locais, OpenLDAP/Azure Active Directory local e DNS local para o Azure. A paisagem local é estendida aos ativos do Azure das assinaturas do Azure. Com essa extensão, as VMs podem fazer parte do domínio local. 

   Os usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs (como serviços de DBMS). A comunicação e a resolução de nomes entre VMs implantadas no local e VMs implantadas no Azure são possíveis. Esse cenário é típico da maneira como a maioria dos ativos do SAP é implantada. Para obter mais informações, consulte [Planejar e projetar para Gateway de VPN do Azure ](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma rede virtual com uma conexão site a site usando o Portal do Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Locatário**: um cliente implantado no carimbo do SAP HANA em Instâncias Grandes é isolado em um *locatário.* Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. As unidades de computação e armazenamento atribuídas aos diferentes locatários não podem visualizar umas às outras nem comunicarem entre si no nível de carimbo do SAP HANA em Instâncias Grandes. Um cliente pode escolher ter implantações em diferentes locatários. Mesmo assim, não há nenhuma comunicação entre locatários no nível de selo de Instância Grande do HANA.
- **Categoria de SKU**: para o SAP HANA em Instâncias Grandes, são oferecidas as duas categorias de SKUs a seguir:
    - **Classe do tipo I:** S72, S72m, S144, S144m, S192 e S192m
    - **Classe do tipo II:** S384, S384m, S384xm, S576, S768 e S960


Uma variedade de recursos adicionais está disponível sobre como implantar uma carga de trabalho do SAP na nuvem. Se você planeja executar uma implantação do SAP HANA do Azure, é necessário ter experiência e estar ciente dos princípios do IaaS do Azure e da implantação das cargas de trabalho do SAP no IaaS do Azure. Antes de continuar, consulte [Usar as soluções SAP nas máquinas virtuais do Azure ](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações. 

## <a name="certification"></a>Certificação

Além de certificação NetWeaver, SAP exige uma certificação especial para SAP HANA dar suporte a SAP HANA em determinadas infraestruturas, como IaaS do Azure.

A principal Nota SAP no NetWeaver e, em certa medida, a certificação do SAP HANA, é [Nota SAP nº 1928533 – Aplicativos do SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533).

A [Nota SAP nº 2316233 - SAP HANA no Microsoft Azure (Instâncias Grandes)](https://launchpad.support.sap.com/#/notes/2316233/E) também é significativa. Ele aborda a solução descrita neste guia. Além disso, você têm suporte para executar SAP HANA no tipo GS5 VM do Azure. As informações para esse caso estão publicadas no [site do SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

A solução do SAP HANA no Azure (Instâncias Grandes) mencionada na Nota SAP nº 2316233 fornece aos clientes da Microsoft e SAP a capacidade de implantar cargas de trabalho grandes do SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA ou SAP HANA no Azure . A solução é baseada no carimbo de hardware dedicado certificado pelo SAP-HANA ([Integração de data center personalizada do SAP HANA - TDI](https://scn.sap.com/docs/DOC-63140)). Se você executar uma solução configurada pela TDI do SAP HANA, todos os aplicativos baseados no SAP HANA (como SAP Business Suite no SAP HANA, SAP BW no SAP HANA, S4/HANA e BW4/HANA) funcionarão na infraestrutura de hardware.

Em comparação com a execução do SAP HANA em VMs, essa solução tem um benefício. Ela fornece volumes de memória muito maiores. Para habilitar essa solução, é necessário reconhecer os aspectos-chave a seguir:

- A camada de aplicativo do SAP e aplicativos não SAP executa em VMs hospedadas nos carimbos de hardware normais do Azure.
- A infraestrutura local do cliente, os data centers e as implantações de aplicativo estão conectados à plataforma de nuvem por meio do ExpressRoute (recomendado) ou de uma VPN (rede privada virtual). O Active Directory e o DNS também são estendidos para o Azure.
- A instância de banco de dados do SAP HANA para carga de trabalho do HANA é executada no SAP HANA no Azure (Instâncias Grandes). O carimbo da Instância Grande está conectado à rede do Azure, portanto, o software em execução nas VMs pode interagir com a instância do HANA em execução no SAP HANA em Instâncias Grandes.
- O hardware do SAP HANA no Azure (Instâncias Grandes) é um hardware dedicado fornecido em uma IaaS com SUSE Linux Enterprise Server ou Red Hat Enterprise Linux pré-instalado. Assim como nas máquinas virtuais, outras atualizações e manutenções no sistema operacional são de sua responsabilidade.
- A instalação do HANA ou de quaisquer componentes adicionais necessários para executar o SAP HANA em unidades do SAP HANA em Instâncias Grandes é de sua responsabilidade. Todas as respectivas operações contínuas e administração do SAP HANA do Azure também são de sua responsabilidade.
- Além das soluções descritas aqui, você pode instalar outros componentes na sua assinatura do Azure que se conecta ao SAP HANA no Azure (Instâncias Grandes). Exemplos são componentes que permitem a comunicação com ou diretamente ao banco de dados do SAP HANA, como servidores de saltos, servidores RDP, SAP HANA Studio, cenários SAP Data Services para SAP BI ou soluções de monitoramento de rede.
- Como no Azure, o SAP HANA em Instâncias Grandes dá suporte para funcionalidade de recuperação de desastre e alta disponibilidade.

## <a name="architecture"></a>Arquitetura

Em um nível alto, a solução SAP HANA do Azure (Instâncias Grandes) tem a camada de aplicativo SAP que reside nas VMs. A camada de banco de dados reside no hardware configurado por TDI do SAP localizado em um carimbo da Instância Grande na mesma região do Azure conectada à IaaS do Azure.

> [!NOTE]
> Implante a camada de aplicativo do SAP na mesma região do Azure que a camada do DBMS do SAP. Essa regra é bem documentada em informações publicadas sobre cargas de trabalho do SAP no Azure. 

A arquitetura geral do SAP HANA do Azure (Instâncias Grandes) fornece uma configuração de hardware certificado por TDI do SAP, que é um servidor de alto desempenho, não virtualizado e bare-metal para o banco de dados do SAP HANA. Além disso, fornece a capacidade e a flexibilidade do Azure para dimensionar recursos para a camada de aplicativos do SAP para atender às suas necessidades.

![Visão geral da arquitetura do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **Direita**: mostra uma infraestrutura local que executa aplicativos diferentes em data centers para que os usuários finais possam acessar aplicativos de linha de negócios, como o SAP. O ideal é que essa infraestrutura local seja conectada ao Azure com [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro**: mostra a IaaS do Azure e, nesse caso, o uso de VMs para hospedar o SAP ou outros aplicativos que usam o SAP HANA, como um sistema de DBMS. Instâncias menores do HANA que funcionam com a memória que as VMs fornecem são implantadas em VMs em conjunto com a camada de aplicativo. Para obter mais informações sobre máquinas virtuais, consulte [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

   Os serviços de rede do Azure são usados para agrupar sistemas SAP em conjunto com outros aplicativos em redes virtuais. Essas redes virtuais conectam-se a sistemas locais, bem como ao SAP HANA do Azure (Instâncias Grandes).

   Para bancos de dados e aplicativos do SAP NetWeaver com suporte para execução no Azure, consulte a [Nota de Suporte SAP nº 1928533 – Aplicativos do SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter documentação sobre como implantar soluções SAP no Azure, consulte:

  -  [Usar o SAP em máquinas virtuais do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Usar as soluções SAP em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Esquerda**: mostra o hardware certificado por TDI do SAP HANA no carimbo da Instância Grande do Azure. As unidades do SAP HANA em Instâncias Grandes são conectadas às redes virtuais da assinatura, usando a mesma tecnologia que a conectividade do local para o Azure.

O carimbo de Instância Grande do Azure em si combina os seguintes componentes:

- **Computação:** servidores baseados em processadores Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 que fornecem a capacidade de computação necessária e são certificados pelo SAP HANA.
- **Rede:** uma malha de rede unificada de alta velocidade que interconecta os componentes de LAN, armazenamento e computação.
- **Armazenamento:** uma infraestrutura de armazenamento que é acessada por meio de uma malha de rede unificada. A capacidade de armazenamento específica fornecida depende da configuração específica do SAP HANA do Azure (Instâncias Grandes) implantada. Mais capacidade de armazenamento está disponível por um custo mensal adicional.

Na infraestrutura multilocatária do carimbo de Instância Grande, os clientes são implantados como locatários isolados. Na implantação do locatário, você nomeia uma assinatura do Azure na inscrição do Azure. Essa assinatura do Azure é aquela com a qual o SAP HANA em Instâncias Grandes é cobrado. Esses locatários têm uma relação de 1:1 para a assinatura do Azure. Para uma rede, é possível acessar uma unidade SAP HANA em Instâncias Grandes implantada em um locatário em uma região do Azure, a partir de diferentes redes virtuais que pertencem a diferentes assinaturas do Azure. Essas assinaturas do Azure devem pertencer à mesma inscrição do Azure. 

Assim como nas VMs, o SAP HANA do Azure (Instâncias Grandes) é oferecido em várias regiões do Azure. Para oferecer recursos de recuperação de desastre, você pode optar por aceitar. Os carimbos de Instância Grande contidos em uma região geopolítica do Azure são conectados uns aos outros. Por exemplo, os Carimbos do SAP HANA em Instâncias Grandes no Oeste dos EUA e Leste dos EUA são conectados por meio de um link de rede dedicado para replicação de recuperação de desastre. 

Assim como é possível escolher entre os diferentes tipos de VM com Máquinas Virtuais do Azure, você pode escolher entre as diferentes SKUs do SAP HANA em Instâncias Grandes que são personalizadas para diferentes tipos de carga de trabalho do SAP HANA. O SAP aplica taxas de soquete de memória para processador para cargas de trabalho variadas com base nas gerações de processadores Intel. A tabela a seguir mostra os tipos de SKU oferecidos.

Desde julho de 2017, o SAP HANA no Azure (Instâncias Grandes) está disponível em várias configurações nas regiões Oeste dos EUA e Leste dos EUA, Leste da Austrália, Sudeste da Austrália, Europa Ocidental e Norte da Europa do Azure.

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no Azure S72<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Disponível |
| --- | SAP HANA no Azure S144<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1,5 TB |  6 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S192<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  2,0 TB |  8 TB | Disponível |
| --- | SAP HANA no Azure S384<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4,0 TB |  16 TB | Disponível |
| Otimizado para OLTP: SAP Business Suite<br /> no SAP HANA ou S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA no Azure S72m<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1,5 TB |  6 TB | Disponível |
|---| SAP HANA no Azure S144m<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3,0 TB |  12 TB | Não é mais oferecido |
|---| SAP HANA no Azure S192m<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU  |  4,0 TB |  16 TB | Disponível |
|---| SAP HANA no Azure S384m<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6,0 TB |  18 TB | Disponível |
|---| SAP HANA no Azure S384xm<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8,0 TB |  22 TB |  Disponível |
|---| SAP HANA no Azure S576<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12,0 TB |  28 TB | Disponível |
|---| SAP HANA no Azure S768<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16,0 TB |  36 TB | Disponível |
|---| SAP HANA no Azure S960<br /> – 20 x processadores Intel® Xeon® E7-8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20,0 TB |  46 TB | Disponível |

- Núcleos de CPU = soma de núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor.
- Threads de CPU = soma de threads de computação fornecidos pelos núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor. Todas as unidades são configuradas por padrão para usar a tecnologia Hyper-Threading.


As configurações específicas de escolhido dependem da carga de trabalho, recursos de CPU e memória desejada. É possível que a carga de trabalho de OLTP use as SKUs otimizadas para a carga de trabalho de OLAP. 

A base de hardware para todas as ofertas é certificada por TDI do SAP HANA. Duas classes diferentes de hardware dividem as SKUs em:

- S72, S72m, S144, S144m, S192 e S192m, que são referidos como "Classe Tipo I" de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que são referidos como "Classe Tipo II" de SKUs.

Um carimbo do SAP HANA em Instâncias Grandes completo não é alocado exclusivamente para uso de um único cliente. Esse fato se aplica aos racks de recursos de computação e armazenamento conectados por meio de uma malha de rede implantada no Azure também. A infraestrutura do SAP HANA em Instâncias Grandes, como o Azure, implanta &quot;locatários&quot; diferentes do cliente isolados uns dos outros nos três níveis a seguir:

- **Rede**: isolamento através de redes virtuais dentro do carimbo do SAP HANA em Instâncias Grandes.
- **Armazenamento**: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos e isolam volumes de armazenamento entre locatários.
- **Computação**: atribuição dedicada de unidades de servidor para um único locatário. Nenhum particionamento rígido ou flexível de unidades de servidor. Nenhum compartilhamento de uma única unidade de host ou de servidor entre locatários. 

As implantações de unidades do SAP HANA em Instâncias Grandes entre locatários diferentes não são visíveis entre si. As unidades do SAP HANA em Instâncias Grandes implantadas em diferentes locatários não podem comunicar-se diretamente entre si no nível de carimbo do SAP HANA em Instâncias Grandes. Somente as unidades do SAP HANA em Instâncias Grandes em um locatário podem comunicar-se entre si no nível de carimbo do SAP HANA em Instâncias Grandes.

Um locatário implantado no carimbo da Instância Grande é atribuído a uma assinatura do Azure para fins de cobrança. Para uma rede, é possível acessar a partir de redes virtuais de outras assinaturas do Azure dentro da mesma inscrição no Azure. Se você implantar com outra assinatura do Azure na mesma região do Azure, você também poderá escolher solicitar um locatário de Instância Grande do HANA separado.

Há diferenças significativas entre executar o SAP HANA no SAP HANA em Instâncias Grandes e no SAP HANA em execução nas VMs implantadas no Azure:

- Não há nenhuma camada de virtualização para o SAP HANA no Azure (Instâncias Grandes). Você obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o servidor do servidor do SAP HANA no Azure (Instâncias Grandes) é dedicado a um cliente específico. Não há possibilidade de que uma unidade de servidor ou host seja particionada de maneira rígida ou flexível. Como resultado, uma unidade do SAP HANA em Instâncias Grandes é usada conforme atribuída como um todo para um locatário e, com isso, para você. Uma reinicialização ou desligamento do servidor não leva automaticamente para a implantação do sistema operacional e do SAP HANA em outro servidor. (Para SKUs de classe Tipo I, a única exceção é se um servidor encontrar problemas e a necessidade de reimplantação precisar ser executada em outro servidor.)
- Ao contrário do Azure, em que tipos de host do processador são selecionados para melhor taxa de preço e desempenho, os tipos de processador escolhidos para o SAP HANA no Azure (Instâncias Grandes) têm o melhor desempenho da linha de processadores Intel E7v3 e E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar várias instâncias do SAP HANA em uma unidade do SAP HANA em Instâncias Grandes
É possível hospedar mais de uma instância ativa do SAP HANA das unidades do SAP HANA em Instâncias Grandes. Para fornecer os recursos de instantâneos de armazenamento e recuperação de desastre, essa configuração requer um conjunto de volumes por instância. Atualmente, as unidades do SAP HANA em Instâncias Grandes podem ser subdivididas da seguinte maneira:

- **S72, S72m, S144, S192**: em incrementos de 256 GB, com 256 GB, a menor unidade inicial. Diferentes incrementos, como 256 GB e 512 GB, podem ser combinados ao máximo da memória da unidade.
- **S144m e S192m**: em incrementos de 256 GB, com 512 GB, a menor unidade. Diferentes incrementos, como 512 GB e 768 GB, podem ser combinados ao máximo da memória da unidade.
- **Classe do Tipo II**: em incrementos de 512 GB, com a menor unidade inicial de 2 TB. Diferentes incrementos, como 512 GB, 1 TB e 1,5 TB, podem ser combinados ao máximo da memória da unidade.

Alguns exemplos de execução de várias instâncias do SAP HANA podem ser semelhantes aos a seguir.

| SKU | Tamanho da memória | Tamanho do armazenamento | Tamanhos com vários bancos de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x instância de 768 GB do HANA<br /> ou 1x instância de 512 GB + 1x instância de 256 GB<br /> ou 3x instâncias de 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x instâncias do HANA de 512GB<br />ou 1x instância de 512 GB + 1x instância de 1 TB<br />ou 6x instâncias de 256 GB<br />ou 1x instância de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8x instâncias de 512 GB<br />ou 4x instâncias de 1 TB<br />ou 4x instâncias de 512 GB + 2x instâncias de 1 TB<br />ou 4x instâncias de 768 GB + 2x instâncias de 512 GB<br />ou 1x instância de 4 TB |
| S384xm | 8 TB | 22 TB | 4x instâncias de 2 TB<br />ou 2x instâncias de 4 TB<br />ou 2x instâncias de 3 TB + 1x instância de 2 TB<br />ou 2x instâncias de 2,5 TB + 1x instância de 3 TB<br />ou 1x instância de 8 TB |


Também há outras variações. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Usar os nós de extensão e camadas de dados do SAP HANA
O SAP dá suporte a um modelo de camadas de dados para SAP BW de diferentes versões do SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de camadas de dados, consulte o documento do SAP [SAP BW/4HANA e SAP BW no HANA com nós de extensão do SAP HANA ](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com o SAP HANA em Instâncias Grandes, é possível usar a configuração da opção 1 dos nós de extensão do SAP HANA, conforme explicado nos documentos de perguntas frequentes e no blog do SAP. Configurações de opção 2 podem ser definidas com os SKUs de instâncias do HANA grandes: S72m, S192, S192m, S384 e S384m. 

Ao examinar a documentação, a vantagem pode não ser aparente à primeira vista. Mas quando você examina para as diretrizes de dimensionamento do SAP, é possível ver uma vantagem usando os nós de extensão da opção 1 e da opção 2 do SAP HANA. A seguir, são apresentados alguns exemplos:

- Diretrizes de dimensionamento do SAP HANA geralmente exigem duas vezes mais volume de dados do que memória. Ao executar a instância do SAP HANA com os dados ativos, você terá apenas 50% ou menos da memória preenchida com dados. Idealmente, o restante da memória é mantido para o SAP HANA fazer seu trabalho.
- Isso significa que, em uma unidade de instância do HANA grande S192 com 2 TB de memória executando um banco de dados do SAP BW, você tem apenas 1 TB como volume de dados.
- Se usar um nó de extensão SAP HANA adicional da opção 1, bem como uma SKU do SAP HANA em Instâncias Grandes S192, ele oferece uma capacidade adicional de 2 TB para o volume de dados. Na configuração de opção 2, oferece até mesmo 4 TB adicionais para o volume de dados mornos. Comparado ao nó ativo, a capacidade total de memória do nó de extensão "passivo" pode ser usada para armazenamento de dados para a opção 1. O dobro da memória pode ser usado para o volume de dados na configuração do nó de extensão da opção 2 do SAP HANA.
- Você terminará com uma capacidade de 3 TB dos dados e uma taxa de ativo para passivo de 1: 2 para a opção 1. Você tem 5 TB de dados e uma taxa de 1: 4 com a configuração do nó de extensão da opção 2.

Quanto maior o volume de dados em comparação com a memória, maiores as chances de que os dados passivos solicitados sejam armazenados no armazenamento em disco.


## <a name="operations-model-and-responsibilities"></a>Responsabilidades e modelo de operações

O serviço fornecido com SAP HANA no Azure (Instâncias Grandes) é alinhado com os serviços do Azure IaaS. Você obtém uma instância de um SAP HANA em Instâncias Grandes com um sistema operacional instalado que é otimizado para o SAP HANA. Assim como com as VMs da IaaS do Azure, a maioria das tarefas de proteção do SO, a instalação de software adicional, instalação do HANA, operação do SO e do HANA e a atualização do SO e do HANA são de sua responsabilidade. A Microsoft não força atualizações do SO nem atualizações do HANA para você.

![Responsabilidades do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Conforme mostrado no diagrama, o SAP HANA do Azure (Instâncias Grandes) é uma oferta de IaaS com vários locatários. Na maior parte, a divisão de responsabilidades está no limite da infraestrutura do SO. A Microsoft é responsável por todos os aspectos do serviço abaixo da linha do sistema operacional. Você é responsável por todos os aspectos do serviço acima da linha. O SO é de sua responsabilidade. Você pode continuar usando métodos locais mais atuais que podem ser empregados para conformidade, segurança, gerenciamento de aplicativos, base e gerenciamento do SO. Os sistemas aparecem como se estivessem em sua rede em todos os aspectos.

Esse serviço é otimizado para o SAP HANA, portanto, há áreas em que você precisa trabalhar com a Microsoft para usar os recursos de infraestrutura subjacentes para obter melhores resultados.

A lista a seguir fornece mais detalhes sobre cada uma das camadas e suas responsabilidades:

**Rede**: todas as redes internas do carimbo da Instância Grande que executam o SAP HANA. Sua responsabilidade inclui o acesso ao armazenamento, a conectividade entre as instâncias (para escalar horizontalmente e outras funções), a conectividade com a paisagem e conectividade com o Azure, onde a camada de aplicativo do SAP está hospedada nas VMs. Além disso, inclui a conectividade da WAN entre os Data Centers do Azure para fins de replicação de recuperação de desastre. Todas as redes são particionadas pelo locatário e têm qualidade de serviço aplicada.

**Armazenamento**: o armazenamento particionado virtualizado para todos os volumes necessários aos servidores do SAP HANA, bem como para os instantâneos. 

**Servidores:** os servidores físicos dedicados para executar os bancos de dados do SAP HANA atribuídos aos locatários. Os servidores de SKUs de classe do Tipo I são abstraídos do hardware. Com esses tipos de servidores, a configuração do servidor é coletada e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Uma movimentação desse tipo (manual) de um perfil por operações pode ser comparada um pouco à recuperação de serviço do Azure. Os servidores das SKUs da classe do Tipo II não oferecem essa capacidade.

**SDDC**: o software de gerenciamento usado para gerenciar data centers como entidades definidas por software. Ele permite que a Microsoft reserve recursos em pool por motivos de desempenho, disponibilidade e escala.

**O/S**: SO escolhido (SUSE Linux ou Red Hat Linux) em execução nos servidores. As imagens do SO oferecidas são fornecidas pelo fornecedor individual do Linux à Microsoft para executar o SAP HANA. É necessário ter uma assinatura com o fornecedor do Linux para a imagem específica otimizada do SAP HANA. Você é responsável por registrar as imagens no fornecedor do SO. 

Depois da entrega feita pela Microsoft, você será responsável por qualquer aplicação de patch no sistema operacional Linux. Essa aplicação de patch inclui pacotes adicionais que podem ser necessários para uma instalação com êxito do SAP HANA e que não foram incluídos pelo fornecedor específico do Linux nas imagens do SO otimizadas do SAP HANA. (Para obter mais informações, consulte a documentação de instalação do HANA do SAP e as Notas SAP.) 

Você é responsável pela aplicação de patch do SO em consequência de mau funcionamento ou otimização do SO e dos drivers em relação ao hardware do servidor específico. Adicionalmente, você é responsável pela aplicação de patch funcional e de segurança do SO. 

Sua responsabilidade também inclui monitoramento e planejamento da capacidade de:

- Consumo de recursos de CPU.
- Consumo de memória.
- Volumes de disco relacionados ao espaço livre, IOPS e latência.
- Tráfego de volume de rede entre o SAP HANA em Instâncias Grandes e a camada de aplicativo do SAP.

A infraestrutura subjacente do HANA em Instâncias Grandes fornece a funcionalidade de backup e restauração do volume do sistema operacional. Usar essa funcionalidade também é sua responsabilidade.

**Middleware:** instância do SAP HANA, principalmente. Administração, operações e monitoramento são de sua responsabilidade. Você pode usar a funcionalidade fornecida para usar instantâneos de armazenamento para fins de backup, restauração e recuperação de desastre. Essas funcionalidades são fornecidas pela infraestrutura. Suas responsabilidades também incluem o projeto de alta disponibilidade ou recuperação de desastre com essas funcionalidade, aproveitando-as e monitorando para determinar se os instantâneos de armazenamento foram executados com êxito.

**Dados:** seus dados gerenciados pelo SAP HANA e outros dados, como arquivos de backups localizados em volumes ou compartilhamentos de arquivos. Suas responsabilidades incluem monitorar o espaço livre em disco e gerenciar o conteúdo nos volumes. Você também é responsável por monitorar a execução com êxito de backups de volumes de disco e instantâneos de armazenamento. A execução com êxito da replicação de dados em sites de recuperação de desastre é de responsabilidade da Microsoft.

**Aplicativos:** as instâncias de aplicativos do SAP ou, no caso de aplicativos não SAP, a camada de aplicativos desses aplicativos. Suas responsabilidades incluem implantação, administração, operações e monitoramento desses aplicativos. Você é responsável pelo planejamento da capacidade de consumo de recursos de CPU, consumo de memória, consumo de armazenamento do Azure e consumo de largura de banda de rede dentro de redes virtuais. Além disso, você é responsável pelo planejamento da capacidade para consumo de recursos de redes virtuais para o SAP HANA no Azure (Instâncias Grandes).

**WANs:** as conexões que você estabelece do local para implantações do Azure para cargas de trabalho. Todos os clientes com o SAP HANA em Instâncias Grandes usam o Azure ExpressRoute para conectividade. Essa conexão não faz parte da solução do SAP HANA do Azure (Instâncias Grandes). Você é responsável pela configuração dessa conexão.

**Arquivar**: talvez você queira arquivar cópias de dados usando seus próprios métodos nas contas de armazenamento. O arquivamento requer gerenciamento, conformidade, custos e operações. Você é responsável por gerar cópias e backups de arquivos no Azure e armazená-los de maneira compatível.

Configurações para o SAP HANA Veja o [SLA para o SAP HANA no Azure (Instâncias Grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionamento

O dimensionamento do HANA em Instâncias Grandes não é diferente do dimensionamento do HANA em geral. Para sistemas existentes e implantados que você deseja migrar de outro RDBMS para o HANA, o SAP fornece vários relatórios que são executados nos sistemas SAP existentes. Se o banco de dados é movido para o HANA, esses relatórios verificam os dados e calculam os requisitos de memória para a instância do HANA. Para obter mais informações sobre como executar esses relatórios e obter os patches ou versões mais recentes, leia as seguintes Notas SAP:

- [Nota SAP nº 1793345 – Dimensionamento do SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Nota SAP nº 1872170 – Relatório de dimensionamento do Suite no HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Nota SAP nº 2121330 – Perguntas frequentes: relatório de dimensionamento do SAP BW no HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Nota SAP nº 1736976 – Relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Nota SAP nº 2296290 – Novo relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde Sizer rápida SAP está disponível para calcular os requisitos de memória da implementação de software SAP sobre HANA.

Os requisitos de memória para o HANA aumentam conforme o volume de dados aumenta. Esteja ciente do seu consumo atual de memória para ajudar a prever o que será no futuro. Com base nos requisitos de memória, é possível e mapear a demanda em uma das SKUs do SAP HANA em Instâncias Grandes.

## <a name="requirements"></a>Requisitos

Estes são os requisitos para execução SAP HANA no Azure (Instâncias Grandes).

**Microsoft Azure**

- Uma assinatura do Azure que pode ser vinculada ao SAP HANA no Azure (Instâncias Grandes).
- Contrato do Suporte Premier do Microsoft Azure. Para obter informações específicas relacionadas à execução do SAP no Azure, consulte [Nota de Suporte SAP nº2 015553 – SAP no Microsoft Azure: Pré-requisitos de suporte](https://launchpad.support.sap.com/#/notes/2015553). Se você usar unidades do HANA em Instâncias Grandes com 384 ou mais CPUs, também será necessário estender o contrato de suporte Premier para incluir o Azure Rapid Response.
- O reconhecimento das SKUs do HANA em Instâncias Grandes será necessário após executar um exercício de dimensionamento com o SAP.

**Conectividade de rede**

- ExpressRoute entre local para Azure: para conectar o data center local ao Azure, certifique-se de solicitar pelo menos uma conexão de 1 Gbps do ISP. 

**Sistema operacional**

- Licenças para SUSE Linux Enterprise Server 12 para aplicativos SAP.

   > [!NOTE] 
   > O sistema operacional fornecido pela Microsoft não está registrado no SUSE. Ele não é conectado a uma instância da Ferramenta de Gerenciamento de Assinaturas.

- Ferramenta de Gerenciamento de Assinaturas do SUSE Linux implantada no Azure em uma VM. Essa ferramenta permite que o SAP HANA no Azure (Instâncias Grandes) seja registrado e atualizado, respectivamente, pelo SUSE. (Não há acesso à Internet no data center do HANA em Instâncias Grandes.) 
- Licenças para Red Hat Enterprise Linux 6.7 ou 7.2 para SAP HANA.

   > [!NOTE]
   > O sistema operacional fornecido pela Microsoft não é registrado na Red Hat. Ele não é conectado a uma instância do Gerenciador de Assinaturas do Red Hat.

- Gerenciador de Assinaturas do Red Hat implantado no Azure em uma VM. O Gerenciador de Assinaturas do Red Hat permite que o SAP HANA do Azure (Instâncias Grandes) seja registrado e atualizado pelo Red Hat. (Não há acesso direto à Internet de dentro do locatário implantado no carimbo da Instância Grande do Azure.)
- A SAP também exige que você tenha um contrato de suporte com seu provedor do Linux. Esse requisito não é removido pela solução do HANA em Instâncias Grandes ou por executar o Linux no Azure. Ao contrário de algumas das imagens da galeria do Linux Azure, a taxa de serviço *não* é incluída na oferta de solução do SAP HANA em Instâncias Grandes. É sua responsabilidade cumprir os requisitos do SAP em relação aos contratos de suporte com o distribuidor do Linux. 
   - Para o SUSE Linux, procure os requisitos do contrato de suporte em [Nota SAP nº 1984787 – SUSE Linux Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) e [Nota SAP n. 1056161 – Suporte de prioridade do SUSE para aplicativos do SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, é necessário ter os níveis de assinatura corretos que incluem atualizações de suporte e serviço para os sistemas operacionais do SAP HANA em Instâncias Grandes. A Red Hat recomenda o Red Hat Enterprise Linux para a assinatura [Soluções SAP](assinatura https://access.redhat.com/solutions/3082481. 

Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Banco de dados**

- As licenças e os componentes de instalação de software para SAP HANA (plataforma ou enterprise edition).

**Aplicativos**

- Licenças e componentes de instalação de software para quaisquer aplicativos do SAP que conectam-se ao SAP HANA e contratos de suporte do SAP relacionados.
- Licenças e os componentes de instalação de software para todos os aplicativos SAP não usado em relação ao SAP HANA no ambiente do Azure (Instâncias Grandes) e relacionadas a contratos de suporte.

**Habilidades**

- Experiência com e conhecimento da IaaS do Azure e seus componentes.
- Experiência e conhecimento de como implantar uma carga de trabalho do SAP no Azure.
- Equipe certificada para instalação do SAP HANA.
- Habilidades de arquitetura do SAP para projetar alta disponibilidade e recuperação de desastre no SAP HANA.

**SAP**

- A expectativa é que você seja um cliente do SAP e tenha um contrato de suporte com o SAP.
- Especialmente para implementações da classe do Tipo II de SKUs do HANA em Instâncias Grandes, consulte o SAP em versões do SAP HANA e as eventuais configurações em hardware de escalonamento vertical de tamanho grande.


## <a name="storage"></a>Armazenamento

O layout de armazenamento para o SAP HANA do Azure (Instâncias Grandes) é configurado pelo SAP HANA no modelo de implantação clássico por meio das diretrizes recomendadas do SAP. As diretrizes estão documentadas no white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

O SAP HANA em Instâncias Grandes da classe do Tipo I é fornecido com quatro vezes o volume de memória como volume de armazenamento. Para a classe do Tipo II de unidades do HANA em Instâncias Grandes, o armazenamento não é quatro vezes maior. As unidades são fornecidas com um volume destinado ao armazenamento de backups do log de transações do HANA. Para obter mais informações, consulte [ Instalar e configurar o SAP HANA (Instâncias Grandes) do Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela a seguir em termos de alocação de armazenamento. A tabela lista a capacidade aproximada dos diferentes volumes fornecidos com as diferentes unidades do SAP HANA Instâncias Grandes.

| SKU de Instância Grande do HANA | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3.328 GB | 768 GB |1.280 GB | 768 GB |
| S192 | 4.608 GB | 1.024 GB | 1.536 GB | 1.024 GB |
| S192m | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S384 | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S576 | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768 | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S960 | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |


Os volumes implantados reais podem variar com base na implantação e na ferramenta utilizada para mostrar os tamanhos do volume.

Se você subdividir uma SKU do SAP HANA em Instâncias Grandes, alguns exemplos de possíveis divisões poderão parecer:

| Partição de memória em GB | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| 1.024 | 1.792 GB | 640 GB | 1.024 GB | 640 GB |
| 1.536 | 3.328 GB | 768 GB | 1.280 GB | 768 GB |


Esses tamanhos são números de volume aproximado que podem variar um pouco com base na implantação e nas ferramentas utilizadas para examinar os volumes. Há também outros tamanhos de partição, como 2,5 TB. Esses tamanhos de armazenamento são calculados com uma fórmula semelhante àquela usada para as partições anteriores. O termo "partições" não significa que o sistema operacional, a memória ou os recursos de CPU sejam de alguma forma particionados. Ele indica as partições de armazenamento para as diferentes instâncias do HANA que talvez você quira implantar em uma única unidade do SAP HANA em Instâncias Grandes. 

Talvez seja necessário mais armazenamento. É possível adicionar armazenamento, comprando armazenamento adicional em unidades de 1 TB. Esse armazenamento adicional pode ser adicionado como volume adicional. Também pode ser usado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes conforme originalmente implantados e principalmente documentados pelas tabelas anteriores. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento descritos anteriormente são anexados às unidades do SAP HANA em Instâncias Grandes como volumes NFS4.

É possível usar instantâneos de armazenamento para fins de backup, restauração e recuperação de desastre. Para obter mais informações, consulte [Alta disponibilidade e recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
O armazenamento usado para o SAP HANA em Instâncias Grandes permite uma criptografia transparente dos dados conforme são armazenados nos discos. Quando uma unidade do SAP HANA em Instâncias Grandes é implantada, é possível ativar esse tipo de criptografia. Além disso, será possível alterar para volumes criptografados após a implantação. A passagem de volumes não criptografados para volumes criptografados é transparente e não requer tempo de inatividade. 

Com a classe do Tipo I de SKUs, o volume no qual o LUN de inicialização está armazenado é criptografado. Para a classe do Tipo II de SKUs do SAP HANA em Instâncias Grandes, é necessário criptografar o LUN de inicialização com os métodos do SO. Para obter mais informações, contate a equipe de Gerenciamento de Serviços da Microsoft.


## <a name="networking"></a>Rede

A arquitetura dos serviços de rede do Azure é um componente essencial da implantação com êxito dos aplicativos do SAP HANA em Instâncias Grandes. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. É provável que nem todos os sistemas SAP sejam baseados no SAP HANA. A paisagem do SAP é provavelmente um híbrido que usa:

- Sistemas SAP implantados no local. Devido aos tamanhos, esses sistemas atualmente não podem ser hospedados no Azure. Um exemplo é um sistema SAP ERP de produção que é executado no SQL Server (como o banco de dados) e requer mais recursos de CPU ou memória do que as VMs podem fornecer.
- Sistemas SAP baseados em SAP HANA implantados localmente.
- Sistemas SAP implantados em VMs. Esses sistemas podem ser de desenvolvimento, teste, área restrita ou instâncias de produção para qualquer um dos aplicativos baseados no SAP NetWeaver que podem implantar com êxito no Azure (em VMs), com base no consumo de recursos e na demanda de memória. Esses sistemas também podem ser baseados em bancos de dados, como o SQL Server. Para obter mais informações, consulte [Nota de Suporte SAP nº 1928533 – Aplicativos do SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E). E esses sistemas podem ser baseados em bancos de dados como o SAP HANA. Para obter mais informações, consulte [Plataformas de IaaS certificadas pelo SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Servidores de aplicativos do SAP implantados no Azure (em VMs) que aproveitam o SAP HANA do Azure (Instâncias Grandes) em carimbos da Instância Grande do Azure.

Uma paisagem do SAP híbrido com quatro ou mais cenários de implantação diferentes é típico. Também há muitos casos de clientes de paisagem do SAP completos que executam no Azure. À medida que as VMs tornam-se mais avançadas, o número de clientes que move todas as soluções SAP no Azure aumenta.

A rede do Azure no contexto dos sistemas SAP implantados no Azure não é complicada. É baseada nos seguintes princípios:

- As redes virtuais do Azure devem estar conectadas ao circuito do ExpressRoute que conecta-se a uma rede local.
- Um circuito do ExpressRoute que conecta-se do local para o Azure normalmente deve ter uma largura de banda de 1 Gbps ou superior. Essa largura de banda mínima permite largura de banda adequada para a transferência de dados entre sistemas locais e sistemas em execução nas VMs. Também permite largura de banda adequada para conexão a sistemas do Azure a partir de usuários locais.
- Todos os sistemas SAP no Azure devem ser configurados em redes virtuais para comunicarem-se entre si.
- O Active Directory e o DNS hospedados no local são estendidos para o Azure por meio do ExpressRoute do local.


> [!NOTE] 
> Com relação à cobrança, somente uma assinatura do Azure pode ser vinculada a apenas um locatário em um carimbo da Instância Grande em uma região do Azure específica. Ao contrário, um único locatário de carimbo da Instância Grande pode ser vinculado a apenas uma assinatura do Azure. Esse requisito é consistente com outros objetos cobráveis no Azure.

Se o SAP HANA do Azure (Instâncias Grandes) for implantado em várias regiões diferentes do Azure, um locatário separado será implantado no carimbo da Instância Grande. É possível executar ambos sob a mesma assinatura do Azure, desde que essas instâncias façam parte da mesma paisagem do SAP. 

> [!IMPORTANT] 
> Somente a implantação do Azure Resource Manager tem suporte com o SAP HANA do Azure (Instâncias Grandes).

 

### <a name="additional-virtual-network-information"></a>Informações adicionais sobre rede virtual

Para conectar uma rede virtual ao ExpressRoute, um gateway do Azure deve ser criado. Para obter mais informações, consulte [Sobre os gateways de rede virtual para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Um gateway do Azure pode ser usado com ExpressRoute para uma infraestrutura fora do Azure ou para um carimbo da Instância Grande do Azure. Um gateway do Azure também pode ser usado para conectar-se entre redes virtuais. Para obter mais informações, consulte [Configurar uma conexão rede a rede para o Gerenciador de Recursos usando PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É possível conectar o gateway do Azure a um máximo de quatro conexões diferentes do ExpressRoute, desde que essas conexões sejam originadas de diferentes roteadores de borda corporativa da Microsoft. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A taxa de transferência fornecida por um gateway do Azure é diferente para ambos os casos de uso. Para obter mais informações, consulte [Sobre o Gateway de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A taxa de transferência máxima que é possível obter com um gateway de rede virtual é de 10 Gbps usando uma conexão do ExpressRoute. A cópia de arquivos entre uma VM que reside em uma rede virtual e um sistema local (como um único fluxo de cópia) não alcança a taxa de transferência total as diferentes SKUs do gateway. Para aproveitar a largura de banda completa do gateway de rede virtual, use vários fluxos. Ou, será necessário copiar arquivos diferentes em fluxos paralelos de um único arquivo.


### <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para SAP HANA em Instâncias Grandes
A arquitetura de rede para o SAP HANA em Instâncias Grandes pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute para o Azure. Essa parte é o domínio do cliente e é conectada ao Azure por meio do ExpressRoute. Observe o canto inferior direito na figura a seguir.
- Serviços de rede do Azure, conforme abordado anteriormente, com redes virtuais, que também possuem gateways. Essa parte é uma área na qual você precisa localizar os projetos apropriados para os requisitos do aplicativo, segurança e requisitos de conformidade. Se você usa o SAP HANA em Instâncias Grandes, é outro ponto a ser considerado em termos do número de redes virtuais e SKUs do gateway do Azure a escolher. Observe a parte superior direita na figura.
- Conectividade do SAP HANA em Instâncias Grandes através da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo o que você precisa fazer é fornecer alguns intervalos de endereço IP após a implantação dos ativos no SAP HANA em Instâncias Grandes conectar o circuito do ExpressRoute às redes virtuais. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Rede no SAP HANA em Instâncias Grandes, o que geralmente é mais transparente para você.

![Rede virtual conectada ao SAP HANA do Azure (Instâncias Grandes) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

O requisito de que os ativos locais devem conectar-se por meio do ExpressRoute para o Azure não altera porque você utiliza o SAP HANA em Instâncias Grandes. O requisito de ter uma ou várias redes virtuais executando as VMs, que hospedam a camada de aplicativo que conecta-se às instâncias do HANA hospedadas nas unidades do SAP HANA em Instâncias Grandes, também não é alterado. 

As diferenças para implantações do SAP no Azure são:

- As unidades do SAP HANA em Instâncias Grandes do locatário do cliente são conectadas por meio de outro circuito do ExpressRoute às redes virtuais. Para separar as condições de carga, os locais para links do ExpressRoute de redes virtuais e os links entre as redes virtuais e o SAP HANA em Instâncias Grandes não compartilham os mesmos roteadores.
- O perfil de carga de trabalho entre a camada de aplicativo do SAP e o SAP HANA em Instâncias Grandes é de natureza diferente, com muitas solicitações e intermitências pequenas, como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura de aplicativos do SAP é mais sensível à latência de rede que os cenários típicos em que os dados são trocados entre o local e o Azure.
- O gateway de rede virtual possui pelo menos duas conexões do ExpressRoute. Ambas as conexões compartilham a largura de banda máxima dos dados recebidos do gateway de rede virtual.

A latência de rede entre as VMs e as unidades do SAP HANA em Instâncias Grandes pode ser maior que uma latência de viagem de ida e volta típica de rede de VM para VM. Dependendo da região do Azure, os valores medidos podem exceder a latência de viagem de ida e volta de 0,7 ms, classificada como abaixo da média na [Nota SAP 1100926 – Perguntas frequentes: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). No entanto, os clientes implantam aplicativos do SAP de produção baseados no SAP HANA com êxito no SAP HANA em Instâncias Grandes. Os clientes que implantaram relatam grandes melhorias executando os aplicativos do SAP no SAP HANA usando as unidades do SAP HANA em Instâncias Grandes. Certifique-se de testar os processos empresariais completamente no SAP HANA em Instâncias Grandes do Azure.
 
Para fornecer latência de rede determinística entre VMs e o SAP HANA em Instâncias Grandes, a escolha da SKU do gateway de rede virtual é essencial. Diferentemente dos padrões de tráfego entre VMs locais e VMs, o padrão de tráfego entre as VMs e o SAP HANA em Instâncias Grandes pode desenvolver pequenas, mas elevadas intermitências de solicitações e volumes de dados a serem transmitidos. Para lidar bem com essas intermitências, é recomendável usar a SKU do gateway UltraPerformance. Para a classe do Tipo II de SKUs do SAP HANA em Instâncias Grandes, o uso da SKU do gateway UltraPerformance como um gateway de rede virtual é obrigatório.

> [!IMPORTANT] 
> Dado o tráfego geral entre o aplicativo do SAP e as camadas do banco de dados, somente as SKUs do gateway HighPerformance ou UltraPerformance para redes virtuais têm suporte para conexão com o SAP HANA do Azure (Instâncias Grandes). Para SKUs do SAP HANA em Instâncias Grandes Tipo II, somente a SKU do gateway UltraPerformance tem suporte como um gateway de rede virtual.



### <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura local mostrada anteriormente é conectada por meio do ExpressRoute no Azure. O circuito do ExpressRoute conecta-se a um roteador de borda corporativa. Para obter mais informações, consulte [Visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Após o estabelecimento da rota, ela conecta-se ao backbone do Azure e todas as regiões do Azure são acessíveis.

> [!NOTE] 
> Para executar paisagens do SAP no Azure, conecte o roteador de borda corporativa mais próximo da região do Azure na paisagem do SAP. Os carimbos da Instância Grande do Azure são conectados por meio de dispositivos de roteador de borda corporativa dedicados para minimizar a latência de rede entre VMs em carimbos da Instância Grande e IaaS do Azure.

O gateway de rede virtual para as VMs que hospedam instâncias do aplicativo do SAP é conectado ao circuito do ExpressRoute. A mesma rede virtual é conectada a um roteador de borda corporativa separado dedicado para conectar os carimbos da Instância Grande.

Esse sistema é um exemplo direto de um único sistema SAP. A camada de aplicativo do SAP é hospedada no Azure. O banco de dados do SAP HANA é executado no SAP HANA do Azure (Instâncias Grandes). A hipótese é que a largura de banda do gateway de rede virtual de 2 Gbps ou 10 Gbps não representa um gargalo.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para conectar o SAP HANA do Azure (Instâncias Grandes), a taxa de transferência do gateway de rede virtual poderá tornar-se um gargalo. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também é possível criar uma rede virtual especial que conecta-se ao SAP HANA em Instâncias Grandes para casos como:

- Execução de backups diretamente das instâncias do HANA na Instância Grande do HANA para uma VM no Azure que hospeda compartilhamentos NFS.
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

Use uma rede virtual separada para hospedar as VMs que gerenciam o armazenamento. Esse acordo evita os efeitos de grandes arquivos ou transferência de dados do SAP HANA em Instâncias Grandes para o Azure no gateway de rede virtual que atende às VMs executando a camada de aplicativo do SAP. 

Uma arquitetura de rede mais escalonável:

- Aproveite várias redes virtuais para uma única camada de aplicativo do SAP maior.
- Implante uma rede virtual separada para cada sistema SAP implantado, em comparação à combinação desses sistemas SAP em sub-redes separadas sob a mesma rede virtual.

 Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantar camada de aplicativo do SAP em várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

A figura mostra a camada de aplicativo do SAP, ou componentes, implantados em várias redes virtuais. Essa configuração introduziu uma sobrecarga de latência inevitável durante a comunicação entre os aplicativos hospedados nessas redes virtuais. Por padrão, o tráfego entre as VMs localizadas em diferentes redes virtuais é roteado pelos roteadores de borda corporativa nessa configuração. Desde setembro de 2016, esse roteamento pode ser otimizado. 

A maneira de otimizar e reduzir a latência na comunicação entre duas redes virtuais é pelo emparelhamento de redes virtuais dentro da mesma região. Esse método funcionará mesmo se essas redes virtuais estiverem em assinaturas diferentes. Com o emparelhamento de rede virtual, a comunicação entre as VMs em duas redes virtuais diferentes pode usar o backbone de rede do Azure para comunicarem diretamente entre si. Latência mostra como se as VMs estivessem na mesma rede virtual. O tráfego que endereça intervalos de endereço IP conectados por meio do gateway de rede virtual do Azure é roteado pelo gateway de rede virtual individual da rede virtual. 

Para obter mais informações sobre o emparelhamento de rede virtual, consulte [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Roteamento no Azure

Três considerações de roteamento de rede são importantes para o SAP HANA do Azure (Instâncias Grandes):

* O SAP HANA do Azure (Instâncias Grandes) pode ser acessado somente por meio de VMs e da conexão do ExpressRoute dedicada, não diretamente do local. O acesso direto do local para as unidades do SAP HANA em Instâncias Grandes, conforme fornecido pela Microsoft a você, não é possível imediatamente. As restrições de roteamento transitivas devem-se à arquitetura de rede atual do Azure usada para o SAP HANA em Instâncias Grandes. Alguns clientes de administração e quaisquer aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução no local, não podem conectar ao banco de dados do SAP HANA.

* Se você tiver unidades do SAP HANA em Instâncias Grandes implantadas em duas regiões diferentes do Azure para recuperação de desastre, as mesmas restrições de roteamento transitório serão aplicadas. Em outras palavras, os endereços IP de uma unidade do SAP HANA em Instâncias Grandes em uma região (por exemplo, Oeste dos EUA) não são roteados para uma unidade do SAP HANA em Instâncias Grandes implantada em outra região (por exemplo, Leste dos EUA). Essa restrição é independente do uso do emparelhamento de rede do Azure entre as regiões ou da conexão cruzada dos circuitos do ExpressRoute que conectam as unidades de do SAP HANA em Instâncias Grandes a redes virtuais. Para uma representação gráfica, consulte a figura na seção "Usar unidades do SAP HANA em Instâncias Grandes em várias regiões". Essa restrição, que vem com a arquitetura implantada, proíbe o uso imediato da replicação do sistema HANA como uma funcionalidade de recuperação de desastre.

* As unidades do SAP HANA do Azure (Instâncias Grandes) têm um endereço IP atribuído do intervalo de endereços do pool de IP do servidor que você enviou. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esse endereço IP é acessível por meio das assinaturas do Azure e do ExpressRoute que conecta as redes virtuais ao HANA do Azure (Instâncias Grandes). O endereço IP atribuído fora desse intervalo de endereços do pool de IP do servidor é atribuído diretamente à unidade de hardware. Ele*não* é atribuído pelo NAT, como foi o caso nas primeiras implantações dessa solução. 

> [!NOTE] 
> Para superar a restrição no roteamento transitório, conforme explicado nos dois primeiros itens da lista, use componentes adicionais para o roteamento. Componentes que podem ser usados para superar a restrição podem ser:

> * Um proxy reverso para rotear dados, de e para. Por exemplo, F5 BIG-IP, NGINX com o Gerenciador de Tráfego implantado no Azure como uma solução de roteamento de tráfego/firewall virtual.
> * Usar [regras IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) em uma VM Linux para habilitar o roteamento entre localidades locais e as unidades do SAP HANA em Instâncias Grandes ou entre unidades do SAP HANA em Instâncias Grandes em regiões diferentes.

> Esteja ciente de que a implementação e o suporte para soluções personalizadas envolvendo IPTables ou dispositivos de rede de terceiros não são fornecidos pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente usado ou pelo integrador. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade com a Internet do SAP HANA em Instâncias Grandes
O SAP HANA em Instâncias Grandes *não* tem conectividade direta com a Internet. Por exemplo, essa limitação pode restringir a capacidade de registrar a imagem do SO diretamente com o fornecedor do SO. Talvez seja necessário trabalhar com o servidor da Ferramenta de Gerenciamento de Assinaturas do SUSE Linux Enterprise Server ou com o Gerenciador de Assinaturas do Red Hat Enterprise Linux.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Criptografia de dados entre VMs e o SAP HANA em Instâncias Grandes
Os dados transferidos entre o SAP HANA em Instâncias Grandes e as VMs não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Para obter mais informações, consulta [esta documentação pelo SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usar unidades do SAP HANA em Instâncias Grandes em várias regiões

Você pode ter motivos para implantar o SAP HANA do Azure (Instâncias Grandes) em várias regiões do Azure, exceto para recuperação de desastre. Talvez você queira acessar o SAP HANA em Instâncias Grandes de cada uma das VMs implantadas nas diferentes redes virtuais nas regiões. Os endereços IP atribuídos às diferentes unidades do SAP HANA em Instâncias Grandes não são propagados para além das redes virtuais que estão diretamente conectadas por meio do gateway às instâncias. Como resultado, uma pequena alteração é introduzida no design da rede virtual. Um gateway de rede virtual pode manipular quatro circuitos do ExpressRoute diferentes em diferentes roteadores de borda corporativa. Cada rede virtual conectada a um dos carimbos da Instância Grande pode ser conectada ao carimbo da Instância Grande em outra região do Azure.

![Rede virtual conectada a carimbos da Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as diferentes redes virtuais em ambas as regiões estão conectadas a dois circuitos do ExpressRoute diferentes que são usados para conectar ao SAP HANA do Azure (Instâncias Grandes) em ambas as regiões do Azure. As conexões recém-introduzidas são as linhas vermelhas retangulares. Com essas conexões, fora das redes virtuais, as VMs em execução em uma dessas redes virtuais podem acessar cada uma das diferentes unidades do SAP HANA em Instâncias Grandes implantadas nas duas regiões. Como mostra a figura, presume-se que há duas conexões do ExpressRoute do local para as duas regiões do Azure. Essa organização é recomendada para motivos de recuperação de desastre.

> [!IMPORTANT] 
> Se foram utilizados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.


