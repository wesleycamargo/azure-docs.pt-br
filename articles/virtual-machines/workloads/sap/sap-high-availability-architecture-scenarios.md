---
title: "Arquitetura e cenários de alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver | Microsoft Docs"
description: "Arquitetura e cenários de HA (alta disponibilidade) para SAP NetWeaver em Máquinas Virtuais do Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arquitetura de alta disponibilidade e cenários para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="definition-of-terminologies"></a>Definição de terminologias

O Termo **HA (alta disponibilidade)** está relacionado a um conjunto de tecnologias que minimiza as interrupções da TI, proporcionando a continuidade dos serviços de TI por meio de componentes redundantes e tolerantes a falhas ou protegidos contra failover no **mesmo** data center. Em nosso caso, dentro de uma Região do Azure.

**A DR (recuperação de desastre)** também está voltada para minimizar as interrupções dos serviços de TI e a recuperação de dados, mas em **diferentes** data centers, que estão localizados a centenas de quilômetros de distância. Em nosso caso, geralmente entre diferentes Regiões do Azure na mesma região geopolítica ou como estabelecidos por você, como cliente.


## <a name="overview-of-high-availability"></a>Visão Geral de Alta Disponibilidade
É possível separar a discussão sobre a alta disponibilidade da SAP no Azure em três partes:

* **Alta disponibilidade da infraestrutura do Azure**, por exemplo, alta disponibilidade de computação (VMs), rede, armazenamento etc., e seus benefícios para aumentar a disponibilidade do aplicativo SAP.

* **Utilizando a Reinicialização de VM da infraestrutura do Azure para atingir “maior disponibilidade” de aplicativos SAP**

  Se você decidir não usar as funcionalidades como WSFC (Clustering de Failover do Windows Server) ou Pacemaker Linux, a Reinicialização de VM do Azure será usada para proteger o sistema SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure em geral.


* **Alta disponibilidade de aplicativo SAP**

  Para obter toda a alta disponibilidade do sistema SAP, precisamos proteger todos os componentes essenciais de sistema SAP, por exemplo:
  * **Servidores de aplicativos SAP** redundantes e
  * Componentes exclusivos (por exemplo, **SPOF (ponto único de falha)**), como
    * **Número da instância do (A)SCS SAP** e
    *  **DBMS**.


Alta disponibilidade da SAP no Azure tem algumas diferenças em comparação com alta disponibilidade da SAP em um ambiente físico ou virtual local. O documento a seguir, [Alta disponibilidade do SAP NetWeaver e continuidade de negócios em ambientes virtuais com VMware e Hyper-V no Microsoft Windows] [ sap-ha-bc-virtual-env-hyperv-vmware-white-paper], descreve configurações do SAP de alta disponibilidade padrão em ambientes virtualizados no Windows.

