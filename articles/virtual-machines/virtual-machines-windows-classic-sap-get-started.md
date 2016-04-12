<properties
   pageTitle="Usando o SAP em máquinas virtuais do Windows | Microsoft Azure"
   description="Saiba como usar o SAP em VMs (máquinas virtuais) do Windows no Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>

# Usando o SAP em máquinas virtuais do Windows

Computação em Nuvem é um termo amplamente usado que está adquirindo cada vez mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações. O Microsoft Azure é a Plataforma de Serviços de Nuvem da Microsoft que oferece um amplo espectro de novas possibilidades. Agora, os clientes podem provisionar e desprovisionar com rapidez os aplicativos como Serviços de Nuvem, para que não sejam limitados por restrições técnicas ou orçamentárias. Em vez de investir tempo e dinheiro na infraestrutura de hardware, as empresas podem se concentrar no aplicativo, nos processos de negócios e em seus benefícios para clientes e usuários.

Com as máquinas virtuais do Microsoft Azure, a Microsoft oferece uma plataforma abrangente de IaaS (Infraestrutura como Serviço). Os aplicativos baseados no SAP NetWeaver têm suporte em Máquinas Virtuais do Azure (IaaS). Os white papers abaixo descrevem como planejar e implementar aplicativos baseados no SAP NetWeaver em máquinas virtuais do Windows no Azure. Você também pode implementar aplicativos baseados no SAP NetWeaver em [máquinas virtuais do Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## SAP NetWeaver no Azure - HA

Título: SAP NetWeaver no Azure - Clustering de instâncias do SAP ASCS/SCS usando o Cluster de Failover do Windows Server no Azure com o SIOS DataKeeper

Resumo: 'Este documento descreve o uso do SIOS DataKeeper para a definição de uma configuração de alta disponibilidade do SAP ASCS/SCS no Azure. O SAP protege seus componentes de ponto único de falha, como o SAP ASCS/SCS ou o Enqueue Replication Services, com configurações de Cluster de Failover do Windows Server que exigem discos compartilhados. Esses componentes SAP são essenciais para a funcionalidade de um sistema SAP. Portanto, a funcionalidade de alta disponibilidade precisa ser implementada para garantir que esses componentes possam sustentar uma falha de servidor ou de VM como é feito por configurações do Cluster do Windows para ambientes bare-metal e do Hyper-V. A partir de agosto de 2015, o Azure sozinho não poderá fornecer os discos compartilhados necessários para as configurações de alta disponibilidade baseadas no Windows exigidas por estes componentes SAP críticos. No entanto, com a ajuda do produto DataKeeper da SIOS, as configurações de Cluster de Failover do Windows Server, como necessárias para o SAP ASCS/SCS, poderão ser criadas na plataforma IaaS do Azure. Este documento descreve uma abordagem passo a passo de como instalar uma configuração de Cluster de Failover do Windows Server com disco compartilhado fornecida pelo SIOS Datakeeper no Azure. O documento explicará os detalhes das configurações no lado do Azure, do Windows e do SAP que farão a configuração de alta disponibilidade funcionar de forma ideal. O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

Atualização: agosto de 2015

[Baixar este guia agora](http://go.microsoft.com/fwlink/?LinkId=613056)

<!-----------HONumber=AcomDC_0330_2016-->