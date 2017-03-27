---
title: "SAP NetWeaver em Máquinas Virtuais do Azure – Planejamento e implementação | Microsoft Docs"
description: "SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de planejamento e implementação"
services: virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 2b58a419-c892-4722-8cb6-2f8beef4430c
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 90320bb319619f9cbe7a8d2c5b06387b92aa6b1c
ms.lasthandoff: 03/03/2017


---
# <a name="sap-netweaver-on-azure-windows-virtual-machines-vms--planning-and-implementation-guide"></a>SAP NetWeaver em VMs (Máquinas Virtuais) Windows do Azure – Guia de planejamento e implementação
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver em VMs [Máquinas Virtuais] do Azure – Guia de Implantação do DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching para VMs e VHDs) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID de Software) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento do Microsoft Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implantação do RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidade e recuperação de desastre com VMs do Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Usando imagens do SQL Server com origem no Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumo geral do SQL Server para SAP no Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS) (Especificações do SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuração de armazenamento) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauração) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerações sobre desempenho para backup e restauração) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Outros) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de implantação) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos do SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma VM com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantando uma VM do Azure Marketplace para o SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implantando uma VM com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implantação de VMs para SAP no Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantando cmdlets do Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Baixar e Importar os cmdlets do PowerShell relevantes do SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ingressar VM em domínio local – Windows apenas) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Baixar, instalar e habilitar o Agente de VM do Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI do Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar Extensão de Monitoramento Avançado do Azure para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de preparação para o Monitoramento Avançado do Azure para SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de integridade para configuração de infraestrutura de monitoramento do Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP)

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

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

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

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de planejamento e implementação) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos) [planning-guide-11.4]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (HA (alta disponibilidade) e DR (recuperação de desastre) para SAP NetWeaver em execução em Máquinas Virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidade para servidores de aplicativos SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Usando a inicialização automática para instâncias SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Somente em nuvem – Implantações de Máquinas Virtuais no Azure sem dependências na rede do cliente local) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre instalações – Implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões do Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de falha) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínios de atualização) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de Disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (O conceito de máquina virtual do Microsoft Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Movendo uma VM do local para o Azure com um disco não generalizado) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma VM com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover uma VM do local para o Azure com um disco não generalizado) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implantar uma VM com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com SAP para o Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco e uma imagem do Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carregando um VHD do local no Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiando discos entre Contas de Armazenamento do Azure) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura de VM/VHD para implantações SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Definindo a montagem automática para os discos anexados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Cenário de treinamento/demonstração de VM única com SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceitos de implantação somente em nuvem de instâncias do SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de monitoramento do Azure para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure)

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

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
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
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:virtual-machines-windows-create-vm-generalized.md
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
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:windows/classic/ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:windows/classic/ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
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
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../azure-resource-manager/xplat-cli-azure-resource-manager.md

O Microsoft Azure permite que empresas adquiram recursos de computação e armazenamento gastando o mínimo de tempo, sem ciclos de compras longos. Máquinas Virtuais do Azure permitem que as empresas implantem no Azure aplicativos clássicos, como aplicativos baseados na SAP NetWeaver, além de aumentar a confiabilidade e disponibilidade desses aplicativos sem necessidade de mais recursos disponíveis localmente. Os Serviços da Máquina Virtual do Azure também dão suporte à conectividade entre locais, o que permite que as empresas integrem ativamente as Máquinas Virtuais do Azure em seus domínios locais, suas nuvens privadas e sua estrutura do sistema da SAP.
Este white paper descreve os conceitos básicos de máquina virtual do Microsoft Azure e fornece uma explicação das considerações de planejamento e implementação para instalações da SAP NetWeaver no Azure e, por esse motivo, é o documento que deve ser lido antes de iniciar implantações reais da SAP NetWeaver no Azure.
O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

## <a name="summary"></a>Resumo
Computação em Nuvem é um termo amplamente usado que está adquirindo cada vez mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações.

O Microsoft Azure é a Plataforma de Serviços de Nuvem da Microsoft que oferece um amplo espectro de novas possibilidades. Agora, os clientes podem provisionar e desprovisionar com rapidez os aplicativos como um serviço na nuvem, para que não sejam limitados por restrições técnicas ou orçamentárias. Em vez de investir tempo e dinheiro na infraestrutura de hardware, as empresas podem se concentrar no aplicativo, nos processos de negócios e em seus benefícios para clientes e usuários.

Com os Serviços de Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma abrangente de IaaS (Infraestrutura como Serviço). Os aplicativos baseados no SAP NetWeaver têm suporte em Máquinas Virtuais do Azure (IaaS). Este whitepaper descreverá como planejar e implementar aplicativos baseados na SAP NetWeaver no Microsoft Azure como a plataforma escolhida.

O whitepaper se concentrará em dois aspectos principais:

* A primeira parte descreverá dois padrões de implantação (ambos com suporte) para aplicativos baseados na SAP NetWeaver no Azure. Ele também descreve a manipulação geral do Azure com implantações da SAP em mente.
* A segunda parte detalhará a implementação dos dois diferentes cenários descritos na primeira parte.

