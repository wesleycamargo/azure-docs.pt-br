<properties
   pageTitle="SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade | Microsoft Azure"
   description="SAP NetWeaver on Windows virtual machines (VMs) – High Availability Guide (SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade)"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# SAP NetWeaver on Windows virtual machines (VMs) – High Availability Guide (SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade)

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de implantação do DBMS"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Cache para VMs e VHDs"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "RAID de software"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Armazenamento do Microsoft Azure"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Estrutura de uma implantação do RDBMS"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Alta disponibilidade e recuperação de desastre com VMs do Azure"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 e posterior"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 e versões anteriores"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Usando imagens do SQL Server do Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Resumo do SQL Server para SAP no Azure geral"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Informações específicas para o RDBMS do SQL Server"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configuração de armazenamento"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Backup e restauração"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Considerações de desempenho para backup e restauração"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Outros"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de implantação"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Recursos da SAP"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Implantando uma VM com uma imagem personalizada"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Cenário 1: Implantando uma VM com origem no Azure Marketplace para SAP"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Cenário 2: Implantando uma VM com uma imagem personalizada para SAP"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Cenários de implantação de VMs para SAP no Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Implantando cmdlets do Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Baixar e importar os cmdlets do PowerShell relevantes para SAP"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Ingressar a VM no domínio local, somente Windows"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Baixar, instalar e habilitar o agente de VM do Azure"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "CLI do Azure"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurar a extensão de monitoramento aprimorado do Azure para SAP"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Verificação de preparação para o monitoramento aprimorado do Azure para SAP"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configurar o monitoramento"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configurar proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Verificações e solução de problemas de configuração de monitoramento de ponta a ponta para SAP no Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de planejamento e implementação"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Recursos"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "HA (alta disponibilidade) e DR (recuperação de desastre) para SAP NetWeaver em execução em máquinas virtuais do Azure"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Alta disponibilidade para servidores de aplicativos SAP"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Usando a inicialização automática para instâncias SAP"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Somente em nuvem - implantações de máquinas virtuais no Azure sem dependências na rede do cliente local"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Entre instalações - implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Regiões do Azure"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Domínios de falha"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Domínios de atualização"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Conjuntos de disponibilidade do Azure"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "O conceito de máquina virtual do Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Armazenamento Premium do Azure"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Movendo uma VM do local para o Azure com um disco não generalizado"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Implantando uma VM com uma imagem específica do cliente"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparação para mover uma VM do local para o Azure com um disco não generalizado"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparação para implantar uma VM com uma imagem específica do cliente para SAP"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparando VMs com SAP para o Azure"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Diferença entre uma imagem do Azure e disco do Azure"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Carregando um VHD do local no Azure"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copiando discos entre Contas de Armazenamento do Azure"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Estrutura de VM/VHD para implantações SAP"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Definindo a montagem automática para os discos anexados"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Cenário de treinamento/demonstração de VM única com SAP NetWeaver"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Conceitos de implantação somente em nuvem de instâncias SAP"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Solução de monitoramento do Azure para SAP"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Armazenamento Premium do Azure"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Rede do Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Armazenamento: Armazenamento do Microsoft Azure e discos de dados"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "SAP NetWeaver on Windows virtual machines (VMs) – High Availability Guide (SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade)"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "Pré-requisitos"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "Recursos"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Diferenças no SAP HA entre os modelos do Azure Resource Manager e de implantação clássico"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "Grupos de recursos"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Clustering com o Azure Resource Manager em comparação com o modelo de implantação clássico"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "WSFC (Windows Server Failover Clustering)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "Modos de quórum"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "Cluster de Failover do Windows local"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "Armazenamento compartilhado"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "Resolução de nome/rede"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Cluster de Failover do Windows com o Microsoft Azure"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "Disco compartilhado no Microsoft Azure com SIOS DataKeeper"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Resolução de nomes no Microsoft Azure"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Alta disponibilidade do SAP NetWeaver no IaaS do Azure"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "Alta disponibilidade para servidores de aplicativos SAP"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "Alta disponibilidade para instâncias do SAP (A)SCS"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Alta disponibilidade para a instância do SAP (A)SCS com Cluster de Failover do Windows no Azure"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "Alta disponibilidade para a instância do DBMS"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "Possíveis cenários de implantação completa de HA"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "Preparação da infraestrutura"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "Implantando VMs com conectividade de rede corporativa (entre instalações) para uso produtivo"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "Implantação somente na nuvem de instâncias do SAP para teste/demonstração"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Rede Virtual do Azure"
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "Endereços IP do DNS"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e a instância clusterizada do DBMS"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "Configurar endereços IP estáticos para as VMs SAP"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "Configurar o endereço IP estático para o ILB (Balanceador de Carga Interno)"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Regras do balanceamento de carga do ASCS/SCS padrão para o Azure ILB (Azure Load Balancer Interno)"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Alteração das regras do balanceamento de carga padrão do ASCS/SCS para o Azure ILB (Azure Load Balancer Interno)"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "Adicionar máquinas do Windows ao domínio"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "Adicionar entradas de registro em ambos os nós de cluster usados para a instância do SAP ASCS/SCS"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "Configuração do Windows Server Failover Cluster para a instância do SAP ASCS/SCS"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "Coletar nós de cluster na configuração do cluster"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "Configurar a testemunha de compartilhamento de arquivos do cluster"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "Criar um compartilhamento de arquivos"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "Configurar o quórum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "Instalando o SIOS DataKeeper Cluster Edition para compartilhamento de disco de cluster do SAP ASCS/SCS"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Adicionar o recurso Microsoft .NET Framework 3.5"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "Instalando o SIOS DataKeeper"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "Configurar o SIOS DataKeeper"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "Instalação do sistema SAP NetWeaver"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "Instalação do SAP com instância do ASCS/SCS de alta disponibilidade"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "Criar Nome de Host Virtual para o SAP ASCS/SCS clusterizado"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "Instalar o primeiro nó do cluster do SAP"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "Modificar o perfil SAP da instância do ASCS/SCS"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "Adicionar porta de investigação"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "Instalando a instância de banco de dados"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "Instalação do segundo nó do cluster"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "Alterar o tipo de inicialização do serviço Windows da instância do SAP ERS"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "Instalação do PAS (Servidor de Aplicativos Primário) SAP"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "Instalação do AAS (Servidor de Aplicativos Adicional) SAP"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "Testando o failover da instância do SAP ASCS/SCS e a replicação do SIOS"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "Ponto de partida — a instância do SAP ASCS/SCS está em execução no Nó A do Cluster"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "Processo de failover do Nó para o Nó B"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "Resultado final — a instância do SAP ASCS/SCS está em execução no Nó B do Cluster"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "Matriz de Disponibilidade de Produto da SAP"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Gerenciar máquinas virtuais usando o PowerShell e o Gerenciador de Recursos do Azure"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Sem ciclos longos de compra, o Microsoft Azure permite que as empresas adquiram recursos de computação, armazenamento e rede sem perder muito tempo. As Máquinas Virtuais do Azure permitem que as empresas implantem no Azure aplicativos clássicos, como os baseados na SAP NetWeaver (ABAP, Java e a pilha ABAP+Java), além de aumentar a confiabilidade e disponibilidade desses aplicativos sem necessidade de mais recursos disponíveis no local. As Máquinas Virtuais do Azure também dão suporte à conectividade entre locais, o que permite que as empresas integrem ativamente as Máquinas Virtuais do Azure a seus domínios locais, suas nuvens privadas e sua estrutura do sistema da SAP.


Este documento detalha todas as etapas necessárias para implantar sistemas SAP altamente disponíveis no Azure usando nosso novo método com o novo modelo de implantação do Azure Resource Manager. O guia orientará você nas etapas principais:


- Localizar os guias e as Notas de instalação apropriados do SAP, listados mais adiante na seção [Recursos][sap-ha-guide-2]. O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

- Entender a diferença entre o modelo de implantação clássico do Azure e o novo modelo de implantação do Azure Resource Manager.

- Entender os modos de quórum do WSFC (Windows Server Failover Cluster) para que você possa escolher o modelo adequado à sua implantação do Azure

- Noções básicas sobre o armazenamento compartilhado do WSFC no Azure

- Entender como os componentes do ponto único de falha do SAP, como o SAP ASCS/SCS e DBMS, e os componentes redundantes, como os servidores de aplicativos SAP, podem ser protegidos no Azure

- Abordagem passo a passo de como instalar e configurar um sistema SAP HA (alta disponibilidade) em um WSFC usando o Microsoft Azure como uma plataforma e o novo Azure Resource Manager.

- As etapas adicionais necessárias para o WSFC no Azure que não são necessárias em implantações locais


Para simplificar a implantação e a configuração, estamos usando os novos modelos do Azure Resource Manager HA de 3 camadas do SAP, que automatiza a implantação da infraestrutura completa necessária para sistemas SAP altamente disponíveis e que dá suporte ao dimensionamento desejado do seu sistema SAP.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos

