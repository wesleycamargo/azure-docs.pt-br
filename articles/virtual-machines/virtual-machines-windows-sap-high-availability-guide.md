---
title: "SAP NetWeaver em VMs (Máquinas Virtuais) do Windows — Guia de alta disponibilidade | Microsoft Docs"
description: "Guia de alta disponibilidade do SAP NetWeaver em máquinas virtuais do Windows"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: goraco
translationtype: Human Translation
ms.sourcegitcommit: 6f2e187a4767ac75350a629ace6e15e3ba3eee33
ms.openlocfilehash: ead66f3578328a2ece9c2d2e7505b667f8746d34


---
# <a name="sap-netweaver-on-windows-virtual-machines-vms---high-availability-guide"></a>SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade
[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver nas máquinas virtuais (VMs) do Windows – Guia de Implantação DBMS [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS Deployment) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High Availability and Disaster Recovery with Azure VMs) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server images out of the Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure Summary) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and Restore) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance Considerations for Backup and Restore) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver nas máquinas virtuais (VMs) do Windows – Guia de Implantação) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma VM com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantando uma VM do Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implantando uma VM com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de Implantação das VMs para SAP no Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantando os cmdlets do Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Baixar e Importar os cmdlets do PowerShell relevantes do SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ingressar a VM no Domínio local - Windows apenas) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Baixar, Instalar e habilitar o Agente de VM do Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI do Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar Extensão de Monitoramento Aprimorado do Azure para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de Preparação para Monitoramento Aprimorado do Azure para SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de Integridade para Configuração da Infraestrutura de Monitoramento do Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Solução de problemas adicional da infraestrutura de Monitoramento do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[HA do guia]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver nas VMs (máquinas virtuai) do Windows – Guia de Planejamento e Implementação) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta Disponibilidade (HA) e Recuperação de Desastre (DR) para SAP NetWeaver em execução nas Máquinas Virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta Disponibilidade para Servidores do Aplicativo SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Usando a Inicialização automática para as instâncias do SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Nuvem Somente - implantações da Máquina Virtual no Azure sem dependências na rede do cliente local) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre Locais - Implantação de uma ou várias VMs do SAP no Azure com a exigência de estar totalmente integrada na rede local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões do Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de Falha) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínio de Atualização) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de Disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de Máquina Virtual do Microsoft Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Movendo uma VM do local para o Azure com um disco não generalizado) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma VM com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover uma VM do local para o Azure com um disco não generalizado) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implantar uma VM com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença Entre um Disco e uma Imagem do Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carregando um VHD do local para o Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiando discos entre as Contas de Armazenamento do Azure) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura da VM/VHD para as implantações do SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Definindo a montagem automática para os discos anexados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Uma VM com o cenário de demonstração/treinamento do  SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceitos da implantação de Somente Nuvem das instâncias do SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de Monitoramento do Azure para o SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Alta disponibilidade do SAP NetWeaver na IaaS (infraestrutura como um serviço) do Azure) [sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (Servidores de aplicativo SAP de alta disponibilidade) [sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (Instância SAP do ASCS/SCS de alta disponibilidade) [sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (Instância SAP do ASCS/SCS de alta disponibilidade com clustering de failover do Windows Server no Azure ) [sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (Instancia DBMS de alta disponibilidade) [sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (Cenários de implantação de alta disponibilidade de ponta a ponta) [sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (Preparar a infraestrutura) [sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (Implantar máquinas virtuais com conectividade de rede corporativa (entre instalações) para uso na produção) [sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (Implantação somente na nuvem de instâncias SAP para teste e demonstração) [sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Rede virtual do Azure) [sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (Endereços IP de DNS) [sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (Nomes do host e endereços IP estáticos para instância SAP de agrupamento ASCS/SCS e instância de agrupamento de DBMS) [sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (Definir endereços IP estáticos para balanceador de carga interno) [sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (Definir endereços IP estáticos para balanceador de carga interno) [sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Regras de balanceamento de carga padrão de ASCS/SCS para o balanceador de carga interno do Azure) [sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (Alterar as regras de balanceamento de carga padrão de ASCS/SCS para o balanceador de carga interno do Azure) [sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (Adicionar máquinas virtuais do Azure ao domínio) [sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (Adicionar entradas de registro nos dois nós de cluster da instância SAP de ASCS/SCS) [sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (Configurar um cluster de clustering de failover do Windows Server para instância SAP de ASCS/SCS) [sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (Coletar os nós de cluster em uma configuração de cluster) [sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (Configurar uma testemunha de compartilhamento de cluster) [sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (Criar um compartilhamento de arquivo) [sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (Definir o quorum da testemunha de compartilhamento no Gerenciador de cluster de failover) [sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (Instalar o SIOS DataKeeper Cluster Edition no disco de compartilhamento do cluster SAP de ASCS/SCS) [sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (Adicionar o .NET Framework 3.5) [sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (Instalar o SIOS DataKeeper) [sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (Configurar o SIOS DataKeeper) [sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (Instalar o sistema SAP NetWeaver) [sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (Instalar SAP com instância de ASCS/SCS de alta disponibilidade) [sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (Criar um nome de host virtual para a instância SAP de ASCS/SCS agrupada) [sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (Instalar o primeiro nó de cluster SAP) [sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (Modificar o perfil SAP da instância do ASCS/SCS) [sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (Adicionar uma porta de investigação) [sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (Instalar a instância do banco de dados) [sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Instalar o segundo nó de cluster) [sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (Alterar o tipo de início da instância de serviço do Windows SAP ERS) [sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (Instalar o Servidor de Aplicativo primário do SAP) [sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (Instalar o Servidor de Aplicativo adicional do SAP) [sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (Testar o failover da instância SAP de ASCS/SCS e replicação de SIOS) [sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (SAP ASCS/SCS instance is running on cluster node A) [sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (Failover do nó A para o nó B) [sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (Instância SAP de ASCS/SCS em execução no nó de cluster B))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
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


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


As Máquinas Virtuais do Azure são a solução para as organizações que precisam de recursos de computação, armazenamento e rede, no mínimo de tempo e sem ciclos de compra longos. Você pode usar Máquinas Virtuais do Azure para implantar aplicativos clássicos como o ABAP baseado no SAP NetWeaver, Java e uma pilha ABAP+Java. Estenda a confiabilidade e a disponibilidade sem recursos locais adicionais. As Máquinas Virtuais do Azure dão suporte à conectividade entre locais, o que permite que as empresas integrem as Máquinas Virtuais do Azure a seus domínios locais, a nuvens privadas e à estrutura do sistema SAP.

Neste artigo, abordaremos as etapas que você pode tomar para implantar sistemas SAP de alta disponibilidade no Azure usando o novo Modelo de implantação do Azure Resource Manager. Vamos percorrer estas tarefas principais:

* Encontrar os guias de instalação e anotações do SAP corretos, listadas na seção [Recursos][sap-ha-guide-2]. Este artigo complementa a documentação de instalação do SAP e observações sobre o SAP, que são os recursos principais que podem ajudá-lo a instalar e a implantar o software SAP em plataformas específicas.
* Descubra as diferenças entre o modelo de implantação clássico do Azure e o modelo de implantação do Azure Resource Manager.
* Aprenda sobre os modos de quorum do Windows Server Failover Cluster para que você possa escolher o modelo certo para sua implantação do Azure.
* Aprenda sobre o armazenamento compartilhado do Windows Server Failover Clustering nos serviços Azure.
* Saiba como proteger componentes com ponto único de falha como ASCS (ABAP SAP Central Services)/SCS (SAP Central Services) e DBMS (sistemas de gerenciamento de banco de dados), e componentes redundantes, como servidores de aplicativos SAP, no Azure.
* Execute um exemplo passo a passo de uma instalação e configuração de um sistema SAP de alta disponibilidade em um cluster do Windows Server Failover Clustering usando o Azure como uma plataforma, com o Azure Resource Manager.
* Saiba mais sobre etapas adicionais necessárias para usar o Windows Server Failover Clustering no Azure, mas que não são necessárias em uma implantação local.

Para simplificar a implantação e a configuração, neste artigo estamos usando os novos modelos do Resource Manager de alta disponibilidade de três camadas SAP. Os modelos automatizam a implantação de toda a infraestrutura de que você precisa para um sistema SAP de alta disponibilidade. A infra-estrutura também dá suporte a dimensionamento de SAPS do sistema SAP.

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c5479-5595-4cd8-870d-15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos
Antes de começar, verifique se os pré-requisitos descritos nos capítulos a seguir são atendidos. Além disso, não deixe de verificar todos os recursos listados na seção [Recursos][sap-ha-guide-2].

Neste artigo, nós usamos modelos do Resource Manager para [SAP Netweaver de três camadas](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Para uma visão geral dos modelos, confira [Modelos do Azure Resource Manager para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="a-name42b8f600-7ba3-4606-b8a5-53c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos
Estes guias também tratam das implantações do SAP no Azure:

* [SAP NetWeaver em VMs (máquinas virtuais) do Azure – guia de planejamento e implementação][planning-guide]
* [SAP NetWeaver nas máquinas virtuais (VMs) do Windows – Guia de Implantação] [deployment-guide]
* [SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de implantação do DBMS][dbms-guide]
* [SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade [este guia]][sap-ha-guide]

> [!NOTE]
> Sempre que possível, daremos a você um link para o guia de instalação do SAP (confira os [guias de instalação do SAP][sap-installation-guides]). Para pré-requisitos e informações sobre o processo de instalação, é recomendável que você leia os guias de instalação do SAP NetWeaver cuidadosamente. Este artigo aborda apenas as tarefas específicas para sistemas baseados no SAP NetWeaver que você pode usar com as Máquinas Virtuais do Azure.
>
>

As seguintes observações do SAP estão relacionadas com o tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: dimensionamento e produtos com suporte |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1999351] |Virtualização no Windows: monitoramento avançado |

Saiba mais sobre as [limitações das assinaturas do Azure][azure-subscription-service-limits-subscription], incluindo limitações máximas e gerais padrão.

## <a name="a-name42156640c6-01cf-45a9-b225-4baa678b24f1ahigh-availability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidade com o Azure Resource Manager versus modelo de implantação clássico
Os modelos do Azure Resource Manager e clássico são diferentes em dois aspectos principais:

* Grupos de recursos
* Requisitos de clustering

### <a name="a-namef76af273-1993-4d83-b12d-65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
No Azure Resource Manager, você pode usar grupos de recursos para gerenciar todos os recursos do aplicativo em sua assinatura do Azure. Em uma abordagem integrada, em um grupo de recursos, todos os recursos têm o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo e excluídos ao mesmo tempo. Você pode obter mais informações sobre os [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="a-name3e85fbe0-84b1-4892-87af-d9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Clustering com o Azure Resource Manager versus modelo de implantação clássico
No modelo do Azure Resource Manager, você não precisa de um serviço de nuvem para usar o balanceador de carga interna do Azure a fim de ter alta disponibilidade.

Para usar o modelo clássico do Azure, siga procedimento descrito no documento [SAP NetWeaver no Azure — clustering de instâncias SAP ASCS/SCS usando o Windows Server Failover Clustering no Azure com o SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [!NOTE]
> Recomendamos fortemente que você use o modelo de implantação do Azure Resource Manager para as instalações do SAP. Ele oferece muitos benefícios que não estão disponíveis no modelo de implantação clássico. Você pode aprender mais sobre os [modelos de implantação] do Azure [virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="a-name8ecf3ba0-67c0-4495-9c14-feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
O Windows Server Failover Clustering é a base de uma instalação do SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster de failover é um grupo servidores 1+n independentes (nós) que trabalham juntos para aumentar a disponibilidade de aplicativos e serviços. Se ocorrer uma falha de nó, o Windows Server Failover Clustering calculará o número de falhas que podem ocorrer e manterá um cluster íntegro para fornecer os serviços e aplicativos definidos. Você pode escolher dentre modos de quorum diferentes para fazer isso.

### <a name="a-name1a3c5408-b168-46d6-99f5-4219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de quorum
Você pode escolher dentre quatro modos de quorum quando usa o Windows Server Failover Clustering:

* **Maioria de nós**. Cada nó do cluster pode votar. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Recomendamos essa opção para clusters com número ímpar de nós. Por exemplo, três nós em um cluster de sete nós podem falhar e o cluster ainda pode ter maioria e continuar a executar.  
* **Maioria de nós e disco**. Todos os nós e um disco designado no armazenamento de cluster (a testemunha de disco) podem votar sempre que estão disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. Se a metade dos nós e o disco estiverem online, o cluster permanecerá em estado íntegro.
* **Maioria de nós e compartilhamento de arquivos**. Todos os nós e um compartilhamento de arquivos designado criado pelo administrador (a testemunha de compartilhamento de arquivos) poderão votar sempre que estiverem disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. É semelhante ao modo Maioria de nós e disco, mas usa uma testemunha de compartilhamento de arquivos em vez de uma testemunha de disco. Esse modo é fácil de implementar, mas se o próprio compartilhamento de arquivos não estiver altamente disponível, ele poderá se tornar um ponto único de falha.
* **Sem maioria: somente disco**. O cluster terá um quorum se um nó estiver disponível e em comunicação com um disco específico no armazenamento de cluster. Somente os nós que também estão em comunicação com esse disco podem ingressar no cluster. Recomendamos que você não use esse modo.  

  ## <a name="a-namefdfee875-6e66-483a-a343-14bbaee33275a-windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover Clustering no local
  O exemplo na Figura 1 mostra um cluster de dois nós. Se a conexão de rede entre os nós falha e eles permanecem ativos e em execução, um compartilhamento de arquivo ou disco de quorum determina qual nó continuará a fornecer os serviços e aplicativos do cluster. O nó que tem acesso ao compartilhamento de arquivo ou disco de quorum é o nó que garante que os serviços continuarão.

Como esse exemplo usa um cluster de dois nós, usamos o modo de quorum Maioria dos nós e compartilhamento de arquivos. A maioria dos nós e disco também é uma opção válida. Em um ambiente de produção, recomendamos que você use um disco de quorum. Você pode usar tecnologia de sistema de rede e de armazenamento para torná-lo altamente disponível.

![Figura 1: exemplo de uma configuração de Windows Server Failover Clustering para SAP ASCS/SCS no Azure][sap-ha-guide-figure-1000]

***Figura 1:** exemplo de uma configuração de Windows Server Failover Clustering para SAP ASCS/SCS no Azure*

### <a name="a-namebe21cf3e-fb01-402b-9955-54fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Armazenamento compartilhado
A Figura 1 também mostra um cluster de armazenamento compartilhado de dois nós. Em um cluster de armazenamento compartilhado local, todos os nós no cluster detectam armazenamento compartilhado. Um mecanismo de bloqueio protege os dados contra corrupção. Todos os nós podem detectar quando outro nó falha. Se um nó falhar, o outro assume a propriedade dos recursos de armazenamento e garante a disponibilidade dos serviços.

> [!NOTE]
> Você não precisa de discos compartilhados de alta disponibilidade com alguns aplicativos de DBMS, como o SQL Server. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="a-nameff7a9a06-2bc5-4b20-860a-46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Resolução de nome e rede
Os computadores clientes acessam o cluster em um endereço IP virtual com um nome de host virtual que o servidor DNS fornece. Os nós locais e o servidor DNS podem lidar com vários endereços IP.

Em uma instalação comum, você usa duas ou mais conexões de rede:

* Uma conexão dedicada com o armazenamento
* Uma conexão de rede interna de cluster para a pulsação
* Uma rede pública que os clientes usam para se conectar ao cluster

## <a name="a-name2ddba413-a7f5-4e4e-9a51-87908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering no Azure
Comparados a implantações de nuvem privada ou bare metal, as Máquinas Virtuais do Azure exigem etapas adicionais para configurar o Windows Server Failover Clustering. Quando você cria um disco de cluster compartilhado, precisa definir vários endereços IP e nomes de host virtual para a instância do SAP ASCS/SCS.

Neste artigo, discutiremos os principais conceitos e as etapas adicionais necessárias quando você cria um cluster SAP de serviços centrais de alta disponibilidade no Azure. Mostramos a você como configurar a ferramenta de terceiros SIOS DataKeeper e como configurar o balanceador de carga interno do Azure. Você pode usar essas ferramentas para criar um cluster de failover do Windows com uma testemunha de compartilhamento de arquivo no Azure.

![Figura 2: configuração do Windows Server Failover Clustering no Azure sem um disco compartilhado][sap-ha-guide-figure-1001]

***Figura 2:** configuração do Windows Server Failover Clustering no Azure sem um disco compartilhado*

### <a name="a-name1a464091-922b-48d7-9d08-7cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartilhado no Azure com SIOS DataKeeper
Você precisa de armazenamento compartilhado em cluster para uma instância do SAP ASCS/SCS de alta disponibilidade. A partir de setembro de 2016, o Azure não oferece armazenamento compartilhado que você possa usar para criar um cluster de armazenamento compartilhado. Você pode usar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento compartilhado de cluster. A solução SIOS fornece replicação síncrona de dados em tempo real. É assim que você pode criar um recurso de disco compartilhado para um cluster:

1. Anexe um VHD (disco rígido virtual) do Azure adicional a cada uma das máquinas virtuais em uma configuração de cluster do Windows.
2. Execute o SIOS DataKeeper Cluster Edition em ambos os nós da máquina virtual.
3. Configure o SIOS DataKeeper Cluster Edition para que ele reflita o conteúdo do volume VHD anexado adicional da máquina virtual de origem para o volume VHD anexado adicional da máquina virtual de destino. O SIOS DataKeeper abstrai os volumes locais de origem e de destino e os apresenta ao Windows Server Failover Clustering como um disco compartilhado.

Saiba mais sobre [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

 ![Figura 3: configuração do Windows Server Failover Cluster no Azure usando SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figura 3:** configuração do Windows Server Failover Cluster no Azure com o SIOS DataKeeper*

> [!NOTE]
> Você não precisa de discos compartilhados de alta disponibilidade com alguns produtos de DBMS, como o SQL Server. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="a-name44641e18-a94e-431f-95ff-303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolução de nomes no Azure
 A plataforma de nuvem do Azure não oferece a opção de configurar endereços IP virtuais, como IPs flutuante. Por esse motivo, você precisa de uma solução alternativa para configurar um endereço IP virtual a fim de acessar o recurso de cluster na nuvem.
O Azure tem um balanceador de carga interno no serviço Azure Load Balancer. Com o balanceador de carga interno, os clientes alcançam o cluster pelo endereço IP virtual do cluster.
Você precisa implantar o balanceador de carga interno no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de encaminhamento de porta com as portas de investigação do balanceador de carga interno.
Os clientes podem se conectar por meio do nome de host virtual. O servidor DNS resolve o endereço IP do cluster e o balanceador de carga interno trata da porta que encaminha para o nó ativo do cluster.

## <a name="a-name2e3fec50-241e-441b-8708-0b1864f66dfaa-high-availability-sap-netweaver-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver de alta disponibilidade no Azure IaaS (Infraestrutura como um serviço)
Para obter a alta disponibilidade do SAP, como no caso de componentes de software SAP, você precisará proteger os componentes a seguir. Isso é discutido com mais detalhes em [SAP NetWeaver em VMs (máquinas virtuais) do Azure – guia de planejamento e implementação][planning-guide-11].

* Servidores de aplicativos SAP
* Instância ASCS/SCS SAP
* Servidor DBMS

### <a name="a-name93faa747-907e-440a-b00a-1ae0a89b1c0ea-high-availability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Alta disponibilidade para servidores de aplicativos SAP
Normalmente, não é necessária uma solução específica de alta disponibilidade para servidores de aplicativos SAP e instâncias de diálogo. Você atinge alta disponibilidade por redundância e configura várias instâncias de caixa de diálogo em diferentes Máquinas Virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas Máquinas Virtuais do Azure.

![Figura 4: alta disponibilidade para servidores de aplicativos SAP][sap-ha-guide-figure-2000]

***Figura 4:** alta disponibilidade para servidores de aplicativos SAP*

Você deve colocar todas as máquinas virtuais que hospedam servidores de aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais são parte do mesmo domínio de atualização. Por exemplo, um domínio de atualização faz com que as máquinas virtuais não sejam atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planejada.
* Todas as máquinas virtuais são parte do mesmo domínio de falha. Por exemplo, um domínio de falha faz com que as máquinas virtuais sejam implantadas de forma que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [Gerenciar a disponibilidade das máquinas virtuais][virtual-machines-manage-availability].

Como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure, nas quais pelo menos duas máquinas virtuais estejam distribuídas. Em uma configuração ideal, cada máquina virtual que está executando uma instância de diálogo SAP deveria ser implantada em uma conta de armazenamento diferente.

### <a name="a-namef559c285-ee68-4eec-add1-f60fe7b978dba-high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instância do SAP ASCS/SCS de alta disponibilidade
![Figura 5: instância do SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-2001]

***Figura 5:** instância do SAP ASCS/SCS de alta disponibilidade*

#### <a name="a-nameb5b1fd0b-1db4-4d49-9162-de07a0132a51a-high-availability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Instância do SAP ASCS/SCS de alta disponibilidade com o Windows Server Failover Clustering no Azure
Comparados a implantações de nuvem privada ou bare metal, as Máquinas Virtuais do Azure exigem etapas adicionais para configurar o Windows Server Failover Clustering. Para criar um cluster de failover do Windows, você precisará de um Disco de Cluster Compartilhado, vários endereços IP e nomes de host virtuais e um balanceador de carga interno do Azure para clustering da instância do SAP ASCS/SCS.

Abordaremos isso em mais detalhes mais adiante neste artigo.

![Figura 6: configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure usando SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figura 6:** configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure com SIOS DataKeeper*

### <a name="a-nameddd878a0-9c2f-4b8e-8968-26ce60be1027a-high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Instância do DBMS de alta disponibilidade
O DBMS é um ponto único de contato de um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A Figura 7 mostra um exemplo de uma solução de alta disponibilidade Always On do SQL Server no Azure usando o Windows Server Failover Clustering e o balanceador de carga interno do Azure. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS usando sua própria replicação DBMS. Nesse caso, você não precisa de discos compartilhados de cluster, o que simplifica a configuração inteira.

![Figura 7: exemplo de um DBMS SAP de alta disponibilidade: Always On do SQL Server][sap-ha-guide-figure-2003]

***Figura 7:** exemplo de um DBMS SAP de alta disponibilidade: Always On do SQL Server*

Para saber mais sobre o clustering do SQL Server no Azure usando o modelo de implantação do Azure Resource Manager, confira estes artigos:

* [Configurar o grupo de disponibilidade Always On em Máquinas Virtuais do Azure manualmente usando o Gerenciador de Recursos][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurar um balanceador de carga interno para um grupo de disponibilidade Always On no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed-0277-4fc8-8f46-c5a29694a816a-end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Cenários de implantação de alta disponibilidade de ponta a ponta
A Figura 8 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure. Nesse cenário, usamos um cluster dedicado para a instância SAP ASCS/SCS e outro para o DBMS.

![Figura 8: modelo 1 de arquitetura de HA do SAP — com cluster dedicado para a instância do ASCS/SCS e cluster dedicado para a instância do DBMS][sap-ha-guide-figure-2004]

***Figura 8:** Modelo de arquitetura do SAP de alta disponibilidade 1: clusters dedicados para ASCS/SCS e DBMS*

## <a name="a-name78092dbe-165b-454c-92f5-4972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparar a infraestrutura
Os modelos do Azure Resource Manager para SAP ajudam a simplificar a implantação dos recursos necessários.

Os modelos de três camadas também dão suporte a cenários de alta disponibilidade, como o Modelo de Arquitetura 1, que tem dois clusters. Cada cluster é um ponto único de falha de SAP para SAP ASCS/SCS e DBMS.

Aqui é onde você pode obter os modelos do Azure Resource Manager para o Cenário 1:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Quando você seleciona a imagem do Marketplace do SAP de três camadas, esta tela é mostrada no portal do Azure:

![Figura 9: especificar parâmetros do Azure Resource Manager de SAP de alta disponibilidade][sap-ha-guide-figure-3000]

***Figura 9:** especificar parâmetros do Azure Resource Manager de SAP de alta disponibilidade*

Em **SYSTEMAVAILABILITY**, selecione **HA**.

Os modelos criam:

* **Máquinas virtuais**:
  * Máquinas virtuais do Servidor de Aplicativos SAP: <*SAPSystemSID*>-di-<*Number*>
  * Máquinas virtuais de cluster ASCS/SCS: <*SAPSystemSID*>-ascs-<*Number*>
  * Um cluster DBMS: <*SAPSystemSID*>-db-<*Number*>
* **Placas de rede para todas as máquinas virtuais, com endereços IP associados**:
  * <*SAPSystemSID*>-nic-di-<*Number*>
  * <*SAPSystemSID*>-nic-ascs-<*Number*>
  * <*SAPSystemSID*>-nic-db-<*Number*>
* **Contas de Armazenamento do Azure**
* **Grupos de Disponibilidade** para:
  * Máquinas virtuais do Servidor de Aplicativos do SAP: <*SAPSystemSID*>-avset-di
  * Máquinas virtuais de cluster ASCS/SCS: <*SAPSystemSID*>-avset-ascs
  * Máquinas virtuais de cluster DBMS: <*SAPSystemSID*>-avset-db
* **Balanceador de carga interno do Azure**:
  * Com todas as portas para a instância ASCS/SCS e endereço IP <*SAPSystemSID*>-lb-ascs
  * Com todas as portas para o DBMS do SQL Server e endereço IP <*SAPSystemSID*>-lb-db
* **Grupo de segurança de rede**: <*SAPSystemSID*>-nsg-ascs-0  
  * Com uma porta de protocolo RDP externa aberta para o <*SAPSystemSID*>-ascs-0 virtual machine

> [!NOTE]
> Todos os endereços IP das placas de rede e dos balanceadores de carga interno do Azure são **dinâmicos** por padrão. Altere-os para endereços IP **Estáticos**. Descrevemos isso posteriormente neste artigo.
>
>

### <a name="a-namec87a8d3f-b1dc-4d2f-b23c-da4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implantar máquinas virtuais com conectividade de rede corporativa (entre locais) para uso na produção
Para sistemas SAP de produção, implante máquinas virtuais do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando o VPN Site a Site ou o ExpressRoute do Azure.

> [!NOTE]
> Você pode usar a instância da Rede Virtual do Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

Em **NEWOREXISTINGSUBNET**, selecione **existente**.

Em **SUBNETID**, adicione a cadeia de caracteres completa de sua SubnetID da Rede do Azure preparada em que planeja implantar suas máquinas virtuais do Azure.

Você pode obter uma lista de todas as sub-redes da rede do Azure usando este comando do PowerShell:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

O campo **ID** mostra o **SUBNETID**.

Você pode recuperar uma lista de todos os valores de **SUBNETID** usando este comando do PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

O **SUBNETID** tem esta aparência:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310a-cloud-only-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implantação somente na nuvem de instâncias do SAP para teste e demonstração
Você também pode implantar o sistema SAP de alta disponibilidade em um modelo de implantação somente na nuvem.

Esse tipo de implantação é principalmente útil para casos de uso de demonstração ou teste. Ele não é adequado para casos de uso de produção.

Em **NEWOREXISTINGSUBNET**, selecione **novo**. Deixe o campo **SUBNETID** vazio.

O modelo do Resource Manager de SAP cria automaticamente a rede virtual e a sub-rede do Azure.

> [!NOTE]
> Você também precisa implantar pelo menos uma máquina virtual dedicada para o Active Directory e DNS na mesma instância de rede virtual do Azure. O modelo não cria essas máquinas virtuais.
>
>

### <a name="a-name47d5300a-a830-41d4-83dd-1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual do Azure
Em nosso exemplo, o espaço de endereço da rede virtual do Azure é 10.0.0.0/16. Há uma sub-rede chamada **Subnet**, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e balanceadores de carga internos são implantados nessa rede virtual.

> [!NOTE]
> Não faça nenhuma alteração nas configurações de rede dentro do sistema operacional convidado. Isso inclui endereços IP, servidores DNS e sub-rede. Configure todas as configurações de rede no Azure. O serviço do protocolo DHCP propaga as configurações.
>
>

### <a name="a-nameb22d7b3b-4343-40ff-a319-097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP de DNS
Verifique se a opção **Servidores DNS** da rede virtual está definida como **DNS Personalizado**.
Em seguida, selecione suas configurações com base no tipo de rede que você tem:

* [Conectividade de Rede Corporativa (Entre Instalações)][planning-guide-2.2]: adicione os endereços IP dos servidores DNS locais.  

    Você pode estender os servidores DNS locais às máquinas virtuais que estão em execução no Azure. Nesse cenário, você pode adicionar os endereços IP das máquinas virtuais do Azure nos quais executa o serviço DNS.
* [Implantação somente em nuvem] [planning-guide-2.1]: implantar uma máquina virtual adicional na mesma instância de rede virtual que serve como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que você configurou para executar o serviço DNS.

![Figura 10: configurar servidores DNS da Rede Virtual do Azure][sap-ha-guide-figure-3001]

***Figura 10:** Configurar DNS servidores de rede virtual do Azure*

> [!NOTE]
> Se você mudar os endereços IP dos servidores DNS, será preciso reinicializar as máquinas virtuais do Azure de modo a aplicar a alteração e propagar os novos servidores DNS.
>
>

Em nosso exemplo, o serviço DNS está instalado e configurado nas seguintes máquinas virtuais do Windows:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="a-name9fbd43c0-5850-4965-9726-2a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e a instância clusterizada do DBMS
Para implantação local, você precisa destes endereços IP e nomes de host reservados:

| Função de nome de host virtual | Nome de host virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Nome de host virtual do primeiro cluster SAP ASCS/SCS (para gerenciamento de cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome de host virtual da instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de host virtual do segundo cluster SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando você criar o cluster, crie os nomes de host virtual **pr1-ascs-vir** e **pr1-dbms- vir** e os endereços IP associados que gerenciam o cluster. O processo é descrito em [Coletar nós de cluster na configuração do cluster] [sap-ha-guide-8.12.1].

Você pode criar manualmente os outros dois nomes de host virtuais, **pr1-ascs-sap** e **pr1-dbms-sap**, e os endereços IP associados no servidor DNS. A instância clusterizada da instância SAP ASCS/SCS e a instância clusterizada do DBMS usam esses recursos. Isso é descrito em [Criar um nome de host virtual para o cluster SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="a-name84c019fe-8c58-4dac-9e54-173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Definir endereços IP estáticos para as máquinas virtuais de SAP
Depois de implantar as máquinas virtuais para usar no seu cluster, você precisará definir endereços IP estáticos para todas as máquinas virtuais. Faça isso na configuração da Rede Virtual do Azure, e não no sistema operacional convidado.

Uma maneira de definir um endereço IP estático é usando o portal do Azure. No portal do Azure, vá para **Grupo de Recursos** > **Placa de Rede** > **Configurações** > **Endereço IP**.

Em **Atribuição**, selecione **Estático**. No campo **Endereço IP**, digite o endereço IP que você deseja usar.

> [!NOTE]
> Se você mudar o endereço IP da placa de rede, será preciso reinicializar as máquinas virtuais do Azure para aplicar a alteração.  
>
>

![Figura 11: definir endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

***Figura 11:** definir endereços IP estáticos para a placa de rede de cada máquina virtual*

Repita essa etapa para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo as máquinas virtuais que você deseja usar para seu serviço DNS/Active Directory.

Em nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor de aplicativos SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segundo servidor de aplicativos SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Último servidor de aplicativos SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância do ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância do ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância do DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância do DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="a-name7a8f3e9b-0624-4051-9e41-b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Definir um endereço IP estático para o balanceador de carga interno
O modelo do Azure Resource Manager para SAP cria um balanceador de carga interno do Azure usado para o cluster da instância do ASCS/SCS e para o cluster do DBMS do SAP.

A implantação inicial define o endereço IP do balanceador de carga interno como **Dinâmico**. É importante alterar o endereço IP para **Estático**.

Em nosso exemplo, temos dois balanceadores de carga internos do Azure que têm esses endereços IP estáticos:

| Função do balanceador de carga interno do Azure | Nome balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno da instância SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno de DBMS SAP |pr1-lb-dbms |10.0.0.33 |

> [!NOTE]
> O endereço IP do nome de host virtual do SAP ASCS/SCS e o mesmo do balanceador de carga interno pr1-lb-ascs do SAP ASCS/SCS.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do balanceador de carga interno do DBMS pr1-lb-dbms.
>
>

Em nosso exemplo, definimos o endereço IP do balanceador de carga interno, **pr1-lb-ascs**, como o endereço IP do nome de host virtual da instância do SAP ASCS/SCS (em nosso exemplo, **10.0.0.43**).

![Figura 12: definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

***Figura 12:** definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS*

Defina o endereço IP do balanceador de carga interno **pr1-lb-dbms** como o endereço IP do nome de host virtual da instância do DBMS (em nosso exemplo, **10.0.0.33**).

### <a name="a-namef19bd997-154d-4583-a46e-7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure
O modelo do Azure Resource Manager de SAP cria as portas de que você precisa:

* Uma instância do ASCS ABAP com número de instância padrão **00**
* Uma instância do SCS Java com número de instância padrão **01**

Quando você instala sua instância SAP ASCS/SCS, deve usar o número de instância padrão 00 para sua instância ASCS ABAP e o número de instância padrão 01 para sua instância SCS Java.

Em seguida, crie os pontos de extremidade do balanceador de carga interno necessários para as portas do ASCS ABAP do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do ASCS com número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Servidor de Mensagens ABAP / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Mensagem interna ABAP / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| HTTP do Servidor de Mensagens / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| HTTP do SAP para Iniciar Serviço ASCS / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| HTTPS do SAP para Iniciar Serviço ASCS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Enfileirar Replicação / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| RM Win *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

***Tabela 1:** números de porta das instâncias do ASCS ABAP do SAP NetWeaver*

Em seguida, crie os pontos de extremidade do balanceador de carga interno necessários para as portas do SCS Java do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Servidor do Gateway / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Servidor de Mensagens Java / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| HTTP do Servidor de Mensagens / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| HTTP do SAP para Iniciar Serviço SCS / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| HTTPS do SAP para Iniciar Serviço SCS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Enfileirar Replicação / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| RM Win *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

***Tabela 2:** números de porta das instâncias do SCS Java do SAP NetWeaver*

![Figura 13: regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

***Figura 13:** regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure*

Defina o endereço IP do balanceador de carga **pr1-lb-dbms** como o endereço IP do nome de host virtual da instância do DBMS (em nosso exemplo, **10.0.0.33**).

### <a name="a-namefe0bd8b5-2b43-45e3-8295-80bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar as regras do balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure
Se você quiser usar números diferentes para as instâncias SAP ASCS ou SCS, precisará atualizar os nomes e valores dessas portas.

Uma maneira de atualizar números de instância é usando o portal do Azure:

Vá para **<*SID*>-lb-ascs load balancer** > **Regras de balanceamento de carga**.

Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere estes valores:

* Nome
* Porta
* Porta de back-end

Por exemplo, se você quer alterar o número de instância do ASCS padrão de 00 para 31, precisa fazer as alterações em todas as portas listadas na Tabela 1.

Veja um exemplo de uma atualização para a porta *lbrule3200*.

![Figura 14: alterar as regras do balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

***Figura 14:** alterar as regras do balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure*

### <a name="a-namee69e9a34-4601-47a3-a41c-d2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas virtuais do Windows ao domínio
Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 15: adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

***Figura 15:** adicionar uma máquina virtual a um domínio*

### <a name="a-name661035b2-4d0f-4d31-86f8-dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS
O Azure Load Balancer tem um balanceador de carga interno que fecha conexões quando elas estão ociosas por determinado período de tempo (um tempo limite de ociosidade). Os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com o processo de enfileiramento do SAP assim que a primeira solicitação para enfileirar/desenfileirar precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se a conexão fica ociosa por um período de tempo, o balanceador de carga interno do Azure fecha as conexões. Isso não é um problema porque o processo de trabalho do SAP restabelece a conexão com o processo de enfileiramento se ele não existe mais. Essas atividades são documentadas nos rastreamentos de desenvolvedor dos processos da SAP, mas criam uma grande quantidade de conteúdo extra nesses rastreamentos. É uma boa ideia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros de TCP/IP com parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Adicione as seguintes entradas de registro do Windows em ambos os nós do cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Vincular à documentação |[https://technet.microsoft.com/pt-BR/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

***Tabela 3:** alterar o primeiro parâmetro de TCP/IP*

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Vincular à documentação |[https://technet.microsoft.com/pt-BR/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

***Tabela 4:** alterar o segundo parâmetro de TCP/IP*

Para aplicar as alterações, reinicie os dois nós do cluster.

### <a name="a-name0d67f090-7928-43e0-8772-5ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster Windows Server Failover Clustering para uma instância do SAP ASCS/SCS
#### <a name="a-name5eecb071-c703-4ccc-ba6d-fe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Coletar nós de cluster em uma configuração do cluster
A primeira etapa é adicionar o clustering de failover a ambos os nós do cluster. Use o assistente Adicionar funções e recursos.

A segunda etapa é configurar o cluster de failover usando o Gerenciador de Cluster de Failover.

No Gerenciador de Cluster de Failover, selecione **Criar Cluster** e adicione apenas o nome do primeiro nó A do cluster. Por exemplo, adicione **pr1-ascs-0**. Não adicione o segundo nó ainda. Você adicionará o segundo nó em uma etapa posterior.

![Figura 16: adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

***Figura 16:** adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster*

Em seguida, você será solicitado a dar o nome de rede (nome de host virtual) do cluster.

![Figura 17: definir o nome do cluster][sap-ha-guide-figure-3008]

***Figura 17:** definir o nome do cluster*

Depois de criar o cluster, execute um teste de validação de cluster.

![Figura 18: executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

***Figura 18:** executar a verificação de validação de cluster*

![Figura 19: nenhum disco de quorum é encontrado][sap-ha-guide-figure-3010]

***Figura 19:** nenhum disco de quorum é encontrado*

Você pode ignorar os avisos sobre discos nessa altura do processo. Você adicionará uma testemunha de compartilhamento de arquivo e discos compartilhados do SIOS mais tarde. Neste estágio, você não precisa se preocupar em ter um quorum.

![Figura 20: recurso de cluster principal precisa de um novo endereço IP][sap-ha-guide-figure-3011]

***Figura 20:** recurso de cluster principal precisa de um novo endereço IP*

O cluster não pode iniciar porque o endereço IP do servidor aponta para um dos nós da máquina virtual. Você precisa alterar o endereço IP do serviço principal do cluster.

Por exemplo, precisamos atribuir um endereço IP (em nosso exemplo **10.0.0.42**) ao nome de host virtual do cluster **pr1-ascs-vir**. Faça isso na página de propriedades do recurso IP do serviço de cluster principal, mostrado na Figura 21.

![Figura 21: na caixa de diálogo **Propriedades**, altere o endereço IP][sap-ha-guide-figure-3012]

***Figura 21:** na caixa de diálogo **Propriedades**, altere o endereço IP*

![Figura 22: atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

***Figura 22:** atribuir o endereço IP reservado para o cluster*

Depois de alterar o endereço IP, coloque o nome de host virtual do cluster online.

![Figura 23: serviço principal do cluster funcionando com o endereço IP correto][sap-ha-guide-figure-3014]

***Figura 23:** serviço principal do cluster funcionando com o endereço IP correto*

Agora que o serviço principal do cluster está em execução, você pode adicionar o segundo nó do cluster.

![Figura 24: Adicionar o segundo nó do cluster][sap-ha-guide-figure-3015]

***Figura 24:** adicionar o segundo nó do cluster*

![Figura 25: adicionar o nome de host do segundo nó do cluster, por exemplo, pr1-ascs-1][sap-ha-guide-figure-3016]

***Figura 25:** adicionar o nome de host do segundo nó de cluster, por exemplo, **pr1-ascs-1***

![Figura 26: não marcar a caixa de seleção][sap-ha-guide-figure-3017]

***Figura 26:** *não* marcar a caixa de seleção*

> [!IMPORTANT]
> Verifique se a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster** *não* está marcada.  
>
>

![Figura 27: ignorar os avisos sobre o quorum de disco][sap-ha-guide-figure-3018]

***Figura 27:** ignorar os avisos sobre o quorum de disco*

Você pode ignorar os avisos sobre quorum e discos. Você configurará o quorum e compartilhará o disco mais tarde, conforme descrito em [Instalando o SIOS DataKeeper Cluster Edition para o disco compartilhado de cluster do SAP ASCS/SCS][sap-ha-guide-8.12.3].

#### <a name="a-namee49a4529-50c9-4dcf-bde7-15a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar a testemunha de compartilhamento de arquivos do cluster
##### <a name="a-name06260b30-d697-4c4d-b1c9-d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar um compartilhamento de arquivos
Selecione uma testemunha de compartilhamento de arquivos em vez de um disco de quorum. O SIOS DataKeeper dá suporte a essa opção.

Nos exemplos citados neste artigo, a testemunha de compartilhamento de arquivo está no servidor DNS/Active Directory em execução no Azure. A testemunha de compartilhamento de arquivo é chamada **domcontr-0**. Como você configuraria uma conexão VPN (de rede virtual privada) para o Azure (via VPN Site a Site ou Azure ExpressRoute), o serviço DNS/Active Directory é local e não é adequado para executar uma testemunha de compartilhamento de arquivo.

> [!NOTE]
> Se seu serviço DNS/Active Directory é executado somente no local, não configure a testemunha de compartilhamento de arquivo no sistema operacional Windows do Active Directory/DNS que está sendo executado no local. A latência de rede entre os nós de cluster em execução no Azure e o Active Directory/DNS local pode ser muito grande e causar problemas de conectividade. Não deixe de configurar a testemunha de compartilhamento de arquivo em uma máquina virtual do Azure que está em execução perto do nó do cluster.  
>
>

A unidade de quorum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos ter 2.048 MB de espaço livre.

A primeira etapa é adicionar o objeto de nome do cluster.

![Figura 28: atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

***Figura 28:** atribuir as permissões no compartilhamento para o objeto de nome do cluster*

Verifique se as permissões incluem o poder de alterar dados no compartilhamento para o objeto de nome de cluster (em nosso exemplo **pr1-ascs-vir$**). Para adicionar o objeto de nome do cluster à lista, selecione **Adicionar**. Altere o filtro para procurar objetos de computação além daqueles mostrados na Figura 29:

![Figura 29: alterar o tipo de objeto para incluir também objetos de computação][sap-ha-guide-figure-3020]

***Figura 29:** alterar o tipo de objeto para incluir também objetos de computação*

![Figura 30: marcar a caixa de seleção para objetos de computação][sap-ha-guide-figure-3021]

***Figura 30:** marcar a caixa de seleção para objetos de computação*

Depois disso, insira o objeto de nome do cluster conforme mostrado na Figura 29. Como o registro já foi criado, você pode alterar as permissões conforme mostrado na Figura 28.

Em seguida, selecione a guia **Segurança** do compartilhamento e defina permissões mais detalhadas para o objeto de nome de cluster.

![Figura 31: definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

***Figura 31:** definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos*

##### <a name="a-name4c08c387-78a0-46b1-9d27-b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Definir o quorum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover
No Gerenciador de Cluster de Failover, altere a configuração do cluster para uma testemunha de compartilhamento de arquivos.

![Figura 32: iniciar o Assistente de configuração de quorum do cluster][sap-ha-guide-figure-3023]

***Figura 32:** iniciar o Assistente de configuração de quorum do cluster*

![Figura 33: configurações de quorum à sua escolha][sap-ha-guide-figure-3024]

***Figura 33:** configurações de quorum à sua escolha*

Selecione **Selecionar a testemunha de quorum**.

![Figura 34: selecionar a testemunha de compartilhamento de arquivo][sap-ha-guide-figure-3025]

***Figura 34:** selecionar a testemunha de compartilhamento de arquivo*

Selecione **Configurar uma testemunha de compartilhamento de arquivo**.

![Figura 35: definir o local do compartilhamento de arquivos para a testemunha de compartilhamento][sap-ha-guide-figure-3026]

***Figura 35:** definir o local do compartilhamento de arquivos para a testemunha de compartilhamento*

Digite o caminho UNC para o compartilhamento de arquivos (em nosso exemplo, \\domcontr-0\FSW).

Selecione **Avançar** para ver uma lista das alterações que você pode fazer. Selecione as alterações que você deseja e selecione **Avançar**.

![Figura 36: confirmação de que você reconfigurou o cluster][sap-ha-guide-figure-3027]

***Figura 36:** confirmação de que você reconfigurou o cluster*

Nesta última etapa, você precisa redefinir com êxito a configuração do cluster, conforme mostrado na Figura 36.  

### <a name="a-name5c8e5482-841e-45e1-a89d-a05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalar SIOS DataKeeper Cluster Edition para disco de compartilhamento de cluster do SAP ASCS/SCS
Agora, você tem uma configuração do Windows Server Failover Clustering em funcionamento no Azure. Mas para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Você não pode criar os recursos de disco compartilhado de que precisa no Azure. O SIOS DataKeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.

#### <a name="a-name1c2788c3-3648-4e82-9e0d-e058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar o .NET Framework 3.5
O Microsoft .NET Framework 3.5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Mas o SIOS DataKeeper requer que o .NET Framework esteja em todos os nós em que você instalar o DataKeeper. Por isso, você deve instalar o .NET Framework 3.5 no sistema operacional convidado de todas as máquinas virtuais no cluster.

Há duas maneiras de adicionar o .NET Framework 3.5. Uma delas é usar o assistente Adicionar funções e recursos no Windows, mostrado na Figura 37.

![Figura 37: instalar o .NET Framework 3.5 usando o assistente Adicionar funções e recursos][sap-ha-guide-figure-3028]

***Figura 37:** instalar o .NET Framework 3.5 usando o assistente Adicionar funções e recursos*

![Figura 38: barra de progresso da instalação quando você instala o .NET Framework 3.5 usando o assistente Adicionar funções e recursos][sap-ha-guide-figure-3029]

***Figura 38:** barra de progresso da instalação quando você instala o .NET Framework 3.5 usando o assistente Adicionar funções e recursos*

A segunda opção para ativar o recurso .NET Framework 3.5 é usar a ferramenta de linha de comando dism.exe. Para esse tipo de instalação, você precisa acessar o diretório SxS na mídia de instalação do Windows. Execute este comando em um prompt de comando elevado:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a2-8083-415b-9878-839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar SIOS DataKeeper
Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Com o SIOS DataKeeper, para criar um armazenamento compartilhado virtual, crie um espelho sincronizado e simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio **DataKeeperSvc**.

> [!NOTE]
> Adicione esse usuário **DataKeeperSvc** ao grupo **Administradores locais** em ambos os nós do cluster.
>
>

Instale o software SIOS em ambos os nós do cluster.

![Instalador do SIOS][sap-ha-guide-figure-3030]

![Figura 39: primeira tela de instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

***Figura 39:** primeira tela de instalação do SIOS DataKeeper*

![Figura 40: o DataKeeper informa a você que um serviço será desabilitado][sap-ha-guide-figure-3032]

***Figura 40:** o DataKeeper informa a você que um serviço será desabilitado*

Na caixa de diálogo mostrada na figura 40, selecione **Sim**.

![Figura 41: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

***Figura 41:** seleção do usuário para o SIOS DataKeeper*

Na tela mostrada na Figura 41, recomendamos que você selecione **Conta de domínio ou servidor**.

![Figura 42: inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

***Figura 42:** inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper*

Insira o nome de usuário da conta de domínio e senhas que você criou para o SIOS DataKeeper.

![Figura 43: fornecer sua licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

***Figura 43:** fornecer sua licença do SIOS DataKeeper*

Instale a chave de licença para o SIOS DataKeeper conforme mostrado na Figura 43. No final da instalação, você precisará reiniciar a máquina virtual.

#### <a name="a-named9c1fc8e-8710-4dff-bec2-1f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar SIOS DataKeeper
Depois de instalar o SIOS DataKeeper em ambos os nós, você precisará iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre o VHD adicional anexado a cada uma das máquinas virtuais. Essas são as etapas para configurar ambos os nós.

![Figura 44: ferramenta de Gerenciamento e Configuração do SIOS DataKeeper][sap-ha-guide-figure-3036]

***Figura 44:** ferramenta de Gerenciamento e Configuração do SIOS DataKeeper*

Inicie a ferramenta de Configuração e Gerenciamento de DataKeeper e selecione **Conectar Servidor**. (Na Figura 44, essa opção é marcada em vermelho.)

![Figura 45: inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento deve se conectar e, em uma segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

***Figura 45:** inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento deve se conectar e, em uma segunda etapa, o segundo nó*

A próxima etapa é criar o trabalho de replicação entre os dois nós.

![Figura 46: criar trabalho de replicação][sap-ha-guide-figure-3038]

***Figura 46:** criar trabalho de replicação*

Um assistente orienta você pelo processo de criação de um trabalho de replicação.

![Figura 47: definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

***Figura 47:** definir o nome do trabalho de replicação*

![Figura 48: definir os dados de base do nó que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

***Figura 48:** definir os dados de base do nó que deve ser o nó de origem atual*

Na primeira etapa, você precisa definir o nome, o endereço TCP/IP e o volume de disco do nó de origem. A segunda etapa é definir o nó de destino. Na primeira etapa, você precisa definir o nome, o endereço TCP/IP e o volume de disco do nó de destino.

![Figura 49: definir os dados base do nó que deve ser o nó de destino atual][sap-ha-guide-figure-3041]

***Figura 49:** definir os dados base do nó que deve ser o nó de destino atual*

Em seguida, defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Particularmente em situações de ressincronização, a compactação do fluxo de replicação reduz consideravelmente o tempo de ressincronização. Observe que a compactação utiliza os recursos de CPU e RAM de uma máquina virtual. Conforme aumenta a taxa de compactação, aumenta o volume de recursos de CPU usados. Você pode ajustar essa configuração mais tarde.

Outra configuração que precisa ser verificada é se a replicação é executada de modo síncrono ou assíncrono. *Quando você protege configurações SAP ASCS/SCS, deve usar a replicação síncrona*.  

![Figura 50: definir detalhes de replicação][sap-ha-guide-figure-3042]

***Figura 50:** definir detalhes de replicação*

A última etapa é definir se o volume que é replicado pelo trabalho de replicação deve ser representado em uma configuração de cluster Windows Server Failover Clustering como um disco compartilhado. Para a configuração do SAP ASCS/SCS, escolha **Sim** para que o cluster do Windows veja o volume replicado como disco compartilhado que pode ser usado como volume de cluster.

![Figura 51: selecionar **Sim** para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

***Figura 51:** selecionar **Sim** para definir o volume replicado como um volume de cluster*

Depois que o volume é criado, a ferramenta de Configuração e Gerenciamento do DataKeeper mostra que o trabalho de replicação está ativo.

![Figura 52: o espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

***Figura 52:** o espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo*

Agora, o Gerenciador de Cluster de Failover mostra o disco como um disco do DataKeeper, conforme mostrado na Figura 53.

![Figura 53: o Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado][sap-ha-guide-figure-3045]

***Figura 53:** o Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado*

## <a name="a-namea06f0b49-8a7a-42bf-8b0d-c12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalar o sistema SAP NetWeaver
Não descreveremos a instalação DBMS porque as configurações variam dependendo do sistema DBMS que você usar. No entanto, supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diferentes fornecedores de DBMS dão para o Azure. Por exemplo, o AlwaysOn ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para Oracle. No cenário que usamos neste artigo, não adicionamos outra proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com esse tipo de configuração de cluster SAP ASCS/SCS no Azure.

> [!NOTE]
> O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema ABAP+Java do SAP tem uma instância SCS e uma instância ABAP+Java em execução no mesmo grupo de cluster de failover da Microsoft. As diferenças de instalação para cada pilha de instalação do SAP NetWeaver serão explicitamente mencionadas. Você pode assumir que todas as outras partes são as mesmas.  
>
>

### <a name="a-name31c6bd4f-51df-4057-9fdf-3fcbc619c170a-install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade
> [!IMPORTANT]
> Não coloque o arquivo da página em volumes espelhados do DataKeeper. O DataKeeper não dá suporte a volumes espelhados. Você pode deixar o arquivo de página na unidade D temporária de uma máquina virtual do Azure, o que é o padrão. Se já não estiver lá, mova o arquivo da página do Windows para a unidade D da máquina virtual do Azure.
>
>

#### <a name="a-namea97ad604-9094-44fe-a364-f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS
Primeiro, no Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância ASCS/SCS. Em seguida, defina o endereço IP atribuído ao nome de host virtual.

> [!NOTE]
> Lembre-se de que o endereço IP que você atribui ao nome de host virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que você atribuiu ao Azure Load Balancer (<*SID*>-lb-ascs).  
>
>

O endereço IP do nome de host virtual do SAP ASCS/SCS (pr1-ascs sap) é o mesmo que o endereço IP do Azure Load Balancer (pr1-lb-ascs).

Apenas uma função do cluster de failover do SAP pode ser executada em um cluster de failover do Windows Server no Azure. Por exemplo, isso significa uma instância ASCS para o sistema ABAP e uma instância SCS para o sistema Java. Para ABAP + Java, seria uma instância ASCS e uma instância SCS.

> [!NOTE]
> Atualmente, clustering de vários SID conforme descrito nos guias de instalação do SAP (confira [Guias de instalação do SAP][sap-installation-guides]) não funciona no Azure.
>
>

![Figura 54: definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

***Figura 54:** definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP*

A entrada é no Gerenciador DNS, no domínio, conforme mostrado na Figura 55.

![Figura 55: novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

***Figura 55:** novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS*

#### <a name="a-nameeb5af918-b42f-4803-bb50-eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o primeiro nó do cluster do SAP
Para instalar o primeiro cluster SAP, execute a primeira opção de nó de cluster no nó A do cluster. Por exemplo, no host **pr1-ascs-0**.

Se quiser manter as portas padrão para o balanceador de carga interno do Azure, escolha:

* **Sistema ABAP**: número da instância **ASCS** **00**
* **Sistema Java**: número da instância **SCS** **01**
* **Sistema ABAP + JAVA**: número da instância **ASCS** **00** e número da instância **SCS** **01**

Se você quiser usar números de instância além de 00 para a instância do ASCS ABAP e 01 para a instância do SCS Java, primeiramente será preciso alterar as regras padrão de balanceamento de carga do Balanceador de Carga Interno do Azure, conforme descrito em [Alterar as regras do balanceamento de carga padrão do ASCS/SCS para o Balanceador de Carga Interno do Azure][sap-ha-guide-8.9].

Em seguida, siga algumas etapas que não estão descritas na documentação de instalação comum do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó ASCS/SCS do cluster.
>
>

#### <a name="a-namee4caaab2-e90f-4f2c-bc84-2cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância ASCS/SCS
Você precisa adicionar um novo parâmetro de perfil. O parâmetro de perfil impede conexões entre os processos de trabalho do SAP e o servidor de enfileiramento de fechar quando estão ociosas por muito tempo. Mencionamos que o cenário de problema em [Adicionar entradas de registro em ambos os nós de cluster da instância SAP ASCS/SCS] [sap-ha-guide-8.11] neste artigo. Nessa seção, também apresentamos duas alterações para alguns parâmetros básicos de conexão TCP/IP. Na segunda etapa, você precisa configurar o servidor de enfileiramento a fim de enviar um sinal **keep_alive** para que as conexões não atinjam o limite de ociosidade do balanceador de carga interno do Azure.

Adicione este parâmetro de perfil ao perfil da instância do SAP ASCS/SCS:

```
enque/encni/set_so_keepalive = true
```
Em nosso exemplo, o caminho é:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Por exemplo, para o perfil da instância do SAP SCS e o caminho correspondente:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

#### <a name="a-name10822f4f-32e7-4871-b63a-9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar uma porta de investigação
Use a funcionalidade de investigação do balanceador de carga interno para fazer com que toda a configuração do cluster funcione com o Balanceador de Carga. Normalmente, um balanceador de carga interno do Azure distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes. No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar parte da carga de trabalho. A funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure atribui o trabalho apenas a uma instância ativa. Com a funcionalidade de investigação, o balanceador de carga interno pode detectar quais instâncias estão ativas e mirar apenas na instância com a carga de trabalho.

Primeiro, verifique a configuração **ProbePort** atual com este comando do PowerShell. Execute-o dentro de uma das máquinas virtuais na configuração do cluster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 56: a porta de investigação da configuração de cluster é 0 por padrão][sap-ha-guide-figure-3048]

***Figura 56:** a porta de investigação da configuração de cluster é 0 por padrão*

Em seguida, defina uma porta de investigação. O número da porta de investigação padrão é 0. Em nosso exemplo, usamos a porta de investigação **62300**.

O número da porta é definido nos modelos do Azure Resource Manager para SAP. Você pode atribuir o número da porta no PowerShell.

Primeiramente, obtenha o recurso de cluster de nome de host virtual **SAP WAC IP**.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

Em seguida, defina a porta de investigação como **62300**.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Para ativar as alterações, pare e inicie a função de cluster **SAP PR1**.

Depois de colocar a função de cluster **SAP PR1** online, verifique se **ProbePort** está definido como o novo valor:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 57: investigar a porta de cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

***Figura 57:** investigar a porta de cluster depois de definir o novo valor*

A **ProbePort** é definida como **62300**. Agora é possível acessar o compartilhamento de arquivos **\\ascsha-clsap\sapmnt** de outros hosts como **ascsha-dbas**.

### <a name="a-name85d78414-b21d-4097-92b6-34d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalar a instância de banco de dados
Para instalar a instância de banco de dados, siga o processo descrito na documentação de instalação do SAP.

### <a name="a-name8a276e16-f507-4071-b829-cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó do cluster
Para instalar o segundo cluster, siga as etapas no guia de instalação do SAP.

### <a name="a-name094bc895-31d4-4471-91cc-1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de início da instância do serviço Windows ERS do SAP
Alterar o tipo de início do serviço Windows ERS (servidor de replicação de enfileiramento) do SAP para **Automático (início atrasado)** em ambos os nós de cluster.

![Figura 58: alterar o tipo de serviço da instância ERS do SAP para atraso automático][sap-ha-guide-figure-3050]

***Figura 58:** alterar o tipo de serviço da instância ERS do SAP para atraso automático*

### <a name="a-name2477e58f-c5a7-4a5d-9ae3-7b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalar o servidor de aplicativos primário SAP
Instalar a instância <*SID*>-di-0 do PAS (Servidor de Aplicativos primário) na máquina virtual que você designou para hospedar o PAS. Não há dependências de especificações do Azure ou do DataKeeper.

### <a name="a-name0ba4a6c1-cc37-4bcf-a8dc-025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalar o servidor de aplicativos SAP adicional
Instale um servidor de aplicativos adicional (AAS) SAP em todas as máquinas virtuais que você designou para hospedar um servidor de aplicativos SAP. Por exemplo, de <*SID*>-di-1 para <*SID*>-di-<n>.

## <a name="a-name18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar o failover da instância do SAP ASCS/SCS e a replicação do SIOS
Você pode testar e monitorar facilmente um failover da instância do SAP ASCS/SCS e a replicação de disco do SIOS usando o Gerenciador de Cluster de Failover e a interface de usuário do SIOS DataKeeper.

### <a name="a-name65fdef0f-9f94-41f9-b314-ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> A instância do SAP ASCS/SCS está em execução no Nó A do Cluster
O grupo de clusters **SAP WAC** está em execução no nó A do cluster. Por exemplo, em **ascsha-clna**. Atribua a unidade de disco compartilhado S, que é parte do grupo de cluster **SAP WAC** e é usada pela instância ASCS/SCS, ao cluster A do nó.

![Figura 59: Gerenciador de Cluster de Failover: o grupo de clusters SAP <*SID*> está em execução no Nó A do cluster][sap-ha-guide-figure-5000]

***Figura 59:** Gerenciador de Cluster de Failover: o grupo de clusters SAP <*SID*> está em execução no Nó A do cluster*

Usando a interface de usuário do SIOS DataKeeper, você pode ver que os dados do disco compartilhado são replicados de modo síncrono da unidade do volume de origem S em um nó A do cluster para a unidade do volume de destino S no nó B do cluster. Por exemplo, de **ascsha-clna [10.0.0.41]** para **ascsha-clnb [10.0.0.42]**.

![Figura 60: no SIOS DataKeeper, replique o volume local do nó A do cluster para o nó B do cluster][sap-ha-guide-figure-5001]

***Figura 60:** no SIOS DataKeeper, replique o volume local do nó A do cluster para o nó B do cluster*

### <a name="a-name5e959fa9-8fcd-49e5-a12c-37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover do nó A para o nó B
Você pode usar estas opções para iniciar um failover do grupo de clusters SAP <*SID*> do nó A do cluster para o nó B do cluster:

* Usar o Gerenciador de Cluster de Failover  
* Usar o PowerShell de Cluster de Failover
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
* Reiniciar o nó A do cluster no sistema operacional convidado do Windows (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B)  
* Reiniciar o nó A do portal do Azure (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B)  
* Reiniciar o nó A usando o Azure PowerShell (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

Após o failover, o grupo de cluster SAP <*SID*> está em execução no nó B do cluster. Por exemplo, em **ascsha clnb**.

![Figura 61: No gerenciador de Cluster de Failover, o grupo de clusters SAP <*SID*> está em execução no nó B do cluster][sap-ha-guide-figure-5002]

***Figura 61**: No gerenciador de Cluster de Failover, o grupo de clusters SAP <*SID*> está em execução no nó B do cluster*

Agora, o disco compartilhado é montado no nó B do cluster. O SIOS DataKeeper está replicando dados da unidade do volume de origem S no nó B do cluster para a unidade do volume de destino S no nó A do cluster. Por exemplo, **ascsha-clna [10.0.0.42]** para **ascsha-clna [10.0.0.41]**.

![Figura 62: o SIOS DataKeeper replica o volume local do nó B do cluster para o nó A do cluster][sap-ha-guide-figure-5003]

***Figura 62:** o SIOS DataKeeper replica o volume local do nó B do cluster para o nó A do cluster*



<!--HONumber=Nov16_HO3-->