Para obter recursos adicionais, confira o capítulo [Recursos][planning-guide-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições prévias
No decorrer do documento, usaremos os termos a seguir:

* IaaS: infraestrutura como serviço.
* PaaS: plataforma como serviço.
* SaaS: software como serviço.
* ARM: Azure Resource Manager
* Componente SAP: um aplicativo SAP individual como ECC, BW, Solution Manager ou EP.  Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como Desenvolvimento, QAS, Treinamento, DR ou Produção.
* Estrutura da SAP: refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
* Sistema SAP: a combinação de camada DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção SAP CRM, etc. Em implantações do Azure não há suporte para dividir essas duas camadas entre local e Azure. Isso significa que um sistema SAP é implantado localmente ou no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou localmente. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, mas o sistema de produção CRM SAP localmente.
* Implantação somente em nuvem: uma implantação em que a assinatura do Azure não está conectada por meio de uma conexão site a site ou de Rota Expressa à infraestrutura de rede local. Na documentação comum do Azure, esses tipos de implantações também são descritos como implantações 'Somente em nuvem'. As Máquinas Virtuais implantadas com esse método são acessadas pela Internet e um endereço IP público e/ou um nome DNS público é atribuído às VMs no Azure. Para o Microsoft Windows, o AD (Active Directory) local e o DNS não se estendem ao Azure nesses tipos de implantações. Portanto, as VMs não fazem parte do Active Directory local. Mesmo é verdadeiro para implementações de Linux usando, por exemplo, OpenLDAP + Kerberos.

> [!NOTE]
> As implantações de Somente Nuvem neste documento são definidas como cenários SAP completos executados exclusivamente no Azure sem a extensão do Active Directory / OpenLDAP ou a resolução de nome do local para a nuvem pública. Não há suporte para configurações somente em nuvem para sistemas SAP de produção ou configurações nas quais o SAP STMS ou outros recursos locais precisem ser usados entre sistemas SAP hospedados no Azure e recursos locais.
>
>

* Entre instalações: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de Rota Expressa entre o(s) datacenter(s) local(is) e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo para a conexão é estender domínios locais, Active Directory/OpenLDAP local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com esta extensão, as VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e resolução de nomes entre máquinas virtuais implantadas localmente e VMs implantadas no Azure são possíveis. Esse é o cenário em que esperamos que a maioria dos ativos SAP sejam implantados.  Para obter mais informações, veja [este][vpn-gateway-cross-premises-options] artigo e [este][vpn-gateway-site-to-site-create].

> [!NOTE]
> Implantações entre instalações de sistemas SAP em que máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local têm suporte para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura da SAP completa no Azure requer que essas VMs sejam parte do domínio local e ADS/OpenLDAP. Em versões anteriores da documentação, falamos sobre cenários de TI híbrido, em que o termo 'Híbrido' está enraizado no fato de que há uma conectividade entre instalações entre o local e o Azure. Além disso, há o fato de que as VMs no Azure são parte do Active Directory/OpenLDAP local.
>
>

Alguma documentação da Microsoft descreve cenários entre instalações de modo um pouco diferente, especialmente para configurações de HA do DBMS. No caso de documentos relacionados a SAP, o cenário entre locais se resume a ter uma conectividade de site a site ou privada (Rota Expressa) e o fato de que a estrutura da SAP é distribuída entre os locais e o Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
Os seguintes guias adicionais estão disponíveis para o tópico das implantações do SAP no Azure:

* [SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de planejamento e implementação (este documento)][planning-guide]
* [SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de implantação][deployment-guide]
* [SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de implantação do DBMS][dbms-guide]
* [SAP NetWeaver em VMs (Máquinas Virtuais) do Azure – Guia de implantação de alta disponibilidade][ha-guide]

> [!IMPORTANT]
> Sempre que possível, um link para o Guia de Instalação do SAP de referência é usado (Referência InstGuide-01, consulte <http://service.sap.com/instguides>). Quando se trata dos pré-requisitos e do processo de instalação, os guias de instalação da SAP NetWeaver devem ser lidos sempre com cuidado, pois este documento só abrange tarefas específicas para sistemas SAP NetWeaver instalados em uma máquina virtual do Microsoft Azure.
>
>

As seguintes Notas do SAP estão relacionadas com o tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: dimensionamento e produtos com suporte |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento avançado |
| [2191498] |SAP no Linux com o Azure: monitoramento avançado |
| [2243692] |Linux na VM do Microsoft Azure (IaaS): problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: instalação e atualização |

Leia também a [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , que contém todas as Notas SAP para Linux.

As limitações gerais padrão e as limitações máximas das assinaturas do Azure podem ser encontradas [neste artigo][azure-subscription-service-limits-subscription]

## <a name="possible-scenarios"></a>Cenários possíveis
SAP é normalmente visto como um dos aplicativos mais essenciais nas empresas. A arquitetura e as operações desses aplicativos geralmente são muito complexas, e é importante que você atenda aos requisitos de disponibilidade e desempenho.

Portanto, as empresas precisam analisar cuidadosamente quais aplicativos podem ser executados em um ambiente de nuvem pública, independentemente do provedor de nuvem escolhido.

Os tipos de sistema possíveis para implantação da SAP NetWeaver com base em aplicativos em ambientes de nuvem pública estão listados abaixo:

1. Sistemas de produção de médio porte
2. Sistemas de desenvolvimento
3. Sistemas de teste
4. Protótipos de sistema
5. Sistemas de demonstração/aprendizado

Para implantar sistemas SAP no Azure IaaS ou IaaS em geral, é importante entender as diferenças significativas entre as ofertas de terceirizados tradicionais ou hosters e as ofertas de IaaS. Enquanto o terceirizado ou hoster tradicional adaptará a infraestrutura (tipo de servidor, rede e armazenamento) para a carga de trabalho que um cliente deseja hospedar, é responsabilidade do cliente, em vez disso, escolher a carga de trabalho certa para implantações de IaaS.

Como uma primeira etapa, os clientes precisam verificar os seguintes itens:

* Os tipos de VM do Azure aos quais a SAP dá suporte
* Os produtos/versões no Azure aos quais a SAP dá suporte
* A versões de SO e DBMS com suporte para as versões SAP específicas no Azure
* Taxa de transferência de SAPS fornecida por diferentes SKUs do Azure

As respostas a essas perguntas podem ser lidas na Nota do SAP [1928533].

Como uma segunda etapa, as limitações de largura de banda e recursos do Azure precisam ser comparadas ao consumo de recursos real de sistemas locais. Portanto, os clientes precisam estar familiarizados com os diferentes recursos dos tipos do Azure aos quais a SAP dá suporte, na área de:

* Recursos de CPU e memória de diferentes tipos de VM e
* Largura de banda IOPS de diferentes tipos de VM e
* Recursos de rede de diferentes tipos de VM.

A maioria desses dados pode ser encontrada [aqui][virtual-machines-sizes]

Tenha em mente que os limites listados no link acima são os limites superiores. Isso não significa que os limites para qualquer um dos recursos, por exemplo, IOPS, possam ser fornecidos em todas as circunstâncias. As exceções, no entanto, são os recursos de CPU e memória de um tipo de VM escolhido. Para os tipos de VM aos quais a SAP dá suporte, os recursos de CPU e memória são reservados e, como tal, ficam disponíveis em qualquer ponto no tempo para consumo dentro da VM.

A plataforma Microsoft Azure, assim como outras plataformas de IaaS, é uma plataforma multilocatário. Isso significa que os recursos de armazenamento, rede e outros são compartilhados entre locatários. Lógica de cotas e limitação inteligente é usada para evitar que um locatário afete o desempenho de outro locatário (vizinho barulhento) de forma drástica. Embora a lógica do Azure tente manter as variações de largura de banda experimentadas pequenas, plataformas altamente compartilhadas tendem a apresentar variações maiores na disponibilidade de recurso/largura de banda do que muitos clientes são habituados a encontrar em suas implantações locais. Como resultado, você pode experimentar diferentes níveis de largura de banda em relação à E/S de armazenamento ou rede (o volume e também a latência) de minuto a minuto. A probabilidade de que um sistema SAP no Azure possa sofrer variações maiores do que aquelas em um sistema local precisa ser levada em conta.

Uma última etapa é avaliar os requisitos de disponibilidade. Pode ocorrer que a infraestrutura subjacente do Azure precise ser atualizada e exija que os hosts que executam as VMs sejam reinicializados. Nesses casos, as VMs em execução nesses hosts seriam desligadas e reiniciadas também. O tempo dessa manutenção, é feito durante o horário comercial não de núcleo para uma região específica, mas a janela potencial de algumas horas durante as quais uma reinicialização ocorrerá é relativamente grande. Existem diversas tecnologias na plataforma Azure que podem ser configuradas para atenuar parcial ou totalmente o impacto dessas atualizações. Melhorias futuras da plataforma Azure, aplicativo SAP e DBMS são projetados para minimizar o impacto dessas reinicializações.

Para implantar com êxito um sistema SAP no Azure, o sistema operacional, o banco de dados e os aplicativos SAP do(s) sistema(s) SAP local(is) devem aparecer na matriz de suporte do Azure da SAP, enquadrar-se nos recursos que a infraestrutura do Azure pode fornecer e que podem funcionar com os SLAs de disponibilidade que o Microsoft Azure oferece. Conforme esses sistemas são identificados, você precisa escolher um dos seguintes dois cenários de implantação.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Somente em nuvem - Implantações de máquinas virtuais no Azure sem dependências na rede do cliente local
![VM única com cenário de demonstração ou treinamento SAP implantado no Azure][planning-guide-figure-100]

Esse cenário é típico de treinamentos ou sistemas de demonstração, nos quais todos os componentes de software SAP e não SAP são instalados em uma única VM. Nesse cenário de implantação, não há suporte para sistemas SAP de produção. Em geral, esse cenário atende aos seguintes requisitos:

* As VMs em si são acessíveis pela rede pública. Não é necessária conectividade de rede direta dos aplicativos em execução nas VMs à rede local do cliente ou da empresa proprietária do conteúdo de demonstrações ou treinamentos.
* No caso de várias VMs que representam o cenário de demonstrações ou treinamentos, a resolução de nomes e as comunicações de rede precisam funcionar entre as máquinas virtuais. Mas as comunicações entre o conjunto de VMs precisam ser isoladas para que vários conjuntos de máquinas virtuais possam ser implantados lado a lado, sem interferência.  
* É necessária conectividade com a Internet para que o usuário final faça logon remoto às VMs hospedadas no Azure. Dependendo do SO convidado, Serviços de Terminal/RDS ou VNC/ssh é usado para acessar a VM para cumprir as tarefas de treinamento ou executar as demonstrações. Se portas SAP como 3200, 3300 e 3600 também puderem ser expostas, a instância do aplicativo SAP poderá ser acessada de qualquer computador desktop conectado à Internet.
* O(s) sistema(s) (e VM(s)) SAP representam um cenário independente no Azure, que só exige conectividade com a Internet pública para acesso de usuário final e não requer uma conexão com outras VMs no Azure.
* O SAPGUI e um navegador são instalados e executados diretamente na VM.
* São necessárias uma reinicialização rápida de uma VM ao estado original e uma nova implantação desse estado original.
* No caso de cenários de demonstração e treinamento que são realizados em várias VMs, um Active Directory/OpenLDAP e/ou serviço DNS é obrigatório para cada conjunto de VMs.

![Grupo de VMs que representam um cenário de demonstração ou treinamento em um serviço de nuvem do Azure][planning-guide-figure-200]

É importante ter em mente que a(s) VM(s) em cada um dos conjuntos precisa(m) ser implantada(s) em paralelo, modo em que os nomes das VMs em cada conjunto são os mesmos.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Entre instalações - Implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local
![VPN com conectividade site a site (entre instalações)][planning-guide-figure-300]

Esse é um cenário entre instalações com muitos padrões de implantação possíveis. Ele pode ser descrito simplesmente como a execução de algumas partes da estrutura da SAP localmente e outras partes da estrutura da SAP no Azure. Todos os aspectos do fato de que parte dos componentes SAP estão em execução no Azure devem ser transparentes para os usuários finais. Portanto, o STMS (sistema de correção de transporte SAP), comunicação RFC, impressão, segurança (como SSO), etc. funcionarão perfeitamente para os sistemas SAP em execução no Azure. Mas o cenário entre instalações também descreve um cenário em que a estrutura da SAP completa é executada no Azure com o domínio do cliente e DNS estendidos no Azure.

> [!NOTE]
> Esse é o cenário de implantação com suporte para a execução de sistemas SAP de produção.
>
>

Leia [este artigo][vpn-gateway-create-site-to-site-rm-powershell] para obter mais informações sobre como conectar sua rede local ao Microsoft Azure

> [!IMPORTANT]
> Quando falamos sobre cenários entre instalações entre implantações de clientes no Azure e localmente, estamos observando a granularidade de sistemas SAP inteiros. Cenários que *não têm suporte* para cenários entre instalações são:
>
> * Executar diferentes camadas de aplicativos SAP em diferentes métodos de implantação. Por exemplo executando a camada do DBMS no local, exceto a camada do aplicativo SAP nas VMs implantadas como VMs do Azure ou vice-versa.
> * Alguns componentes de uma camada SAP no Azure e alguns localmente. Por exemplo dividir as Instâncias da camada do aplicativo SAP entre as VMs locais e do Azure.
> * Não há suporte para a distribuição de VMs que executam instâncias SAP de um sistema por várias Regiões do Azure.
>
> O motivo dessas restrições é o requisito de uma rede de alto desempenho com latência muito baixa em um sistema SAP, especialmente entre as instâncias do aplicativo e a camada DBMS de um sistema SAP.
>
>

### <a name="supported-os-and-database-releases"></a>Versões de banco de dados e SO com suporte
* O software do servidor da Microsoft com suporte para os Serviços da Máquina Virtual do Azure está listado neste artigo: <http://support.microsoft.com/kb/2721672>.
* As versões do sistema de operacional e versões do sistema de banco de dados para as quais há suporte pelos Serviços da Máquina Virtual do Azure em conjunto com o software SAP estão documentadas na Nota do SAP [1928533].
* As versões e aplicativos do SAP para os quais há suporte nos Serviços da Máquina Virtual do Azure estão documentadas na Nota do SAP [1928533].
* Imagens de 64 bits são as únicas para as quais há suporte para execução como VMs convidadas no Azure para cenários SAP. Isso também significa que apenas aplicativos e bancos de dados SAP de 64 bits têm suporte.

## <a name="microsoft-azure-virtual-machine-services"></a>Serviços da Máquina Virtual do Microsoft Azure
A plataforma Microsoft Azure é uma plataforma de serviços de nuvem na Internet hospedada em data centers da Microsoft. A plataforma inclui os Serviços da Máquina Virtual do Microsoft Azure (IaaS, ou infraestrutura como serviço) e um conjunto de recursos avançados de PaaS (plataforma como serviço).

A plataforma Microsoft Azure reduz a necessidade de compras antecipadas de tecnologia e infraestrutura. Isso simplifica a manutenção e operação de aplicativos, fornecendo computação sob demanda e armazenamento para hospedar, dimensionar e gerenciar aplicativos Web e aplicativos conectados. O gerenciamento de infraestrutura é automatizado com uma plataforma projetada para alta disponibilidade e dimensionamento dinâmico, para atender às necessidades de uso com a opção de um modelo de preços pré-pago.

![Posicionamento de Serviços da Máquina Virtual do Microsoft Azure][planning-guide-figure-400]

Com os Serviços da Máquina Virtual do Azure, a Microsoft permite implantar imagens de servidor personalizadas no Azure como instâncias de IaaS (veja a Figura 4). As máquinas virtuais no Azure são baseadas em VHDs (discos rígidos virtuais) do Hyper-V e podem executar diferentes sistemas operacionais como SO convidado.

Do ponto de vista operacional, o Serviço da Máquina Virtual do Azure oferece experiências semelhantes a máquinas virtuais implantadas localmente. No entanto, ele tem a vantagem significativa de que você não precisa comprar, administrar e gerenciar a infraestrutura. Desenvolvedores e administradores têm controle total da imagem do sistema operacional nessas máquinas virtuais. Os administradores podem fazer logon remotamente nessas máquinas virtuais para realizar tarefas de manutenção e solução de problemas, bem como tarefas de implantação de software. Em termos de implantação, as únicas restrições são os tamanhos e as recursos das VMs do Azure. Elas podem não ter uma configuração tão refinada, já que isso pode ser feito localmente. Há uma variedade de tipos VM que representam uma combinação de:

* Número de vCPUs,
* Memória,
* Número de VHDs que podem ser anexados,
* Larguras de banda de rede e de armazenamento.

O tamanho e as limitações dos vários tamanhos diferentes de máquinas virtuais oferecidos podem ser vistos em uma tabela [neste artigo][virtual-machines-sizes]

Conforme você perceberá, existem diferentes famílias ou séries de máquinas virtuais. Desde dezembro de 2015, você pode distinguir as seguintes famílias de VMs:

* Tipos de VM A0-A7: nem todas elas são certificadas para SAP. Primeira série de VMs com a qual o IaaS do Azure foi introduzido.
* Tipos de VM A8-A11: instâncias de computação de alto desempenho. Em execução em hosts de computação diferentes e com melhor desempenho que outras VMs da série A.
* Tipos de VM série D: desempenho superior a A0-A7. Nem todos os tipos de VM são certificados com SAP.
* Tipos de VM da série DS: usam os mesmos hosts da série D, mas são capazes de conectar o Armazenamento Premium do Azure (confira o capítulo [Armazenamento Premium do Azure][planning-guide-3.3.2] deste documento). Outra vez, nem todos os tipos de VM são certificados com SAP.
* Tipos de VM da série G: tipos de VM de memória alta.
* Tipos de VM da Série GS: similares à Série G, mas incluem a opção de usar o Armazenamento Premium do Azure (confira o capítulo [Armazenamento Premium do Azure][planning-guide-3.3.2] deste documento). Ao usar as VMs da série GS como servidores de banco de dados, é obrigatório usar o Armazenamento Premium para arquivos de log de transações e dados do BD

Você pode encontrar as configurações de memória e CPU mesmo em outra série de VMs. No entanto, quando você verificar o desempenho de taxa de transferência dessas VMs, eles podem diferir significativamente entre séries diferentes. Isso ocorre apesar de terem a mesma configuração de CPU e memória. O motivo é que o hardware do servidor de host subjacente, na introdução dos diferentes tipos de VM, tinha características de taxa de transferência diferentes.  Normalmente, a diferença mostrada no desempenho de taxa de transferência também está incluído no preço das diferentes VMs.

Observe que nem todas as séries de VMs diferentes podem ser oferecidas em cada uma das Regiões do Azure (para Regiões do Azure, consulte o capítulo seguinte). Além disso, lembre-se de que nem todas as VMs ou séries de VMs são certificadas para SAP.

> [!IMPORTANT]
> Para o uso de aplicativos baseados no SAP NetWeaver, somente o subconjunto de tipos de VM e as configurações listadas na Nota do SAP [1928533] têm suporte.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões do Azure
A Microsoft permite implantar máquinas virtuais nas chamadas “Regiões do Azure”. Uma Região do Azure pode ser um ou vários data centers cuja localização é muito próxima. Para a maioria das regiões geopolíticas no mundo, a Microsoft tem pelo menos duas Regiões do Azure. Por exemplo na Europa, há uma Região do Azure da ‘Europa Setentrional’ e uma da ‘Europa Ocidental’. Essas duas Regiões do Azure, em uma mesma região geopolítica, são separadas pela distância suficientemente significativa para que desastres técnicos ou naturais não afetem ambas as Regiões do Azure na mesma região geopolítica. Como a Microsoft está constantemente desenvolvendo novas Regiões do Azure em diferentes regiões geopolíticas globalmente, o número dessas regiões está crescendo e, em dezembro de 2015, atingiu o número de 20 Regiões do Azure com Regiões adicionais já anunciadas. Você, como um cliente, pode implantar sistemas SAP em todas essas regiões, incluindo as duas Regiões do Azure na China. Para obter informações atualizadas sobre as regiões do Azure, confira este site: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>O conceito de máquina virtual do Microsoft Azure
O Microsoft Azure oferece uma solução de IaaS (infraestrutura como serviço) para hospedar máquinas virtuais com funcionalidades semelhantes às de uma solução de virtualização local. É possível criar máquinas virtuais no Portal do Azure, PowerShell ou CLI, que também oferecem recursos de gerenciamento e implantação.

O Gerenciador de Recursos do Azure permite que você provisione seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Mais informações sobre o uso de modelos ARM podem ser encontradas aqui:

* [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure][virtual-machines-linux-cli-deploy-templates]
* [Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Outro recurso interessante é a capacidade de criar imagens de máquinas virtuais, que permite preparar determinados repositórios do qual você pode implantar rapidamente instâncias de máquina virtual que atendam às suas necessidades.

Mais informações sobre a criação de imagens de Máquinas Virtuais podem ser encontradas [neste artigo (Windows)][virtual-machines-windows-capture-image] ou [neste artigo (Linux)][virtual-machines-linux-capture-image].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios com falhas
Domínios de falha representam uma unidade física de falha, relacionada muito intimamente à infraestrutura física contida nos data centers e, embora uma folha ou rack físico possa ser considerado um domínio de falha, não há nenhum mapeamento direto um-para-um entre os dois.

Quando você implanta várias máquinas virtuais como parte de um sistema SAP nos Serviços da Máquina Virtual do Microsoft Azure, você pode influenciar o controlador de malha do Azure para implantar seu aplicativo em diferentes domínios de falha, atendendo assim aos requisitos do SLA do Microsoft Azure. No entanto, a distribuição dos domínios de falha em uma unidade de escala do Azure (coleção de centenas de nós de computação ou nós de armazenamento e rede) ou a atribuição de VMs a um domínio de falha específico é algo sobre o que você não tem controle direto. Para comandar o controlador de malha do Azure a implantar um conjunto de VMs em diferentes domínios de falha, é necessário atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implantação. Para obter mais informações sobre os Conjuntos de Disponibilidade do Azure, confira o capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3] neste documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domínios de Atualização
Domínios de Atualização representam uma unidade lógica que ajudam a determinar como uma máquina virtual em um sistema SAP, que consiste em instâncias SAP em execução em várias VMs, será atualizada. Quando ocorre uma atualização, o Microsoft Azure percorre o processo de atualizar esses domínios de atualização individualmente. Ao distribuir as VMs no momento da implantação em diferentes domínios de atualização, você pode proteger seu sistema SAP parcialmente contra um potencial tempo de inatividade. Para forçar o Azure para implantar as VMs de um sistema SAP espalhados em diferentes domínios de atualização, você precisa definir um atributo específico no momento da implantação de cada VM. Semelhante aos domínios de falha, uma unidade de escala do Azure é dividida em vários domínios de atualização. Para comandar o controlador de malha do Azure a implantar um conjunto de VMs em diferentes domínios de atualização, é necessário atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implantação. Para obter mais informações sobre os Conjuntos de Disponibilidade do Azure, confira o capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3] abaixo.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de Disponibilidade do Azure
Máquinas Virtuais do Azure dentro de um conjunto de disponibilidade do Azure serão distribuídas pelo controlador de malha do Azure em diferentes domínios de falha e de atualização. O objetivo da distribuição por diferentes domínios de falha e de atualização é impedir que todas as VMs de um sistema SAP sejam desligadas em caso de manutenção da infraestrutura ou de uma falha em um domínio de falha. Por padrão, VMs não fazem parte de um conjunto de disponibilidade. A participação de uma VM em um conjunto de disponibilidade é definida no momento da implantação ou posteriormente, por meio da reconfiguração ou reimplantação de uma VM.

Para entender o conceito dos Conjuntos de Disponibilidade do Azure e a maneira como eles se relacionam com os Domínios de Atualização e de Falha, leia [este artigo][virtual-machines-manage-availability]

Para definir conjuntos de disponibilidade para ARM por meio de um modelo json, confira [as especificações da API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e pesquise por "disponibilidade".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: armazenamento do Microsoft Azure e discos de dados
Máquinas Virtuais do Microsoft Azure utilizam diferentes tipos de armazenamento. Ao implementar aplicativos SAP nos Serviços da Máquina Virtual do Azure, é importante entender as diferenças entre esses dois tipos principais de armazenamento:

* Armazenamento volátil não persistente.
* Armazenamento persistente.

O armazenamento não persistente é conectado diretamente às máquinas virtuais em execução e reside nos nós de computação em si – o armazenamento de instância local (armazenamento temporário). O tamanho depende do tamanho da máquina virtual escolhida quando a implantação foi iniciada. Esse tipo de armazenamento é volátil e, portanto, o disco é inicializado quando uma instância de máquina virtual é reiniciada. Normalmente, o arquivo de paginação do sistema operacional está localizado nesse disco temporário.

- - -
> ![Windows][Logo_Windows] Windows
>
> Em VMs do Windows, a unidade temporária está montada como a unidade D:\ em uma VM implantada.
>
> ![Linux][Logo_Linux] Linux
>
> Em VMs Linux, ela está montada como /mnt/resource ou /mnt. Veja mais detalhes aqui:
>
> * [Como anexar um disco de dados na máquina virtual Linux][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx>
>
>

- - -
A unidade real é volátil, pois está sendo armazenada no próprio servidor de host. Se a VM for movida em uma reimplantação (por exemplo, devido a manutenção no host ou a desligamento e reinicialização), o conteúdo da unidade será perdido. Portanto, armazenar dados importantes nessa unidade não é uma opção. O tipo de mídia usado para esse tipo de armazenamento difere entre diferentes séries de VMs com características de desempenho muito diferentes que, a partir de junho de 2015, têm a seguinte aparência:

* A5-A7: desempenho muito limitado. Não recomendado para nada além do arquivo de paginação
* A8-A11: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a&1; GB/s.
* Série D: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a&1; GB/s.
* Série DS: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a&1; GB/s.
* Série G: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a&1; GB/s.
* Série GS: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a&1; GB/s.

As instruções acima estão sendo aplicadas aos tipos de VM que são certificados com a SAP. A série de VMs com IOPS e taxa de transferência excelentes se qualifica para aproveitar alguns recursos do DBMS. Confira o [Guia de Implantação do DBMS][dbms-guide] para obter mais detalhes.

O Armazenamento do Microsoft Azure fornece armazenamento persistente e os níveis típicos de proteção e redundância encontrados em armazenamento SAN. Discos baseados no Armazenamento do Azure são VHDs (discos rígidos virtuais) localizados nos Serviços de Armazenamento do Azure. O disco do SO local (Windows C:\, Linux / ( /dev/sda1 )) é colocado no Armazenamento do Azure e os Volumes/Discos adicionais são montados na VM armazenada nele também.

É possível carregar um VHD do local existente ou criar outros vazios do Azure e anexá-los às VMs implantadas. Esses VHDs são referenciados como Discos do Azure.

Depois de criar ou carregar um VHD no Armazenamento do Azure, é possível montá-lo e anexá-lo a uma Máquina Virtual existente e copiar o VHD (desmontado) existente.

Como esses VHDs são persistentes, os dados e alterações presentes em tais VHDs são preservados ao reinicializar e recriar uma instância de Máquina Virtual. Mesmo que uma instância seja excluída, esses VHDs permanecem seguros e podem ser reimplantados ou, em caso de discos que não são de SO, podem ser montados em outras VMs.

Dentro da rede do Armazenamento do Azure, níveis de redundância diferentes podem ser configurados:

* O nível mínimo que pode ser selecionado é ‘redundância local’, que é equivalente a três réplicas de dados dentro do mesmo data center de uma Região do Azure (confira o capítulo [Regiões do Azure][planning-guide-3.1]).
* Armazenamento com redundância de zona que difundirá as três imagens por data centers diferentes na mesma Região do Azure.
* O nível de redundância padrão é a redundância geográfica que replica o conteúdo de modo assíncrono em outras 3 imagens dos dados em outra Região do Azure que, por sua vez, está hospedada na mesma região geopolítica.

Também consulte a tabela na parte superior deste artigo no que diz respeito às opções de redundância diferentes: <https://azure.microsoft.com/pricing/details/storage/>

Mais informações em relação ao armazenamento do Azure podem ser encontradas aqui:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Armazenamento Standard do Azure
O armazenamento de BLOBs Standard do Azure era o tipo de armazenamento disponível quando o IaaS do Azure foi lançado. Havia cotas IOPS impostas para cada VHD. A latência experimentada não era na mesma classe que a encontrada nos dispositivos SAN/NAS normalmente implantados para sistemas SAP high-end hospedados localmente. No entanto, o Armazenamento Standard do Azure provou ser suficiente para centenas de sistemas SAP que eram, enquanto isso, implantados no Azure.

O Armazenamento Standard do Azure é cobrado com base nos dados que são realmente armazenados, no volume de transações de armazenamento, nas transferências de dados de saída e na opção de redundância escolhidos. Muitos VHDs podem ser criados com no máximo 1 TB de tamanho, no entanto, desde que eles permaneçam vazios, não haverá cobrança. Se você, em seguida, preencher um VHD com 100 GB, você será cobrado pelo armazenamento de 100 GB e não pelo tamanho nominal com o qual o VHD foi criado.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento Premium do Azure
Em abril de 2015, a Microsoft introduziu o Armazenamento Premium do Azure. O Armazenamento Premium foi introduzido com a meta de fornecer:

* Melhor latência de E/S.
* Melhor taxa de transferência.
* Menos variação na latência de E/S.

Muitas alterações foram introduzidas para essa finalidade, e as duas mais importantes delas são:

* O uso de discos SSD em nós de Armazenamento do Azure
* Um novo cache de leitura com o suporte do SSD local de um nó de computação do Azure

Ao contrário do armazenamento Standard, no qual não havia mudança nos recursos dependendo do tamanho do disco (ou do VHD), o Armazenamento Premium atualmente tem três categorias diferentes de disco que são mostradas no final deste artigo antes da seção de Perguntas Frequentes: <https://azure.microsoft.com/pricing/details/storage/>

Você vê que IOPS/VHD e taxa de transferência de disco/VHD dependem da categoria de tamanho dos discos

A base de custo no caso do Armazenamento Premium não é o volume real de dados armazenados nesses VHDs, mas a categoria de tamanho de um desses VHDs, independentemente da quantidade de dados armazenados no VHD em questão.

Você também pode criar VHDs no Armazenamento Premium que não estão sendo mapeados diretamente para as categorias de tamanho mostradas. Esse pode ser o caso, especialmente ao copiar VHDs do Armazenamento Standard para o Armazenamento Premium. Nesses casos, um mapeamento para a próxima maior opção de disco de Armazenamento Premium é executado.

Esteja ciente de que somente certas séries de VMs podem se beneficiar do Armazenamento Premium do Azure. Desde dezembro de 2015, essas são as séries GS e DS. A série DS é basicamente o mesmo que a série D, exceto pelo fato de que a série DS tem a capacidade montar VMs baseadas no Armazenamento Premium, além de VHDs que são hospedados no Armazenamento Standard do Azure. O mesmo é válido para a série G em comparação com a série GS.

Se estiver verificando a parte das VMs da série DS [neste artigo][virtual-machines-sizes], você perceberá também que há limitações de volume de dados para VHDs do Armazenamento Premium na granularidade do nível da VM. VMs diferentes da série DS ou série GS também têm diferentes limitações em relação ao número de VHDs que podem ser montados. Esses limites também são documentados no artigo mencionado acima. Mas, essencialmente, isso significa que, se você montar 32 VHDs/discos P30 para uma única VM DS14, por exemplo, você NÃO poderá obter 32 vezes a taxa de transferência máxima de um disco P30. Em vez disso, a taxa de transferência máxima no nível da VM, conforme documentada no artigo, limitará a taxa de transferência de dados.

Mais informações sobre o Armazenamento Premium podem ser encontradas aqui: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure
Ao implantar serviços ou VMs no Azure, a implantação de VHDs e Imagens de VM deve ser organizada em unidades chamadas Contas de Armazenamento do Azure. Ao planejar uma implantação do Azure, você precisa considerar cuidadosamente as restrições do Azure. De um lado, há um número limitado de Contas de Armazenamento por assinatura do Azure. Embora cada Conta de Armazenamento do Azure possa conter um grande número de arquivos VHD, há um limite fixo para o total de IOPS por Conta de Armazenamento. Ao implantar centenas de VMs SAP com sistemas DBMS criando chamadas de E/S significativas, é recomendado distribuir as VMs de DBMS com IOPS elevado entre várias Contas de Armazenamento do Azure. Deve-se ter cuidado para não exceder o limite atual de Contas de Armazenamento do Azure por assinatura. Como o armazenamento é uma parte essencial da implantação do banco de dados para um sistema SAP, esse conceito será abordado com mais detalhes no já mencionado [Guia de Implantação do DBMS][dbms-guide].

Mais informações sobre as Contas de Armazenamento do Azure podem ser encontradas [neste artigo][storage-scalability-targets]. Ao ler este artigo, você descobrirá que há diferenças entre as limitações encontradas nas Contas de Armazenamento Standard do Azure e aquelas encontradas nas Contas de Armazenamento Premium. As principais diferenças referem-se ao volume de dados que pode ser armazenado em uma dessas Contas de Armazenamento. No Armazenamento Standard, o volume é uma magnitude maior do que com o Armazenamento Premium. Por outro lado, a Conta de Armazenamento Standard é bastante limitada em IOPS (consulte a coluna “Taxa de Solicitação Total”), enquanto a Conta de Armazenamento Premium do Azure não tem nenhuma limitação desse tipo. Discutiremos detalhes e os resultados dessas diferenças ao discutir as implantações de sistemas SAP, especialmente os servidores DBMS.

Em uma Conta de Armazenamento, você tem a possibilidade de criar diferentes contêineres para organizar e categorizar diferentes VHDs. Esses contêineres são geralmente usados para, por exemplo, separar os VHDs de VMs diferentes. Não há nenhuma implicação de desempenho em usar apenas um contêiner ou vários contêineres sob uma única Conta de Armazenamento do Azure.

Dentro do Azure, um nome de VHD segue a seguinte conexão de nomenclatura, que precisa fornecer um nome exclusivo para o VHD contido no Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Conforme mencionado, a cadeia de caracteres acima deve identificar exclusivamente o VHD que está armazenado no Armazenamento do Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rede do Microsoft Azure
O Microsoft Azure fornece uma infraestrutura de rede que permite o mapeamento de todos os cenários que desejamos criar com software SAP. Os recursos são:

* Acesso externo diretamente às VMs via ssh/VNC ou Serviços de Terminal do Windows
* Acesso a serviços e portas específicas usadas por aplicativos nas VMs
* Comunicação interna e resolução de nomes entre um grupo de VMs implantadas como VMs do Azure
* Conectividade entre instalações entre a rede local de um cliente e a rede do Azure
* Conectividade entre Regiões do Azure ou data centers entre sites do Azure

Mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Há muitas possibilidades diferentes para configurar a resolução de nomes e IPs no Azure. Neste documento, cenários somente em nuvem contam com o padrão de usar o DNS do Azure (em vez de definir um serviço DNS próprio). Também há um novo serviço DNS do Azure que pode ser usado, em vez de configurar seu próprio servidor DNS. Encontre mais informações [neste artigo][virtual-networks-manage-dns-in-vnet] e [nesta página](https://azure.microsoft.com/services/dns/).

Para cenários entre instalações, contamos com o fato de que o OpenLDAP/AD/DNS local foi estendido por meio de VPN ou conexão privada ao Azure. Para determinados cenários, conforme documentado aqui, é necessário ter uma réplica do AD/OpenLDAP instalada no Azure.

Como a resolução de rede e nomes é uma parte essencial da implantação do banco de dados para um sistema SAP, esse conceito será abordado com mais detalhes no [Guia de Implantação do DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure
Ao criar uma Rede Virtual do Azure, você pode definir o intervalo de endereços dos endereços IP privados alocados pela funcionalidade DHCP do Azure. Em cenários entre instalações, o intervalo de endereços IP definido ainda será alocado pelo Azure, usando DHCP. No entanto, a resolução de nomes de domínio será feita localmente (supondo que as VMs são uma parte de um domínio local) e, portanto, pode resolver endereços além dos diferentes Serviços de Nuvem do Azure.

[comment]: <> (O MSSedusch ainda é necessário? TODO Originalmente, uma rede virtual do Azure era associada a um grupo de afinidades. Com isso, uma rede virtual no Azure foi restrita à unidade de escala do Azure à qual o grupo de afinidades foi atribuído. No final das contas, isso significa que a Rede Virtual foi restrita aos recursos disponíveis na unidade de escala do Azure. Isso mudou desde então, e agora as redes virtuais do Azure podem ampliar-se por mais de uma unidade de escala do Azure. No entanto, isso exige que as Redes Virtuais do Azure **NÃO** estejam mais associadas aos Grupos de Afinidade no momento da criação. Já mencionamos anteriormente que ao contrário do que era recomendado há um ano, você **NÃO deve mais aproveitar os Grupos de Afinidade do Azure**. Para obter detalhes, confira <https://azure.microsoft.com/blog/regional-virtual-networks/>)

Cada máquina Virtual no Azure precisa ser conectado a uma Rede Virtual.

Mais detalhes podem ser encontrados [neste artigo][resource-groups-networking] e [nesta página](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO: não foi possível encontrar um artigo que inclua o tópico OpenLDAP + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Por padrão, quando uma VM é implantada, não é possível alterar a configuração de rede virtual. As configurações de TCP/IP devem ser deixadas para o servidor DHCP do Azure. O comportamento padrão é a atribuição de IP dinâmico.
>
>

O endereço MAC da placa de rede virtual pode mudar, por exemplo, depois de redimensionar, e o SO convidado Windows ou Linux selecionará a nova placa de rede e usará automaticamente o DHCP para atribuir os endereços IP e DNS nesse caso.

##### <a name="static-ip-assignment"></a>Atribuição de endereço IP estático
É possível atribuir endereços IP reservados ou fixos às VMs em uma Rede Virtual do Azure. Executar as VMs em uma Rede Virtual do Azure oferece uma ótima possibilidade de aproveitar essa funcionalidade se necessário ou quando exigido por alguns cenários. A atribuição de IPS permanece válida durante toda a existência da VM, independentemente de a VM estar em execução ou desligada. Como resultado, você precisa levar o número total de VMs (VMS em execução e paradas) em consideração ao definir o intervalo de endereços IP para a rede virtual. O endereço IP permanecerá atribuído até que a VM seja excluída juntamente com sua interface de rede ou até que o endereço IP seja desatribuído novamente. Veja informações detalhadas [neste artigo][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Várias NICs por VM
Você pode definir várias vNICs (placas de interface de rede virtual) para uma Máquina Virtual do Azure. Com a capacidade de ter várias vNICs, você pode começar a configurar o tráfego de rede no qual, por exemplo, o tráfego do cliente é roteado por meio de uma vNIC e o tráfego de back-end é roteado por meio de uma segunda vNIC. Dependendo do tipo de VM, há diferentes limitações quanto ao número de vNICs. Restrições, funcionalidades e detalhes exatos podem ser encontradas nesses artigos:

* [Criar uma VM com diversas NICs][virtual-networks-multiple-nics]
* [Implantar VMs com diversas NICs usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implantar VMs com diversas NICs usando o PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implantar VMs com diversas NICs usando a CLI do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade site a site
entre instalações refere-se a VMs do Azure e locais vinculadas por uma conexão VPN permanente e transparente. Espera-se que esse se torne o padrão de implantação SAP mais comum no Azure. Pressupõe-se que os procedimentos operacionais e processos com instâncias SAP no Azure funcionem de modo transparente. Isso significa que você deve ser capaz de imprimir partindo desses sistemas, bem como usar o TMS (sistema de gerenciamento de transporte) SAP para transportar alterações de um sistema de desenvolvimento no Azure para um sistema de teste que é implantado localmente. Encontre mais documentações relacionadas ao site a site [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN
Para criar uma conexão site a site (data center local para data center do Azure), você precisará obter e configurar um dispositivo VPN ou usar o RRAS (Serviço de Roteamento e Acesso Remoto), que foi introduzido como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma conexão VPN site a site usando PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Sobre dispositivos VPN para conexões de Gateway de VPN Site a Site][vpn-gateway-about-vpn-devices]
* [Perguntas frequentes sobre o Gateway de VPN][vpn-gateway-vpn-faq]

![Conexão site a site entre locais e o Azure][planning-guide-figure-600]

A Figura acima mostra que duas assinaturas do Azure têm subintervalos de endereços IP reservados para uso em redes virtuais no Azure. A conectividade da rede local para o Azure é estabelecida via VPN.

#### <a name="point-to-site-vpn"></a>VPN ponto a site
VPN ponto a site requer que cada computador cliente conecte-se com seu próprio VPN ao Azure. Para os cenários SAP que estamos vendo, a conectividade ponto a site não é prática. Portanto, não será feita nenhuma referência posterior à conectividade VPN ponto a site.

[comment]: <> (MSSedusch – mais informações podem ser encontradas aqui)
[comment]: <> (MShermannd TODO: o link não é mais válido, mas, mesmo assim, o ARM não tem suporte – veja o próximo link abaixo)
[comment]: <> (MSSedusch – <http://msdn.microsoft.com/library/azure/dn133798.aspx>.)
[comment]: <> (MShermannd TODO: ainda não há suporte para Ponto a Site com o ARM )
[comment]: <> (MSSedusch – <https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multi-site-vpn"></a>VPN de múltiplos sites
Hoje em dia, o Azure também oferece a possibilidade de criar a conectividade VPN de múltiplos sites para uma assinatura do Azure. Antes, uma única assinatura era limitada a uma ligação VPN site a site. Essa limitação desapareceu com ligações VPN de múltiplos sites para uma única assinatura. Isso torna possível aproveitar mais de uma Região do Azure para uma assinatura específica por meio de configurações entre instalações.

Para obter mais documentação, veja [este artigo][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO: nenhum link de documentação do ARM encontrado)

#### <a name="vnet-to-vnet-connection"></a>Conexão VNet a VNet
Usando VPN de múltiplos sites, você precisa configurar uma Rede Virtual do Azure separada em cada uma das regiões. No entanto, com frequência, você tem o requisito de que os componentes de software em diferentes regiões devem comunicar-se uns com os outros. Idealmente, essa comunicação não deve ser roteada de uma Região do Azure para local e daí para outra Região do Azure. Como atalho, o Azure oferece a possibilidade de configurar uma conexão de uma Rede Virtual do Azure em uma região a outra Rede Virtual do Azure hospedada em outra região. Essa funcionalidade é chamada de conexão de VNet a VNet. Mais detalhes sobre essa funcionalidade podem ser encontrados aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure--expressroute"></a>Conexão Privada ao Azure - Rota Expressa
A Rota Expressa do Microsoft Azure permite a criação de conexões privadas entre data centers do Azure e a infraestrutura de local do cliente, ou então em um ambiente de colocalização. A Rota Expressa é oferecida por diversos provedores VPN MPLS (com pacotes comutados) ou outros Provedores de Serviços de Rede. As conexões da Rota Expressa não passam pela Internet pública. As conexões de Rota Expressa oferecem maior segurança, mais confiabilidade por meio de múltiplos circuitos paralelos, velocidades maiores e latências menores do que conexões típicas pela Internet.

Encontre mais detalhes sobre a Rota Expressa do Azure e ofertas aqui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

A Rota Expressa permite várias assinaturas do Azure por meio de um circuito de Rota Expressa, conforme documentado aqui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premise"></a>Túneis forçados em caso de entre instalações
Para VMs ingressando em domínios locais por meio de site a site, ponto a site ou Rota Expressa, você precisa certificar-se de que as configurações de proxy da Internet estejam sendo implantadas para todos os usuários nessas VMs também. Por padrão, o software em execução nas VMs ou usuários usando um navegador para acessar a Internet não passariam pelo proxy da empresa, conectariam-se diretamente à Internet por meio do Azure. Mas mesmo a configuração do proxy não é uma solução perfeita para direcionar o tráfego por meio do proxy da empresa, pois verificar o proxy é responsabilidade do software e dos serviços. Se o software em execução na VM não está fazendo isso ou se um administrador manipular as configurações, o tráfego para a Internet poderá ser novamente desviado diretamente através do Azure para a Internet.

Para evitar isso, você pode configurar o Túnel Forçado com conectividade site a site entre o local e o Azure. A descrição detalhada do recurso de Túnel Forçado é publicada aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

O Túnel Forçado com Rota Expressa está habilitado por clientes que anunciam uma rota padrão por meio de sessões de emparelhamento via protocolo BGP da Rota Expressa.

#### <a name="summary-of-azure-networking"></a>Resumo da Rede do Azure
Este capítulo continha muitos pontos importantes sobre a Rede do Azure. Eis aqui um resumo dos pontos principais:

* As Redes Virtuais do Azure permitem configurar a rede de acordo com suas necessidades
* As Redes Virtuais do Azure podem ser aproveitadas para atribuir intervalos de endereços IP às VMs ou atribuir endereços IP fixos às VMs
* Para configurar uma conexão site a site ou ponto a site, você precisa primeiro criar uma Rede Virtual do Azure
* Após a implantação de uma máquina virtual, não é possível alterar a Rede Virtual atribuída à VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Cotas nos Serviços da Máquina Virtual do Azure
Precisamos ser claros sobre o fato de que a infraestrutura de rede e de armazenamento é compartilhada entre as VMs que executam uma variedade de serviços na infraestrutura do Azure. E assim como nos data centers do próprio cliente, o excesso de provisionamento de alguns recursos de infraestrutura ocorre até um certo grau. A plataforma Microsoft Azure usa cotas de disco, CPU, rede e outras para limitar o consumo de recursos e para manter o desempenho consistente e previsível.  Os diferentes tipos de VM (A5, A6 etc.) têm diferentes cotas para o número de discos, CPU, RAM e rede.

> [!NOTE]
> Recursos de CPU e memória dos tipos de VM aos quais o SAP dá suporte são pré-alocados nos nós de host. Isso significa que, após a VM ser implantada, os recursos no host estarão disponíveis conforme definido pelo tipo de VM.
>
>

Ao planejar e dimensionar soluções SAP no Azure, as cotas para cada tamanho de máquina virtual devem ser consideradas.  As cotas de VM são descritas [aqui][virtual-machines-sizes].

As cotas descritas representam os valores máximos teóricos.  O limite de IOPS por VHD pode ser obtido com o E/Ss pequenas (8 kb), mas é possível que não possa ser obtido com o E/Ss grandes (1 Mb).  O limite de IOPS é aplicado na granularidade de VHDs individuais.

Como uma árvore de decisão grosseira para decidir se um sistema SAP se ajusta aos Serviços da Máquina Virtual do Azure e seus recursos ou se um sistema existente precisa ser configurado de forma diferente para implantar o sistema no Azure, a árvore de decisão abaixo pode ser usada:

![Árvore de decisão para decidir a capacidade de implantar um sistema SAP no Azure][planning-guide-figure-700]

**Etapa 1**: as informações mais importantes para começar são o requisito de SAPS para um determinado sistema do SAP. Os requisitos de SAPS precisam ser divididos entre a parte do DBMS e a parte do aplicativo SAP, mesmo que o sistema SAP já esteja implantado localmente em uma configuração de duas camadas. Para os sistemas existentes, os SAPS relacionados ao hardware em uso geralmente podem ser determinados ou estimados com base nos parâmetros de comparação de SAP existentes. Os resultados podem ser encontrados aqui: <http://global.sap.com/campaigns/benchmark/index.epx>.
Para sistemas SAP recém-implantados, você deverá ter feito um exercício de dimensionamento que deve determinar os requisitos de SAPS do sistema.
Confira também o blog e o documento anexado para dimensionamento do SAP no Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Etapa 2**: para sistemas existentes, o volume de E/S e operações de E/S por segundo no servidor DBMS devem ser medidos. Para sistemas recém-planejados, o exercício de dimensionamento para o novo sistema também deverá dar uma ideia dos requisitos de E/S no lado do DBMS. Se não tiver certeza, eventualmente, você precisará realizar uma prova de conceito.

**Etapa 3**: compare o requisito de SAPS do servidor DBMS com os SAPS que os diferentes tipos de VM do Azure podem fornecer. As informações sobre os SAPS dos diferentes tipos de VM do Azure estão documentadas na Nota do SAP [1928533]. O foco deve ser primeiro na VM de DBMS, já que a camada de banco de dados é a camada em um sistema SAP NetWeaver que não é escalado horizontalmente na maioria das implantações. Por outro lado, a camada do aplicativo SAP pode ser escalada horizontalmente. Se nenhum dos tipos de VM do Azure aos quais a SAP dá suporte podem oferecer os SAPS necessários, a carga de trabalho do sistema SAP planejado não pode ser executada no Azure. Você precisará implantar o sistema localmente ou precisará alterar o volume de carga de trabalho para o sistema.

**Etapa 4**: conforme documentado [aqui][virtual-machines-sizes], o Azure impõe uma cota de IOPS por VHD independentemente de você usar o Armazenamento Standard ou o Armazenamento Premium. Dependo do tipo de VM, o número de VHDs que podem ser montados varia. Como resultado, você pode calcular um número de IOPS máximo que pode ser alcançado com cada um dos diferentes tipos de VM. Dependendo do layout do arquivo de banco de dados, você pode distribuir VHDs para que se tornem um volume no SO convidado. No entanto, se o volume de IOPS atual de um sistema SAP implantado exceder os limites calculados do maior tipo de VM do Azure e se não houver nenhuma chance de compensar com mais memória, a carga de trabalho do sistema SAP poderá ser gravemente afetada. Nesses casos, você pode atingir um ponto em que você não deve implantar o sistema no Azure.

**Etapa 5**: especialmente em sistemas do SAP implantados localmente em configurações de duas camadas, as chances são de que o sistema talvez precise ser configurado no Azure em uma configuração de três camadas. Nesta etapa, você precisa verificar se há um componente na camada do aplicativo SAP que não pode ser escalado horizontalmente e que não se ajusta aos recursos de CPU e memória oferecidos pelos diferentes tipos de VM do Azure. Se realmente existir tal componente, o sistema SAP e sua carga de trabalho não poderão ser implantados no Azure. No entanto, se for possível escalar horizontalmente os componentes do aplicativo SAP em várias VMs do Azure, o sistema poderá ser implantado no Azure.

**Etapa 6**: se os componentes de camada de aplicativo do DBMS e SAP puderem ser executados em VMs do Azure, a configuração precisará ser definida em relação a:

* Número de VMs do Azure
* Tipos de VM para os componentes individuais
* Número de VHDs na VM de DBMS para fornecer IOPS suficientes

## <a name="managing-azure-assets"></a>Gerenciando Ativos do Azure
### <a name="azure-portal"></a>Portal do Azure
O Portal do Azure é uma das três interfaces para gerenciar implantações de VM do Azure. As tarefas básicas de gerenciamento, como implantar VMs com base em imagens, podem ser feitas por meio do Portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes do Azure também são tarefas que o Portal do Azure pode realizar muito bem. No entanto, funcionalidades como carregar VHDs do local para o Azure ou copiar um VHD dentro do Azure são tarefas que exigem ferramentas de terceiros ou administração por meio do PowerShell ou CLI.

![Portal do Microsoft Azure - visão geral de máquina virtual][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Tarefas de administração e configuração para a instância de Máquina Virtual são possíveis de dentro do Portal do Azure.

Além de reiniciar e desligar uma máquina Virtual, você também pode anexar, desanexar e criar discos de dados para a instância de Máquina Virtual, capturar a instância para preparação de imagem e configurar o tamanho da instância de Máquina Virtual.

O Portal do Azure oferece as funcionalidades básicas para implantar e configurar VMs e muitos outros serviços do Azure. No entanto, nem todas as funcionalidades disponíveis são cobertas pelo Portal do Azure. No Portal do Azure, não é possível executar tarefas como:

* Carregar VHDs no Azure
* Copiar VMs

[comment]: <> (MShermannd TODO: e o serviço de automação para as VMs SAP? )
[comment]: <> (MSSedusch: implantação de vários SOs de VMs ao mesmo tempo possível)
[comment]: <> (MSSedusch: além disso, não é possível nenhum tipo de automação relacionada à implantação com o Portal do Azure. Tarefas, como a implantação com script de várias VMs, não são possíveis por meio do Portal do Azure.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gerenciamento via cmdlets do Microsoft Azure PowerShell
O Windows PowerShell é uma estrutura poderosa e extensível que foi amplamente adotada por clientes implantando grandes quantidades de sistemas no Azure. Após a instalação dos cmdlets do PowerShell em um desktop, laptop ou estação de gerenciamento dedicada, os cmdlets do PowerShell podem ser executados remotamente.

O processo para habilitar um laptop/computador desktop local para usar cmdlets do Azure PowerShell e como configurá-los para uso com as assinaturas do Azure é descrito [neste artigo][powershell-install-configure].

As etapas mais detalhadas sobre como instalar, atualizar e configurar os cmdlets do Azure PowerShell também podem ser encontradas [neste capítulo do Guia de Implantação][deployment-guide-4.1].

A experiência do cliente até agora tem sido que o PS (PowerShell) é certamente a ferramenta mais eficiente para implantar VMs e criar etapas personalizadas na implantação de VMs. Todos os clientes que executam instâncias SAP no Azure estão usando cmdlets do PS para complementar as tarefas de gerenciamento que eles realizam no Portal do Azure ou até mesmo usando cmdlets do PS exclusivamente para gerenciar suas implantações no Azure. Já que os cmdlets específicos do Azure compartilham a mesma convenção de nomenclatura que os mais de 2.000 cmdlets relacionados do Windows, aproveitar esses cmdlets é uma tarefa fácil para os administradores do Windows.

Confira o exemplo aqui: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO: descrever o novo comando da CLI quando testado )
A implantação da Extensão de Monitoramento do Azure para o SAP (confira o capítulo [Solução de Monitoramento do Azure para o SAP][planning-guide-9.1] neste documento) só é possível por meio do PowerShell ou da CLI. Portanto, é obrigatório instalar e configurar o PowerShell ou CLI ao implantar ou administrar um sistema SAP NetWeaver no Azure.  

Conforme o Azure fornece mais funcionalidade, serão adicionados novos cmdlets do PS que exigirão uma atualização dos cmdlets. Portanto, faz sentido verificar o site de Download do Azure pelo menos uma vez por mês <https://azure.microsoft.com/downloads/> para obter uma nova versão dos cmdlets. A nova versão será instalada substituindo a versão antiga.

Para ver uma lista geral dos comandos do PowerShell relacionados ao Azure, verifique aqui: <https://msdn.microsoft.com/library/azure/dn708514.aspx>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gerenciamento por meio de comandos da CLI do Microsoft Azure
Para clientes que usam Linux e querem gerenciar recursos do Azure, o Powershell pode não ser uma opção. Como alternativa, a Microsoft oferece a CLI do Azure.
A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com a Plataforma Azure. A CLI do Azure fornece grande parte das mesmas funcionalidades encontradas no Portal do Azure.

Para obter informações sobre instalação, configuração e como usar os comandos da CLI para realizar tarefas do Azure, consulte

* [Instalar a CLI do Azure][xplat-cli]
* [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure][virtual-machines-linux-cli-deploy-templates]
* [Use the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager][xplat-cli-azure-resource-manager] (Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager)

Leia também o capítulo [CLI do Azure para VMs do Linux][deployment-guide-4.5.2] no [Guia de Implantação][planning-guide] sobre como usar a CLI do Azure para implantar a Extensão de Monitoramento do Azure para o SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes maneiras de implantar VMs para SAP no Azure
Neste capítulo, você aprenderá as diferentes maneiras de implantar uma VM no Azure. Procedimentos adicionais de preparação, bem como a manipulação de VHDs e VMs no Azure, são abordados neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs para SAP
O Microsoft Azure oferece várias maneiras de implantar VMs e discos associados. Portanto, é muito importante entender as diferenças, pois os preparativos das VMs podem ser diferentes dependendo do método de implantação. Em geral, observaremos os seguintes cenários:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Movendo uma VM do local para o Azure com um disco não generalizado
Você planeja mover um sistema SAP específico do local para o Azure. Isso pode ser feito carregando, no Azure, o VHD que contém os binários do sistema operacional, os binários da SAP e os binários do DBMS, além dos VHDs com os arquivos de dados e arquivos de log do DBMS. Ao contrário do que ocorre no [cenário 2 abaixo][planning-guide-5.1.2], você mantém o nome de host, SID do SAP e contas do usuário SAP na VM do Azure como foram configurados no ambiente local. Portanto, não é necessário generalizar a imagem. Confira os capítulos [Preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento para ver as etapas de preparação local e o carregamento das VMs ou VHDs não generalizados para o Azure. Leia o capítulo [Cenário 3: mover uma VM do local usando um VHD do Azure não generalizado com SAP][deployment-guide-3.4] no [Guia de Implantação][deployment-guide] para ver as etapas detalhadas da implantação, como uma imagem no Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implantando uma VM com uma imagem específica do cliente
Devido a requisitos de patch específicos da sua versão do SO ou DBMS, as imagens fornecidas no Azure Marketplace podem não atender às suas necessidades. Portanto, você precisará criar uma VM usando sua própria imagem “privada” de VM do SO/DBMS, que poderá ser implantada várias vezes posteriormente. Para preparar tal imagem “privada” para duplicação, os itens a seguir devem ser considerados:


[comment]: <> (MSSedusch > Veja mais detalhes aqui: )
[comment]: <> (MShermannd TODO: o primeiro link é sobre o modelo clássico. Não localizei um artigo de documentação do Azure)
[comment]: <> (MSSedusch > <https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[comment]: <> (MSSedusch > <http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
- - -
> ![Windows][Logo_Windows] Windows
>
> As configurações do Windows (como o nome de host e SID do Windows) devem ser abstraídas/generalizadas na VM local por meio do comando sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Siga as etapas descritas nestes artigos para [SUSE][virtual-machines-linux-create-upload-vhd-suse] ou [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] para preparar um VHD para ser carregado no Azure.
>
>

- - -
Se já tiver instalado o conteúdo do SAP na VM local (especialmente para sistemas de duas camadas), você poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure por meio do procedimento de renomeação da instância ao qual o Software Provisioning Manager SAP dá suporte (Nota do SAP [1619720]). Confira os capítulos [Preparação para implantar uma VM com uma imagem específica do cliente para o SAP][planning-guide-5.2.2] e [Carregar um VHD do local para o Azure][planning-guide-5.3.2] deste documento para ver as etapas de preparação do local e o carregamento de uma VM generalizada para o Azure. Leia o capítulo [Cenário 2: implantando uma VM com uma imagem personalizada para o SAP][deployment-guide-3.3] no [Guia de Implantação][deployment-guide] para ver as etapas detalhadas de implantação, como uma imagem no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantando uma VM com origem no Azure Marketplace
Você gostaria de usar uma imagem de VM fornecida pela Microsoft ou por terceiros, partindo do Azure Marketplace, para implantar a VM. Depois de implantar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS na VM, como você faria em um ambiente local. Para obter uma descrição de implantação mais detalhada, confira o capítulo [Cenário 1: implantando uma VM do Azure Marketplace para o SAP][deployment-guide-3.2] no [Guia de Implantação][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparando VMs com SAP para o Azure
Antes de carregar as VMs no Azure, você precisará certificar-se de que as VMs e VHDs atendem a certos requisitos. Há pequenas diferenças, dependendo do método de implantação usado.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma VM do local para o Azure com um disco não generalizado
Um método de implantação comum é mover, do local para o Azure, uma VM existente que executa um sistema SAP. Essa VM e o sistema SAP presente nela só devem ser executados no Azure usando o mesmo nome de host e, muito provavelmente, a mesma SID da SAP. Nesse caso, o SO convidado da VM não deve ser generalizado para várias implantações. Se a rede local foi estendida para o Azure (confira o capítulo [Entre instalações - Implantação de uma ou várias VMs do SAP no Azure com o requisito de estarem totalmente integradas à rede local][planning-guide-2.2] neste documento), então, até as mesmas contas de domínio poderão ser usadas na VM, já que elas foram utilizadas antes localmente.

Os requisitos ao preparar seu próprio Disco de VM do Azure são:

* Originalmente, o VHD contendo o sistema operacional podia ter um tamanho máximo de somente 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora o VHD que contém o sistema operacional pode ser até 1 TB de tamanho, assim como também é o caso de qualquer outro VHD hospedado no Armazenamento do Azure.

[comment]: <> (MShermannd TODO: é preciso verificar se a CLI também se converte em estática )
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* VHDs que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato de VHD fixo. O mesmo limite de tamanho do disco de SO se aplica também aos discos de dados. VHDs podem ter um tamanho máximo de 1 TB. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* Adicione outra conta local com privilégios de administrador que possa ser usada pelo suporte da Microsoft ou que possa ser atribuída como contexto no qual serviços e aplicativos sejam executados até que a VM seja implantada e mais usuários apropriados possam ser usados.
* Para o caso de uso de um cenário de implantação de Somente Nuvem (confira o capítulo [Somente Nuvem - implantações da Máquina Virtual no Azure sem dependências na rede do cliente local][planning-guide-2.1] deste documento) em combinação com esse método de implantação, as contas de domínio podem não funcionar depois do Disco do Azure ser implantado no Azure. Isso é especialmente verdadeiro para contas usadas para executar serviços como os aplicativos SAP ou DBMS. Portanto, você precisa substituir essas contas de domínio por contas locais da VM e excluir as contas de domínio locais na VM. Manter os usuários do domínio local na imagem da VM não é um problema quando a VM é implantada no cenário Entre Instalações, conforme descrito no capítulo [Entre Instalações - implantação de uma ou várias VMs do SAP no Azure com o requisito de integração completa na rede local][planning-guide-2.2] neste documento.
* Se as contas de domínio tiverem sido usadas como usuários ou logons DBMS ao executar o sistema localmente, e essas VMs destinarem-se à implantação em cenários somente em nuvem, os usuários do domínio precisarão ser excluídos. Você precisa certificar-se de que o administrador local e um outro usuário local da VM sejam adicionados como um logon/usuário no DBMS, como administradores.
* Adicione outras contas locais, já que elas podem ser necessárias para o cenário de implantação específico.

- - -
> ![Windows][Logo_Windows] Windows
>
> Nesse cenário, nenhuma generalização (sysprep) da VM é necessária para carregar e implantar a VM no Azure.
> Verifique se a unidade D:\ não é usada. Defina a montagem automática de disco para os discos anexados conforme descrito no capítulo [Definindo a montagem automática para os discos anexados][planning-guide-5.5.3] deste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Nesse cenário, nenhuma generalização (waagent -deprovision) da VM é necessária para carregar e implantar a VM no Azure.
> Certifique-se de que /mnt/resource não é usado e que todos os discos são montados por meio de uuid. Para o disco de SO, certifique-se de que a entrada do carregador de inicialização também reflete a montagem baseada em uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implantar uma VM com uma imagem específica do cliente para SAP
Os arquivos VHD que contêm um SO generalizado também são armazenados em contêineres nas Contas de Armazenamento do Azure. Você pode implantar uma nova VM de um VHD de imagem desse tipo referenciando o VHD como um VHD de origem em seus arquivos do modelo de implantação, conforme descrito no capítulo [Cenário 2: implantando uma VM com uma imagem personalizada para o SAP][deployment-guide-3.3] do [Guia de Implantação][deployment-guide].

Os requisitos ao preparar sua própria Imagem de VM do Azure são:

* Originalmente, o VHD contendo o sistema operacional podia ter um tamanho máximo de somente 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora o VHD que contém o sistema operacional pode ser até 1 TB de tamanho, assim como também é o caso de qualquer outro VHD hospedado no Armazenamento do Azure.

[comment]: <> (MShermannd TODO: é preciso verificar se a CLI também se converte em estática )
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* VHDs que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato de VHD fixo. O mesmo limite de tamanho do disco de SO se aplica também aos discos de dados. VHDs podem ter um tamanho máximo de 1 TB. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* Como todos os usuários do Domínio registrados como usuários na VM não existirão em um cenário de Somente Nuvem (confira o capítulo [Somente Nuvem - implantações da Máquina Virtual no Azure sem dependências na rede do cliente local][planning-guide-2.1] deste documento), os serviços que usam tais contas de domínio poderão não funcionar depois da imagem ser implantada no Azure. Isso é especialmente verdadeiro para contas usadas para executar serviços como os aplicativos SAP ou DBMS. Portanto, você precisa substituir essas contas de domínio por contas locais da VM e excluir as contas de domínio locais na VM. Manter os usuários do domínio local na imagem da VM pode não ser um problema quando a VM é implantada no cenário Entre Instalações, conforme descrito no capítulo [Entre Instalações - implantação de uma ou várias VMs do SAP no Azure com o requisito de estarem totalmente integradas na rede local][planning-guide-2.2] neste documento.
* Adicione outra conta local com privilégios de administrador que possa ser usada pelo suporte da Microsoft em investigações de problemas ou que possa ser atribuída como contexto no qual serviços e aplicativos sejam executados até que a VM seja implantada e mais usuários apropriados possam ser usados.
* Em implantações somente em nuvem e quando contas de domínio tiverem sido usadas como usuários ou logons DBMS ao executar o sistema localmente, os usuários do domínio precisarão ser excluídos. Você precisa certificar-se de que o administrador local e um outro usuário local da VM sejam adicionados como um logon/usuário do DBMS, como administradores.
* Adicione outras contas locais, já que elas podem ser necessárias para o cenário de implantação específico.
* Se a imagem contém uma instalação do SAP NetWeaver e renomear o nome do host do nome original no ponto de implantação do Azure é algo provável, é recomendável copiar as versões mais recentes do DVD do Gerenciador de provisionamento de software SAP para o modelo. Isso permitirá que você use a funcionalidade de renomeação SAP fornecida para adaptar o nome do host alterado e/ou alterar a SID do sistema SAP na imagem de VM implantada, assim que uma nova cópia for iniciada.

- - -
> ![Windows][Logo_Windows] Windows
>
> Verifique se a unidade D:\ não é usada. Defina a montagem automática de disco para os discos anexados conforme descrito no capítulo [Definindo a montagem automática para os discos anexados][planning-guide-5.5.3] deste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que /mnt/resource não é usado e que todos os discos são montados por meio de uuid. Para o disco de SO, certifique-se de que a entrada do carregador de inicialização também reflete a montagem baseada em uuid.
>
>

- - -
* A GUI da SAP (para fins administrativos e de instalação) podem ser pré-instalados em um modelo desse tipo.
* Outros softwares necessários para executar as VMs com êxito em cenários entre instalações podem ser instalados, desde que esse software possa trabalhar com a renomeação da VM.

Se a VM estiver suficientemente preparada para ser genérica e eventualmente independente de contas/usuários não disponíveis no cenário de implantação do Azure de destino, a última etapa de preparação, a generalização de uma imagem desse tipo, será realizada.

##### <a name="generalizing-a-vm"></a>Generalizando uma VM
- - -
[comment]: <> (MShermannd TODO: é preciso encontrar artigos/documentos melhores sobre como generalizar as VMs para o ARM )
> ![Windows][Logo_Windows] Windows
>
> A última etapa é fazer logon em uma VM com uma conta de administrador. Abra uma janela de comando do Windows como “administrador”. Vá até ...\windows\system32\sysprep e execute sysprep.exe.
> Uma janela pequena será exibida. É importante marcar a opção "Generalizar" (o padrão é desmarcado) e alterar a Opção de Desligamento do seu padrão de “Reinicializar” para “Desligar”. Este procedimento pressupõe que o processo sysprep seja executado localmente no SO convidado de uma VM.
> Se desejar executar o procedimento com uma VM já em execução no Azure, siga as etapas descritas [neste artigo][virtual-machines-windows-capture-image].
>
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][virtual-machines-linux-capture-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferindo VMs e VHDs do local para o Azure
Já que carregar discos e imagens de VM para o Azure não é possível por meio do Portal do Azure, você precisa usar a CLI ou cmdlets do Azure PowerShell. Outra possibilidade é o uso da ferramenta “AzCopy”. Essa ferramenta pode copiar VHDs entre o local e o Azure (em ambas as direções). Ela também pode copiar VHDs entre Regiões do Azure. Consulte [esta documentação][storage-use-azcopy] para download e uso do AzCopy.

Uma terceira alternativa seria usar várias ferramentas de terceiros orientadas por GUI. No entanto, certifique-se de que essas ferramentas dão suporte a Blobs de Páginas do Azure. Para nossos propósitos, precisamos usar o armazenamento de Blob de Páginas do Azure (as diferenças são descritas aqui: <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>). Além disso, as ferramentas fornecidas pelo Azure são muito eficientes ao compactar as VMs e VHDs que precisam ser carregados. Isso é importante, porque essa eficiência na compactação reduz o tempo de upload (que varia mesmo assim, dependendo do link de upload do recurso local para a Internet e da região de implantação do Azure de destino). Faz sentido supor que carregar uma VM ou um VHD de um local europeu para os EUA com base em data centers do Azure levará mais tempo do que carregar os mesmos VHDs/VMs para data centers europeus do Azure.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregando um VHD do local no Azure
Para carregar uma VM ou VHD existente a partir da rede local, essa VM ou VHD precisa atender aos requisitos listados no capítulo [Preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento.

Essa VM NÃO precisa ser generalizada e pode ser carregada no estado e forma que tem após o desligamento no lado local. O mesmo é verdadeiro para VHDs adicionais que não contêm nenhum sistema operacional.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregando um VHD e tornando-o um Disco do Azure
Nesse caso, desejamos carregar um VHD, com ou sem um SO, e montá-lo em uma VM como um disco de dados ou usá-lo como disco de SO. Este é um processo de várias etapas

**Powershell**

* Fazer logon na assinatura com *Login-AzureRmAccount*
* Definir a assinatura de seu contexto com *Set-AzureRmContext* e o parâmetro SubscriptionId ou SubscriptionName - confira <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregue o VHD com *Add-AzureRmVhd* para uma Conta de Armazenamento do Azure - confira <https://msdn.microsoft.com/library/mt603554.aspx>
* Definir o disco do SO de uma nova configuração da VM para o VHD com *Set-AzureRmVMOSDisk* - confira <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM a partir da configuração da VM com *New-AzureRmVM* - confira <https://msdn.microsoft.com/library/mt603754.aspx>
* Adicionar um disco de dados a uma nova VM com *Add-AzureRmVMDataDisk* - confira <https://msdn.microsoft.com/library/mt603673.aspx>

**CLI do Azure**

* Mudar para o modo do Azure Resource Manager com *azure config mode arm*
* Fazer logon na assinatura com *logon do azure*
* Selecione a assinatura com o *conjunto de contas do Azure `<subscription name or id`>*
* Carregar o VHD com o comando *azure storage blob upload* – veja [Usando a CLI do Azure com o Armazenamento do Azure][storage-azure-cli]
* Criar uma nova VM especificando o VHD carregado como disco de SO com *criar vm do azure* e o parâmetro -d
* Adicionar um disco de dados a uma nova VM com *anexar novo disco de vm*

**Modelo**

* Carregar o VHD com o Powershell ou a CLI do Azure
* Implantar a VM com um modelo JSON referenciando o VHD, conforme mostrado [neste modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma imagem de VM
Para carregar uma VM ou VHD existente da rede local para usar como uma imagem de VM do Azure, essa VM ou VHD precisa atender aos requisitos listados no capítulo [Preparação para implantar uma VM com uma imagem específica do cliente para o SAP][planning-guide-5.2.2] deste documento.

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar sua VM – veja [Como capturar uma máquina virtual do Windows no modelo de implantação do Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][virtual-machines-linux-capture-image-capture]
* Fazer logon na assinatura com *Login-AzureRmAccount*
* Definir a assinatura de seu contexto com *Set-AzureRmContext* e o parâmetro SubscriptionId ou SubscriptionName - confira <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregue o VHD com *Add-AzureRmVhd* para uma Conta de Armazenamento do Azure - confira <https://msdn.microsoft.com/library/mt603554.aspx>
* Definir o disco do SO de uma nova configuração da VM para o VHD com *Set-AzureRmVMOSDisk -SourceImageUri -CreateOption fromImage* - confira <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM a partir da configuração da VM com *New-AzureRmVM* - confira <https://msdn.microsoft.com/library/mt603754.aspx>

**CLI do Azure**

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar sua VM – veja [Como capturar uma máquina virtual do Windows no modelo de implantação do Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][virtual-machines-linux-capture-image-capture] para Linux
* Mudar para o modo do Azure Resource Manager com *azure config mode arm*
* Fazer logon na assinatura com *logon do azure*
* Selecione a assinatura com o *conjunto de contas do Azure `<subscription name or id`>*
* Carregar o VHD com o comando *azure storage blob upload* – veja [Usando a CLI do Azure com o Armazenamento do Azure][storage-azure-cli]
* Criar uma nova VM especificando o VHD carregado como disco de SO com *criar vm do azure* e o parâmetro -Q

**Modelo**

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar sua VM – veja [Como capturar uma máquina virtual do Windows no modelo de implantação do Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][virtual-machines-linux-capture-image-capture] para Linux
* Carregar o VHD com o Powershell ou a CLI do Azure
* Implantar a VM com um modelo JSON referenciando o VHD da imagem, conforme mostrado [neste modelo JSON de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-to-on-premises"></a>Baixando VHDs para o local
A infraestrutura como serviço do Azure não é uma rua de mão única, em que você pode apenas carregar VHDs e sistemas SAP. Você também pode mover sistemas SAP do Azure de volta para o universo local.

Durante o tempo de download, os VHDs não podem estar ativos. Mesmo ao baixar VHDs montados em VMs, a VM precisa ser desligada. Se você quiser apenas baixar o conteúdo do banco de dados, que deverá então ser usado para configurar um novo sistema local, e se for aceitável que o sistema no Azure ainda possa estar operacional durante o tempo de download e instalação do novo sistema, você poderá evitar um longo tempo de inatividade executando um backup de banco de dados compactado em um VHD e simplesmente baixando esse VHD, em vez de também baixar a VM base do SO.

#### <a name="powershell"></a>Powershell
Depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o cmdlet do PowerShell Save-AzureRmVhd no destino local para baixar os discos VHD para a realidade local. Para fazer isso, é necessária a URL do VHD que você pode encontrar na 'Seção de Armazenamento' do Portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado), e você precisa saber também para onde o VHD deve ser copiado.

Em seguida, você pode aproveitar o comando simplesmente definindo o parâmetro SourceUri como a URL do VHD a baixar e o LocalFilePath como a localização física do VHD (incluindo seu nome). O comando seria semelhante ao seguinte:

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Para obter mais detalhes do cmdlet Save-AzureRmVhd, verifique aqui <https://msdn.microsoft.com/library/mt622705.aspx>.

#### <a name="cli"></a>CLI
Depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o comando azure storage blob download da CLI do Azure no destino local para baixar os discos VHD para o universo local. Para fazer isso, você precisa do nome e do contêiner do VHD que você pode encontrar na 'Seção de Armazenamento' do Portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado), e você precisa saber também para onde o VHD deve ser copiado.

Em seguida, você pode aproveitar o comando simplesmente definindo os parâmetros blob e contêiner do VHD a baixar e o destino como a localização física de destino do VHD (incluindo seu nome). O comando seria semelhante ao seguinte:

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download>
```

### <a name="transferring-vms-and-vhds-within-azure"></a>Transferindo VMs e VHDs no interior do Azure
#### <a name="copying-sap-systems-within-azure"></a>Copiando sistemas SAP no interior do Azure
Um sistema SAP ou até mesmo um servidor DBMS dedicado com suporte a uma camada de aplicativo SAP provavelmente consistirá em vários VHDs contendo o SO com os binários ou então os dados e arquivo(s) de log do banco de dados SAP. Nem a funcionalidade do Azure de copiar VHDs, nem a funcionalidade do Azure de salvar VHDs em disco têm um mecanismo de sincronização que capturaria instantâneos de vários VHDs de forma síncrona. Portanto, o estado dos VHDs copiados ou salvos, mesmo se eles fossem montados na mesma VM, seria diferente. Isso significa que, no caso concreto de ter diferentes dados e arquivo(s) de log contidos nos diferentes VHDs, o banco de dados, no final, seria inconsistente.

**Conclusão: para copiar ou salvar VHDs que fazem parte de uma configuração do sistema SAP, você precisa interromper o sistema SAP e também precisar desligar a VM implantada. Somente então você poderá copiar ou baixar o conjunto de VHDs para criar uma cópia do sistema SAP no Azure ou no localmente.**

Discos de dados são armazenados como arquivos VHD em uma Conta de Armazenamento do Azure e podem ser diretamente anexados a uma máquina virtual ou ser usados como uma imagem. Nesse caso, o VHD é copiado para outro local antes de ser anexado à máquina virtual. O nome completo do arquivo VHD no Azure deve ser exclusivo dentro do Azure. Conforme mencionado anteriormente, o nome em questão é um tipo de nome de três partes com a seguinte aparência:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>Powershell
Você pode usar os cmdlets do Azure PowerShell para copiar um VHD, conforme mostrado [neste artigo][storage-powershell-guide-full-copy-vhd].

##### <a name="cli"></a>CLI
Você pode usar a CLI do Azure para copiar um VHD, conforme mostrado [neste artigo][storage-azure-cli-copy-blobs]

##### <a name="azure-storage-tools"></a>Ferramentas do Armazenamento do Azure
* <http://azurestorageexplorer.codeplex.com/releases/view/125870>

Também há edições profissionais de Gerenciadores do Armazenamento do Azure que podem ser encontradas aqui:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD no interior de uma conta de armazenamento é um processo que leva apenas alguns segundos (semelhante ao hardware de SAN criando instantâneos com cópia lenta e cópia em gravação). Depois de ter uma cópia do arquivo VHD, você pode anexá-lo a uma máquina virtual ou usá-lo como uma imagem para anexar cópias do VHD a máquinas virtuais.

##### <a name="powershell"></a>Powershell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLI
```
azure config mode arm

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiando discos entre Contas de Armazenamento do Azure
Esta tarefa não pode ser realizada no Portal do Azure. Você pode usar cmdlets do Azure PowerShell, a CLI do Azure ou um navegador de armazenamento de terceiros. Os comandos CLI ou cmdlets do PowerShell podem criar e gerenciar blobs, os quais incluem a capacidade de copiar blobs em regiões entre contas de armazenamento de forma assíncrona na assinatura do Azure.

##### <a name="powershell"></a>Powershell
Também é possível copiar VHDs entre assinaturas. Para obter mais informações, leia [este artigo][storage-powershell-guide-full-copy-vhd].

O fluxo básico da lógica de cmdlet do PS tem esta aparência:

* Crie um contexto da conta de armazenamento para a conta de armazenamento de origem com *New-AzureStorageContext* - confira <https://msdn.microsoft.com/library/dn806380.aspx>
* Crie um contexto da conta de armazenamento para a conta de armazenamento de destino com *New-AzureStorageContext* - confira <https://msdn.microsoft.com/library/dn806380.aspx>
* Iniciar a cópia com

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verificar o status da cópia em um loop com

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

Para obter exemplos, veja [este artigo][storage-powershell-guide-full-copy-vhd]

##### <a name="cli"></a>CLI
* Iniciar a cópia com

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* Verificar o status da cópia em um loop com

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

Para obter exemplos, veja [este artigo][storage-azure-cli-copy-blobs]

### <a name="disk-handling"></a>Administração de discos
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura de VM/VHD para implantações SAP
Idealmente, a administração da estrutura de uma VM e os VHDs associados deve ser muito simples. Em instalações locais, os clientes desenvolveram muitas formas de estruturar uma instalação de servidor.

* Um VHD base que contém o sistema operacional e todos os binários do DBMS e/ou SAP. Desde março de 2015, este VHD pode ser de até 1 TB de tamanho, enquanto restrições anteriores o limitavam a 127 GB.
* Um ou vários VHDs contendo o arquivo de log do DBMS do banco de dados SAP e o arquivo de log da área de armazenamento temporário do DBMS (se o DBMS der suporte a isso). Se os requisitos de IOPS do log de banco de dados forem altos, você precisará distribuir vários VHDs para alcançar o volume de IOPS necessário.
* Um número de VHDs contendo um ou dois arquivos de banco de dados do banco de dados SAP e também os arquivos de dados temporários do DBMS (se o DBMS der suporte a isso).

![Configuração de referência da VM IaaS do Azure para SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd TODO: descreva a estrutura do Linux )

- - -
> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes, vimos configurações em que, por exemplo, binários de DBMS e SAP não foram instalados na unidade c:\ na qual o sistema operacional estava instalado. Havia vários motivos para isso, mas quando analisamos a raiz, geralmente era que as unidades eram pequenas e as atualizações de SO precisavam de espaço adicional há 10-15 anos atrás. Nenhuma das condições se aplica com muita frequência nos dias de hoje. Hoje, a unidade c:\ pode ser mapeada em VMs ou discos de grande volume. Para manter as implantações simples em sua estrutura, é recomendável seguir o seguinte padrão de implantação para sistemas SAP NetWeaver no Azure
>
> O arquivo de paginação do sistema operacional Windows deve estar na unidade D: (disco não persistente)
>
> ![Linux][Logo_Linux] Linux
>
> Coloque o arquivo de troca do Linux em /mnt /mnt/resource no Linux, conforme descrito [neste artigo][virtual-machines-linux-agent-user-guide]. O arquivo de troca pode ser configurado no arquivo de configuração do Agente do Linux /etc/waagent.conf. Adicione ou remova as seguintes configurações:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, é necessário reiniciar o Agente do Linux com

```
sudo service waagent restart
```

Leia a Nota do SAP [1597355] para obter mais detalhes sobre o tamanho do arquivo recomendado

- - -
O número de VHDs usados para os arquivos de dados do DBMS e o tipo de armazenamento do Azure esses VHDs são hospedados em deve ser determinado pelos requisitos de IOPS e pela latência necessária. Cotas exatas são descritas [neste artigo][virtual-machines-sizes]

Experiência de implantações do SAP nos últimos 2 anos nos ensinou algumas lições que podem ser resumidas como:

* O tráfego IOPS para diferentes arquivos de dados nem sempre é o mesmo, já que sistemas de cliente existentes podem ter arquivos de dados dimensionados diferentemente que representam seus bancos de dados SAP. Como resultado, usar uma configuração RAID em vários VHDs para colocar os arquivos de dados formados desses VHDs pelos LUNs mostrou-se uma melhor opção. Havia situações, especialmente com o Armazenamento Standard do Azure, nas quais uma taxa de IOPS atingiu a cota de um único VHD com o log de transações do DBMS. Nesses cenários, é recomendado usar o Armazenamento Premium ou, alternativamente, agregar vários VHDs de Armazenamento Standard com um RAID de software.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Performance best practices for SQL Server in Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices] (Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure)
>
> ![Linux][Logo_Linux] Linux
>
> * [Configurar RAID de software no Linux][virtual-machines-linux-configure-raid]
> * [Configurar o LVM em uma VM Linux no Azure][virtual-machines-linux-configure-lvm]
> * [Segredos do Armazenamento do Azure e otimizações de E/S do Linux](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* O Armazenamento Premium está apresentando desempenho significativamente melhor, especialmente para gravações de log de transações críticas. Para cenários SAP que se espera que forneçam produção como desempenho, é altamente recomendável usar séries de VMs que podem aproveitar o Armazenamento Premium do Azure.

Tenha em mente que o VHD que contém o SO, e conforme recomendamos, os binários da SAP e o banco de dados (VM base), não está mais limitado a 127 GB. Agora ele pode ter até 1 TB de tamanho. Isso deve ser espaço suficiente para manter todos os arquivos necessários, incluindo, por exemplo, logs de trabalho em lotes SAP.

Para obter mais sugestões e mais detalhes, especificamente para as VMs do DBMS, confira o [Guia de Implantação do DBMS][dbms-guide]

#### <a name="disk-handling"></a>Administração de discos
Na maioria dos cenários, você precisa criar discos adicionais para implantar o banco de dados SAP na VM. Falamos sobre as considerações no número de VHDs no capítulo [Estrutura da VM/VHD para as implantações do SAP][planning-guide-5.5.1] deste documento. O Portal do Azure permite anexar e desanexar discos depois que uma VM base é implantada. Os discos podem ser anexados/desanexados quando a VM está instalada e em execução, bem como quando ela é interrompida. Ao anexar um disco, o Portal do Azure oferece a possibilidade de anexar um disco vazio ou um disco existente que nesse momento não está anexado a outra VM.

**Observação**: VHDs só podem ser anexados a uma única VM em um determinado momento.

![Anexar/desanexar discos do Armazenamento Standard do Azure][planning-guide-figure-1400]

Você precisa decidir se deseja criar um VHD novo e vazio (que seria criado na mesma Conta de Armazenamento que a VM base está) ou se você deseja selecionar um VHD existente que foi carregado anteriormente e deve ser anexado à VM agora.

**IMPORTANTE**: você **NÃO** deseja usar o Cache de Host com o Armazenamento Standard do Azure. Você deve deixar a preferência de Cache de Host no padrão de NENHUM. Com o Armazenamento Premium do Azure, você deverá habilitar o Cache de Leitura se a característica de E/S for lida principalmente como tráfego típico de E/S em relação os arquivos de dados do banco de dados. No caso do arquivo de log de transações do banco de dados, é recomendado usar a opção sem cache.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no Portal do Azure][virtual-machines-windows-attach-disk-portal]
>
> Se discos forem anexados, você precisará fazer logon na VM para abrir o Gerenciador de Disco do Windows. Se a montagem automática não estiver habilitada como recomendado no capítulo [Definindo a montagem automática para os discos anexados][planning-guide-5.5.3], o volume recém-vinculado precisará ser colocado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Se discos forem anexados, você precisará fazer logon na VM e inicializá-los, conforme descrito [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Se o novo disco for um disco vazio, também será necessário formatar o disco. Para formatação, especialmente para arquivos de dados e de log do DBMS, aplicam-se as mesmas recomendações existentes para implantações bare-metal do DBMS.

Como já mencionado no capítulo [O Conceito de Máquina Virtual do Microsoft Azure][planning-guide-3.2], uma conta de Armazenamento do Azure não fornece recursos infinitos em termos de volume de E/S, IOPS e volume de dados. Geralmente as VMs de DBMS são mais afetadas por isso. Talvez seja melhor usar uma conta de armazenamento separada para cada VM se você tiver algumas VMs com volumes altos de E/S para implantar, para permanecer dentro do limite de volume da Conta de Armazenamento do Azure. Caso contrário, você precisa ver como pode equilibrar essas VMs entre diferentes Contas de Armazenamento sem atingir o limite de cada Conta de Armazenamento individual. Mais detalhes são analisados no [Guia de Implantação do DBMS][dbms-guide]. Você também deve ter essas limitações em mente para VMs de servidor de aplicativos SAP puro ou outras VMs que eventualmente possam exigir VHDs adicionais.

Outro tópico relevante para as Contas de Armazenamento é se os VHDs em uma Conta de Armazenamento estão sendo replicados geograficamente. A replicação geográfica é habilitada ou desabilitada no nível da Conta de Armazenamento e não no nível da VM. Se a replicação geográfica estiver habilitada, os VHDs na Conta de Armazenamento serão replicados para outro data center do Azure na mesma região. Antes de decidir sobre isso, você deve considerar a seguinte restrição:

A replicação geográfica do Azure funciona localmente em cada VHD em uma VM e não replica as E/Ss em ordem cronológica entre vários VHDs em uma VM. Portanto, o VHD que representa a VM base, bem como VHDs adicionais vinculados à VM, são replicados de modo independente uns dos outros. Isso significa que não há nenhuma sincronização entre as alterações nos diferentes VHDs. O fato de que as E/Ss são replicadas de modo independente da ordem em que são gravadas significa que a replicação geográfica não tem valor para servidores de banco de dados com bancos de dados distribuídos entre vários VHDs. Além do DBMS, também pode haver outros aplicativos em que os processos gravam ou manipulam dados em diferentes VHDs e nos quais é importante manter a ordem das alterações. Se isso for um requisito, a replicação geográfica no Azure não deverá ser habilitada. Dependendo de você precisar/querer ou não a replicação geográfica para um conjunto de VMs, mas não para outro conjunto, você já pode categorizar VMs e os VHDs relacionados a elas em diferentes Contas de Armazenamento com replicação geográfica habilitada ou desabilitada.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Definindo a montagem automática para os discos anexados
- - -
> ![Windows][Logo_Windows] Windows
>
> Para VMs que são criadas por meio de imagens ou discos próprios, é necessário verificar e possivelmente definir o parâmetro de montagem automática. Definir esse parâmetro permitirá que a VM, após uma reinicialização ou reimplantação no Azure, monte novamente as unidades anexadas/montadas, de modo automático.
> O parâmetro é definido para as imagens fornecidas pela Microsoft no Azure Marketplace.
>
> Para definir a montagem automática, verifique a documentação do executável de linha de comando diskpart.exe aqui:
>
> * [Opções de linha de comando do DiskPart](https://technet.microsoft.com/library/cc766465.aspx)
> * [Montagem automática](http://technet.microsoft.com/library/cc753703.aspx)
>
> A janela de linha de comando do Windows deve ser aberta como administrador.
>
> Se discos forem anexados, você precisará fazer logon na VM para abrir o Gerenciador de Disco do Windows. Se a montagem automática não estiver habilitada como recomendado no capítulo [Definindo a montagem automática para os discos anexados][planning-guide-5.5.3], o volume recém-anexado precisará ser colocado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Você deve inicializar um disco vazio anexado recentemente, conforme descrito [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Você também precisa adicionar novos discos a /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Implantação final
Para ver a Implantação final e as etapas exatas, especialmente com relação à implantação do Monitoramento Estendido SAP, confira o [Guia de Implantação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Acessando sistemas SAP em execução nas VMs do Azure
Para cenários somente em nuvem, você talvez queira se conectar a esses sistemas SAP pela Internet pública usando a GUI da SAP. Nesses casos, os procedimentos a seguir precisam ser aplicados.

Posteriormente neste documento, discutiremos o cenário principal, conectar-se a sistemas SAP em implantações entre instalações que têm uma conexão site a site (túnel VPN) ou uma conexão de Rota Expressa do Azure entre os sistemas locais e sistemas do Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos serviços SAP
Com o Azure Resource Manager, não há mais pontos de extremidade padrão como havia no antigo modelo clássico. Todas as portas de uma VM do Azure ARM permanecem abertas desde que:

1. Nenhum grupo de segurança de rede seja definido para a sub-rede ou a interface de rede. O tráfego de rede para VMs do Azure possa ser protegido por meio dos chamados "Grupos de Segurança de Rede". Para obter mais informações, consulte [O que é um NSG (Grupo de Segurança de Rede)?][virtual-networks-nsg]
2. Nenhum Azure Load Balancer seja definido para a interface de rede   

Veja a diferença de arquitetura entre o modelo clássico e o ARM, conforme descrito [neste artigo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuração da conectividade do sistema SAP e GUI da SAP para cenário somente em nuvem
Confira este artigo que descreve os detalhes deste tópico: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Alterando as configurações de Firewall na VM
Pode ser necessário configurar o firewall em suas máquinas virtuais para permitir o tráfego de entrada para o sistema SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Por padrão, o Firewall do Windows em uma VM implantada no Azure é ativado. Agora você precisa permitir que a porta SAP seja aberta, caso contrário a GUI da SAP não poderá se conectar.
> Para fazer isso:
>
> * Abra Painel de Controle\Sistema e Segurança \Firewall do Windows em “Configurações Avançadas”.
> * Agora, clique com o botão direito do mouse em Regras de Entrada e escolha “Nova Regra”.
> * No Assistente que aparece a seguir, escolha criar uma nova regra de “Porta”.
> * Na próxima etapa do assistente, deixe a configuração como TCP e digite o número da porta que deseja abrir. Como a ID da nossa instância SAP é 00, usamos 3200. Se a instância tiver um número de instância diferente, a porta que você definiu anteriormente com base no número da instância deverá ser aberta.
> * Na próxima parte do assistente, você deve deixar o item “Permitir Conexão” marcado.
> * Na próxima etapa do assistente, você deve definir se a regra se aplica à rede de Domínio, Privada e Público. Ajuste essa configuração se necessário, conforme suas necessidades. Entretanto, ao conectar-se com a GUI da SAP do exterior pela rede pública, é necessário ter a regra aplicada à rede pública.
> * Na última etapa do assistente, você deve dar um nome para a regra e, em seguida, salvar a regra ao pressionar “Concluir”
>
> A regra entrará em vigor imediatamente.
>
> ![Definição da regra de porta][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens do Linux no Azure Marketplace não habilitam o firewall iptables por padrão, e a conexão com o sistema SAP deve funcionar. Se você habilitou o iptables ou outro firewall, confira a documentação do iptables ou do firewall usado para permitir o tráfego tcp de entrada para a porta 32xx (em que xx é o número do sistema de seu sistema SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Recomendações de segurança
A GUI da SAP não se conecta imediatamente a nenhuma das instâncias da SAP (porta 32xx) em execução, mas se conecta primeiro através da porta aberta para o processo do servidor de mensagens SAP (porta 36xx). No passado, a mesma porta foi usada pelo servidor de mensagens para a comunicação interna com as instâncias do aplicativo. Para impedir que servidores de aplicativo locais se comuniquem inadvertidamente com um servidor de mensagens no Azure, as portas de comunicação interna podem ser alteradas. É altamente recomendável alterar a comunicação interna entre o servidor de mensagens SAP e suas instâncias de aplicativo para um número da porta diferente em sistemas que foram clonados por meio de sistemas locais, como um clone de desenvolvimento para testes de projeto, etc. Isso pode ser feito com o parâmetro de perfil padrão:

> rdisp/msserv_internal
>
>

como documentado em: <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm>

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Conceitos de implantação somente em nuvem de instâncias do SAP
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Cenário de treinamento/demonstração de VM única com SAP NetWeaver
![Executando sistemas de demonstração SAP em VMs únicas com os mesmos nomes de VM, isoladas em serviços de nuvem do Azure][planning-guide-figure-1700]

Neste cenário (confira o capítulo [Somente Nuvem][planning-guide-2.1] deste documento), estamos implementando um cenário do sistema de treinamento/demonstração típico no qual o cenário completo está contido em uma única VM. Vamos supor que a implantação é feita por meio de modelos de imagem de VM. Também pressupomos que várias dessas VMs de demonstração/treinamento precisam ser implantadas com as VMs tendo o mesmo nome.

Pressupõe-se que você criou uma Imagem da VM, como descrito em algumas seções do capítulo [Preparando VMs com o SAP para o Azure][planning-guide-5.2] deste documento.

A sequência de eventos para implementar o cenário tem esta aparência:

[comment]: <> (MShermannd TODO: é preciso fornecer um exemplo/descrição do ARM usando o modelo JSON + um esclarecimento sobre o nome exclusivo da VM na rede virtual do ARM )   
##### <a name="powershell"></a>Powershell
* Criar um novo grupo de recursos para cada estrutura de treinamento/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* Criar uma nova conta de armazenamento

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada estrutura de treinamento/demonstração para habilitar o uso do mesmo nome do host e endereços IP. A rede virtual é protegida por um Grupo de Segurança de Rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de Área de Trabalho Remota e porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Crie uma máquina virtual. Para o cenário somente em nuvem, todas as VMs terão o mesmo nome. A SID das instâncias do SAP NetWeaver nessas VMs também será a mesma. No Grupo de Recursos do Azure, o nome da VM deve ser exclusivo, mas em diferentes Grupos de Recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão de “Administrador” do Windows ou “raiz” para Linux não são válidas. Portanto, um novo nome de usuário do administrador deve ser definido, junto com uma senha. O tamanho da VM também deve ser definido.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione discos adicionais e restaure o conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) devem ser exclusivos no Azure.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLI
O código de exemplo a seguir pode ser usado no Linux. Para Windows, use o PowerShell conforme descrito acima ou adapte o exemplo para usar %rgName% em vez de $rgName e defina a variável de ambiente usando o comando *set*do Windows.

* Criar um novo grupo de recursos para cada estrutura de treinamento/demonstração

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* Criar uma nova conta de armazenamento

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* Crie uma nova rede virtual para cada estrutura de treinamento/demonstração para habilitar o uso do mesmo nome do host e endereços IP. A rede virtual é protegida por um Grupo de Segurança de Rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de Área de Trabalho Remota e porta 22 para SSH.

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da Internet

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet
```

* Crie uma máquina virtual. Para o cenário somente em nuvem, todas as VMs terão o mesmo nome. A SID das instâncias do SAP NetWeaver nessas VMs também será a mesma. No Grupo de Recursos do Azure, o nome da VM deve ser exclusivo, mas em diferentes Grupos de Recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão de “Administrador” do Windows ou “raiz” para Linux não são válidas. Portanto, um novo nome de usuário do administrador deve ser definido, junto com uma senha. O tamanho da VM também deve ser definido.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* Opcionalmente, adicione discos adicionais e restaure o conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) devem ser exclusivos no Azure.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>Modelo
Você pode usar os modelos de exemplo no repositório azure-quickstart-templates no github.

* [VM Linux Simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [VM Windows Simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM de imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementar um conjunto de VMs que precisam se comunicar dentro do Azure
Esse cenário somente em nuvem é um cenário típico para fins de treinamento e demonstração, no qual o software que representa o cenário de demonstração/treinamento é distribuído entre várias VMs. Os diferentes componentes instalados em diferentes VMs precisam se comunicar uns com os outros. Novamente, nesse cenário, nenhuma comunicação de rede local ou cenário entre instalações é necessário.

Este cenário é uma extensão da instalação descrita no capítulo [VM única com um cenário de demonstração/treinamento do SAP NetWeaver][planning-guide-7.1] deste documento. Nesse caso, mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo a seguir, a estrutura de treinamento consiste em uma VM SAP ASCS/SCS, uma VM executando um DBMS e uma VM de instância de servidor de aplicativos SAP.

Antes de criar esse cenário, você precisa pensar sobre configurações básicas, conforme já praticado no cenário anterior.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nomeação de grupos de recursos e máquinas virtuais
Todos os nomes de grupos de recursos devem ser exclusivos. Desenvolva seu próprio esquema de nomenclatura de seus recursos, como `<rg-name`>-sufixo.

O nome da máquina virtual deve ser exclusivo dentro do grupo de recursos.

#### <a name="setup-network-for-communication-between-the-different-vms"></a>Instalação de rede para comunicação entre as diferentes VMs
![Conjunto de VMs em uma Rede Virtual do Azure][planning-guide-figure-1900]

Para evitar conflitos de nome com clones das mesmas estruturas de treinamento/demonstração, você precisa criar uma Rede Virtual do Azure para cada estrutura. A resolução de nomes DNS será fornecida pelo Azure, ou você pode configurar seu próprio servidor DNS fora do Azure (não será mais discutido aqui). Neste cenário, não configuraremos nosso próprio DNS. A comunicação por meio de nomes do host será habilitada para todas as máquinas virtuais dentro de uma Rede Virtual do Azure.

Os motivos para separar estruturas de treinamento ou demonstração por redes virtuais, e não apenas pelos grupos de recursos, podem ser:

* A estrutura da SAP, como foi configurada, precisa de sua própria AD/OpenLDAP, e um servidor de domínio precisa fazer parte de cada uma das estruturas.  
* A estrutura da SAP, como foi configurada, tem componentes que precisam trabalhar com endereços IP fixos.

Mais detalhes sobre Redes Virtuais do Azure e como defini-las podem ser encontrados [neste artigo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantar VMs SAP com conectividade de rede corporativa (entre instalações)
Você executa uma estrutura da SAP e deseja dividir a implantação entre bare-metal para servidores high-end DBMS, ambientes virtualizados locais para camadas de aplicativo, além de IaaS do Azure e sistemas SAP menores, de duas camadas, configurados. A hipótese fundamental é que os sistemas SAP em uma estrutura da SAP precisem se comunicar entre si e com muitos outros componentes de software implantados na empresa, independentemente da forma de implantação. Não deve haver tampouco nenhuma diferença introduzida pela forma de implantação para o usuário final conectando-se à GUI da SAP ou outras interfaces. Essas condições só podem ser atendidas quando temos o Active Directory/OpenLDAP e serviços DNS locais estendidos aos sistemas do Azure por meio de conectividade de site a site/multissite ou conexões privadas, como a Rota Expressa do Azure.

Para obter mais informações sobre os detalhes da implementação do SAP no Azure, recomendamos que você leia o capítulo [Conceitos da implantação de Somente Nuvem das instâncias do SAP][planning-guide-7] deste documento, que explica algumas das construções básicas do Azure e como elas devem ser usadas com os aplicativos SAP no Azure.

### <a name="scenario-of-a-sap-landscape"></a>Cenário de uma estrutura da SAP
O cenário entre instalações pode ser descrito como nos gráficos abaixo:

![Conexão site a site entre ativos locais e no Azure][planning-guide-figure-2100]

O cenário mostrado acima descreve um cenário no qual o AD/OpenLDAP e DNS locais são estendidos para o Azure. No lado local, um determinado intervalo de endereços IP é reservado para cada assinatura do Azure. O intervalo de endereços IP será atribuído a uma Rede Virtual do Azure no lado do Azure.

#### <a name="security-considerations"></a>Considerações de segurança
O requisito mínimo é o uso de protocolos de comunicação segura, como SSL/TLS, para acesso via navegador ou conexões VPN para acesso ao sistema para os serviços do Azure. A suposição é que as empresas administram a conexão VPN entre sua rede corporativa e o Azure de modo muito diferente. Algumas empresas podem, cegamente, abrir todas as portas. Outras empresas podem querer ser muito precisas em relação às portas que precisam abrir, etc.

As portas de comunicação SAP típicas estão listadas na tabela a seguir. Basicamente, é suficiente abrir a porta do gateway da SAP.

| O Barramento de | Número da Porta | Exemplo `<nn`> = 01 | Intervalo Padrão (Mín-Máx) | Comentário |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` confira * |3201 |3200 – 3299 |Dispatcher SAP, usado pela GUI da SAP para Windows e Java |
| Servidor de mensagens |sapms`<sid`> confira ** |3600 |número livre de sapms`<anySID`> |sid = ID do sistema SAP |
| Gateway |sapgw`<nn`> confira * |3301 |livre |Gateway SAP, usado para comunicação CPIC e RFC |
| Roteador SAP |sapdp99 |3299 |livre |Apenas os nomes de serviço CI (instância central) podem ser reatribuídos em /etc/services, para um valor arbitrário, após a instalação. |

*) nn = Número da Instância SAP

**) sid = ID do sistema SAP

As informações mais detalhadas sobre as portas necessárias, para os diferentes produtos ou serviços do SAP, para os produtos do SAP podem ser encontradas aqui <http://scn.sap.com/docs/DOC-17124>.
Com este documento, você deve ser capaz de abrir portas dedicadas no dispositivo VPN necessárias para cenários e produtos específicos da SAP.

Outras medidas de segurança ao implantar VMs em um cenário como esse poderiam ser criar um [Grupo de Segurança de Rede][virtual-networks-nsg] para definir as regras de acesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Lidando com séries de máquina virtual diferentes
Durante os últimos 12 meses, a Microsoft adicionou muitos outros tipos de VM diferentes no número de vCPUs, na memória ou, mais importante, no hardware em que são executadas. Nem todas essas VMs são compatíveis com o SAP (confira os tipos de VM com suporte na Nota do SAP [1928533]). Algumas dessas VMs são executadas em diferentes gerações de hardware de host. Essas gerações de hardware do host estão sendo implantadas na granularidade de uma Unidade de Escala do Azure. Isso significa que podem ocorrer casos em que os diferentes tamanhos de VM escolhidos por você não podem ser executados na mesma unidade de escala. Um conjunto de disponibilidade é limitado na capacidade de abranger unidades de escala com base em hardware diferente.  Por exemplo Se você quiser executar o DBMS nas VMs A5-A11 e a camada do aplicativo SAP nas VMs da Série G, será forçado a implantar um único sistema SAP ou diferentes sistemas SAP em diferentes Conjuntos de Disponibilidade.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimindo em uma impressora de rede local da instância SAP no Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Imprimindo via TCP/IP no cenário entre instalações
Configurar suas impressoras de rede com base em TCP/IP locais em uma VM do Azure é, no geral, igual ao que ocorre em sua rede corporativa, pressupondo que você tenha um túnel VPN site a site ou conexão de Rota Expressa estabelecida.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para fazer isso:
>
> * Algumas impressoras de rede vêm com um assistente de configuração que torna mais fácil configurar sua impressora em uma VM do Azure. Se nenhum software assistente foi distribuído com a impressora, o modo "manual" de configurá-la é criar uma nova porta de impressora TCP/IP.
> * Abra o Painel de Controle -> Dispositivos e Impressoras -> Adicionar uma impressora
> * Escolha Adicionar uma impressora usando um endereço TCP/IP ou nome do host
> * Digite o endereço IP da impressora
> * A porta de impressora padrão é 9100
> * Se necessário, instale manualmente o driver de impressora apropriado.
>
> ![Linux][Logo_Linux] Linux
>
> * assim como para o Windows, apenas siga o procedimento padrão para instalar uma impressora de rede
> * basta seguir os guias públicos do Linux para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.
>
>

- - -
![Impressão em rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada em host por SMB (impressora compartilhada) em cenário entre instalações
Impressoras baseadas em host não são compatíveis com rede por design. Mas uma impressora baseada em host pode ser compartilhada entre computadores em uma rede, desde que a impressora esteja conectada a um computador ligado. Conecte sua rede corporativa via site a site ou Rota Expressa e compartilhe a impressora local. O protocolo SMB usa NetBIOS em vez de DNS como serviço de nomes. O nome do host NetBIOS pode ser diferente do nome do host DNS. O caso padrão é com o nome do host NetBIOS e o nome do host DNS sendo idênticos. O domínio DNS não faz sentido no namespace do NetBIOS. Da mesma forma, o nome do host DNS totalmente qualificado consistindo no nome do host DNS e no domínio DNS não deve ser usado no namespace do NetBIOS.

O compartilhamento de impressora é identificado por um nome exclusivo na rede:

* Nome do host SMB (sempre necessário).
* Nome do host do compartilhamento (sempre necessário).
* Nome do domínio se o compartilhamento de impressora não estiver no mesmo domínio que o sistema SAP.
* Além disso, um nome de usuário e uma senha podem ser necessárias para acessar o compartilhamento de impressora.

Como:

- - -
> ![Windows][Logo_Windows] Windows
>
> Compartilhe a impressora local.
> Na VM do Azure, abra o Windows Explorer e digite o nome do compartilhamento da impressora.
> Um assistente de instalação de impressora vai guiá-lo pelo processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre configuração de impressoras de rede no Linux ou inclusão de um capítulo sobre impressão no Linux. Isso funcionará da mesma forma em uma VM Linux do Azure, desde que a VM seja parte de um VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL <https://access.redhat.com/documentation/pt-br/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento de impressora)
No Azure, a capacidade dos Serviços de Área de Trabalho Remota de fornecer aos usuários acesso a seus dispositivos de impressora local em uma sessão remota não está disponível.

- - -
> ![Windows][Logo_Windows] Windows
>
> Mais detalhes sobre como imprimir com o Windows podem ser encontrados aqui: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração de sistemas SAP do Azure ao Sistema de Correção e Transporte (TMS) para cenáros entre instalações
O Sistema de Alteração e Transporte SAP (TMS) deve ser configurado para exportar e importar solicitações de transporte entre sistemas na estrutura. Suponhamos que as instâncias de desenvolvimento de um sistema SAP (DES) estejam localizadas no Azure enquanto a garantia de qualidade e os sistemas de produção (PRD) sejam locais. Além disso, pressupomos que exista um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurando o domínio de transporte
Configure seu domínio de transporte no sistema designado por você como o controlador de domínio de transporte, conforme descrito em [Configurando o controlador de domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Um TMSADM de usuário do sistema será criado e o destino RFC necessário será gerado. Você pode verificar essas conexões RFC usando a transação SM59. A resolução do nome do host deve estar habilitada em seu domínio de transporte.

Como:

* Em nosso cenário, decidimos que o sistema QAS local será o controlador de domínio CTS. Chame a transação STMS. A caixa de diálogo TMS é exibida. Uma caixa de diálogo Configurar Domínio de Transporte é exibida. (Essa caixa de diálogo só aparece se você ainda não tiver configurado um domínio de transporte.)
* Verifique se o TMSADM do usuário criado automaticamente está autorizado (SM59 -> Conexão ABAP -> TMSADM@E61.DOMAIN_E61 -> Detalhes -> Utilitários(M) -> Teste de Autorização). A tela inicial da transação STMS deve mostrar que esse sistema SAP está funcionando agora como o controlador do domínio de transporte, conforme mostrado aqui:

![Tela inicial da transação STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte
A sequência de inclusão de um sistema SAP em um domínio de transporte será semelhante ao seguinte:

* No sistema de desenvolvimento no Azure, vá para o sistema de transporte (Cliente 000) e chame a transação STMS. Escolha Outra Configuração na caixa de diálogo e continue com Incluir o Sistema no Domínio. Especifique o controlador de domínio como o host de destino ([Incluindo sistemas SAP no domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema está agora aguardando para ser incluído no domínio de transporte.
* Por motivos de segurança, você precisa, em seguida, voltar para o controlador de domínio para confirmar a solicitação. Escolha Visão Geral do Sistema e Aprovação do sistema em espera. Em seguida, confirme o prompt e a configuração será distribuída.

Esse sistema SAP agora contém as informações necessárias sobre todos os outros sistemas SAP no domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados a todos os outros sistemas SAP e o sistema SAP é inserido no perfil de transporte do programa de controle de transporte. Verifique se os RFCs e o acesso ao diretório de transporte do domínio funcionam.

Continue com a configuração do seu sistema de transporte normalmente, conforme descrito na documentação [Sistema de Alteração e Transporte](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Como:

* Certificar-se de que o STMS local está configurado corretamente.
* Certificar-se de que o nome do host do controlador de domínio de transporte pode ser resolvido pela máquina virtual no Azure e vice-versa.
* Chamar a transação STMS -> Outras Configurações -> Incluir Sistema no Domínio.
* Confirmar a conexão no sistema TMS local.
* Configurar camadas, grupos e rotas de transporte como de costume.

Em cenários entre instalações conectados site a site, a latência entre locais e o Azure ainda pode ser significativa. Se seguirmos a sequência de transportar objetos através de sistemas de desenvolvimento e teste para produção ou pensarmos sobre como aplicar os transportes ou pacotes de suporte aos diferentes sistemas, perceberemos que, dependendo do local do diretório de transporte central, alguns dos sistemas encontrarão alta latência ao ler ou gravar dados no diretório de transporte central. A situação é semelhante a configurações de estrutura da SAP em que os diferentes sistemas estão distribuídos por diferentes data centers com distância significativa entre os data centers.

Para contornar essa latência e fazer com que os sistemas trabalhem rapidamente na leitura ou gravação de ou para o diretório de transporte, você pode configurar dois domínios de transporte STMS (um com os sistemas locais e o outro com os sistemas no Azure) e então vincular os domínios de transporte. Verifique esta documentação que explica os princípios por trás deste conceito no SAP TMS: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Como:

* Configurar um domínio de transporte em cada localização (local e no Azure) usando a transação STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Vincular os domínios com um link de domínio e confirmar o vínculo entre os dois domínios.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuir a configuração para o sistema vinculado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizadas no Azure e localmente (entre instalações)
O tráfego RFC entre os sistemas locais e que estão no Azure precisa funcionar. Para configurar uma conexão, chame a transação SM59 em um sistema de origem em que você precisa definir uma conexão RFC com o sistema de destino. A configuração é semelhante à configuração padrão de uma conexão RFC.

Supomos que no cenário entre instalações, as VMs executando sistemas SAP que precisarem se comunicar uns com os outros estão no mesmo domínio. Portanto, a configuração de uma conexão RFC entre os sistemas SAP não difere das etapas de configuração e entradas em cenários locais.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acessando compartilhamentos de arquivos 'locais' de instâncias SAP localizadas no Azure ou vice-versa
As instâncias SAP localizadas no Azure precisam acessar compartilhamentos de arquivos que estão nas instalações corporativas. Além disso, as instâncias SAP locais precisam acessar compartilhamentos de arquivos que estão localizados no Azure. Para habilitar os compartilhamentos de arquivos, você deve configurar as permissões e opções de compartilhamento no sistema local. Certifique-se de abrir as portas na conexão VPN ou Rota Expressa entre o Azure e seu datacenter.

## <a name="supportability"></a>Capacidade de suporte
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Solução de monitoramento do Azure para SAP
Para habilitar o monitoramento de sistemas SAP críticos no Azure, as ferramentas de monitoramento SAP SAPOSCOL ou Agente de Host SAP retiram dados do host de Serviço de Máquina Virtual do Azure por meio de uma Extensão de Monitoramento do Azure para SAP. Já que as demandas da SAP eram muito específicas para aplicativos SAP, a Microsoft decidiu não implementar genericamente a funcionalidade necessária no Azure, mas sim deixar para os clientes a tarefa de implantar as configurações e os componentes de monitoramento necessários em suas máquinas virtuais em execução no Azure. No entanto, o gerenciamento de ciclo de vida e a implantação dos componentes de monitoramento serão automatizados principalmente pelo Azure.

#### <a name="solution-design"></a>Design da solução
A solução desenvolvida para habilitar o Monitoramento da SAP baseia-se na arquitetura do Agente de VM do Azure e do framework de extensão. A ideia do Agente de VM e do framework de extensão é permitir a instalação de aplicativos de software disponíveis na Galeria de extensão de VM do Azure em uma VM. A ideia do princípio por trás desse conceito é permitir (em casos como a extensão de monitoramento do Azure para SAP), a implantação de uma funcionalidade especial em uma VM e a configuração desse software, no momento da implantação.

Desde fevereiro de 2014, o 'Agente de VM do Azure' que permite a manipulação de extensões de VM do Azure específicas dentro da VM é injetado em VMs do Windows por padrão na criação de VMs no Portal do Azure. No caso do Linux SUSE ou Red Hat, o agente de VM já é parte da imagem do Azure Marketplace. Caso alguém carregasse uma VM Linux do local para o Azure, o agente de VM deveria ser instalado manualmente.

Blocos de construção básicos da Solução de monitoramento no Azure para SAP se parece com o seguinte:

![Componentes de extensão do Microsoft Azure][planning-guide-figure-2400]

Conforme mostrado no diagrama de bloco acima, uma parte da solução de monitoramento para SAP é hospedada na Imagem de VM do Azure e Galeria de extensões do Azure, que é um repositório global replicado gerenciado pelo Azure Operations. É responsabilidade da equipe de MS/SAP conjunta trabalhar para que a implementação da SAP no Azure funcione com o Azure Operations para publicar novas versões da Extensão de Monitoramento do Azure para SAP. Essa extensão de monitoramento do Azure para SAP usará a extensão do MAD (Diagnóstico do Microsoft Azure) ou o LAD (Diagnóstico do Linux Azure) para obter as informações necessárias.

Quando você implanta uma nova VM do Windows, o 'Agente de VM do Azure' é adicionado automaticamente à VM. A função desse agente é coordenar o carregamento e a configuração das extensões do Azure para o monitoramento de sistemas SAP NetWeaver. Para VMs Linux, o Agente de VM do Azure já é parte da imagem do SO do Azure Marketplace.

No entanto, há uma etapa que ainda precisa ser executada pelo cliente. Esta é a habilitação e configuração de coleta de desempenho. O processo relacionado com a configuração é automatizado por um script do PowerShell ou comando da CLI. O script do PowerShell pode ser baixado na Central de Script do Microsoft Azure, como descrito no [Guia de Implantação][deployment-guide].

A arquitetura geral da solução de monitoramento do Azure para SAP é semelhante a:

![Solução de monitoramento do Azure para SAP NetWeaver][planning-guide-figure-2500]

**Para obter instruções exatas e ver as etapas detalhadas de como usar esses cmdlets do PowerShell ou o comando CLI durante as implantações, siga as instruções fornecidas no [Guia de Implantação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração de instância SAP localizada no Azure ao SAProuter
Instâncias SAP em execução no Azure precisam ser acessíveis também do SAProuter.

![Conexão de rede de roteador SAP][planning-guide-figure-2600]

Um SAProuter habilitará a comunicação TCP/IP entre os sistemas participantes se não houver conexão direta por IP. Isso tem a vantagem de que nenhuma conexão de ponta a ponta entre os parceiros de comunicação é necessária no nível da rede. O SAProuter está escutando na porta 3299, por padrão.
Para se conectar a instâncias SAP por meio de um SAProuter, você precisa fornecer o nome do host e a cadeia de caracteres do SAProuter ao realizar qualquer tentativa de conexão.

## <a name="sap-netweaver-as-java"></a>Servidor de Aplicativos para Java do SAP NetWeaver
Até aqui, o foco do documento tem sido o SAP NetWeaver em geral ou a pilha ABAP do SAP NetWeaver. Nesta pequena seção, são listadas as considerações específicas para a pilha Java da SAP. Um dos mais importantes aplicativos baseados exclusivamente em Java do SAP NetWeaver é o SAP Enterprise Portal. Outros aplicativos com base em SAP NetWeaver, como o SAP PI e o SAP Solution Manager usam tanto o ABAP do SAP NetWeaver quanto pilhas Java. Portanto, é certamente necessário considerar também aspectos específicos relacionados à pilha Java do SAP NetWeaver.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
A configuração de um Portal SAP em uma Máquina Virtual do Azure não será diferente de uma instalação local se você estiver implantando em cenários entre instalações. Como o DNS é feito por local, as configurações de porta das instâncias individuais podem ser feitas do mesmo modo que são configuradas localmente. As recomendações e as restrições descritas neste documento até este ponto se aplicam a um aplicativo como o SAP Enterprise Portal ou a pilha Java do SAP NetWeaver em geral.

![Portal SAP exposto][planning-guide-figure-2700]

Um cenário de implantação especial por alguns clientes é a exposição direta do SAP Enterprise Portal à Internet enquanto o host de máquina virtual está conectado à rede da empresa por meio de túnel VPN site a site ou Rota Expressa. Para esse cenário, você precisa certificar-se de que portas específicas estejam abertas e não estejam bloqueadas por um grupo de segurança de rede ou firewall. A mesma mecânica precisa ser aplicada quando você deseja se conectar a uma instância SAP Java do local em um cenário somente em nuvem.

O URI inicial do portal é http(s):`<Portalserver`>:5XX00/irj em que a porta é formada por mais de 50000 (número do sistema × 100). O URI padrão do portal do sistema SAP 00 é `<dns name`>.`<azure region`>.Cloudapp.azure.com:PublicPort/irj. Para obter mais detalhes, veja <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuração de ponto de extremidade][planning-guide-figure-2800]

Se você quiser personalizar a URL e/ou as portas do seu SAP Enterprise Portal, consulte esta documentação:

* [Alterar URL do portal](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar os Números de porta padrão, Números de porta do portal](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

<a name="7cf991a1-badd-40a9-944e-7baae842a058"></a>
## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>HA (alta disponibilidade) e DR (recuperação de desastre) para SAP NetWeaver em execução em máquinas virtuais do Azure
### <a name="definition-of-terminologies"></a>Definição de terminologias
O Termo **HA (alta disponibilidade)** geralmente está relacionado a um conjunto de tecnologias que minimiza as interrupções da TI, proporcionando a continuidade dos serviços de TI por meio de componentes redundantes e tolerantes a falhas ou protegidos contra failover no **mesmo** data center. Em nosso caso, dentro de uma Região do Azure.

**A DR (recuperação de desastre)** também está voltada para minimizar as interrupções dos serviços de TI e a recuperação de dados, mas em **diferentes** data centers, que estão normalmente localizados a centenas de quilômetros de distância. Em nosso caso, geralmente entre diferentes Regiões do Azure na mesma região geopolítica ou como estabelecidos por você, como cliente.

### <a name="overview-of-high-availability"></a>Visão Geral de Alta Disponibilidade
É possível separar a discussão sobre a alta disponibilidade da SAP no Azure em duas partes:

* **Alta disponibilidade da infraestrutura do Azure**, por exemplo, alta disponibilidade de computação (VMs), rede, armazenamento etc., e seus benefícios para aumentar a disponibilidade do aplicativo SAP.
* **Alta disponibilidade de aplicativos SAP**, por exemplo, alta disponibilidade de componentes de software SAP:
  * Servidores de aplicativos SAP
  * Instância ASCS/SCS SAP
  * servidor do BD

e como ele pode ser combinado com alta disponibilidade de infraestrutura do Azure.

Alta disponibilidade da SAP no Azure tem algumas diferenças em comparação com alta disponibilidade da SAP em um ambiente físico ou virtual local. O seguinte documento do SAP descreve as configurações padrão da Alta Disponibilidade do SAP nos ambientes virtualizados no Windows: <http://scn.sap.com/docs/DOC-44415>. Há não configuração de HA da SAP integrada com sapinst, para Linux, da mesma maneira que existe para o Windows. Em relação à alta disponibilidade local do SAP para Linux, encontre mais informações aqui: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure
Não há nenhum SLA de VM única disponível em máquinas virtuais do Azure no momento. Para ter uma ideia de como a disponibilidade de uma única VM pode ser, você pode simplesmente compilar o produto dos diferentes SLAs do Azure disponíveis: <https://azure.microsoft.com/support/legal/sla/>.

A base para o cálculo é 30 dias por mês, ou 43.200 minutos. Portanto, 0,05% de tempo de inatividade corresponde a 21,6 minutos. Conforme ocorre normalmente, a disponibilidade dos serviços diferentes se multiplicará da seguinte maneira:

(Serviço de disponibilidade nº 1/100) * (Serviço de disponibilidade nº 2/100) * (Serviço de disponibilidade nº 3/100) *…

Assim como:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidade de VM (máquina virtual)
Há dois tipos de eventos de plataforma do Azure que podem afetar a disponibilidade das máquinas virtuais: manutenção planejada e manutenção não planejada.

* Eventos de manutenção planejada são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma na qual suas máquinas virtuais são executadas.
* Eventos de manutenção não planejada ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta algum tipo de falha. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure migrará automaticamente sua máquina virtual do servidor físico não íntegro hospedando sua máquina virtual para um servidor físico íntegro. Esses eventos são raros, mas podem também reinicializar a sua máquina virtual.

Mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância de Armazenamento do Azure
Os dados da sua Conta de Armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o SLA do Armazenamento do Azure mesmo diante de falhas transitórias de hardware

Já que o armazenamento do Azure está mantendo 3 imagens dos dados por padrão, RAID5 ou RAID1 em vários discos do Azure não são necessárias.

Mais detalhes podem ser encontrados neste artigo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a Reinicialização de VM da infraestrutura do Azure para atingir "maior disponibilidade" de aplicativos SAP
Se você decidir não usar as funcionalidades como WSFC (Clustering de Failover do Windows Server) ou um equivalente em Linux (este último uma ainda não tem suporte no Azure em combinação com software SAP), a Reinicialização de VM do Azure será usada para proteger o sistema SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure em geral.

> [!NOTE]
> É importante mencionar que a Reinicialização de VM do Azure protege principalmente as VMs, NÃO os aplicativos. A Reinicialização de VM não oferece alta disponibilidade para aplicativos SAP, mas oferece um certo nível de disponibilidade da infraestrutura e, portanto, indiretamente, "maior disponibilidade" de sistemas SAP. Não há, tampouco, nenhum SLA para o tempo necessário para reiniciar uma VM após uma interrupção de host planejada ou não planejada. Portanto, esse método de 'alta disponibilidade' não é adequado para componentes críticos de um sistema SAP como (A)SCS ou DBMS.
>
>

Outro elemento de infraestrutura importante para alta disponibilidade é o armazenamento. Por exemplo O SLA do Armazenamento do Azure tem disponibilidade de 99,9%. Se alguém implantar todas as VMs com seus discos em uma única Conta de Armazenamento do Azure, a indisponibilidade potencial do Armazenamento do Azure causará indisponibilidade de todas as VMs localizadas na Conta de Armazenamento do Azure e também de todos os componentes SAP em execução dentro dessas VMs.  

Em vez de colocar todas as VMs em uma única Conta de Armazenamento do Azure, você também pode contas de armazenamento dedicado para cada VM e, dessa forma, aumentar a disponibilidade geral da VM e do aplicativo SAP usando várias Contas de Armazenamento do Azure independentes.

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa alta disponibilidade de infraestrutura do Azure poderia ter esta aparência:

![Utilizando a alta disponibilidade da infraestrutura do Azure para atingir "maior" disponibilidade de aplicativos SAP][planning-guide-figure-2900]

Para componentes críticos da SAP, alcançamos o seguinte até agora:

* Alta disponibilidade de servidores de aplicativos (SA) SAP

As instâncias do servidor de aplicativos SAP são componentes redundantes. Cada instância de SA SAP está implantada em sua própria VM, que está em execução nos diferentes Domínios de Falha e Atualização do Azure (confira os capítulos [Domínios de Falha][planning-guide-3.2.1] e [Domínios de Atualização][planning-guide-3.2.2]). Isso é garantido usando os Conjuntos de Disponibilidade do Azure (confira o capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3]). Potencial indisponibilidade planejada ou não planejada de um domínio de falha ou atualização do Azure causará indisponibilidade de um número restrito de VMs com suas instâncias SAP.
Cada instância de SA SAP será colocada em sua própria conta de Armazenamento do Azure – a indisponibilidade potencial de uma Conta de Armazenamento do Azure causará indisponibilidade de apenas uma VM com sua instância de SA SAP. No entanto, lembre-se de que há um limite de Contas de Armazenamento do Azure dentro de uma assinatura do Azure. Para assegurar o início automático da instância do (A)SCS após a reinicialização da VM, defina o parâmetro Autostart no perfil para iniciar a instância do (A)SCS descrito no capítulo [Usando a Inicialização automática para as instâncias do SAP][planning-guide-11.5].
Leia também o capítulo [Alta Disponibilidade para Servidores do Aplicativo SAP][planning-guide-11.4.1] para obter mais detalhes.

* *Maior* disponibilidade de instância do (A)SCS SAP

Aqui, utilizamos a Reinicialização de VM do Azure para proteger a VM com instância do (A)SCS SAP instalada. No caso de tempo de inatividade planejado ou não planejado de servidores do Azure, as VMs serão reiniciadas em outro servidor disponível. Como mencionado anteriormente, a Reinicialização de VM do Azure protege principalmente as VMs e NÃO os aplicativos, neste caso, a instância do (A)SCS. Por meio da Reinicialização de VM, alcançaremos indiretamente "maior disponibilidade" da instância do (A)SCS SAP. Para assegurar o início automático da instância do (A)SCS após a reinicialização da VM, defina o parâmetro Autostart no perfil para iniciar a instância do (A)SCS descrito no capítulo [Usando a Inicialização automática para as instâncias do SAP][planning-guide-11.5]. Isso significa que a instância do (A)SCS como um SPOF (ponto único de falha) em execução em uma única VM será o fator determinante para a disponibilidade de toda a estrutura da SAP.

* *Maior* disponibilidade de servidor DBMS

Aqui, de modo semelhante ao caso de uso de instância do (A)SCS SAP, utilizamos a do Azure para proteger a VM com software do DBMS instalado, e podemos obter "maior disponibilidade" de software DBMS por meio da Reinicialização de VM.
DBMS em execução em uma única VM também é um SPOF, e é o fator determinante para a disponibilidade de toda a estrutura da SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Alta disponibilidade de aplicativo SAP no Azure IaaS
Para obter alta disponibilidade total no sistema SAP, é necessário proteger todos os componentes críticos do sistema SAP, por exemplo, de servidores de aplicativos SAP redundantes e componentes exclusivos (por exemplo, ponto único de falha), como a instância do (A)SCS SAP e DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicativos SAP
Para as instâncias de caixa de diálogo/servidores de aplicativos SAP, não é necessário pensar em uma solução de alta disponibilidade específica. A alta disponibilidade é obtida simplesmente pela redundância e, portanto, pela existência de um número suficiente deles em máquinas virtuais diferentes. Eles devem ser todos colocados no mesmo conjunto de disponibilidade do Azure, para evitar que as VMs possam ser atualizadas ao mesmo tempo durante o tempo de inatividade da manutenção planejada. A funcionalidade essencial baseada nos diferentes Domínios de Falha e Atualização em uma Unidade de Escala do Azure já foi introduzida no capítulo [Domínios de Atualização][planning-guide-3.2.2]. Os Conjuntos de Disponibilidade do Azure foram apresentados no capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3] deste documento.

Não há um número infinito de domínios de falha e atualização que possam ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure. Isso significa que, colocando um número de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde, mais de uma VM termina no mesmo domínio de atualização ou falha

Implantando algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas e supondo que temos 5 domínios de atualização, o quadro a seguir surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![Alta disponibilidade dos servidores de aplicativos SAP no Azure][planning-guide-figure-3000]

Mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Alta disponibilidade para a instância do (A)SCS SAP no Windows
O WSFC (Cluster de Failover do Windows Server) é uma solução usada com frequência para proteger a instância do (A)SCS SAP. Ele também é integrado ao sapinst na forma de uma "instalação de alta disponibilidade". Neste momento, a infraestrutura do Azure não é capaz de fornecer a funcionalidade para configurar o Cluster de Failover do Windows Server necessário do mesmo modo como isso é feito localmente.

Desde janeiro de 2016, a plataforma de nuvem do Azure executando o sistema operacional Windows não oferece a possibilidade de usar um volume compartilhado clusterizado em um disco compartilhado entre duas VMs do Azure.

Uma solução válida, no entanto, é o uso de software de terceiros que fornece um volume compartilhado pela replicação síncrona e transparente de disco, que pode ser integrada ao WSFC. Essa abordagem implica que apenas o nó de cluster ativo é capaz de acessar uma das cópias de disco em um ponto no tempo. Desde janeiro de 2016, essa configuração de alta disponibilidade tem suporte para proteger a instância do (A)SCS SAP no SO convidado Windows em VMs do Azure, em combinação com o software de terceiros SIOS DataKeeper.

A solução SIOS DataKeeper fornece um recurso de cluster de disco compartilhado para Clusters de Failover do Windows por ter:

* Um VHD do Azure adicionais anexado a cada uma das máquinas virtuais (VMs) que estão em uma configuração de Cluster do Windows
* A Edição de Cluster do SIOS DataKeeper em execução em ambos os nós de VM
* A Edição de Cluster do SIOS DataKeeper configurada de forma que ela faça, de modo síncrono, o espelhamento do conteúdo do VHD adicional anexado volume das VMs de origem para o volume adicional anexado ao VHD da VM de destino.
* O SIOS DataKeeper está abstraindo os volumes locais de origem e de destino e apresentando-os ao Cluster de Failover do Windows como um único disco compartilhado.

Você pode encontrar todos os detalhes sobre como instalar um Cluster de Failover do Windows com o SIOS Datakeeper e o SAP no white paper [Clustering SAP ASCS Instance using Windows Server Failover Cluster on Azure with SIOS DataKeeper][ha-guide-classic] (Clustering da instância do ASCS SAP usando o Cluster de Failover do Windows Server no Azure com o SIOS DataKeeper).

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Alta disponibilidade para a instância do (A)SCS SAP em Linux
Desde dezembro de 2015, também não há equivalente ao WSFC do disco compartilhado para VMs Linux no Azure. Soluções alternativas usando software de terceiros como SIOS para Windows ainda não são validadas para execução da SAP em Linux no Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta disponibilidade para a instância do banco de dados SAP
A configuração típica de alta disponibilidade de DMBS SAP é baseada nas duas VMs DBMS nas quais a funcionalidade de alta disponibilidade de DBMS é usada para replicar dados da instância de DBMS ativa para a segunda VM em uma instância de DBMS passiva.

Funcionalidade da Recuperação de desastre e da Alta Disponibilidade para o DBMS em geral e o DBMS específico é descrita no [Guia de Implantação do DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema SAP completo
Aqui estão dois exemplos de uma arquitetura de alta disponibilidade SAP NetWeaver completa no Azure - um para Windows e outro para Linux.
Os conceitos, conforme explicado abaixo, talvez precisem ser um pouco comprometidos quando você implantar muitos sistemas SAP e o número de VMs implantadas estiver excedendo o limite máximo de contas de armazenamento por assinatura. Nesses casos, os VHDs de VMs devem ser combinados em uma conta de armazenamento. Normalmente você faria isso pela combinação de VHDs de VMs das camadas de aplicativo SAP de sistemas SAP diferentes.  Também combinamos diferentes VHDs de diferentes VMs DBMS de sistemas SAP diferentes em uma conta de armazenamento do Azure. Mantendo os limites IOPS das Contas de Armazenamento do Azure em mente (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] no Windows
![Arquitetura de alta disponibilidade de aplicativos SAP NetWeaver com o SQL Server no Azure IaaS][planning-guide-figure-3200]

As seguintes construções do Azure são usadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e a aplicação de patches ao host:

* O sistema completo é implantado no Azure (necessário – a camada de DBMS, a instância de (A)SCS e a camada de aplicativo completo precisam ser executadas no mesmo local).
* O sistema completo é executado dentro de uma assinatura do Azure (obrigatório).
* O sistema completo é executado dentro de uma rede virtual do Azure (obrigatório).
* A separação das VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todas as máquinas virtuais pertencendo à mesma rede virtual.
* Todas as VMs que executam instâncias DBMS de um sistema SAP estão em um conjunto de disponibilidade. Presumimos que haja mais de uma VM executando instâncias de DBMS por sistema, já que recursos nativos de alta disponibilidade de DBMS são usados, como o SQL Server AlwaysOn ou o Oracle Data Guard.
* Todas as VMs que executam instâncias de DBMS usam sua própria conta de armazenamento. Arquivos de log e arquivos de dados de DBMS são replicados de uma conta de armazenamento para outra usando funções de alta disponibilidade de DBMS que sincronizam os dados. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows SQL, mas não do serviço do SQL Server inteiro.
* Todas as VMs que executam uma instância do (A)SCS de um sistema SAP estão em um conjunto de disponibilidade. Dentro dessas VMs, é configurar o WSFC (Cluster de Failover do Windows Server) para proteger a instância do (A)SCS.
* Todas as VMs que executam instâncias do (A)SCS usam sua própria conta de armazenamento. Os arquivos de instância do (A)SCS e a pasta global do SAP são replicados de uma conta de armazenamento para outra usando a replicação do SIOS DataKeeper. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows (A)SCS, mas não do serviço do (A)SCS inteiro.
* TODAS as VMs que representam a camada do servidor de aplicativos SAP estão em um terceiro conjunto de disponibilidade.
* TODAS as VMs executando servidores de aplicativos SAP usam sua própria conta de armazenamento. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um servidor de aplicativos SAP, em que outros servidores de aplicativos SAP continuam em execução.

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] no Linux
A arquitetura para alta disponibilidade da SAP em Linux no Azure é basicamente a mesma que aquela para Windows, conforme descrito acima. Desde janeiro de 2016, porém, há duas restrições:

* somente o SAP ASE 16 tem suporte atualmente no Azure em Linux sem nenhum recurso de replicação do ASE.
* ainda não há uma solução de alta disponibilidade do (A)SCS SAP com suporte no Azure em Linux

Como consequência, desde janeiro de 2016, um sistema SAP-Linux-Azure não pode atingir a mesma disponibilidade que um sistema SAP-Windows-Azure devido à ausência de alta disponibilidade para a instância do (A)SCS e o banco de dados do SAP ASE de instância única.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Usando a inicialização automática para instâncias SAP
A SAP ofereceu a funcionalidade para iniciar as instâncias SAP imediatamente após a inicialização do SO na VM. As etapas exatas foram documentadas no Artigo da Base de Dados de Conhecimento do SAP [1909114] - Como iniciar as instâncias do SAP automaticamente usando o parâmetro Autostart. No entanto, a SAP não recomenda mais o uso da configuração, porque não há nenhum controle na ordem de reinicializações de instâncias, supondo que mais de uma VM tenha sido afetada ou várias instâncias tenham sido executadas por VM. Supondo um cenário típico do Azure de uma instância do servidor de aplicativos SAP em uma VM e o caso de uma única VM eventualmente sendo reiniciada, a Inicialização automática não é realmente crítica, e pode ser habilitado adicionando este parâmetro:

    Autostart = 1

No perfil de início da instância de SAP ABAP e/ou instância de Java.

> [!NOTE]
> O parâmetro Inicialização automática também pode ter algumas desvantagens. Mais detalhadamente, o parâmetro aciona o início de uma instância de Java ou SAP ABAP quando o serviço do Windows/Linux relacionado da instância é iniciado. Esse é certamente o caso quando o sistema operacional é inicializado. No entanto, reinicializações de serviços SAP também são uma coisa comum para a funcionalidade de gerenciamento de ciclo de vida do Software SAP, como SUM ou outras atualizações. Essas funcionalidades não estão esperando em hipótese nenhuma que uma instância seja reiniciada automaticamente. Portanto, o parâmetro Inicialização automática deve ser desabilitado antes de executar essas tarefas. O parâmetro de Inicialização automática também não deve ser usado para instâncias do SAP que estão clusterizadas, como ASCS/SCS/CI.
>
>

Consulte informações adicionais sobre a inicialização automática para instâncias SAP aqui:

* [Iniciar/parar SAP ao iniciar/parar seu servidor Unix](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciando e parando agentes de gerenciamento do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como habilitar automaticamente a inicialização automática do banco de dados HANA](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP maiores de 3 camadas
Aspectos de alta disponibilidade de configurações SAP de 3 camadas já foram discutidos nas seções anteriores. Mas e quanto a sistemas em que os requisitos do servidor DBMS são grandes demais para que ele fosse localizado no Azure, mas nos quais a camada do aplicativo SAP poderia ser implantada no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização de configurações SAP de 3 camadas
Não há suporte para dividir a camada de aplicativo em si ou o aplicativo e a camada DBMS entre o local e o Azure. Um sistema SAP é completamente implantado localmente OU no Azure. Também não há suporte para executar alguns dos servidores de aplicativos localmente e outros no Azure. Esse é o ponto de partida da discussão. Também não damos suporte à implantação dos componentes do DBMS de um sistema SAP e da camada de servidor de aplicativos SAP em duas Regiões do Azure diferentes. Por exemplo DBMS no Oeste dos EUA e camada de aplicativo SAP nos EUA Central. O motivo para não dar suporte a essas configurações é a sensibilidade de latência da arquitetura do SAP NetWeaver.

No entanto, ao longo do ano passado, parceiros de data center desenvolveram colocalizações para Regiões do Azure. Essas colocalizações geralmente são muito perto dos data centers físicos em uma Região do Azure. A curta distância e a conexão de ativos no local por meio de Rota Expressa no Azure podem resultar em uma latência menor que 2 ms. Nesses casos, é possível localizar a camada de DBMS (incluindo o armazenamento SAN/NAS) em uma dessas colocalizações e a camada de aplicativo SAP no Azure. Desde dezembro de 2015, não temos nenhuma implantação desse tipo. Mas diferentes clientes com implantações de aplicativos não SAP já estão usando essas abordagens.

### <a name="offline-backup-of-sap-systems"></a>Backup offline de sistemas SAP
Dependendo da configuração SAP escolhida (2 ou 3 camadas), pode haver necessidade de backup. O conteúdo da própria VM, além de ter um backup do banco de dados. Os backups relacionados ao DBMS devem ser feitos com métodos de banco de dados. Uma descrição detalhada dos diferentes bancos de dados pode ser encontrada no [Guia do DBMS][dbms-guide]. Por outro lado, os dados da SAP podem fazer backup de maneira offline (incluindo o conteúdo do banco de dados) conforme descrito nesta seção, ou online, conforme descrito na próxima seção.

O backup offline basicamente exigiria um desligamento da VM por meio do Portal do Azure e uma cópia do disco da VM base, além de todos os VHDs anexados à VM. Isso preservaria uma imagem da VM e seus discos associados em um ponto no tempo. É recomendável copiar os backups para outra Conta de Armazenamento do Azure. Portanto, o procedimento descrito no capítulo [Copiando discos entre as Contas de Armazenamento do Azure][planning-guide-5.4.2] deste documento seria aplicável.
Além da finalização usando o Portal do Azure, alguém também poderá fazer isso por meio do Powershell ou da CLI, como descrito aqui: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Uma restauração de estado consiste em excluir a VM base, bem como os discos originais da VM base e os VHDs montados, copiando os VHDs salvos de volta à conta de armazenamento original e, em seguida, reimplantando o sistema.
Este artigo mostra um exemplo de como fazer o script desse processo no Powershell: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se de instalar uma nova licença SAP, já que restaurar um backup de VM, conforme descrito acima, cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online de um sistema SAP
O backup do DBMS é executado com métodos específicos do DBMS, como descrito no [Guia do DBMS][dbms-guide].

O backup de outras VMs no sistema SAP pode ser feito usando a funcionalidade de Backup de máquinas virtuais do Azure. O Backup da Máquina Virtual do Azure foi introduzido no início de 2015 e ao mesmo tempo é um método padrão para fazer o backup de uma VM completa no Azure. O Backup do Azure armazena os backups no Azure e permite uma restauração de uma VM novamente.

> [!NOTE]
> Desde dezembro de 2015 o uso do Backup da VM NÃO mantém a ID exclusiva da VM que é usada para licenciamento SAP. Isso significa que uma restauração de um backup de VM exige a instalação de uma nova chave de licença da SAP, já que a VM restaurada é considerada como uma nova VM e não uma substituta da VM antiga que foi salva.
> Desde janeiro de 2016, o Backup da VM do Azure ainda não dá suporte às VMs que são implantadas com o Azure Resource Manager.
>
> ![Windows][Logo_Windows] Windows
>
> Teoricamente, as VMs que executam os bancos de dados podem sofrer backup de uma maneira consistente também, caso os sistemas DBMS ofereçam suporte ao VSS (Serviço de Cópias de Sombra do Volume do Windows <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) como, por exemplo, o SQL Server faz.
> No entanto, lembre-se de que com base nos backups de VM do Azure, as restaurações pontuais não são possíveis. Portanto, a recomendação é executar backups de bancos de dados com a funcionalidade do DBMS em vez de depender do backup de VM do Azure
>
> Para se familiarizar com o Backup da Máquina Virtual do Azure, comece aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>.
>
> Outras possibilidades são usar uma combinação do Microsoft Data Protection Manager instalado em uma VM do Azure e o Backup do Azure para backup/restauração de bancos de dados. Mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>.  
>
> ![Linux][Logo_Linux] Linux
>
> Não há nenhum equivalente ao VSS do Windows em Linux. Portanto, apenas backups consistentes com arquivo são possíveis, enquanto os consistentes com aplicativo não são. O backup do DBMS SAP deve ser feito usando a funcionalidade DBMS. O sistema de arquivos que inclui os dados relacionados ao SAP podem ser salvos, por exemplo, usando tar como descrito aqui: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como local de recuperação de desastre para estruturas da SAP de produção
Desde meados de 2014, extensões para vários componentes do Hyper-V, o System Center e o Azure permitem o uso do Azure como local de recuperação de desastre para VMs em execução local com base no Hyper-V.

Um blog detalhando como implantar essa solução está documentado aqui: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>Resumo
Os pontos principais de alta disponibilidade para sistemas SAP no Azure são:

* Neste momento, o único ponto de falha da SAP não pode ser protegido exatamente da mesma maneira como pode ser feito em implantações locais. O motivo é que os clusters desse disco compartilhado ainda não podem ser criados no Azure sem o uso de software de terceiros.
* Para a camada de DBMS, você precisa usar a funcionalidade DBMS que não depende da tecnologia de cluster de disco compartilhado. Os detalhes estão documentados no [Guia do DBMS][dbms-guide].
* Para minimizar o impacto de problemas nos domínios de falha na manutenção de host ou de infraestrutura do Azure, você deve usar conjuntos de disponibilidade do Azure:
  * É recomendável ter um conjunto de disponibilidade para a camada do aplicativo SAP.
  * É recomendável ter um conjunto de disponibilidade separado para a camada de DBMS SAP.
  * NÃO é recomendável aplicar o mesmo conjunto de disponibilidade para VMs de sistemas SAP diferentes.
* Para fins de Backup da camada do DBMS SAP, verifique o [Guia do DBMS][dbms-guide].
* Fazer backup de instâncias de caixas de diálogo SAP não faz muito sentido, já que é normalmente mais rápido reimplantar instâncias de caixa de diálogo simples.
* Fazer backup da VM que contém o diretório global do sistema SAP e com ela todos os perfis das diferentes instâncias faz sentido e deve ser executado com o Backup do Windows ou, por exemplo, tar no Linux. Como há diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2) que facilitam o backup usando versões mais recentes do Windows Server, é recomendável executar o Windows Server 2012 (R2) como sistema operacional convidado Windows.