Antes de começar, certifique-se de que os pré-requisitos descritos nos capítulos a seguir sejam atendidos e de que você verificou todos os recursos listados no capítulo de recursos.

Estamos usando modelos do Azure Resource Manager para SAP NetWeaver de três camadas: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

Uma visão geral dos modelos do Azure Resource Manager para SAP é fornecida aqui: [https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos

Os seguintes guias adicionais estão disponíveis para o tópico das implantações do SAP no Azure:

- [SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de planejamento e implementação][planning-guide]
- [SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de implantação][deployment-guide]
- [SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de implantação do DBMS][dbms-guide]
- [SAP NetWeaver on Windows virtual machines (VMs) – High Availability Guide (SAP NetWeaver em VMs (máquinas virtuais) do Windows — guia de alta disponibilidade) (este guia)][sap-ha-guide]


> [AZURE.NOTE] Sempre que possível, um link que faz referência ao Guia de Instalação do SAP é usado (confira os [guias de instalação do SAP][sap-installation-guides]). Quando se trata dos pré-requisitos e do processo de instalação, os guias de instalação da SAP NetWeaver devem ser lidos sempre com cuidado, pois este documento só abrange tarefas específicas para sistemas baseados em SAP NetWeaver instalados em uma Máquina Virtual do Microsoft Azure.

As seguintes Notas do SAP estão relacionadas com o tópico do SAP no Azure:


| Número da observação | Title                                                    
| ------------- |----------------------------------------------------------
| [1928533] | Aplicativos SAP no Azure: dimensionamento e produtos com suporte 
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte         
| [1999351] | Monitoramento aprimorado do Azure para SAP                        
| [2178632] | Métricas-chave de monitoramento para SAP no Microsoft Azure        
| [1999351] | Virtualização no Windows: monitoramento avançado           


Limitações gerais padrão e limitações máximas das assinaturas do Azure podem ser encontradas [neste artigo][azure-subscription-service-limits-subscription].

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Diferenças no SAP HA entre os modelos do Azure Resource Manager e de implantação clássico 

> [AZURE.NOTE] O modelo de implantação clássico também é conhecido como modelo ASM (Gerenciamento de Serviços do Azure).

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
Os grupos de recursos são um novo conceito que contêm todos os recursos que têm o mesmo ciclo de vida, ou seja, que são criados e excluídos ao mesmo tempo. Leia este artigo para obter mais informações sobre os grupos de recursos.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Clustering com o Azure Resource Manager em comparação ao Modelo de Implantação Clássico 

O novo modelo do Azure Resource Manager está oferecendo as seguintes mudanças em comparação com o modelo de implantação clássico para HA:

- Não há necessidade de ter um serviço de nuvem para usar um Azure ILB (Azure Load Balancer Interno)

Se você ainda quiser usar o modelo clássico antigo do Azure, será preciso seguir o procedimento descrito no documento [SAP NetWeaver on Azure — Clustering SAP ASCS/SCS Instances using Windows Server Failover Cluster on Azure with SIOS DataKeeper (SAP NetWeaver no Azure — clustering de instâncias SAP ASCS/SCS usando o Cluster de Failover do Windows Server no Azure com o SIOS DataKeeper)](http://go.microsoft.com/fwlink/?LinkId=613056).

> [AZURE.NOTE] É altamente recomendável usar o novo modelo de implantação do Azure Resource Manager para suas instalações de SAP, uma vez que ele está oferecendo muitos benefícios em comparação com o modelo de implantação clássico. Mais informações podem ser encontradas [neste artigo][virtual-machines-azure-resource-manager-architecture-benefits-arm].


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> WSFC (Windows Server Failover Clustering) 

Um Microsoft WSFC é a base técnica de uma instalação do SAP ASCS/SCS altamente disponível e do DBMS no Windows.

Um cluster de failover é um grupo servidores 1+n independentes (nós) que trabalham juntos para aumentar a disponibilidade de aplicativos e serviços. No caso de falhas de nó, o WSFC deve determinar o número de falhas que podem ocorrer enquanto ainda mantém um cluster íntegro para que seja possível fornecer os aplicativos e/ou serviços definidos. Para que isso seja possível, há diferentes modos de quórum disponíveis.
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de quórum

Com o WSFC, quatro modos de quórum diferentes estão disponíveis:

- **Maioria dos Nós:** cada nó pode votar. O cluster funciona somente com a maioria dos votos, ou seja, mais da metade. Essa opção é recomendada no caso de número ímpar de nós. Por exemplo: três nós em um cluster de sete nós podem falhar e o cluster ainda terá a maioria e continuará em execução.

- **Maioria dos Nós e Discos:** cada nó mais um disco designado no armazenamento de cluster (a "testemunha de disco") podem votar sempre que estiverem disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, mais da metade. Esse modo faz sentido em um ambiente de cluster com um número par de nós. Desde que a metade dos nós e o disco estejam online, o cluster permanece em estado íntegro.

- **Maioria dos Nós e Compartilhamentos de Arquivos:** cada nó mais um compartilhamento de arquivos designado criado pelo administrador (a testemunha de compartilhamento de arquivos) podem votar sempre que estiverem disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, mais da metade. Esse modo faz sentido em um ambiente de cluster com um número par de nós e é semelhante ao modo Maioria dos Nós e Discos, embora ele use um compartilhamento de arquivos de testemunha em vez de um disco de testemunha. É fácil implementá-lo, mas se o próprio compartilhamento de arquivos não estiver altamente disponível, ele pode se tornar um ponto único de falha.

- **Sem Maioria: Somente Disco:** o cluster terá quórum se um nó estiver disponível e em comunicação com um disco específico no armazenamento de cluster. Somente os nós que também estão em comunicação com esse disco podem ingressar no cluster. Esse modo não é recomendável.  

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Cluster de Failover do Windows local

Neste exemplo, temos um cluster que consiste em dois nós. Se a conexão de rede entre os nós falhar enquanto ambos os nós estiverem ativos, será necessário determinar qual nó supostamente continuará fornecendo os aplicativos e serviços do cluster. Um disco de quórum ou compartilhamento de arquivos serve a essa finalidade. O nó que tem acesso ao disco de quórum ou ao compartilhamento de arquivos é aquele que garante a acessibilidade dos serviços.

Neste exemplo, estamos usando um cluster de dois nós. Por esse motivo, escolhemos o modo de quórum de nós e compartilhamento de arquivos. A maioria dos nós e discos também é uma opção válida. Em um ambiente produtivo, é recomendável usar um disco de quórum, bem como uma tecnologia de sistema de armazenamento e rede para torná-lo altamente disponível.

![Figura 1: Configuração proposta do Windows Server Failover Cluster para SAP ASCS/SCS no Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Configuração proposta do Windows Server Failover Cluster para SAP ASCS/SCS no Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Armazenamento compartilhado

A figura acima mostra um cluster de armazenamento compartilhado com dois nós. Em um cluster de armazenamento compartilhado local, há um armazenamento compartilhado que fica visível para todos os nós no cluster. Um mecanismo de bloqueio protege os dados contra corrupção. Além disso, todos os nós podem detectar se o outro nó falhar. Se um nó falhar, o outro assume a propriedade dos recursos de armazenamento e garante a disponibilidade dos serviços.

> [AZURE.NOTE] Para atingir alta disponibilidade com algum DBMS, como o SQL Server, os discos compartilhados não são necessários. O AlwaysOn do SQL Server faz a replicação dos arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Portanto, a configuração de cluster do Windows não precisa de um disco compartilhado.

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Resolução de nome/rede

O cluster pode ser acessado por um endereço IP virtual e um nome de host virtual fornecido pelo servidor DNS. Os nós locais e o Servidor DNS pode lidar com vários endereços IP.

Em uma instalação comum, são usadas duas ou mais conexões de rede:

- Uma conexão dedicada com o armazenamento e
- Uma conexão de rede interna de cluster para a pulsação e
- Uma rede pública usada pelos clientes para se conectar ao cluster.



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Cluster de failover do Windows com o Microsoft Azure

Em comparação com implantações de nuvem privada ou bare-metal, as Máquinas Virtuais do Microsoft Azure exigem etapas adicionais para configurar um WSFC. Para criar um Disco de Cluster Compartilhado, vários endereços IP e nomes de host virtuais são necessários para a instância do SAP ASCS/SCS.

Abaixo discutimos os conceitos e as etapas adicionais necessárias ao criar um cluster de Serviços Centrais HA no Microsoft Azure. As etapas mostram como configurar a ferramenta de terceiros SIOS DataKeeper e configurar o Azure Load Balancer Interno. Essas ferramentas nos darão a possibilidade de criar um Cluster de Failover do Windows com uma Testemunha de Compartilhamento de Arquivos no Microsoft Azure.


![Figura 2: Esquema de uma configuração do Windows Server Failover Cluster no Azure sem Disco Compartilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Esquema de uma configuração do Windows Server Failover Cluster no Azure sem Disco Compartilhado_


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartilhado no Microsoft Azure com SIOS DataKeeper

A partir de setembro de 2016, o Microsoft Azure não fornece armazenamento compartilhado para criar um cluster de armazenamento compartilhado. No entanto, o armazenamento compartilhado de cluster é necessário para uma instância do SAP ASCS/SCS altamente disponível.

O software de terceiros SIOS DataKeeper Cluster Edition permite criar um armazenamento espelhado que simula o armazenamento compartilhado de cluster. A solução SIOS fornece replicação síncrona de dados em tempo real. Um recurso de disco compartilhado para um cluster é criado desta forma:

- Tendo um VHD adicional do Azure anexado a cada uma das VMs que estão em uma configuração de Cluster do Windows.
- Tendo um SIOS DataKeeper Cluster Edition em execução em ambos os nós da VM.
- Tendo o SIOS DataKeeper Cluster Edition configurado de forma que ele espelhe o conteúdo do volume anexado do VHD adicional das VMs de origem para o volume anexado do VHD adicional da VM de destino. O SIOS DataKeeper está abstraindo os volumes locais de origem e de destino e apresentando-os ao Cluster de Failover do Windows como um disco compartilhado.

Para obter mais detalhes sobre o SIOS DataKeeper, acesse este site [http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)

 ![Figura 2: Esquema de uma configuração do Windows Server Failover Cluster no Azure usando o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Esquema de uma configuração do Windows Server Failover Cluster no Azure usando o SIOS DataKeeper_

> [AZURE.NOTE] Para atingir alta disponibilidade com algum DBMS, como o SQL Server, os discos compartilhados não são necessários. O AlwaysOn do SQL Server está fazendo a replicação dos arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Portanto, a configuração de cluster do Windows não precisa de um disco compartilhado.

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolução de nomes no Microsoft Azure

A plataforma de nuvem do Microsoft Azure não possibilita a configuração de endereços IP virtuais, como IPs flutuantes. Por esse motivo, você precisa de uma solução alternativa para configurar um endereço IP virtual a fim de acessar o recurso de cluster na nuvem. O Azure fornece o ILB (Balanceador de Carga Interno). Com o ILB, o cluster pode ser acessado pelo endereço IP virtual do cluster. Portanto, é preciso implantar o ILB no grupo de recursos que contém os nós do cluster. Em seguida, é preciso configurar todas as regras necessárias de encaminhamento de porta com as portas de investigação do ILB. Os clientes podem se conectar por meio do nome de host virtual. O Servidor DNS resolve o endereço IP do cluster e o ILB trata do encaminhamento para o nó ativo do cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Alta disponibilidade do SAP NetWeaver no IaaS do Azure

Conforme já abordado no capítulo sobre HA [SAP NetWeaver on Azure virtual machines (VMs) – do SAP NetWeaver on Windows virtual machines (VMs) – Planning and Implementation Guide (SAP NetWeaver em VMs (máquinas virtuais) do Azure — do SAP NetWeaver em VMs (máquinas virtuais) do Windows – guia de planejamento e implementação)][planning-guide-11], para obter a alta disponibilidade do aplicativo SAP, por exemplo, a HA dos componentes de software do SAP, precisamos proteger os seguintes componentes:

- Servidores de aplicativos SAP
- Instância do SAP ASCS/SCS
- Servidor DBMS

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Alta disponibilidade para servidores de aplicativos SAP

Para servidores de aplicativos SAP/instâncias de caixa de diálogo, normalmente, não é necessária uma solução específica de alta disponibilidade. Você atinge alta disponibilidade por redundância e, com isso, têm várias instâncias de caixa de diálogo configuradas em diferentes máquinas virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas VMs do Azure.

![Figura 4: HA dos servidores de aplicativos SAP][sap-ha-guide-figure-2000]

_**Figura 4:** HA dos servidores de aplicativos SAP_


Todas as máquinas virtuais que hospedam servidores de aplicativos SAP devem ser colocadas no mesmo **Conjunto de Disponibilidade do Azure**. O Conjunto de Disponibilidade do Azure garantirá:

- Que todas as VMs que fazem parte dos mesmos domínios de atualização, por exemplo, não possam ser atualizadas ao mesmo tempo durante o período de inatividade da manutenção planejada.
- E que todas as VMs que farão parte dos mesmos domínios de falha, por exemplo, sejam implantadas de maneira que se evite um ponto único de falha que possa afetar a disponibilidade de todas as VMs.

Para saber mais, confira este artigo: [Gerenciar a disponibilidade de máquinas virtuais][virtual-machines-manage-availability].

Uma vez que a conta de armazenamento do Azure pode ser um possível Ponto Único de Falha, é importante que você tenha pelo menos duas contas de armazenamento do Azure em que pelo menos duas VMs serão distribuídas. O ideal é que cada uma das VMs em execução nas instâncias de caixa de diálogo do SAP seja implantada em uma conta de armazenamento diferente.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Alta disponibilidade para instâncias do SAP (A)SCS 

![Figura 5: Alta disponibilidade da instância do SAP ASCS/SCS][sap-ha-guide-figure-2001]

_**Figura 5:** Alta disponibilidade da instância do SAP ASCS/SCS_


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Alta disponibilidade para a instância do SAP (A)SCS com Cluster de Failover do Windows no Azure

Em comparação com implantações de nuvem privada ou bare-metal, as Máquinas Virtuais do Microsoft Azure exigem etapas adicionais para configurar um WSFC. Para criar um cluster de failover do Windows, são necessários um Disco de Cluster Compartilhado, vários endereços IP e nomes de host virtuais e um Azure ILB (Azure Load Balancer Interno) para clustering da instância do SAP ASCS/SCS.

Isso será discutido em mais detalhes mais adiante no documento.

![Figura 6: Esquema de uma configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure usando o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Esquema de uma configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure usando o SIOS DataKeeper_


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Alta disponibilidade para a instância do DBMS

O DBMS também é um SPOF (Ponto Único de Falha) de um sistema SAP e precisa ser protegido usando uma solução HA. Veja abaixo o exemplo de uma solução HA AlwaysOn do SQL Server no Azure usando o Windows Server Failover Cluster e o Azure Load Balancer Interno. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS usando sua própria replicação DBMS. Portanto, não precisamos de discos compartilhados de cluster que simplifiquem toda a configuração.


![Figura 7: HA dos servidores SAP DBMS — um exemplo da configuração HA do AlwaysOn do SQL Server][sap-ha-guide-figure-2003]

_**Figura 7:** HA dos servidores SAP DBMS — um exemplo da configuração HA do AlwaysOn do SQL Server_


Este documento não abrange o clustering do DBMS.

Mais informações sobre o clustering do SQL Server no Azure usando o modelo de implantação do Azure Resource Manager podem ser encontradas nestes artigos:

- [Configurar manualmente o grupo de disponibilidade Always On na VM do Azure - Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Configurar um balanceador de carga interno para um grupo de disponibilidade AlwaysOn no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Possíveis cenários de implantação completa de HA

Veja o exemplo de uma arquitetura HA do SAP NetWeaver no Azure, em que usamos um cluster dedicado para a instância do SAP ASCS/SCS e outro para o DBMS.

![Figura 8: Modelo 1 de arquitetura de HA do SAP — com cluster dedicado para a instância do ASCS/SCS e cluster dedicado para a instância do DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modelo 1 de arquitetura de HA do SAP — com cluster dedicado para a instância do ASCS/SCS e cluster dedicado para a instância do DBMS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparação da infraestrutura

Para simplificar a implantação dos recursos necessários para SAP, desenvolvemos modelos do Azure Resource Manager para SAP.

Esses modelos de 3 camadas também dão suporte a cenários de alta disponibilidade. Por exemplo:

- **Modelo 1 de arquitetura**: com dois clusters, cada um para Pontos Únicos de Falhas do SAP ASCS/SCS e DBMS

Os modelos do Azure Resource Manager para o Cenário 1 estão disponíveis aqui:

- Imagem do Azure Marketplace: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- Imagem personalizada: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Ao clicar na imagem do marketplace de 3 camadas do SAP, você verá a seguinte interface de usuário no Portal do Azure:

![Figura 9: Especificando parâmetros do Azure Resource Manager para HA do SAP][sap-ha-guide-figure-3000]

_**Figura 9:** Especificando parâmetros do Azure Resource Manager para HA do SAP_


Certifique-se de escolher **HA** para a opção SYSTEMAVAILABILITY.

Os modelos criarão:

- Todas as **VMs** necessárias para:
    - VMs dos servidores de aplicativos SAP: `<SAPSystemSID>-di-<Number>`
    - VMs de cluster do ASCS/SCS: `<SAPSystemSID>-ascs-<Number>`
    - Cluster do DBMS: `<SAPSystemSID>-db-<Number>`
- **Placas de rede para todas as VMs com endereços IP associados**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Contas de Armazenamento do Azure**
- **Grupos de Disponibilidade** para:
    - VMs do servidor de aplicativos SAP: `<SAPSystemSID>-avset-di`
    - VMs de cluster do SAP ASCS/SCS: `<SAPSystemSID>-avset-ascs`
    - VMs de cluster do DBMS: `<SAPSystemSID>-avset-db`
- **Azure ILB (Azure Load Balancer Interno)** com todas as portas para a instância do ASCS/SCS e o endereço IP `<SAPSystemSID>-lb-ascs`
-	**Azure ILB (Azure Load Balancer Interno)** com todas as portas o DBMS do SQL Server e o endereço IP `<SAPSystemSID>-lb-db`
- **Grupo de Segurança de Rede**: `<SAPSystemSID>-nsg-ascs-0` com a porta RDP externa aberta para a VM `<SAPSystemSID>-ascs-0`


> [AZURE.NOTE]  TODOS os endereços IP das placas de rede e dos ILBs do Azure são inicialmente criados como **Dinâmicos**. Você precisa alterá-los para endereços IP **Estáticos**, conforme descrito posteriormente neste documento.


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implantação de VMs com conectividade de rede corporativa (entre instalações) para uso produtivo

Para sistemas SAP de produção, você implantará VMs do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando o Site a Site (VPN) ou o ExpressRoute do Azure.

> [AZURE.NOTE]  Nesse caso, sua rede VNET e sub-rede do Azure já foram criadas e preparadas.


No campo **NEWOREXISTINGSUBNET**, escolha Existente.

No campo de texto **SUBNETID**, você precisa adicionar a cadeia de caracteres completa de sua SubnetID da Rede do Azure preparada em que planeja implantar suas VMs do Azure.

Você pode obter uma lista de todas as sub-redes da rede do Azure usando este comando do PowerShell:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


A **SUBNETID** é exibida na Id do campo.

Uma lista de todas as **SUBNETIDs** pode ser recuperada usando o seguinte comando PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

A **SUBNETID** se parece com esta:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implantação somente na nuvem de instâncias do SAP para teste/demonstração

Você também pode implantar o sistema SAP HA no modelo de implantação somente na nuvem.

Essa implantação é apropriada, principalmente, para o caso de uso de demonstração, mas não para os de produção.

No campo NEWOREXISTINGSUBNET, escolha _**novo**_. Deixe o campo SUBNETID **vazio**.

A rede virtual e a sub-rede do Azure serão criadas automaticamente pelo modelo do Azure Resource Manager para SAP.

> [AZURE.NOTE] Além disso, você precisa implantar pelo menos uma VM dedicada para AD/DNS na mesma rede virtual. Essas VMs não são criadas pelo modelo.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual do Azure

Em nosso exemplo, o espaço de endereço da VNET do Azure é 10.0.0.0/16. Há uma sub-rede chamada _**Sub-rede**_, com um intervalo de endereços de 10.0.0.0/24. Todas as VMs e ILBs são implantados nessa VNET.
  
> [AZURE.NOTE] Não faça qualquer alteração nas configurações de rede no convidado (por exemplo, endereço IP, servidores DNS, sub-rede, etc.). Todas as configurações de rede são feitas por meio do Azure e propagadas pelo serviço DHCP.

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP do DNS

Verifique se a opção **Servidores DNS** da VNET está definida como **DNS Personalizado**. No caso de:

- **[Conectividade de Rede Corporativa (Entre Instalações)][planning-guide-2.2]**: adicione os endereços IP dos servidores DNS locais. Os servidores DNS locais podem ser estendidos para as VMs em execução no Azure. Nesse caso, você pode adicionar os endereços IP dessas VMs do Azure que são configuradas para executar o serviço DNS.

-	**[Implantação somente na nuvem][planning-guide-2.1]**: implante uma VM adicional na mesma VNET, que atuará como servidor(es) DNS. Adicione os endereços IP dessas VMs do Azure que são configuradas para executar o serviço DNS.


![Figura 10: Configurando servidores DNS para a VNET do Azure][sap-ha-guide-figure-3001]

_**Figura 10:** Configurando servidores DNS para a VNET do Azure_

> [AZURE.NOTE] Se você mudar os endereços IP dos servidores DNS, será preciso reinicializar as VMs do Azure de modo a aplicar a alteração e propagar os novos servidores DNS. Em nosso exemplo, o serviço DNS está instalado e configurado nas seguintes VMs do Windows



| Função VM | Nome do host da VM | Nome da placa de rede | Endereço IP estático  
| ---------------|-------------|--------------------|-------------------
| 1º servidor DNS | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 2º servidor DNS | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e a instância clusterizada do DBMS

Assim como nas instalações locais, precisamos dos seguintes endereços IP/nomes de host reservados:

| Função do nome de host virtual | Nome de host virtual | Endereço IP estático virtual 
| ----------------------------------------------------------------------------|------------------|---------------------------
| 1º nome de host virtual de cluster do SAP ASCS/SCS (usado para o gerenciamento de cluster) | pr1-ascs-vir | 10\.0.0.42                 
| Nome de host virtual da **INSTÂNCIA** do SAP ASCS/SCS | pr1-ascs-sap | `10.0.0.43`             
| Segundo nome de host virtual de cluster do SAP DBMS (usado para o gerenciamento de cluster) | pr1-dbms-vir | 10\.0.0.32                 
 

Os nomes de host virtuais _**pr1-ascs-vir**_ e _**pr1-dbms-vir**_, bem como os endereços IP associados, que são usados para gerenciar o cluster em si, são criados durante a criação do cluster, conforme descrito no capítulo [Coletar nós de cluster na configuração do cluster][sap-ha-guide-8.12.1].

Os outros dois nomes de host virtuais _**pr1-ascs-sap**_ e _**pr1-dbms-sap**_, bem como os endereços IP associados, que são usados pela instância clusterizada do SAP ASCS/SCS e pela instância clusterizada do DBMS, podem ser criados manualmente no servidor DNS, conforme descrito no capítulo [Criar nome de host virtual para SAP ASCS/SCS clusterizado][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Configurar endereços IP estáticos para as VMs SAP

Depois de implantar as máquinas virtuais para clustering, precisamos configurar um endereços IP estático para todas as VMs. Isso não pode ser feito no sistema operacional convidado, mas precisa ser definido na configuração de Rede Virtual do Azure.

Uma maneira de fazer isso é usando o Portal do Azure. No Portal do Azure, navegue para:

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

Altere o campo Atribuição de **Dinâmico** para **Estático** e informe o **endereço IP** desejado.

> [AZURE.NOTE] Se você mudar o endereço IP da placa de rede, será preciso reinicializar as VMs do Azure para aplicar a alteração.


![Figura 11: Configurando o endereço IP estático para a placa de rede de cada VM][sap-ha-guide-figure-3002]

_**Figura 11:** Configurando o endereço IP estático para a placa de rede de cada VM_

Repita essa etapa para todas as interfaces de rede, ou seja, para todas as VMs, incluindo as VMs que você deseja usar para o serviço AD/DNS.

Em nosso exemplo, temos as VMs e os endereços IP estáticos que se seguem:

| Função VM | Nome do host da VM | Nome da placa de rede | Endereço IP estático  
| ----------------------------------------|--------------|--------------------|-------------------
| 1º servidor de aplicativos SAP | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 2º servidor de aplicativos SAP | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| Último servidor de aplicativos SAP | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| 1º nó de cluster para a instância do ASCS/SCS | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| 2º nó de cluster para a instância do ASCS/SCS | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| 1º nó de cluster para a instância do DBMS | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| 2º nó de cluster para a instância do DBMS | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Configurar o endereço IP estático para o ILB (Balanceador de Carga Interno)

O modelo do Azure Resource Manager para SAP cria um Azure ILB usado para o cluster da instância do ASCS/SCS e para o cluster do DBMS do SAP.

A implantação inicial define o endereço IP do ILB para **Dinâmico**. É importante alterar o endereço IP para **Estático**.

Em nosso exemplo, temos dois ILBs do Azure com os seguintes endereços IP estáticos:

| Função do ILB do Azure | Nome do ILB do Azure | Endereço IP estático 
| ---------------------------|----------------|-------------------
| ILB da instância do SAP ASCS/SCS | pr1-lb-ascs | `10.0.0.43`         
| ILB do SAP DBMS | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**Endereço IP do nome de host virtual do SAP ASCS/SCS = endereço IP do Azure Load Balancer do SAP ASCS/SCS pr1-lb-ascs** **Endereço IP do nome virtual do DBMS = endereço IP do Azure Load Balancer do DBMS pr1-lb-dbms**

Em nosso exemplo, defina o endereço IP do Balanceador de Carga Interno, _pr1-lb-ascs_, para o endereço IP do nome de host virtual da instância do SAP ASCS/SCS (`10.0.0.43`)

![Figura 12: Configurar endereço IP estático para ILB (Balanceador de Carga Interno) para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

_**Figura 12:** Configurar endereço IP estático para ILB (Balanceador de Carga Interno) para a instância do SAP ASCS/SCS_

Da mesma forma, defina o endereço IP do Balanceador de Carga, _pr1-lb-dbms_, para o endereço IP do nome de host virtual da instância do DBMS (em nosso exemplo 10.0.0.33).

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras do balanceamento de carga do ASCS/SCS padrão para o Azure ILB (Azure Load Balancer Interno) do Azure

Por padrão, o modelo do Azure Resource Manager para SAP cria todas as portas necessárias para:

- Instância do ASCS ABAP com número de instância padrão **00**
- Instância do SCS Java com número de instância padrão **01**

Portanto, durante a instalação da instância do SAP ASCS/SCS, você precisa usar esses números de instância padrão de 00 e 01 para sua instância do ASCS ABAP e/ou do SCS Java.

Os seguintes pontos de extremidade do ILB do Azure são necessários e criados para as portas do ASCS ABAP do SAP NetWeaver:


| Serviço/nome da regra do balanceamento de carga | Números de portas padrão | Portas concretas para (instância do ASCS com número de instância 00) (ERS com 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enfileirar Servidor / _lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| Servidor de Mensagens ABAP / _lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| Mensagem interna ABAP / _lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| HTTP do Servidor de Mensagens / _Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| HTTP do SAP para Iniciar Serviço ASCS / _Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| HTTPS do SAP para Iniciar Serviço ASCS / _Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| Enfileirar Replicação / _Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| HTTP do SAP para Iniciar Serviço ERS _Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| HTTP do SAP para Iniciar Serviço ERS _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| RM Win _Lbrule5985_ | | 5985                                                                     
| Compartilhamento de Arquivos _Lbrule445_ | | 445                                                                      

_**Tabela 1:** Números de porta das instâncias do ASCS ABAP do SAP NetWeaver_


Os seguintes pontos de extremidade do ILB do Azure são necessários e devem ser criados para as portas do SCS Java do SAP NetWeaver:

| Serviço/nome da regra do balanceamento de carga | Números de portas padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enfileirar Servidor / _lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| Servidor do Gateway / _lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Servidor de Mensagens Java / _lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| HTTP do Servidor de Mensagens / _Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| HTTP do SAP para Iniciar Serviço SCS / _Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| HTTPS do SAP para Iniciar Serviço SCS / _Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| Enfileirar Replicação / _Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| HTTP do SAP para Iniciar Serviço ERS _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| HTTP do SAP para Iniciar Serviço ERS _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| RM Win _Lbrule5985_ | | 5985                                                                     
| Compartilhamento de Arquivos _Lbrule445_ | | 445                                                                      

_**Tabela 2:** Números de porta das instâncias do SCS Java do SAP NetWeaver_


![Figura 13: Regras de balanceamento de carga do ASCS/SCS padrão para o Azure ILB (Azure Load Balancer Interno)][sap-ha-guide-figure-3004]

_**Figura 13:** Regras de balanceamento de carga do ASCS/SCS padrão para o Azure ILB (Azure Load Balancer Interno)_

Da mesma forma, defina o endereço IP do Balanceador de Carga, _**pr1-lb-dbms**_, para o endereço IP do nome de host virtual da instância do DBMS (em nosso exemplo _**10.0.0.33**_).

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alteração das regras do balanceamento de carga padrão do ASCS/SCS para o Azure ILB (Azure Load Balancer Interno)

Se quiser usar outros números de instância para a instância do SAP ASCS ou SCS, você precisará atualizar os nomes e valores dessas portas.

Uma maneira de atualizar isso é usando o Portal do Azure.

Acesse `<SID>-lb-ascs load balancer -> Load Balancing Rules`

Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere:

- Nome
- Port
- Porta de back-end

Por exemplo, se quisermos alterar o número de instância padrão do ASCS de 00 para 31, por exemplo, você precisa fazer as alterações para todas as portas listadas na _**Tabela 1:** Números de porta das instâncias do ASCS ABAP do SAP NetWeaver_.

Veja abaixo o exemplo de uma atualização para a porta _lbrule3200_.

![Figura 14: Alteração das regras do balanceamento de carga padrão do ASCS/SCS para o Azure ILB (Azure Load Balancer Interno)][sap-ha-guide-figure-3005]

_**Figura 14:** Alteração das regras do balanceamento de carga padrão do ASCS/SCS para o Azure ILB (Azure Load Balancer Interno)_


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas do Windows ao domínio

Depois de atribuir endereços IP estáticos às VMs, adicione as VMs ao domínio.

![Figura 15: Adição de uma VM a um domínio][sap-ha-guide-figure-3006]

_**Figura 15:** Adição de uma VM a um domínio_


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registro em ambos os nós de cluster usados para a instância do SAP ASCS/SCS

Os Azure Load Balancers, incluindo o Azure Load Balancer Interno, fecham conexões quando estas ficam ociosas por um determinado período (tempo limite de ociosidade). Por outro lado, os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com Enfileirar Processo SAP assim que a primeira solicitação para enfileirar/desenfileirar precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se as conexões ficarem ociosas por algum tempo, o ILB do Azure as fechará. Isso não é realmente um problema, pois o processo de trabalho do SAP restabelecerá a conexão para o processo de enfileiramento se ele não existir mais. No entanto, essas atividades serão documentadas nos rastreamentos do desenvolvedor dos processos SAP e, portanto, criarão um monte de conteúdo nesses rastreamentos sem um bom motivo. Portanto, é recomendável alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós do cluster. As alterações dos parâmetros de TCP/IP precisam ser combinadas com parâmetros de perfil do SAP que descreveremos mais adiante neste documento.

Adicione as seguintes entradas de Registro de janelas em ambos os nós do cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nome de variável | `KeepAliveTime`                                              
| Tipo de variável | REG\_DWORD (Decimal)                                        
| Valor | 120000                                                     
| Vincular à documentação | [https://technet.microsoft.com/pt-BR/library/cc957549.aspx](https://technet.microsoft.com/pt-BR/library/cc957549.aspx) 


_**Tabela 3:** Primeiro parâmetro TCP/IP a ser alterado_


| Caminho | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nome de variável | `KeepAliveInterval`                                          
| Tipo de variável | REG\_DWORD (Decimal)                                        
| Valor | 120000                                                     
| Vincular à documentação | [https://technet.microsoft.com/pt-BR/library/cc957548.aspx](https://technet.microsoft.com/pt-BR/library/cc957548.aspx)


_**Tabela 4:** Segundo parâmetro TCP/IP a ser alterado_

Em seguida, **reinicialize ambos os nós do cluster** para aplicar as alterações.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configuração do Windows Server Failover Cluster para a instância do SAP ASCS/SCS

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Coletar nós de cluster na configuração do cluster

A primeira etapa é adicionar o recurso Clustering de Failover para ambos os nós do cluster. Isso é feito com o "**Assistente para Adicionar Funções e Recursos**" e não deve exigir mais descrições.

A segunda etapa seria configurar o Cluster de Failover usando o Gerenciador de Cluster de Failover do Windows.

No MMC do Gerenciador de Cluster de Failover, clique em Criar Cluster e adicione apenas o nome do primeiro nó A do cluster, por exemplo, _**pr1-ascs-0**_. Não adicione o segundo nó ainda. Ele será adicionado em uma etapa posterior.

![Figura 16: Primeira etapa para adicionar uma configuração de cluster de failover — adicionar nome de servidor/VM do primeiro nó que devem ser nós de cluster][sap-ha-guide-figure-3007]

_**Figura 16:** Primeira etapa para adicionar uma configuração de cluster de failover — adicionar nome de servidor/VM do primeiro nó que devem ser nós de cluster_

Nas próximas etapas, será solicitado o nome da rede (nome de host virtual) do cluster.

![Figura 17: Segunda etapa para adicionar uma configuração de cluster de failover — definir o nome do cluster][sap-ha-guide-figure-3008]

_**Figura 17:** Segunda etapa para adicionar uma configuração de cluster de failover — definir o nome do cluster_


Depois que o cluster for criado, um Teste de Validação de Cluster será executado

![Figura 18: Última etapa para adicionar uma configuração de cluster de failover — Verificação da Validação de Cluster está em execução][sap-ha-guide-figure-3009]

_**Figura 18:** Última etapa para adicionar uma configuração de cluster de failover — Verificação da Validação de Cluster está em execução_


![Figura 19: Última etapa para adicionar uma configuração de cluster de failover — Verificação da Validação de Cluster mostrará avisos sobre disco de quórum não encontrado][sap-ha-guide-figure-3010]

_**Figura 19:** Última etapa para adicionar uma configuração de cluster de failover — Verificação da Validação de Cluster mostrará avisos sobre disco de quórum não encontrado_

Todos os avisos sobre os discos podem ser ignorados nessa fase, uma testemunha de compartilhamento de arquivos será adicionada com os discos compartilhados SIOS. Nesse estágio, um quórum não nos interessa.

![Figura 20: Recurso principal do cluster com endereço IP definido — no entanto, é necessário um novo endereço IP][sap-ha-guide-figure-3011]

_**Figura 20:** Recurso principal do cluster com endereço IP definido — no entanto, é necessário um novo endereço IP_


Como o endereço IP do servidor está apontando para um dos nós da VM, o cluster não pode ser iniciado. Agora, precisamos alterar o endereço IP do serviço do principal serviço do cluster.

Por exemplo, é necessário atribuir um endereço IP (em nosso exemplo _**10.0.0.42**_) para o nome de host virtual do cluster _**pr1-ascs-vir**_. Isso é feito por meio da página de propriedade do recurso IP do principal serviço do cluster, conforme mostrado abaixo

![Figura 21: Usar 'Propriedades' para alterar para o endereço IP correto][sap-ha-guide-figure-3012]

_**Figura 21:** Usar 'Propriedades' para alterar para o endereço IP correto_


![Figura 22: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

_**Figura 22:** Atribuir o endereço IP reservado para o cluster_

Depois de alterar o endereço IP, coloque o nome de host virtual do cluster online.

![Figura 23: Serviço principal do cluster funcionando com o endereço IP correto][sap-ha-guide-figure-3014]

_**Figura 23:** Serviço principal do cluster funcionando com o endereço IP correto_

Agora que o serviço principal do cluster está em execução, você pode adicionar o segundo nó do cluster

![Figura 24: Adicionar o segundo nó do cluster][sap-ha-guide-figure-3015]

_**Figura 24:** Adicionar o segundo nó do cluster_

![Figura 25: Adicionar o nome de host do segundo nó do cluster, por exemplo, pr1-ascs-1][sap-ha-guide-figure-3016]

_**Figura 25:** Adicionar o nome de host do segundo nó do cluster, por exemplo, pr1-ascs-1_

![Figura 26: NÃO marcar a caixa de seleção!][sap-ha-guide-figure-3017]

_**Figura 26:** NÃO marcar a caixa de seleção!_

> [AZURE.NOTE]  
Verifique se a caixa de seleção "Adicione todo o armazenamento qualificado ao cluster" **NÃO** está marcada!

![Figura 27: Ignorar novamente o aviso sobre o quórum de disco][sap-ha-guide-figure-3018]

_**Figura 27:** Ignorar novamente o aviso sobre o quórum de disco_

Você pode ignorar os avisos sobre quórum e discos. A configuração de disco compartilhado e quórum será definida mais tarde, conforme descrito no capítulo **[Instalando o SIOS DataKeeper Cluster Edition para o disco compartilhado de cluster do SAP ASCS/SCS][sap-ha-guide-8.12.3]**.

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar a testemunha de compartilhamento de arquivos do cluster

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar um compartilhamento de arquivos

Escolhemos uma Testemunha de Compartilhamento de Arquivos em vez de um Disco de Quorum. Essa opção é compatível com o SIOS DataKeeper.

Na configuração que usamos nas ilustrações deste documento, a Testemunha de Compartilhamento de Arquivos está configurada no servidor AD/DNS em execução no Azure e é chamada de _**domcontr-0**_. Uma vez que você configurou uma conexão VPN com o Azure (por meio do Site a Site ou do ExpressRoute), seu AD/DNS reside no local e consequentemente não é adequado para execução de uma Testemunha de Compartilhamento de Arquivos.

> [AZURE.NOTE] No caso em que o AD/DNS estiver em execução apenas no local, não configure a Testemunha de Compartilhamento de Arquivos no sistema operacional Windows do AD/DNS em execução no local, pois a latência de rede entre os nós de cluster em execução no Azure e no AD/DNS local pode ser muito grande e gerar problemas de conectividade. Certifique-se de configurar a Testemunha de Compartilhamento de Arquivos em uma VM Windows do Azure em execução perto do nó do cluster.

A Unidade de Quorum precisa de pelo menos 1024 MB de espaço livre. O recomendado é de 2048 MB

A primeira etapa é adicionar o objeto de nome do cluster

![Figura 28: Atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

_**Figura 28:** Atribuir as permissões no compartilhamento para o objeto de nome do cluster_

Verifique se as permissões incluem a capacidade de alterar dados no compartilhamento para o objeto de nome de cluster (em nosso exemplo _**pr1-ascs-vir$**_). Para adicionar o objeto de nome do cluster na lista mostrada acima, é necessário pressionar '**Add**' e, em seguida, alterar o filtro para permitir a verificação de objetos de computador, assim como mostrado abaixo.

![Figura 29: Alterar o tipo de objeto para incluir também objetos de computador][sap-ha-guide-figure-3020]

_**Figura 29:** Alterar o tipo de objeto para incluir também objetos de computador_

![Figura 30: Marcar a caixa para objetos de computador][sap-ha-guide-figure-3021]

_**Figura 30:** Marcar a caixa para objetos de computador_

Depois disso, insira o objeto de nome do cluster, conforme mostrado na Figura 29. Como o registro deve ser criado agora, você pode alterar as permissões, conforme mostrado na Figura 28.

A próxima etapa é usar a guia 'Segurança' do compartilhamento e definir as permissões granulares mais refinadas para o objeto de nome do cluster.

![Figura 31: Definir atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

_**Figura 31:** Definir atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Configurar o quórum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover

A próxima etapa é alterar a configuração do cluster para uma testemunha de compartilhamento de arquivos usando o Gerenciador de Cluster de Failover.

![Figura 32: Chamar 'Assistente para Configurar a Definição de Quorum de Cluster', como mostrado aqui][sap-ha-guide-figure-3023]

_**Figura 32:** Chamar 'Assistente para Configurar a Definição de Quorum de Cluster', como mostrado aqui_

![Figura 33: Tela de seleção de configurações diferentes de quórum][sap-ha-guide-figure-3024]

_**Figura 33:** Tela de seleção de configurações diferentes de quórum_

Nessa seleção, você precisa escolher "_**Selecione a testemunha de quórum**_".

![Figura 34: Tela de seleção da testemunha de compartilhamento de arquivos][sap-ha-guide-figure-3025]

_**Figura 34:** Tela de seleção da testemunha de compartilhamento de arquivos_

Em nosso caso, você precisa escolher "_**Configurar uma testemunha de compartilhamento de arquivos**_".

![Figura 35: Definir o local do compartilhamento de arquivos para compartilhamento de testemunha][sap-ha-guide-figure-3026]

_**Figura 35:** Definir o local do compartilhamento de arquivos para compartilhamento de testemunha_


Digite o caminho UNC para o compartilhamento de arquivos (em nosso exemplo `\\domcontr-0\FSW`)

Pressione 'Avançar', o que resultará em uma lista das alterações que você quer fazer. Verifique-as e pressione 'Avançar' novamente para alterar a configuração do cluster.

![Figura 36: Tela mostrando reconfiguração bem-sucedida no cluster][sap-ha-guide-figure-3027]

_**Figura 36:** Tela mostrando reconfiguração bem-sucedida no cluster_

Nesta última etapa, a definição do cluster deve ser reconfigurada com êxito.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalando o SIOS DataKeeper Cluster Edition para compartilhamento de disco de cluster do SAP ASCS/SCS

O estado agora é que temos uma configuração funcional do Windows Server Failover Cluster no Azure. No entanto, essa configuração de cluster ainda não tem um recurso de disco compartilhado. Para instalar um SAP ASCS/SCS, precisamos de recursos de disco compartilhado. É nessa hora que o SIOS DataKeeper Cluster Edition entra em ação. Como o Azure não nos permite criar recursos de disco compartilhado com a funcionalidade necessária, precisamos confiar no SIOS DataKeeper, por exemplo, para fornecer essa funcionalidade.

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar o recurso Microsoft .NET Framework 3.5

O Microsoft .NET Framework 3.5 não é habilitado automaticamente nem está instalado na maioria das versões recentes do Windows Server. No entanto, o SIOS DataKeeper requer o .NET Framework em todos os nós em que é instalado. De modo que é necessário instalar o .NET 3.5 em todo o SO convidado das diferentes VMs.

Há duas maneiras de adicionar o .Net Framework 3.5. A primeira possibilidade é usar "**Adicionar Funções e Recursos**" no Windows, conforme mostrado abaixo:

![Figura 37: Instalar o .Net Framework 3.5 por meio do ‘Assistente para Adicionar Funções e Recursos’][sap-ha-guide-figure-3028]

_**Figura 37:** Instalar o .Net Framework 3.5 por meio do ‘Assistente para Adicionar Funções e Recursos’_

![Figura 38: Barra de progresso instalando o .Net Framework 3.5 por meio do ‘Assistente para Adicionar Funções e Recursos’][sap-ha-guide-figure-3029]

_**Figura 38:** Barra de progresso instalando o .Net Framework 3.5 por meio do ‘Assistente para Adicionar Funções e Recursos’_

A segunda possibilidade para habilitar o recurso .Net Framework 3.5 é usar a ferramenta de linha de comando _**dism.exe**_. Para esse tipo de instalação, o diretório 'sxs' da mídia de instalação do Windows precisa estar acessível. O seguinte comando precisa ser executado em uma janela de linha de comando com privilégios elevados:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalando o SIOS DataKeeper

Vamos analisar a instalação do SIOS DataKeeper Cluster Edition. Ele precisa ser instalado em cada um dos dois nós no cluster. O SIOS DataKeeper permite que tenhamos armazenamento compartilhado virtual criando um espelho sincronizado e simulando o Armazenamento Compartilhado de Cluster.

Antes de instalar o software SIOS, é preciso criar um usuário de domínio _**DataKeeperSvc**_.

> [AZURE.NOTE] Adicione esse usuário _**DataKeeperSvc**_ ao grupo **Administradores locais** em ambos os nós do cluster.
  
Instalar o software SIOS em ambos os nós do cluster

![Instalador do SIOS][sap-ha-guide-figure-3030]

![Figura 39: Primeira tela de instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Figura 39:** Primeira tela de instalação do SIOS DataKeeper_

![Figura 40: O DataKeeper informando sobre um serviço a ser desabilitado][sap-ha-guide-figure-3032]

_**Figura 40:** O DataKeeper informando sobre um serviço a ser desabilitado_

Quando você visualizar o pop-up da Figura 40, escolha "_**Sim**_".

![Figura 41: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Figura 41:** Seleção do usuário para o SIOS DataKeeper_


Na tela acima, é recomendável escolher _**Conta de Domínio ou Servidor**_.

![Figura 42: Fornecer usuário do domínio e senha para a instalação SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Figura 42:** Fornecer usuário do domínio e senha para a instalação SIOS DataKeeper_

Especifique a conta de domínio que você criou para o SIOS DataKeeper e as senhas dessa conta.

![Figura 43: Fornecer sua licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Figura 43:** Fornecer sua licença do SIOS DataKeeper_

Instale a chave de licença para o SIOS DataKeeper, conforme mostrado na Figura 43. No final da instalação, será solicitado que você **reinicialize a VM**.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar o SIOS DataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, temos que iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre o VHD adicional anexado a cada uma das VMs. As etapas a seguir mostram a configuração em ambos os nós.

![Figura 44: Ferramenta de Gerenciamento e Configuração do DataKeeper][sap-ha-guide-figure-3036]

_**Figura 44:** Ferramenta de Gerenciamento e Configuração do DataKeeper_


Inicie a Ferramenta de Gerenciamento e Configuração do DataKeeper e pressione o link "_**Conectar Servidor**_" (circulado em vermelho acima)

![Figura 45: Inserir o nome ou endereço TCP/IP do primeiro nó e, em uma segunda etapa do segundo nó, a ferramenta de gerenciamento deve se conectar][sap-ha-guide-figure-3037]

_**Figura 45:** Inserir o nome ou endereço TCP/IP do primeiro nó e, em uma segunda etapa do segundo nó, a ferramenta de gerenciamento deve se conectar_

A próxima etapa é criar o Trabalho de Replicação entre os dois nós

![Figura 46: Criar Trabalho de Replicação][sap-ha-guide-figure-3038]

_**Figura 46:** Criar Trabalho de Replicação_

Um assistente guiará você pelo processo

![Figura 47: Definir o nome do Trabalho de Replicação][sap-ha-guide-figure-3039]

_**Figura 47:** Definir o nome do Trabalho de Replicação_

![Figura 48: Definir os dados base do nó que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

_**Figura 48:** Definir os dados base do nó que deve ser o nó de origem atual_

Na primeira etapa, o nome, o endereço TCP/IP e o volume de disco do nó de origem precisam ser definidos. A segunda etapa é definir o nó de destino. Mais uma vez, o nome, o endereço TCP/IP e o volume de disco do nó de destino precisam ser definidos.

![Figura 49: Definir os dados base do nó que deve ser o nó de destino atual][sap-ha-guide-figure-3041]

_**Figura 49:** Definir os dados base do nó que deve ser o nó de destino atual_

A próxima etapa é definir os algoritmos de compactação que devem ser aplicados. Para nossos propósitos, é recomendável compactar o fluxo de replicação. Particularmente em situações de ressincronização, a compactação do fluxo de replicação reduz consideravelmente o tempo de ressincronização. Lembre-se de que a compactação utiliza recursos de CPU e RAM de uma VM. Sendo assim, quanto mais alta a taxa de compactação, mais utilização de CPU. Você pode ajustar e alterar essa configuração posteriormente.

Outra configuração que precisa ser verificada é se a replicação é executada de modo síncrono ou assíncrono. **Para proteger as configurações do SAP ASCS/SCS, é necessária a configuração da Replicação Síncrona**.

![Figura 50: Definir detalhes da replicação][sap-ha-guide-figure-3042]

_**Figura 50:** Definir detalhes da replicação_

A última etapa é definir se o volume que é replicado pelo trabalho de replicação deve ser representado em uma configuração de cluster WSFC como um disco compartilhado. Para a configuração do SAP ASCS/SCS, precisamos escolher 'SIM' para que o cluster do Windows veja o volume replicado como disco compartilhado que pode ser usado como volume de cluster.

![Figura 51: Pressionar 'Sim' para habilitar o volume replicado como volume de cluster][sap-ha-guide-figure-3043]

_**Figura 51:** Pressionar 'Sim' para habilitar o volume replicado como volume de cluster_

Após o fim da criação, a Ferramenta de Gerenciamento do DataKeeper lista o trabalho de replicação como ativo.

![Figura 52: Espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

_**Figura 52:** Espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

Como resultado, o disco agora pode ser visto no Gerenciador de Cluster de Failover do Windows como um disco do DataKeeper, conforme mostrado abaixo.

![Figura 53: O disco replicado pelo DataKeeper é mostrado no Gerenciador de Cluster de Failover][sap-ha-guide-figure-3045]

_**Figura 53:** O disco replicado pelo DataKeeper é mostrado no Gerenciador de Cluster de Failover_


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalação do sistema SAP NetWeaver

Não descreveremos a configuração do DBMS, uma vez que as configurações variam de acordo com o sistema DBMS usado. No entanto, supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diferentes fornecedores de DBMS dão para o Azure. Por exemplo O AlwaysOn ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para Oracle. Em nosso cenário de exemplo usado nesta documentação, não protegemos o DBMS.

Também não há considerações especiais com o DBMS diferente para interagir com uma configuração clusterizada do SAP ASCS/SCS no Azure.

> [AZURE.NOTE]  
O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A maior diferença é que um sistema ABAP do SAP tem uma instância do ASCS. O sistema Java do SAP tem uma instância do SCS e um sistema ABAP+Java do SAP uma instância do ASCS e do SCS em execução no mesmo grupo de cluster de failover da Microsoft. Qualquer diferença de instalação para cada pilha de instalação do SAP NetWeaver será explicitamente mencionada. É pressuposto que todas as outras partes sejam iguais.

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalação do SAP com instância do ASCS/SCS de alta disponibilidade

> [AZURE.NOTE]  
NÃO coloque seu arquivo de paginação nos volumes espelhados do DataKeeper, pois ele não é compatível com o DataKeeper. Você pode deixar o arquivo de paginação na unidade D:\\ temporária de uma VM do Azure, onde ele já foi colocado na implantação de uma VM no Azure. Se não for o caso, corrija-o e coloque o arquivo de paginação do Windows na unidade D:\\ da VM do Azure.

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Criar Nome de Host Virtual para o SAP ASCS/SCS clusterizado

A primeira etapa é criar a entrada DNS necessária para o Nome de Host Virtual da instância do ASCS/SCS. A ferramenta usada para fazer isso é o Gerenciador de DNS do Windows. Além do Nome de Host Virtual, o endereço IP atribuído a ele também precisa ser definido.

> [AZURE.NOTE]  
**Lembre-se de que o endereço IP que atribuímos ao Nome de Host Virtual da instância do ASCS/SCS precisa ser o mesmo endereço IP que atribuímos ao Azure Load Balancer (`<sid>-lb-ascs`) Endereço IP do Nome de Host Virtual do SAP ASCS/SCS `(pr1-ascs-sap)` = endereço IP do Azure Load Balancer `(pr1-lb-ascs)`**

Isso também significa que apenas uma função do Cluster de Failover SAP, por exemplo, para o sistema ABAP uma instância do ASCS, para sistema Java uma instância do SCS e para ABAP+Java uma instância do ASCS e do SCS pode ser executada em um Windows Server Failover Cluster no Azure.

> [AZURE.NOTE] O clustering de vários SIDs, conforme descrito no Guia de Instalação do SAP (confira [Guias de Instalação do SAP][sap-installation-guides]), atualmente não funciona no Azure.

![Figura 54: Definindo a entrada DNS para o nome virtual de cluster e endereço TCP/IP do SAP ASCS/SCS][sap-ha-guide-figure-3046]

_**Figura 54:** Definindo a entrada DNS para o nome virtual de cluster e endereço TCP/IP do SAP ASCS/SCS_

A entrada é mostrada no gerenciador de DNS no domínio, conforme mostrado na figura a seguir.

![Figura 55: Novo nome virtual e endereço TCP/IP listados para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

_**Figura 55:** Novo nome virtual e endereço TCP/IP listados para a configuração de cluster do SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o primeiro nó do cluster do SAP

A instalação do primeiro nó do cluster do ASCS/SCS não é nada diferente da maneira que está documentada na documentação de Instalação do SAP:
- Executar a opção Primeiro Nó do Cluster no nó A do cluster, por exemplo, no host _**pr1-ascs-0**_, como em nosso exemplo.

Se quiser manter as portas padrão para o Azure Load Balancer Interno, escolha:

- Para o **sistema ABAP** — número da instância do **ASCS** **00**
- Para o **sistema Java** — número da instância do **SCS** **01**
- Para **sistema ABAP + JAVA** — número da instância do **ASCS** **00** e número da instância do **SCS** **01**

Se você quiser usar outros números de instância além de 00 para a instância do ASCS ABAP e 01 para a instância do SCS Java, primeiramente será preciso alterar as regras padrão de balanceamento de carga do Azure ILB, conforme descrito em **[Alteração das regras do balanceamento de carga padrão do ASCS/SCS para o Azure ILB (Azure Load Balancer Interno)][sap-ha-guide-8.9]**.

Após essa etapa, você precisa executar algumas etapas que não estão descritas na documentação de instalação comum do SAP.

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância do ASCS/SCS

Um novo parâmetro de perfil deve ser adicionado. Esse parâmetro de perfil impede o fechamento de conexões entre os processos de trabalho do SAP e o servidor de enfileiramento quando estiverem ociosas por muito tempo. Mencionamos o cenário de problema no capítulo **[Adicionar entradas de registro em ambos os nós do cluster usados para a instância do SAP ASCS/SCS][sap-ha-guide-8.11]** deste documento. Nessa seção, também apresentamos duas alterações para alguns parâmetros básicos de conexão TCP/IP. Na segunda etapa, precisamos configurar o servidor de enfileiramento para enviar um sinal **keep\_alive** para que as conexões não atinjam o limite de ociosidade do ILB do Azure. Para essa finalidade, adicione este parâmetro de perfil:

```
enque/encni/set_so_keepalive = true
```

ao perfil da instância do SAP ASCS/SCS. Em nosso exemplo, o caminho é:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Por exemplo, para o perfil da instância do SAP SCS e o caminho correspondente

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar porta de investigação

Para fazer com que toda a configuração do cluster funcione com um Azure Load Balancer, precisamos aproveitar a funcionalidade de investigação do Balanceador de Carga Interno. Normalmente, um Azure Load Balancer Interno equilibra e distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes. No entanto, ele não funcionaria em tal configuração de cluster, uma vez que apenas uma das instâncias é ativa e a outra é passiva e não pode aceitar carga de trabalho. Para habilitar as configurações em que o Azure Load Balancer Interno atribuirá trabalho apenas à instância ativa, uma funcionalidade de investigação foi estabelecida. Por meio dessa funcionalidade, o Balanceador de Carga Interno tem a possibilidade de verificar quais instâncias estão ativas e subsequentemente visa apenas essas instâncias com a carga de trabalho. Primeiramente, vamos verificar a configuração _**ProbePort**_ atual com este comando do PowerShell executado em uma das VMs envolvidas na configuração do cluster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 56: A porta de investigação da configuração do cluster é 0 por padrão][sap-ha-guide-figure-3048]

_**Figura 56:** A porta de investigação da configuração do cluster é 0 por padrão_

Por padrão, o número da porta de investigação é definida como 0. Para que a configuração funcione, uma porta precisa ser definida. Em nosso caso, precisamos usar a porta de investigação _**62300**_, pois esse número de porta é definido nos modelos do Azure Resource Manager para SAP. A atribuição desse número de porta pode ser feita com os dois comandos abaixo:

Primeiramente, obtenha o recurso de cluster de nome de host virtual _**SAP WAC IP**_

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

Em seguida, defina a porta de investigação para 62300

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Você precisa parar e iniciar a função de cluster _**SAP PR1**_ para ativar as mudanças.

Depois de colocar a função de cluster _**SAP PR1**_ online, verifique se _**ProbePort**_ está definida com o novo valor:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 57: Porta de investigação do cluster após a mudança][sap-ha-guide-figure-3049]

_**Figura 57:** Porta de investigação do cluster após a mudança_

Você pode ver que _**ProbePort**_ agora está definida como _**62300**_. Agora é possível acessar o compartilhamento de arquivos _**\\\ascsha-clsap\\sapmnt**_ de outros hosts como _**ascsha dbas**_.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalando a instância de banco de dados

A instalação da instância de banco de dados não é diferente do processo descrito na documentação de instalação do SAP. Portanto, ela não está documentada aqui.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalação do segundo nó do cluster

Novamente, a instalação do segundo nó do cluster não difere da documentação de instalação do SAP. Sendo assim, siga as etapas no guia de instalação para instalar o segundo nó do cluster.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de inicialização do serviço Windows da instância do SAP ERS

Altere o tipo de inicialização do(s) serviço(s) Windows do SAP ERS para _**Automático (Atraso na Inicialização)**_ em ambos os nós do cluster.

![Figura 58: Altere o tipo de serviço da instância do SAP ERS para atraso automático][sap-ha-guide-figure-3050]

_**Figura 58:** Altere o tipo de serviço da instância do SAP ERS para atraso automático_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalação do PAS (Servidor de Aplicativos Primário) SAP

Execute a instalação `<sid>-di-0` da Instância do Servidor de Aplicativos Primário na VM designada para hospedar o PAS. Não há dependências de especificações do Azure ou DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalação do AAS (Servidor de Aplicativos Adicional) SAP

Execute a instalação de um Servidor de Aplicativos SAP adicional em todas as VMs designadas para hospedar um Servidor de Aplicativos SAP, por exemplo, em `<sid>-di-1` até `<sid>-di-<n>`.

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testando o failover da instância do SAP ASCS/SCS e a replicação do SIOS

Você pode testar e monitorar facilmente um failover da instância do SAP ASCS/SCS e a replicação de disco do SIOS usando o _**Gerenciador de Cluster de Failover**_ e a interface de usuário do SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Ponto de partida — a instância do SAP ASCS/SCS está em execução no Nó A do Cluster

O grupo de clusters _**SAP WAC**_ está em execução no nó A do cluster, por exemplo, _**ascsha-clna**_. O disco compartilhado S: faz parte do grupo de clusters _**SAP WAC**_ e é usado pela instância do ASCS/SCS, é atribuído ao nó A do cluster.

![Figura 59: Gerenciador de Cluster de Failover: grupo de clusters SAP <SID> está em execução no Nó A do cluster][sap-ha-guide-figure-5000]

_**Figura 59:** Gerenciador de Cluster de Failover: grupo de clusters SAP <SID> está em execução no Nó A do cluster_

Usando a interface de usuário do SIOS DataKeeper, você pode ver que os dados do disco compartilhado são replicados de modo síncrono do volume de origem S: em um nó A do cluster (por exemplo, _**ascsha-clna [10.0.0.41]**_) para o volume de destino _**S:**_ no nó B do cluster (por exemplo, _**ascsha-clnb [10.0.0.42]**_)

![Figura 60: SIOS DataKeeper: replicando o volume local do nó A do cluster para o nó B do cluster][sap-ha-guide-figure-5001]

_**Figura 60:** SIOS DataKeeper: replicando o volume local do nó A do cluster para o nó B do cluster_


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Processo de failover do Nó para o Nó B

Você pode iniciar um failover do grupo de clusters SAP <SID> do nó A do cluster para o nó B do cluster:

- usando o Gerenciador de Cluster de Failover

- usando o PowerShell de Cluster de Failover

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- reiniciando o nó A do cluster no SO convidado Windows (Isso iniciará um failover automático do grupo de clusters SAP <SID> do Nó A para o Nó B)

- reiniciando o nó A do cluster no Portal do Azure (Isso iniciará um failover automático do grupo de clusters SAP <SID> do Nó A para o Nó B)

- reiniciando o nó A do cluster usando o Azure PowerShell (Isso iniciará um failover automático do grupo de clusters SAP <SID> do Nó A para o Nó B)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> Resultado final — a instância do SAP ASCS/SCS está em execução no Nó B do Cluster

Depois do failover, o grupo de clusters `SAP <SID>` está em execução no nó B do cluster, por exemplo, em _**ascsha-clnb**_.

![Figura 61: Gerenciador de Cluster de Failover: grupo de clusters SAP <SID> está em execução no Nó B do cluster][sap-ha-guide-figure-5002]

_**Figura 61:** Gerenciador de Cluster de Failover: grupo de clusters SAP <SID> está em execução no Nó B do cluster_

O disco compartilhado agora é montado no nó B do cluster. O SIOS DataKeeper está replicando dados do volume de origem S: no nó B do cluster (por exemplo, _**ascsha-clnb [10.0.0.42]**_) para o volume de destino S: no nó A do cluster (por exemplo, _**ascsha-clna [10.0.0.41]**_).

![Figura 62: SIOS DataKeeper: replicando o volume local do nó B do cluster para o nó A do cluster][sap-ha-guide-figure-5003]

_**Figura 62:** SIOS DataKeeper: replicando o volume local do nó B do cluster para o nó A do cluster_

<!---HONumber=AcomDC_0907_2016-->