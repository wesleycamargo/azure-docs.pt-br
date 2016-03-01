<properties
   pageTitle="Usando o SAP em Máquinas Virtuais (VMs) do Azure | Microsoft Azure"
   description="Usando o SAP em Máquinas Virtuais (VMs) do Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>
   
# Usando o SAP em Máquinas Virtuais (VMs) do Azure

Computação em Nuvem é um termo amplamente usado que está adquirindo cada vez mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações. O Microsoft Azure é a Plataforma de Serviços de Nuvem da Microsoft que oferece um amplo espectro de novas possibilidades. Agora, os clientes podem provisionar e desprovisionar com rapidez os aplicativos como Serviços de Nuvem, para que não sejam limitados por restrições técnicas ou orçamentárias. Em vez de investir tempo e dinheiro na infraestrutura de hardware, as empresas podem se concentrar no aplicativo, nos processos de negócios e em seus benefícios para clientes e usuários.

Com os Serviços de Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma abrangente de IaaS (Infraestrutura como Serviço). Os aplicativos baseados no SAP NetWeaver têm suporte em Máquinas Virtuais do Azure (IaaS). Os white papers abaixo descrevem como planejar e implementar aplicativos baseados no SAP NetWeaver no Microsoft Azure como a plataforma escolhida.

## Planejamento e implementação

Título: SAP NetWeaver em Máquinas Virtuais do Azure – Guia de planejamento e implementação

Resumo: este é o documento que você deverá ler primeiro se estiver pensando em executar o SAP NetWeaver em Máquinas Virtuais do Azure. Este guia de planejamento e implementação ajudará você a avaliar se um sistema baseado no SAP NetWeaver planejado ou existente poderá ser implantado em um ambiente de Máquinas Virtuais do Azure. Ele aborda vários cenários de implantação do SAP NetWeaver e inclui configurações do SAP específicas para o Azure. O documento lista e descreve todas as informações de configuração necessárias de que você precisará no lado do SAP/Azure para executar um cenário SAP híbrido. As medidas que você pode tomar para garantir a alta disponibilidade dos sistemas baseados no SAP NetWeaver em IaaS também serão abordadas.

Atualização: agosto de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkId=397963)
## Implantação
Título: SAP NetWeaver em Máquinas Virtuais do Azure – Guia de Implantação

Resumo: este documento oferece orientação de procedimentos para a implantação do software SAP NetWeaver em máquinas virtuais no Azure. Este documento se concentra em três cenários de implantação específicos, com ênfase em habilitar as Extensões de Monitoramento do Azure para SAP, incluindo recomendações para a solução de problemas para as Extensões de Monitoramento do Azure para SAP. Este documento pressupõe que você já tenha lido o guia de planejamento e implementação.

Atualização: setembro de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkId=397964)

## SAP DBMS no Azure
Título: Guia de implantação do SAP DBMS no Azure

Resumo: este artigo aborda considerações sobre planejamento e implementação para os sistemas DBMS que devem ser executados em conjunto com o SAP. Na primeira parte, são listadas e apresentadas as considerações gerais. As partes seguintes do documento estão relacionadas às implantações dos diferentes DBMS com suporte do SAP no Azure. Os DBMS diferentes apresentados são SQL Server, SAP ASE, Oracle, SAP MaxDB e IBM DB2 para Linux, Unix e Windows. Nestas partes específicas, serão discutidas as considerações que você terá de fazer ao executar sistemas SAP no Azure em conjunto com os DBMS. Serão apresentados assuntos como os métodos de backup e de alta disponibilidade com suporte dos diferentes DBMS no Azure a serem usados com os aplicativos SAP.

Atualização: dezembro de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkId=397965)

## SAP NetWeaver no Azure

Título: SAP NetWeaver - Criando uma solução de recuperação de desastre baseada no Azure

Resumo: este documento oferece uma orientação passo a passo para a criação de uma solução de Recuperação de Desastre baseada no Azure para o SAP NetWeaver. A solução descrita pressupõe que o cenário SAP esteja sendo executado virtualizado no local com base no Hyper-V. Na primeira parte do documento, os serviços do ASR (Azure Site Recovery) são apresentados com seus componentes. A segunda parte do documento descreve especificidades dos cenários baseados no SAP NetWeaver. As possibilidades de uso de ASR com instâncias dos aplicativos SAP NetWeaver e SAP Central Services serão abordadas e descritas. A segunda parte se concentrará no aproveitamento do ASR para o SAP Central Services, que é protegido por configurações do Cluster de Failover do Windows Server.

Atualização: setembro de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkID=521971)

## SAP NetWeaver no Azure - HA

Título: SAP NetWeaver no Azure - Clustering de instâncias do SAP ASCS/SCS usando o Cluster de Failover do Windows Server no Azure com o SIOS DataKeeper

Resumo: 'Este documento descreve o uso do SIOS DataKeeper para a definição de uma configuração de alta disponibilidade do SAP ASCS/SCS no Azure. O SAP protege seus componentes de ponto único de falha, como o SAP ASCS/SCS ou o Enqueue Replication Services, com configurações de Cluster de Failover do Windows Server que exigem discos compartilhados. Esses componentes SAP são essenciais para a funcionalidade de um sistema SAP. Portanto, a funcionalidade de alta disponibilidade precisa ser implementada para garantir que esses componentes possam sustentar uma falha de servidor ou de VM como é feito por configurações do Cluster do Windows para ambientes bare-metal e do Hyper-V. A partir de agosto de 2015, o Azure sozinho não poderá fornecer os discos compartilhados necessários para as configurações de alta disponibilidade baseadas no Windows exigidas por estes componentes SAP críticos. No entanto, com a ajuda do produto DataKeeper da SIOS, as configurações de Cluster de Failover do Windows Server, como necessárias para o SAP ASCS/SCS, poderão ser criadas na plataforma IaaS do Azure. Este documento descreve uma abordagem passo a passo de como instalar uma configuração de Cluster de Failover do Windows Server com disco compartilhado fornecida pelo SIOS Datakeeper no Azure. O documento explicará os detalhes das configurações no lado do Azure, do Windows e do SAP que farão a configuração de alta disponibilidade funcionar de forma ideal. O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

Atualização: agosto de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkId=613056)

## SAP NetWeaver em máquinas virtuais SUSE Linux do Azure

Título: Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure

Resumo: no momento, não há suporte oficial do SAP para execução do SAP NetWeaver em VMs Linux do Azure. Independentemente, talvez os clientes queiram fazer alguns testes ou considerem a execução de sistemas de treinamento ou de demonstração SAP em VMs Linux do Azure, já que não há a necessidade de contatar o suporte SAP. Este artigo deve ajudar a configurar as VMs SUSE Linux do Azure para execução de SAP e fornece algumas dicas básicas para evitar possíveis armadilhas comuns.

Atualização: dezembro de 2015

[Esse artigo pode ser encontrado aqui](virtual-machines-sap-on-linux-suse-quickstart.md)

<!---HONumber=AcomDC_0218_2016-->