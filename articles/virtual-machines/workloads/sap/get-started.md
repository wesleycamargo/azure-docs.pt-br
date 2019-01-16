---
title: Introdução às VMs SAP no Azure | Microsoft Docs
description: Saiba como executar as soluções SAP em VMs (máquinas virtuais) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/07/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d3256fcf33a9ad1eb34ec5dbb0c701c8166a49e2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075168"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Usar o Azure para hospedar e executar cenários de carga de trabalho do SAP

Ao escolher o Microsoft Azure, você poderá executar de forma confiável seus cenários e cargas de trabalho críticas do SAP em uma plataforma escalonável, em conformidade e de eficácia comprovada na empresa.  Obtenha a escalabilidade, flexibilidade e economia de custos do Azure. Com a parceria expandida entre a Microsoft e a SAP, você pode executar aplicativos da SAP entre cenários de desenvolvimento/teste e produção no Azure, com suporte total. Do SAP NetWeaver ao SAP S4/HANA, SAP BI, do Linux ao Windows, do SAP HANA ao SQL, estamos com você.

Além de hospedar cenários do SAP NetWeaver com os diferentes DBMS no Azure, você pode hospedar diferentes outros cenários de carga de trabalho do SAP, assim como SAP BI no Azure. 

A exclusividade do Azure para SAP HANA é uma oferta exclusiva que distingue o Azure da concorrência. Para habilitar a hospedagem de cenários SAP com maior demanda de recursos de CPU e de memória que envolvem o SAP HANA, o Azure oferece o uso do hardware bare-metal dedicado ao cliente com a finalidade de executar implantações do SAP HANA que requerem até 24 TB (expansão de 120 TB) de memória para S/4HANA ou outra carga de trabalho do SAP HANA. Essa solução exclusiva do Azure do SAP HANA no Azure (Instâncias Grandes) permite que você execute o SAP HANA em um hardware bare-metal dedicado com a camada de aplicativo SAP ou camada de middleware de carga de trabalho hospedada em Máquinas Virtuais do Azure nativas. Esta solução está documentada em vários documentos na seção "SAP HANA no Azure (Instâncias Grandes)".   

Hospedar cenários de carga de trabalho do SAP no Azure também pode criar requisitos de integração de identidade e logon único usando o Azure Active Directory para diferentes componentes SAP e ofertas SaaS ou PaaS do SAP. Uma lista de tais cenários de integração e de logon único com entidades SAP e do AAD (Azure Active Directory) é descrita e documentada na seção "Integração de Identidade do AAD SAP e Logon Único".

## <a name="latest-changes"></a>Últimas alterações

Versão da [Lista de verificação de planejamento e implantação de carga de trabalho do SAP](sap-deployment-checklist.md)

Esclarecimento sobre uso e configuração de [Dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) em caminhos de comunicação críticos de latência de sistemas SAP em:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Configurações de infraestrutura do SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)



## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA no SAP HANA no Azure (Instâncias Grandes)

Uma série de documentação leva você por meio do SAP HANA no Azure (instâncias grandes) ou no curto em instâncias grandes HANA. Os documentos abordam as áreas listadas das instâncias grandes HANA:

- [Visão geral do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infraestrutura e conectividade para o SAP HANA no Azure (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalar o SAP HANA no SAP HANA no Azure (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Solução de problemas e monitoramento do SAP HANA no Azure (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Próximas etapas:

- Leitura [visão geral e arquitetura do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em Máquinas Virtuais do Azure
Esta seção da documentação aborda diferentes aspectos do SAP HANA. Como pré-requisito, você deve estar familiarizado com os serviços de entidade de segurança do Azure que fornecem serviços elementares de IaaS do Azure, portanto, principalmente dados de Conhecimento de computação do Azure, armazenamento e rede. Muitos desses tópicos são tratados na [Guia de Planejamento do Azure relacionado ao SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

A documentação específica para o HANA no Azure consiste nesta lista de artigos e seus sub particulares:

- [Início Rápido: Instalação manual do SAP HANA de instância única em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Implantar o SAP S/4HANA ou o BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurações de infraestrutura do SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilidade do SAP HANA em uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidade do SAP HANA entre regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup para SAP HANA nas Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup do Azure do SAP HANA no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup do SAP HANA com base em instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em Máquinas Virtuais do Azure
Nesta seção, você encontrará documentação de planejamento e implantação para o SAP NetWeaver e o Business One no Azure. A documentação neste capítulo se concentra principalmente em torno de Noções básicas e o uso de bancos de dados do HANA não com a carga de trabalho SAP no Azure. Enquanto os documentos e artigos para alta disponibilidade são a base para alta disponibilidade do HANA no Azure também. a lista de artigos, como:

- [SAP Business One em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Implantar SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Executando o SAP NetWeaver em VMs do SUSE Linux no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteger uma implantação de aplicativo SAP NetWeaver de várias camadas usando o Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Sobre bancos de dados do HANA não sob carga de trabalho do SAP no Azure, como a lista de documentos:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para NetWeaver do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, liveCache e implantação do Servidor de Conteúdo nas VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para bancos de dados do SAP HANA no Azure, confira a seção SAP HANA em máquinas virtuais do Azure.

Para alta disponibilidade da carga de trabalho do SAP no Azure, o documento de entrada é:

- [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

O documento de entrada aponta para vários outros documentos de arquitetura e o cenário. Nos documentos subsequentes do cenário, são fornecidos links para documentos técnicos detalhados explicando a implantação e configuração dos métodos diferentes de alta disponibilidade. Os documentos diferentes de estabelecer e configurar a alta disponibilidade para carga de trabalho do SAP NetWeaver são aqueles que abrangem o Linux, bem como sistemas operacionais do Windows.


Para a integração entre o Azure Active Directory (AAD) e serviços SAP e Single-Sign-On, os documentos são listados como:

- [Tutorial: Integração do Azure Active Directory com o SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente de 4hana/s Fiori Launchpad SAML Single Sign-On com o Microsoft Azure Active Directory](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para a integração de serviços do Azure em componentes da SAP, a lista de documentos se parece com:

- [Usar SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usar o Conector do SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