Há não configuração de HA da SAP integrada com sapinst, para Linux, da mesma maneira que existe para o Windows. Em relação à alta disponibilidade local do SAP para Linux, encontre mais informações em [Informações sobre parceiros de alta disponibilidade][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure

### <a name="single-instance-virtual-machine-sla"></a>SLA de máquina virtual de instância única

Atualmente, não há um SLA de VM individual de 99,9% com armazenamento premium. Para ter uma ideia de como a disponibilidade de uma única VM pode ser, você pode compilar o produto dos diferentes [Contratos de Nível de Serviço do Azure] disponíveis[azure-sla].

A base para o cálculo é 30 dias por mês, ou 43.200 minutos. Portanto, 0,05% de tempo de inatividade corresponde a 21,6 minutos. Normalmente, a disponibilidade dos serviços diferentes se multiplica da seguinte maneira:

(Serviço de disponibilidade nº 1/100) * (Serviço de disponibilidade nº 2/100) * (Serviço de disponibilidade nº 3/100) \*…

Assim como:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Várias instâncias de máquinas virtuais no mesmo conjunto de disponibilidade
Para todas as Máquinas Virtuais que tenham duas ou mais instâncias implantadas no mesmo **Conjunto de Disponibilidade**, garantimos que você terá Conectividade de Máquina Virtual em pelo menos 99,95% do tempo.

Quando duas ou mais VMs são parte do mesmo conjunto de disponibilidade, cada máquina virtual em um conjunto de disponibilidade recebe um **domínio de atualização** e um **domínio de falha** da plataforma subjacente do Azure.

Os **Domínios de falha** fazem com que as VMs sejam implantadas em um hardware diferente que não compartilha fonte de energia e chave de rede. Quando há tempo de inatividade não planejado de servidores, chaves de rede ou fontes de alimentação, apenas uma VM é afetada.

Os **Domínios de atualização** fazem com que VMs diferentes não sejam reinicializadas ao mesmo tempo durante a manutenção planejada de infraestrutura do Azure; elas são reiniciadas apenas uma por vez.

Para saber mais, consulte [Gerenciar a disponibilidade de máquinas virtuais Windows no Azure][azure-virtual-machines-manage-availability].

O conjunto de disponibilidade é usado para alcançar alta disponibilidade de:

* Servidores de aplicativos SAP redundantes  

* Clusters com dois ou mais nós (por exemplo, VMs) que protegem SPOFs como DBMS e instância do (A)SCS SAP

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>Manutenção planejada e não planejada de VM (máquina virtual)

Há dois tipos de eventos de plataforma do Azure que podem afetar a disponibilidade das máquinas virtuais: manutenção planejada e manutenção não planejada.

* Eventos de **manutenção planejada** são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma na qual suas máquinas virtuais são executadas.

* Os eventos de **manutenção não planejada** ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta algum tipo de falha. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure migra automaticamente sua máquina virtual do servidor físico não íntegro hospedando sua máquina virtual para um servidor físico íntegro. Esses eventos são raros, mas podem também reinicializar a sua máquina virtual.

Para saber mais, consulte [Gerenciar a disponibilidade de máquinas virtuais Windows no Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundância de Armazenamento do Azure
Os dados da sua Conta de Armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o SLA do Armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Já que o armazenamento do Azure está mantendo três imagens dos dados por padrão, RAID5 ou RAID1 em vários discos do Azure não são necessárias.

Para saber mais, veja [Replicação do Armazenamento do Azure][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks são um novo tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs armazenados em Contas de Armazenamento do Azure. Managed Disks alinham-se automaticamente ao Conjunto de Disponibilidade da máquina virtual à qual eles estão conectados e, portanto, aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução na máquina virtual.
Para saber mais, veja [Visão geral dos Azure Managed Disks][azure-storage-managed-disks-overview].

É recomendável usar o Managed Disk, porque eles simplificam a implantação e o gerenciamento de suas máquinas virtuais.
**O SAP atualmente dá suporte apenas a Managed Disks Premium**. Para obter mais informações, leia a Nota SAP [1928533].

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>Utilizando a alta disponibilidade da infraestrutura do Azure para atingir “maior” disponibilidade de aplicativos SAP

Se você decidir não usar as funcionalidades como Clustering de Failover do Windows Server (WSFC) ou Pacemaker Linux (atualmente compatível apenas com SLES 12 ou posterior), a Reinicialização de VM do Azure será usada para proteger o sistema SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure em geral.

Essa abordagem está descrita com mais detalhes no documento [Utilizando a reinicialização de VM da infraestrutura do Azure para atingir "maior disponibilidade" do sistema SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Alta disponibilidade de aplicativo SAP no Azure IaaS

Para obter toda a alta disponibilidade do sistema SAP, precisamos proteger todos os componentes essenciais de sistema SAP, por exemplo:

* **Servidores de aplicativos SAP** redundantes e

* Componentes exclusivos (por exemplo, **SPOF (ponto único de falha)**), como
  * **Número da instância do (A)SCS SAP** e
  *  **DBMS**.

Vamos indicar em detalhes abaixo como alcançar alta disponibilidade para todos os três componentes essenciais do sistema SAP.

### <a name="high-availability-for-sap-application-servers"></a>Alta disponibilidade para servidores de aplicativos SAP

> Este capítulo é aplicável a:
>
> ![Windows][Logo_Windows] Windows e ![Linux][Logo_Linux] Linux
>

Normalmente, não é necessária uma solução específica de alta disponibilidade para Servidor de Aplicativos SAP e instâncias de diálogo. Você atinge alta disponibilidade por redundância e configura várias instâncias de caixa de diálogo em diferentes Máquinas Virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas Máquinas Virtuais do Azure.

![Figura 1: servidor de aplicativos SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 1:** servidor de aplicativos SAP de alta disponibilidade_

Você deve colocar todas as máquinas virtuais que hospedam instâncias do Servidor de Aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais são parte do mesmo **domínio de atualização**. Por exemplo, um domínio de atualização faz com que as máquinas virtuais não sejam atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planejada.
A funcionalidade básica baseada nos domínios de falha e de atualização diferentes em uma Unidade de Escala do Azure já foi introduzida no capítulo [Domínios de atualização][planning-guide-3.2.2].

* Todas as máquinas virtuais são parte do mesmo **domínio de falha**. Por exemplo, um domínio de falha faz com que as máquinas virtuais sejam implantadas de forma que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

Não há um número infinito de domínios de falha e atualização que possam ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure. Isso significa que, colocando um número de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde, mais de uma VM termina no mesmo domínio de atualização ou falha.

Implantando algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas e supondo que temos cinco domínios de atualização, o quadro a seguir surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![Figura 2: HA dos servidores de aplicativos SAP no conjunto de disponibilidade do Azure][planning-guide-figure-3000]
_**Figura 2:** HA dos servidores de aplicativos SAP no conjunto de disponibilidade do Azure_ Saiba mais detalhes nesta documentação: [Gerenciar a disponibilidade das máquinas virtuais][virtual-machines-manage-availability].


Os conjuntos de disponibilidade do Azure foram apresentados no capítulo [Conjuntos de disponibilidade do Azure] [ planning-guide-3.2.3] do documento Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver.


**Somente disco não gerenciado:** como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure nas quais pelo menos duas máquinas virtuais estejam distribuídas. Em uma configuração ideal, os discos de cada máquina virtual que está executando uma instância de diálogo SAP deveria ser implantada em uma conta de armazenamento diferente.

> [!IMPORTANT]
>
> Recomendamos o uso de Azure Managed Disks em suas instalações HA do SAP, pois eles se alinham automaticamente ao conjunto de disponibilidade da máquina virtual à qual eles estão conectados e, portanto, aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução na máquina virtual.  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>Arquitetura de alta disponibilidade para a instância do (A)SCS SAP

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Arquitetura de alta disponibilidade para a instância do (A)SCS SAP no Windows


> ![Windows][Logo_Windows] Windows
>

Você pode usar a solução **Clustering de Failover do Windows Server** (**WSFC**) para proteger a instância do (A)SCS SAP. Há duas variações da solução:

* Clustering de instância do (A)SCS SAP usando **discos compartilhados em cluster**

   Você pode encontrar mais informações sobre a arquitetura de alta disponibilidade com discos compartilhados em cluster neste documento: [Clustering de instância do (A)SCS SAP no Windows - Cluster de failover usando disco compartilhado em cluster][sap-high-availability-guide-wsfc-shared-disk].   

* Clustering de instância do (A)SCS SAP usando **compartilhamento de arquivos**

  Você pode encontrar mais informações sobre a arquitetura de alta disponibilidade com compartilhamento de arquivos neste documento: [Clustering de instância do (A)SCS SAP no Windows - Cluster de failover usando compartilhamento de arquivos][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Alta disponibilidade para a instância do (A)SCS SAP em Linux


> ![Linux][Logo_Linux] Linux
>

Você pode encontrar mais informações sobre o clustering de instância do (A)SCS SAP usando a estrutura de cluster do SUSE Linux Enterprise Server neste documento: [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP][sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>Configuração multi-SID do SAP NetWeaver para a instância do (A)SCS SAP em cluster

> ![Windows][Logo_Windows] Windows
>
>Atualmente, o Multi-SID tem suporte apenas com **WSFC (Cluster de failover do Windows Server)**. Há suporte para multi-SID usando **compartilhamento de arquivos** e **disco compartilhado**.
>

Você pode encontrar mais informações sobre a arquitetura de HA de multi-SID nestes documentos:

* [Alta disponibilidade multi-SID da instância do (A)SCS SAP com clustering de failover do Windows Server e compartilhamento de arquivos][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Alta disponibilidade multi-SID da instância do (A)SCS SAP com clustering de failover do Windows Server e disco compartilhado][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Instância do DBMS de alta disponibilidade

O DBMS também é um ponto único de contato em um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A figura a seguir mostra uma solução de alta disponibilidade Always On do SQL Server no Clustering de Failover do Windows Server e no balanceador interno de carga do Azure. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS usando sua própria replicação DBMS. Nesse caso, você não precisa de disco compartilhado de cluster, o que simplifica toda a configuração.

![Figura 3: exemplo de um DBMS SAP de alta disponibilidade com o Always On do SQL Server][sap-ha-guide-figure-2003]

_**Figura 3:** exemplo de um DBMS SAP de alta disponibilidade com o Always On do SQL Server_

Para saber mais sobre o clustering do **DBMS do SQL Server** no Azure usando o modelo de implantação do Azure Resource Manager, confira estes artigos:

* [Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Azure usando o Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configurar um balanceador de carga interno do Azure para um grupo de disponibilidade AlwaysOn no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para saber mais sobre o clustering do **DBMS do SAP HANA** no Azure usando o modelo de implantação do Azure Resource Manager, confira este artigo:

* [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure][sap-hana-ha]
