<properties
   pageTitle="SAP NetWeaver em VMs (máquinas virtuais) do Linux – Guia de Implantação | Microsoft Azure"
   description="SAP NetWeaver em VMs (máquinas virtuais) do Linux – Guia de implantação"
   services="virtual-machines-linux,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="sedusch"/>

# SAP NetWeaver em VMs (máquinas virtuais) do Azure – Guia de implantação

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
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
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2243692]: https://service.sap.com/sap/support/notes/2243692
[2233094]: https://service.sap.com/sap/support/notes/2233094

[azure-portal]: https://portal.azure.com
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-ps]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Linux – Guia de Planejamento e Implementação"
[planning-guide-classic]: virtual-machines-windows-classic-sap-planning-guide.md
[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Linux – Guia de implantação"
[deployment-guide-classic]: virtual-machines-windows-classic-sap-deployment-guide.md
[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "SAP NetWeaver em VMs (máquinas virtuais) do Linux – Guia de implantação de DBMS"
[dbms-guide-classic]: virtual-machines-windows-classic-sap-dbms-guide.md
[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971
[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[getting-started]: virtual-machines-linux-sap-getting-started-arm.md
[getting-started-windows-classic]: virtual-machines-windows-classic-sap-getting-started.md

[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-getting-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-getting-started.md#f2a5e9d8-49e4-419e-9900-af783173481c
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-getting-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-getting-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-getting-started.md#4bb7512c-0fa0-4227-9853-4004281b1037

[getting-started-planning]: virtual-machines-linux-sap-getting-started-arm.md#3da0389e-708b-4e82-b2a2-e92f132df89c
[getting-started-deployment]: virtual-machines-linux-sap-getting-started-arm.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-dbms]: virtual-machines-linux-sap-getting-started-arm.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82

[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Recursos da SAP"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Cenários de implantação de VMs para SAP no Microsoft Azure"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Implantando uma VM com uma imagem personalizada"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Cenário 1: Implantando uma VM com origem no Azure Marketplace para SAP"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Cenário 2: Implantando uma VM com uma imagem personalizada para SAP"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Implantando cmdlets do Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Baixar e importar os cmdlets do PowerShell relevantes para SAP"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Ingressar a VM no domínio local, somente Windows"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Baixar, instalar e habilitar o agente de VM do Azure"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurar a extensão de monitoramento aprimorado do Azure para SAP"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "PowerShell do Azure"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "CLI do Azure"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Verificação de preparação para o monitoramento aprimorado do Azure para SAP"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP"
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configurar proxy"
[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configurar o monitoramento"
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Verificações e solução de problemas de configuração de monitoramento de ponta a ponta para SAP no Azure"
[deployment-guide-figure-100]: ./media/virtual-machines-linux-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-300]: ./media/virtual-machines-linux-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-linux-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-500]: ./media/virtual-machines-linux-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-600]: ./media/virtual-machines-linux-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-700]: ./media/virtual-machines-linux-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-linux-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-50]: ./media/virtual-machines-linux-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-900]: ./media/virtual-machines-linux-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-1000]: ./media/virtual-machines-linux-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]: ./media/virtual-machines-linux-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-linux-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-linux-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]: ./media/virtual-machines-linux-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda

[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Recursos"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Somente em nuvem - implantações de máquinas virtuais no Azure sem dependências na rede do cliente local"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Entre instalações - implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Regiões do Azure"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "O conceito de máquina virtual do Microsoft Azure"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Domínios de falha"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Domínios de atualização"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Conjuntos de disponibilidade do Azure"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Armazenamento Premium do Azure"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Movendo uma VM do local para o Azure com um disco não generalizado"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Implantando uma VM com uma imagem específica do cliente"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparando VMs com SAP para o Azure"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparação para mover uma VM do local para o Azure com um disco não generalizado"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparação para implantar uma VM com uma imagem específica do cliente para SAP"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Diferença entre uma imagem do Azure e disco do Azure"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Carregando um VHD do local no Azure"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copiando discos entre Contas de Armazenamento do Azure"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Estrutura de VM/VHD para implantações SAP"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Definindo a montagem automática para os discos anexados"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Conceitos de implantação somente em nuvem de instâncias SAP"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Cenário de treinamento/demonstração de VM única com SAP NetWeaver"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Solução de monitoramento do Azure para SAP"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Alta disponibilidade para servidores de aplicativos SAP"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Usando a inicialização automática para instâncias SAP"
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Rede do Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Armazenamento: Armazenamento do Microsoft Azure e discos de dados"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Armazenamento Premium do Azure"
[planning-guide-figure-100]: ./media/virtual-machines-linux-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-200]: ./media/virtual-machines-linux-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-300]: ./media/virtual-machines-linux-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-400]: ./media/virtual-machines-linux-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-linux-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-linux-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-linux-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-figure-1300]: ./media/virtual-machines-linux-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-linux-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-linux-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-linux-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-linux-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-2100]: ./media/virtual-machines-linux-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-linux-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-linux-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-linux-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-linux-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-linux-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-linux-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-linux-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-linux-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-3000]: ./media/virtual-machines-linux-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-linux-sap-planning-guide/3200-sap-ha-with-sql.png

[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Estrutura de uma implantação do RDBMS"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Cache para VMs e VHDs"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "RAID de software"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Armazenamento do Microsoft Azure"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Alta disponibilidade e recuperação de desastre com VMs do Azure"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Informações específicas para o RDBMS do SQL Server"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 e posterior"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 e versões anteriores"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Usando imagens do SQL Server do Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Resumo do SQL Server para SAP no Azure geral"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configuração de armazenamento"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Backup e restauração"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Considerações de desempenho para backup e restauração"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Outros"
[dbms-guide-figure-100]: ./media/virtual-machines-linux-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-linux-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-linux-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-linux-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-linux-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-linux-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-linux-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-linux-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-linux-sap-dbms-guide/900-sap-cache-server-on-premises.png

[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[Logo_Windows]: ./media/virtual-machines-linux-sap-shared/Windows.png
[Logo_Linux]: ./media/virtual-machines-linux-sap-shared/Linux.png

[vm-size-specs]: virtual-machines-size-specs.md
[azure-subscription-service-limits-subscription]: azure-subscription-service-limits.md#subscription
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: vpn-gateway-cross-premises-options.md
[vpn-gateway-site-to-site-create]: vpn-gateway-site-to-site-create.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md "Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-deploy-rmtemplates-powershell.md "Gerenciar máquinas virtuais usando o PowerShell e o Gerenciador de Recursos do Azure"
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-manage-availability]: virtual-machines-manage-availability.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-how-to-attach-disk.md
[virtual-networks-reserved-private-ip]: virtual-networks-static-private-ip-arm-ps.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-sql-server-infrastructure-services.md
[storage-redundancy]: storage-redundancy.md
[storage-scalability-targets]: storage-scalability-targets.md
[virtual-networks-manage-dns-in-vnet]: virtual-networks-manage-dns-in-vnet.md
[resource-groups-networking]: resource-groups-networking.md
[virtual-networks-static-private-ip-arm-pportal]: virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-multiple-nics]: virtual-networks-multiple-nics.md
[virtual-network-deploy-multinic-arm-template]: virtual-network-deploy-multinic-arm-template.md
[virtual-network-deploy-multinic-arm-ps]: virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-cli]: virtual-network-deploy-multinic-arm-cli.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-about-vpn-devices]: vpn-gateway-about-vpn-devices.md
[vpn-gateway-vpn-faq]: vpn-gateway-vpn-faq.md
[vpn-gateway-create-site-to-site-rm-powershell]: vpn-gateway-create-site-to-site-rm-powershell.md
[powershell-install-configure]: powershell-install-configure.md
[xplat-cli]: xplat-cli.md
[virtual-machines-deploy-rmtemplates-azure-cli]: virtual-machines-deploy-rmtemplates-azure-cli.md
[xplat-cli-azure-resource-manager]: xplat-cli-azure-resource-manager.md
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-create-upload-vhd-suse.md
[storage-use-azcopy]: storage-use-azcopy.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[storage-azure-cli]: storage-azure-cli.md
[storage-powershell-guide-full-copy-vhd]: storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-azure-cli-copy-blobs]: storage-azure-cli.md#copy-blobs
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-size-specs]: virtual-machines-size-specs.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-sql-server-performance-best-practices.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md
[virtual-machines-sql-server-alwayson-availability-groups-powershell]: virtual-machines-sql-server-alwayson-availability-groups-powershell.md
[virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener]: virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md
[virtual-networks-configure-vnet-to-vnet-connection]: virtual-networks-configure-vnet-to-vnet-connection.md
[azure-subscription-service-limits]: azure-subscription-service-limits.md
[virtual-machines-configuring-oracle-data-guard]: virtual-machines-configuring-oracle-data-guard.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-attach-disk-preview]: virtual-machines-attach-disk-preview.md
[virtual-machines-workload-template-sql-alwayson]: virtual-machines-workload-template-sql-alwayson.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-how-to-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux
[resource-group-authoring-templates]: resource-group-authoring-templates.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-linux-create-upload-vhd-step-1]: virtual-machines-linux-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[deploy-template-powershell]: resource-group-template-deploy.md#deploy-with-powershell
[deploy-template-cli]: resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: resource-group-template-deploy.md#deploy-with-the-preview-portal
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[resource-group-overview]: ../resource-group-overview.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-networks-nsg]: virtual-networks-nsg.md
[storage-premium-storage-preview-portal]: storage-premium-storage-preview-portal.md
[storage-introduction]: storage-introduction.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-azure-resource-manager-architecture]: virtual-machines-azure-resource-manager-architecture.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-networks-create-vnet-arm-pportal]: virtual-networks-create-vnet-arm-pportal.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[virtual-machines-azurerm-versus-azuresm]: virtual-machines-azurerm-versus-azuresm.md

[install-extension-cli]: https://github.com/Azure/azure-linux-extensions/blob/master/AzureEnhancedMonitor/README.md

[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd

[sap-pam]: https://support.sap.com/pam "Matriz de Disponibilidade de Produto da SAP"

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

O Microsoft Azure permite que empresas adquiram recursos de computação e armazenamento gastando o mínimo de tempo, sem ciclos de compras longos. As Máquinas Virtuais do Azure permitem que as empresas implantem aplicativos clássicos no Azure, como aplicativos baseados no SAP NetWeaver, além de aumentar a confiabilidade e disponibilidade desses aplicativos sem que sejam necessários mais recursos disponíveis localmente. O Microsoft Azure também dá suporte à conectividade entre locais, o que permite que as empresas integrem ativamente as Máquinas Virtuais do Azure a seus domínios locais, suas nuvens privadas e sua estrutura do sistema da SAP.

Este white paper descreve passo a passo como uma Máquina Virtual do Azure é preparada para a implantação de aplicativos baseados no SAP NetWeaver. Ele pressupõe que você tenha conhecimento das informações contidas no [Guia de Implantação e Planejamento][planning-guide]. Caso contrário, o documento em questão precisa ser lido primeiro.

O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## Introdução
Um grande número de empresas do mundo inteiro usa aplicativos baseados no SAP NetWeaver – com destaque para o SAP Business Suite – para executar seus processos corporativos críticos. Sendo assim, a integridade do sistema é um ativo fundamental e a capacidade de dar suporte às empresas em caso de problemas, inclusive incidentes de desempenho, é um requisito vital. O Microsoft Azure fornece instrumentação de plataforma superior para acomodar os requisitos de capacidade de suporte de todos os aplicativos críticos para a empresa. Este guia garante que uma Máquina Virtual do Microsoft Azure alvo de uma implantação de Software SAP seja configurada de modo que o suporte empresarial possa ser oferecido, independentemente da forma como a Máquina Virtual tiver sido criada, tenha ela sido retirada do Azure Marketplace ou criada usando uma imagem específica do cliente. A seguir, todas as etapas de configuração necessárias são descritas detalhadamente.

## Pré-requisitos e recursos
### Pré-requisitos
Antes de começar, certifique-se de que os pré-requisitos descritos nos capítulos a seguir sejam atendidos.

#### Computador local
A configuração de uma Máquina Virtual do Azure para implantação de Software SAP é composta por várias etapas. Para gerenciar VMs do Windows ou do Linux, você precisa usar um script do PowerShell e o Portal do Microsoft Azure. Para fazer isso, é necessário ter um computador local executando o Windows 7 ou superior. Se você quiser gerenciar apenas VMs do Linux e quiser usar um computador Linux para essa tarefa, você também poder usar a CLI (interface de linha de comando) do Azure.

#### Conexão com a Internet
Você precisará de uma conexão com a Internet para baixar e executar as ferramentas e scripts necessários. Além disso, a Máquina Virtual do Microsoft Azure que executar a Extensão de monitoramento avançado do Azure precisará de acesso à Internet. Caso essa VM do Azure faça parte de um domínio local ou de uma Rede Virtual do Azure, certifique-se de que as configurações de proxy relevantes estejam definidas conforme descrito no capítulo [Configurar proxy][deployment-guide-configure-proxy] deste documento.

#### Assinatura do Microsoft Azure
Já existe uma conta do Azure e suas credenciais de logon são conhecidas.

#### Consideração sobre topologia e rede
A topologia e a arquitetura de implantação de SAP no Azure precisam ser definidas. A arquitetura no que diz respeito a:

* Contas do Armazenamento do Microsoft Azure a serem usadas
* Rede Virtual na qual o sistema SAP será implantado
* Grupo de Recursos no qual o sistema SAP será implantado
* Região do Azure na qual o sistema SAP será implantado
* Configuração do SAP (duas ou três camadas)
* Tamanho da VM e o número de VHDs adicionais a serem montados nas VMs
* Configuração do sistema de Transporte e Correção do SAP

As Contas de Armazenamento ou as Redes Virtuais do Azure já devem ter sido criadas e configuradas. Veja como criar e configurá-las no [Guia de Planejamento e Implementação][planning-guide].

#### Dimensionamento do SAP
* A carga de trabalho projetada do SAP foi determinada, por exemplo, usando o SAP Quicksizer, e o número de SAPs correspondente é conhecido 
* Os recursos de CPU e consumo de memória necessários para o sistema SAP devem ser conhecidos
* As operações de E/S necessárias por segundo devem ser conhecidas
* A largura de banda de rede necessária para eventual comunicação entre VMs diferentes no Azure é conhecida
* A largura de banda de rede necessária entre os ativos locais e os sistemas SAP implantados no Azure é conhecida

#### Grupos de recursos
Grupos de recursos são um novo conceito que contém todos os recursos que têm o mesmo ciclo de vida, ou seja, que são criados e excluídos ao mesmo tempo. Leia [este artigo][resource-group-overview] para obter mais informações sobre os grupos de recursos.

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos da SAP
Durante a configuração, são necessários os seguintes recursos:

* Nota SAP [1928533]
	* a lista de tamanhos de Máquinas Virtuais do Azure que têm suporte para a implantação de Software SAP 
	* informações importantes sobre a capacidade por tamanho de Máquina Virtual do Azure
	* combinação de sistema operacional e banco de dados e software SAP com suporte
* Nota SAP [2015553] listando os pré-requisitos para ter suporte do SAP durante a implantação de software SAP no Microsoft Azure.
* Nota SAP [1999351] que contém informações adicionais de solução de problemas para o Monitoramento Avançado do Azure para SAP.
* Nota SAP [2178632] que contém informações detalhadas sobre todas as métricas de monitoramentos disponíveis para SAP no Microsoft Azure. 
* Nota SAP [1409604] que contém a versão necessária do Agente de Host do SAP para Windows no Microsoft Azure ao implantar no novo Azure Resource Manager.
* Nota SAP [2191498] que contém a versão necessária do Agente de Host do SAP para Linux no Microsoft Azure ao implantar no novo Azure Resource Manager.
* Nota SAP [2243692] que contém informações sobre o licenciamento para SAP no Linux no Azure
* [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as Notas SAP para Linux
* Cmdlets do PowerShell específicos para SAP que fazem parte do [Azure PowerShell][azure-ps]
* CLI do Azure específica para SAP que faz parte da [CLI do Azure][azure-cli]
* [Portal do Microsoft Azure][azure-portal]

[comentário]: <> (MSSedusch TODO Adicionar nível de patch ARM para o Agente de Host do SAP na Nota SAP 1409604)
 
Os guias a seguir também abordam o uso do SAP no Microsoft Azure:

* [SAP NetWeaver em VMs (máquinas virtuais) do Azure – Guia de planejamento e implementação][planning-guide]
* [SAP NetWeaver em VMs (máquinas virtuais) do Azure – Guia de Implantação (este documento)][deployment-guide]
* [SAP NetWeaver em VMs (máquinas virtuais) do Azure – Guia de implantação de DBMS][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implantação de VMs para SAP no Microsoft Azure
Neste capítulo, você aprenderá as diferentes formas de implantação e as etapas para cada tipo de implantação.

### Implantação de VMs para SAP
O Microsoft Azure oferece várias maneiras de implantar VMs e discos associados. Assim, é muito importante entender as diferenças, uma vez que as preparações das VMs podem ser diferentes dependendo da forma de implantação. De modo geral, analisamos os seguintes cenários:

#### Implantando uma VM com origem no Azure Marketplace
Você gostaria de usar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implantar a VM. Depois de implantar a VM no Microsoft Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP na VM, como faria em um ambiente local. Para instalar o software SAP na VM do Azure, a SAP e a Microsoft recomendam carregar e armazenar a mídia de instalação do SAP em VHDs do Azure ou criar uma VM do Azure funcionando como um "servidor de arquivos" que contém todas as mídias de instalação do SAP necessárias.

[comentário]: <> (MSSedusch TODO por que precisamos para recomendar um gerenciamento de arquivos, como Servidor de Arquivos ou VHD? É tão diferente da versão local?)

Para obter mais detalhes, consulte o capítulo [Cenário 1: Implantando uma VM com origem no Azure Marketplace para SAP][deployment-guide-3.2].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Implantando uma VM com uma imagem personalizada
Devido a requisitos de patch específicos no que diz respeito à sua versão de SO ou DBMS, as imagens fornecidas no Azure Marketplace podem não atender às suas necessidades. Portanto, você precisará criar uma VM usando sua própria imagem “privada” de VM do SO/DB, que poderá ser implantada várias vezes posteriormente. As etapas para criar uma imagem privada diferem para imagens do Windows e do Linux.

___

> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode ser usada para implantar várias máquinas virtuais, as configurações do Windows (como o nome de host e SID do Windows) devem ser abstraídas/generalizadas na VM local. Isso pode ser feito usando o sysprep conforme descrito em <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem do Linux que pode ser usada para implantar várias máquinas virtuais, algumas configurações do Linux devem ser abstraídas/generalizadas na VM local. Isso pode ser feito usando o waagent -deprovision conforme descrito [neste artigo][virtual-machines-linux-capture-image] ou [neste artigo][virtual-machines-linux-agent-user-guide-command-line-options].

___

Você poderá configurar o conteúdo do banco de dados usando o Gerenciador de Provisionamento de Software SAP para instalar um novo sistema SAP, restaurar um backup de banco de dados de um VHD anexado à máquina virtual ou restaurar diretamente um backup de banco de dados do Armazenamento do Azure se o DBMS der suporte a isso. (Consulte o [Guia de Implantação de DBMS][dbms-guide]). Se já tiver instalado um sistema SAP na VM local (especialmente para sistemas de duas camadas), você poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure por meio do procedimento de Renomeação do Sistema ao qual o Gerenciador de Provisionamento de Software SAP dá suporte (Nota SAP [1619720]). Caso contrário, você pode instalar o software SAP posteriormente, após a implantação da VM do Azure.

Para obter mais detalhes, consulte o capítulo [Cenário 2: Implantando uma VM com uma imagem personalizada para SAP][deployment-guide-3.3].

#### Movendo uma VM do local para o Microsoft Azure com um disco não generalizado
Você planeja mover um sistema SAP específico do local para o Microsoft Azure. Isso pode ser feito carregando o VHD que contém o SO, os binários SAP e eventuais binários DBMS, mais os VHDs com os arquivos de log e de dados do DBMS para o Microsoft Azure. Ao contrário do cenário descrito no capítulo [Implantando uma VM com uma imagem personalizada][deployment-guide-3.1.2] acima, você mantém o nome de host, a SID do SAP e as contas de usuário do SAP na VM do Azure, uma vez que elas foram configuradas no ambiente local. Portanto, não é necessário generalizar o sistema operacional. Esse caso se aplicará principalmente para cenários entre locais em que uma parte da estrutura da SAP é executada localmente e parte é executada no Microsoft Azure.

Para obter mais detalhes, consulte o capítulo [Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP][deployment-guide-3.4].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implantando uma VM com origem no Azure Marketplace para SAP
O Microsoft Azure oferece a possibilidade de implantar uma instância VM originada no Azure Marketplace, o que oferece algumas imagens de SO padrão do Windows Server e diferentes distribuições do Linux. Também é possível implantar uma imagem que inclui SKUs do DBMS, como o SQL Server. Para obter detalhes sobre o uso dessas imagens com SKUs do DBMS, consulte o [Guia de Implantação de DBMS][dbms-guide]

A sequência de etapas específicas do SAP para implantar uma VM com origem no Azure Marketplace seriam semelhantes a:

![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM do Azure Marketplace][deployment-guide-figure-100]

As etapas no fluxograma a seguir precisam ser executadas:

#### Criar uma máquina virtual usando o Portal do Azure
A maneira mais fácil de criar uma nova máquina virtual usando uma imagem com origem no Azure Marketplace é por meio do Portal do Azure. Navegue até <https://portal.azure.com/#create>. Insira o tipo do sistema operacional que você deseja implantar, como Windows ou SUSE, e selecione a versão. Certifique-se de selecionar o modelo de implantação do "Azure Resource Manager" e clique em "Criar".

O assistente o orientará com relação aos parâmetros necessários para criar a máquina virtual, bem como todos os recursos necessários, como interfaces de rede ou contas de armazenamento. Alguns desses parâmetros são:

1. Noções básicas
    1. Nome: o nome do recurso, ou seja, o nome da máquina virtual
    1. Nome de usuário e senha/chave pública SSH: insira o nome de usuário e a senha do usuário que é criado durante o provisionamento. Para uma máquina virtual Linux, você também pode inserir a chave pública SSH que deseja usar para fazer logon na máquina usando SSH.
    1. Assinatura: selecione a assinatura que deseja usar para provisionar a nova máquina virtual.
    1. Grupo de recursos: o nome do grupo de recursos. Você pode inserir o nome de um novo grupo de recursos ou de um grupo de recursos que já existe
    1. Local: selecione o local no qual a nova máquina virtual deve ser implantada. Se quiser conectar a máquina virtual à sua rede local, certifique-se de selecionar o local da Rede Virtual que conecta o Azure à sua rede local. Para obter mais detalhes, consulte o capítulo [Rede do Microsoft Azure][planning-guide-microsoft-azure-networking] no [Guia de Planejamento][planning-guide].
1. Tamanho: leia a Nota SAP [1928533] para obter uma lista dos tipos de VM com suporte. Também certifique-se de selecionar o tipo correto se quiser usar o Armazenamento Premium. Nem todos os tipos VM dão suporte ao Armazenamento Premium. Consulte os capítulos [Armazenamento: Armazenamento e Discos de Dados do Microsoft Azure][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Armazenamento Premium do Azure][planning-guide-azure-premium-storage] no [Guia de Planejamento][planning-guide] para obter mais detalhes.
1. Configurações
    1. Conta de armazenamento: você pode selecionar uma conta de armazenamento existente ou criar uma nova. Leia o capítulo [Armazenamento do Microsoft Azure][dbms-guide-2.3] do [Guia de DBMS][dbms-guide] para obter mais detalhes sobre os diferentes tipos de armazenamento. Observe que nem todos os tipos de armazenamento têm suporte para a execução de aplicativos SAP.
    1. Rede virtual e sub-rede: selecione a rede virtual que está conectada à sua rede local se quiser integrar a máquina virtual à sua intranet.
    1. Endereço IP público: selecione o endereço IP público que você deseja usar ou insira os parâmetros para criar um novo endereço IP público. Você pode usar um endereço IP público para acessar sua máquina virtual pela Internet. Certifique-se de criar também um grupo de segurança de rede para filtrar o acesso à sua máquina virtual.
    1. Grupo de segurança de rede: consulte [O que é um NSG (grupo de segurança de rede)?][virtual-networks-nsg] para obter mais detalhes.
    1. Monitoramento: você pode desabilitar a configuração de diagnóstico. Ela será habilitada automaticamente quando você executar os comandos para habilitar o Monitoramento Avançado do Azure, conforme descrito no capítulo [Configurar Monitoramento][deployment-guide-configure-monitoring-scenario-1].
    1. Disponibilidade: selecione um conjunto de disponibilidade ou insira os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte o capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3].
1. Resumo: valide as informações fornecidas na página de resumo e clique em OK.

Depois que você concluir o assistente, sua máquina virtual será implantada no grupo de recursos selecionado.

#### Criar uma máquina virtual usando um modelo
Você também pode criar uma implantação usando um dos modelos de SAP publicados no [repositório azure-quickstart-templates do github][azure-quickstart-templates-github]. Ou pode criar uma máquina virtual manualmente usando o [Portal do Azure][virtual-machines-windows-tutorial], o [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] ou a [CLI do Azure][virtual-machines-linux-tutorial].

* [Modelo de configuração de duas camadas (apenas uma máquina virtual)][sap-templates-2-tier-marketplace-image] Use este modelo se quiser criar um sistema de duas camadas usando apenas uma máquina virtual.
* [Modelo de configuração de três camadas (várias máquinas virtuais)][sap-templates-3-tier-marketplace-image] Use este modelo se quiser criar um sistema de três camadas usando várias máquinas virtuais.

Após você ter aberto um dos modelos acima, o Portal do Azure navegará até o painel Editar Parâmetros. Insira as seguintes informações:

* **sapSystemId**: ID do sistema SAP
* **osType**: sistema operacional que você deseja implantar, por exemplo, Windows Server 2012 R2 ou SLES 12
    * A lista contém somente versões com suporte da SAP no Microsoft Azure
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAPs que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de tecnologia SAP ou Integrador de sistemas
* **systemAvailability**: (apenas para o modelo de três camadas) disponibilidade do sistema 
    * Selecione HA para uma configuração adequada para uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois servidores para o ASCS serão criados.
* storageType: (apenas para o modelo de duas camadas) tipo de armazenamento que deve ser usado 
    * Para sistemas maiores, é altamente recomendável utilizar o Armazenamento Premium. Para obter mais informações sobre os diferentes tipos de armazenamento, leia 
        * [Armazenamento do Microsoft Azure][dbms-guide-2.3] no [Guia de DBMS][dbms-guide]
        * [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure][storage-premium-storage-preview-portal]
        * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome de usuário e senha
    * É criado um novo usuário que pode ser usado para fazer logon no computador.
* **newOrExistingSubnet**: determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede existente.
* **subnetId**: a ID da sub-rede à qual as máquinas virtuais devem estar conectadas. Selecione a sub-rede da sua VPN ou a rede virtual da Rota Expressa para conectar a máquina virtual à sua rede local. A ID geralmente é semelhante a /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Após ter inserido todos os parâmetros, selecione a assinatura e o grupo de recursos que deseja usar. Você pode selecionar um grupo de recursos existente ou criar um novo selecionando "+Novo" no menu suspenso. Se criar um novo grupo de recursos, você também precisará selecionar a região na qual o grupo de recursos e a máquina virtual serão criados.

Leia os termos legais, aceite-os e clique em Criar.

Observe que o Agente de VM do Azure é implantado por padrão quando uma imagem do Azure Marketplace é usada.

#### Definir configurações de proxy
Dependendo da configuração da rede local, poderá ser necessário configurar o proxy em sua máquina virtual se ela estiver conectada à rede local por meio da VPN ou da Rota Expressa. Caso contrário, é possível que a máquina virtual não consiga acessar a Internet e, portanto, não consiga baixar as extensões necessárias ou coletar dados de monitoramento. Consulte o capítulo [Configurar proxy][deployment-guide-configure-proxy] neste documento.

#### Ingressar em domínio (somente para Windows)
Caso a implantação no Azure esteja conectada ao AD/DNS local por meio do Azure Site a Site ou da Rota Expressa (também chamado de conexão entre locais no [Guia de Planejamento e Implementação][planning-guide]), é esperado que a VM ingresse um domínio local. Considerações sobre esta etapa são descritas no capítulo [Ingressar VM em domínio local (somente para Windows)][deployment-guide-4.3] deste documento.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar o monitoramento
Configure a Extensão de Monitoramento Avançado do Azure para SAP, conforme descrito no capítulo [Configurar Extensão de Monitoramento Avançado do Azure para SAP][deployment-guide-4.5] deste documento.

Verifique os pré-requisitos para Monitoramento do SAP para ver as versões mínimas necessárias do Kernel do SAP e do Agente de Host do SAP nos recursos listados no capítulo [Recursos de SAP][deployment-guide-2.2] deste documento.

#### Verificação de monitoramento
Verifique se o monitoramento está funcionando conforme descrito no capítulo [Verificações e solução de problemas para configuração de monitoramento de ponta a ponta para SAP no Azure][deployment-guide-troubleshooting-chapter].

#### Etapas pós-implantação
Após a criação da VM, ela será implantada e caberá a você instalar todos os componentes de software necessário na VM. Portanto, esse tipo de implantação de VM exigiria que o software a ser instalado já estivesse disponível no Microsoft Azure em alguma outra VM ou como um disco que pudesse ser anexado. Ou estamos analisando cenários de conexão entre locais em que a conectividade com os ativos locais (compartilhamentos de instalação) seja presumida.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implantando uma VM com uma imagem personalizada para SAP
Conforme descrito de forma detalhada no [Guia de Planejamento e Implementação][planning-guide], há uma maneira de preparar e criar uma imagem personalizada e usá-la para criar várias novas VMs. A sequência de etapas no fluxograma seria semelhante a:
 
![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM do Marketplace provado][deployment-guide-figure-300]

As etapas no fluxograma a seguir precisam ser executadas:

#### Criar máquina virtual
Para criar uma implantação usando uma imagem de SO privada por meio do Portal do Azure, use um dos modelos de SAP publicados no [repositório azure-quickstart-templates do github][azure-quickstart-templates-github]. Você também pode criar uma máquina virtual manualmente usando o [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [Modelo de configuração de duas camadas (apenas uma máquina virtual)][sap-templates-2-tier-user-image] Use este modelo se quiser criar um sistema de duas camadas usando apenas uma máquina virtual e sua própria imagem de SO.
* [Modelo de configuração de três camadas (várias máquinas virtuais)][sap-templates-3-tier-user-image] Use este modelo se quiser criar um sistema de três camadas usando várias máquinas virtuais e sua própria imagem de SO.

Após você ter aberto um dos modelos acima, o Portal do Azure navegará até o painel Editar Parâmetros. Insira as seguintes informações:

* **sapSystemId**: ID do sistema SAP
* **osType**: tipo de sistema operacional que você deseja implantar, Windows ou Linux
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAPs que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de tecnologia SAP ou Integrador de sistemas
* **systemAvailability**: (apenas para o modelo de três camadas) disponibilidade do sistema 
    * Selecione HA para uma configuração adequada para uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois servidores para o ASCS serão criados.
* **storageType**: (apenas para o modelo de duas camadas) tipo de armazenamento que deve ser usado 
    * Para sistemas maiores, é altamente recomendável utilizar o Armazenamento Premium. Para obter mais informações sobre os diferentes tipos de armazenamento, leia 
        * [Armazenamento do Microsoft Azure][dbms-guide-2.3] no [Guia de DBMS][dbms-guide]
        * [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure][storage-premium-storage-preview-portal]
        * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome de usuário e senha
    * É criado um novo usuário que pode ser usado para fazer logon no computador.
* **userImageVhdUri**: URI do VHD da imagem de SO privada, por exemplo, https://`<nome\_da\_conta`>.blob.core.windows.net/vhds/imagem\_do\_usuário.vhd
* **userImageStorageAccount**: nome da conta de armazenamento em que a imagem de SO privada está armazenada, por exemplo, `<accountname`> no exemplo de URI acima
* **newOrExistingSubnet**: determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede existente.
* **subnetId**: a ID da sub-rede à qual as máquinas virtuais devem estar conectadas. Selecione a sub-rede da sua VPN ou a rede virtual da Rota Expressa para conectar a máquina virtual à sua rede local. A ID geralmente é semelhante a /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Após ter inserido todos os parâmetros, selecione a assinatura e o grupo de recursos que deseja usar. Você pode selecionar um grupo de recursos existente ou criar um novo selecionando "+Novo" no menu suspenso. Se criar um novo grupo de recursos, você também precisará selecionar a região na qual o grupo de recursos e a máquina virtual serão criados.

Leia os termos legais, aceite-os e clique em Criar.

#### Instalar o agente de VM (somente para Linux)
O agente do Linux já deverá estar instalado na imagem do usuário se você quiser usar os modelos acima. Caso contrário, a implantação falhará. Baixe e instale o agente de VM na imagem do usuário, conforme descrito no capítulo [Baixar, instalar e habilitar o agente de VM do Azure][deployment-guide-4.4] deste documento. Se não usar os modelos acima, você também poderá instalar o agente de VM posteriormente.

#### Ingressar em domínio (somente para Windows)
Caso a implantação no Azure esteja conectada ao AD/DNS local por meio do Azure Site a Site ou da Rota Expressa (também chamado de conexão entre locais no [Guia de Planejamento e Implementação][planning-guide]), é esperado que a VM ingresse um domínio local. Considerações sobre esta etapa são descritas no capítulo [Ingressar VM em domínio local (somente para Windows)][deployment-guide-4.3] deste documento.

#### Definir configurações de proxy
Dependendo da configuração da rede local, poderá ser necessário configurar o proxy em sua máquina virtual se ela estiver conectada à rede local por meio da VPN ou da Rota Expressa. Caso contrário, é possível que a máquina virtual não consiga acessar a Internet e, portanto, não consiga baixar as extensões necessárias ou coletar dados de monitoramento. Consulte o capítulo [Configurar proxy][deployment-guide-configure-proxy] neste documento.

#### Configurar o monitoramento
Configure a Extensão de Monitoramento do Azure para SAP, conforme descrito no capítulo [Configurar Monitoramento Avançado do Azure para SAP][deployment-guide-4.5] deste documento. Verifique os pré-requisitos para Monitoramento do SAP para ver as versões mínimas necessárias do Kernel do SAP e do Agente de Host do SAP nos recursos listados no capítulo [Recursos de SAP][deployment-guide-2.2] deste documento.

#### Verificação de monitoramento
Verifique se o monitoramento está funcionando conforme descrito no capítulo [Verificações e solução de problemas para configuração de monitoramento de ponta a ponta para SAP no Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP
Este cenário trata do caso de um sistema SAP que é movido em sua forma atual do local para o Azure. Isso significa que não há alterações de nome de host do Windows ou do Linux, da SID do SAP ou de algo parecido. Nesse caso, o VHD não é referenciado como uma imagem durante a implantação, mas é usado diretamente como o disco do sistema operacional. No que diz respeito à implantação, este caso é diferente do dois casos anteriores pelo fato de que o Agente de VM não pode ser instalado automaticamente durante a implantação. Portanto, o Agente de VM do Azure precisa ser baixado da Microsoft e ser instalado e habilitado na VM manualmente posteriormente. Depois dessa tarefa ter sido realizada com êxito, pode continuar iniciando a Extensão de Monitoramento do Azure para Host SAP e sua configuração. Para obter detalhes sobre a função do Agente de VM do Azure, consulte este artigo:

[comentário]: <> (MSSedusch TODO Atualizar link do Windows abaixo)

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/>

___

O fluxo de trabalho das diferentes etapas é semelhante a:
 
![Fluxograma de implantação de VM para sistemas SAP usando um disco de VM][deployment-guide-figure-400]

Supondo que o disco já tenha sido carregado e definido no Azure (consulte o [Guia de Planejamento e Implantação][planning-guide]), siga estas etapas

#### Criar máquina virtual
Para criar uma implantação usando um disco de SO privado por meio do Portal do Azure, use o modelo de SAP publicado no [repositório azure-quickstart-templates do github][azure-quickstart-templates-github]. Você também pode criar uma máquina virtual manualmente usando o PowerShell ou a CLI do Azure.

* [Modelo de configuração de duas camadas (apenas uma máquina virtual)][sap-templates-2-tier-os-disk]
    * Use este modelo para criar um sistema de duas camadas usando apenas uma máquina virtual.

Após você ter aberto o modelo acima, o Portal do Azure navegará até o painel Editar Parâmetros. Insira as seguintes informações:

* **sapSystemId**: ID do sistema SAP
* **osType**: tipo de sistema operacional que você deseja implantar, Windows ou Linux
* **sapSystemSize**: o tamanho do sistema SAP
    * A quantidade de SAPs que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de tecnologia SAP ou Integrador de sistemas
* **storageType**: (apenas para o modelo de duas camadas) tipo de armazenamento que deve ser usado 
    * Para sistemas maiores, é altamente recomendável utilizar o Armazenamento Premium. Para obter mais informações sobre os diferentes tipos de armazenamento, leia 
        * [Armazenamento do Microsoft Azure][dbms-guide-2.3] no [Guia de DBMS][dbms-guide]
        * [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure][storage-premium-storage-preview-portal]
        * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
* **osDiskVhdUri**: URI do disco de SO privado, por exemplo, https://`<nome\_da\_conta`>.blob.core.windows.net/vhds/disco\_de\_so.vhd
* **newOrExistingSubnet**: determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede existente.
* **subnetId**: a ID da sub-rede à qual as máquinas virtuais devem estar conectadas. Selecione a sub-rede da sua VPN ou a rede virtual da Rota Expressa para conectar a máquina virtual à sua rede local. A ID geralmente é semelhante a /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Após ter inserido todos os parâmetros, selecione a assinatura e o grupo de recursos que deseja usar. Você pode selecionar um grupo de recursos existente ou criar um novo selecionando "+Novo" no menu suspenso. Se criar um novo grupo de recursos, você também precisará selecionar a região na qual o grupo de recursos e a máquina virtual serão criados.

Leia os termos legais, aceite-os e clique em Criar.

#### Instalar o Agente de VM
O agente do Linux já deverá estar instalado no disco de SO se você quiser usar os modelos acima. Caso contrário, a implantação falhará. Baixe e instale o agente de VM na VM, conforme descrito no capítulo [Baixar, instalar e habilitar o agente de VM do Azure][deployment-guide-4.4] deste documento.

Se não usar os modelos acima, você também poderá instalar o agente de VM posteriormente.

#### Ingressar em domínio (somente para Windows)
Caso a implantação no Azure esteja conectada ao AD/DNS local por meio do Azure Site a Site ou da Rota Expressa (também chamado de conexão entre locais no [Guia de Planejamento e Implementação][planning-guide]), é esperado que a VM ingresse um domínio local. Considerações sobre esta etapa são descritas no capítulo [Ingressar VM em domínio local (somente para Windows)][deployment-guide-4.3] deste documento.

#### Definir configurações de proxy
Dependendo da configuração da rede local, poderá ser necessário configurar o proxy em sua máquina virtual se ela estiver conectada à rede local por meio da VPN ou da Rota Expressa. Caso contrário, é possível que a máquina virtual não consiga acessar a Internet e, portanto, não consiga baixar as extensões necessárias ou coletar dados de monitoramento. Consulte o capítulo [Configurar proxy][deployment-guide-configure-proxy] neste documento.

#### Configurar o monitoramento
Configure a Extensão de Monitoramento Avançado do Azure para SAP, conforme descrito no capítulo [Configurar Monitoramento Avançado do Azure para SAP][deployment-guide-4.5] deste documento.

Verifique os pré-requisitos para Monitoramento do SAP para ver as versões mínimas necessárias do kernel do SAP e do Agente de Host do SAP nos recursos listados no capítulo [Recursos de SAP][deployment-guide-2.2] deste documento.

#### Verificação de monitoramento
Verifique se o monitoramento está funcionando conforme descrito no capítulo [Verificações e solução de problemas para configuração de monitoramento de ponta a ponta para SAP no Azure][deployment-guide-troubleshooting-chapter].

### Cenário 4: Atualizando a configuração de monitoramento para SAP
Há casos em que você precisa atualizar a configuração de monitoramento:

* A equipe conjunta da MS/SAP estendeu os recursos de monitoramentos e decidiu adicionar mais contadores ou excluir alguns contadores. 
* A Microsoft apresenta uma nova versão da infraestrutura subjacente do Azure que fornece os dados de monitoramento e a Extensão de Monitoramento Avançado do Azure para SAP está se adaptando a essas alterações.
* Você adiciona mais VHDs montados em sua VM do Azure ou remove um VHD. Nesse caso, você precisa atualizar a coleção de dados relacionados a armazenamento. Se você alterar a configuração adicionando ou excluindo pontos de extremidade ou atribuindo endereços IP a uma VM, isso não afetará a configuração de monitoramento.
* Você altera o tamanho de sua VM do Azure, por exemplo, de A5 para qualquer outro tamanho de VM.
* Você adiciona novas interfaces de rede à sua VM do Azure

Para atualizar a configuração de monitoramento, faça o seguinte:

* Atualize a infra-estrutura de monitoramento seguindo as etapas explicadas no capítulo [Configurar a Extensão de Monitoramento Avançado do Azure para SAP][deployment-guide-4.5] deste documento. Uma nova execução do script descrito neste capítulo detectará que uma configuração de monitoramento foi implantada e executará todas as alterações necessárias para a configuração de monitoramento. 

___

> ![Windows][Logo_Windows] Windows
>
> Para a atualização do Agente de VM do Azure, não é necessária intervenção do usuário. O Agente de VM atualiza a si mesmo automaticamente e não requer uma reinicialização da VM.
>
> ![Linux][Logo_Linux] Linux
>
> Siga as etapas em [neste artigo][virtual-machines-linux-update-agent] para atualizar o Agente Linux do Azure.

___

## Etapas detalhadas de implantação única

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implantando cmdlets do Azure PowerShell
* Acesse <https://azure.microsoft.com/downloads/>
* Na seção "Ferramentas de Linha de Comando", há uma seção chamada "Windows PowerShell". Siga o link "Instalar".
* O Gerenciador de Download da Microsoft será exibido com um item de linha terminado em .exe. Selecione a opção "Executar".
* Será exibido um pop-up perguntando se você deseja executar o Microsoft Web Platform Installer. Pressione SIM
* Será exibida uma tela como esta:
 
![Tela de instalação de cmdlets do Azure PowerShell][deployment-guide-figure-500] <a name="figure-5"></a>

* Pressione Instalar e aceite o EULA.

Verifique frequentemente se os cmdlets do PowerShell foram atualizados. Normalmente, há atualizações mensais. A maneira mais fácil de fazer isso é seguir as etapas de instalação conforme descrito acima, até a tela de instalação mostrada [nesta][deployment-guide-figure-5] figura. Nessa tela, a data de lançamento dos cmdlets é mostrada, bem como o número da versão atual. A menos que indicado de forma diferente nas Notas SAP [1928533] ou [2015553], é recomendável para trabalhar com a versão mais recente dos cmdlets do Azure PowerShell.

A versão dos cmdlets do Azure instalados atualmente no desktop/laptop pode ser verificada com o comando de PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

O resultado deve ser apresentado como mostrado abaixo [nesta][deployment-guide-figure-6] figura.

![Resultado da verificação de versão de cmdlet do Azure PowerShell][deployment-guide-figure-600] <a name="figure-6"></a>

Se a versão do cmdlet do Azure instalada no desktop/laptop for a versão atual, a primeira tela depois de iniciar o Microsoft Web Platform Installer será um pouco diferente da mostrada [nesta][deployment-guide-figure-5] figura.

Observe o círculo vermelho na [figura][deployment-guide-figure-7] abaixo.
 
![Tela de instalação de cmdlets do Azure PowerShell indicando que a versão mais recente dos cmdlets do Azure PowerShell está instalada][deployment-guide-figure-700] <a name="figure-7"></a>

Se a tela for semelhante à tela [acima][deployment-guide-figure-7], indicando que a versão mais recente do cmdlet do Azure já está instalada, não é necessário prosseguir com a instalação. Nesse caso, você pode "Sair" da instalação nesta etapa.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implantando a CLI do Azure
* Acesse <https://azure.microsoft.com/downloads/>
* Na seção "Ferramentas de Linha de Comando", há uma seção chamada "Interface de linha de comando do Azure". Siga o link de instalação do seu sistema operacional.

Verifique frequentemente se a CLI do Azure foi atualizada. Normalmente, há atualizações mensais. A maneira mais fácil de fazer isso é seguir as etapas de instalação descritas acima.

A versão da CLI do Azure instalada atualmente no desktop/laptop pode ser verificada com o comando de PS:

```
azure --version
```

O resultado deve ser apresentado como mostrado abaixo [nesta][deployment-guide-figure-azure-cli-version] figura.

![Resultado da verificação de versão da CLI do Azure][deployment-guide-figure-760] <a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Ingressar a VM no domínio local, somente Windows
Em casos em que você implantar VMs SAP em um cenário com conexão entre locais em que o AD local e o DNS são estendidos para o Azure, espera-se que as VMs ingressem em um domínio local. As etapas detalhadas para ingressar uma VM em um domínio local e os softwares adicionais necessários para ser membro de um domínio local dependem do cliente. Normalmente, ingressar uma VM em um domínio local significa instalar software adicional, como software de proteção contra malware, ou vários agentes de software de backup ou monitoramento.

Além disso, você precisa certificar-se de que, para casos em que as configurações de proxy de Internet são forçadas ao ingressar em um domínio, a Conta do sistema local do Windows (S-1-5-18) na VM convidada também tenha essas configurações. A opção mais fácil é forçar o proxy com a Política de grupo do domínio, que se aplica a sistemas dentro do domínio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Baixar, instalar e habilitar o agente de VM do Azure
As etapas a seguir são necessárias quando uma VM para SAP é implantada por meio de uma imagem de SO que não seja generalizada, por exemplo, não seja preparada para o sistema Windows. Não é necessário instalar o agente para máquinas virtuais implantadas por meio do Azure Marketplace. Essas imagens já contêm o Agente do Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Baixe o Agente de VM do Azure:
	* Baixe o pacote do instalador do Agente de VM do Azure de: <https://go.microsoft.com/fwlink/?LinkId=394789>
	* Armazene o pacote de MSI do Agente de VM localmente no laptop ou em um servidor
* Instale o Agente de VM do Azure:
	* Conecte-se à VM do Azure implantada com os Serviços de Terminal (RDP)
	* Abra uma janela do Windows Explorer na VM e abra um diretório de destino para o arquivo MSI do Agente de VM
	* Arraste e solte o arquivo MSI do Instalador de Agente de VM do Azure de seu laptop/servidor local para o diretório de destino do Agente de VM na VM
	* Clique duas vezes no arquivo MSI na VM
	* Para uma VM ingressada em domínios locais, certifique-se de que as configurações eventuais de proxy de Internet se apliquem também à Conta de sistema local do Windows (S-1-5-18) na VM, conforme descrito no capítulo [Configurar proxy][deployment-guide-configure-proxy]. O Agente de VM será executado nesse contexto e precisará ser capaz de se conectar ao Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Instale o Agente de VM para Linux usando o seguinte comando

```
sudo zypper install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar proxy
As etapas para configurar o proxy diferem entre o Windows e o Linux.

#### Windows
Essas configurações também devem ser válidas para a conta LocalSystem acessar a Internet. Se as configurações de proxy não forem definidas pela política de grupo, você poderá configurá-las para a conta LocalSystem seguindo estas etapas.

1.	Abra gpedit.msc
1.	Navegue até Configuração do Computador –> Modelos Administrativos -> Componentes do Windows -> Internet Explorer e habilite "Definir as configurações de proxy por máquina (não por usuário)"
1.	Abra o Painel de Controle e navegue até Rede e Internet -> Opções de Internet
1.	Abra a guia Conexões e clique em Configurações da LAN
1.	Desabilite "Detectar configurações automaticamente"
1.	Habilite "Usar um servidor proxy para a rede local" e insira a porta e o host do proxy

#### Linux
Defina o proxy correto no arquivo de configuração do Agente Convidado do Microsoft Azure, que fica localizado em /etc/waagent.conf. Os parâmetros a seguir precisam ser definidos:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Reinicie o agente depois de ter alterado sua configuração com

```
sudo service waagent restart
```

As configurações de proxy em /etc/waagent.conf também se aplicam para as extensões de VM necessárias. Se você quiser usar os repositórios do Azure, certifique-se de que o tráfego para esses repositórios não passe pela intranet local. Se você tiver criado rotas definidas pelo usuário para habilitar o túnel forçado, certifique-se de adicionar uma rota que encaminhe o tráfego para 104.45.17.148 diretamente pela Internet, sem passar por sua conexão site a site. Você também precisa adicionar rotas para os endereços IP listados em /etc/regionserverclnt.cfg. Um exemplo é mostrado na captura de tela abaixo. Para obter mais detalhes sobre rotas definidas pelo usuário, consulte [este artigo][virtual-networks-udr-overview].

![Túnel forçado][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar a extensão de monitoramento aprimorado do Azure para SAP
Quando a VM estiver preparada conforme descrito no capítulo [Cenários de implantação de VMs para SAP no Microsoft Azure][deployment-guide-3], o Agente de VM do Azure será instalado na máquina. A próxima etapa importante é implantar a Extensão de Monitoramento Avançado do Azure para SAP, que está disponível no Repositório de extensões do Azure em data centers globais do Microsoft Azure. Para obter mais detalhes, consulte o [Guia de Planejamento e Implementação][planning-guide-9.1].

Você pode usar o Azure PowerShell ou a CLI do Azure para instalar e configurar a Extensão de Monitoramento Avançado do Azure para SAP. Leia o capítulo [Azure PowerShell][deployment-guide-4.5.1] se quiser instalar a extensão em uma VM Windows ou Linux usando uma máquina Windows. Para instalar a extensão em uma VM Linux usando um desktop Linux, leia o capítulo [CLI do Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para VMs Linux e Windows
Para realizar a tarefa de instalar a Extensão de Monitoramento Avançado do Azure para SAP, realize as seguintes etapas:

* Certifique-se de ter a versão mais recente do cmdlet do Microsoft Azure PowerShell instalada. Consulte o capítulo [Implantando cmdlets do Azure PowerShell][deployment-guide-4.1] deste documento.  
* Execute o seguinte cmdlet do PowerShell. Para obter uma lista dos ambientes disponíveis, execute o comando Get-AzureRmEnvironment. Se você quiser usar o Azure público, seu ambiente será AzureCloud. Para o Azure na China, selecione AzureChinaCloud.

```powershell
	$env = Get-AzureRmEnvironment -Name <name of the environment>
	Login-AzureRmAccount -Environment $env
	Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Após você ter fornecido os dados de sua conta e a Máquina Virtual do Azure, o script implantará as extensões necessárias e habilitará os recursos necessários. Isso pode levar vários minutos. Leia [este artigo do MSDN][msdn-set-azurermvmaemextension] para obter mais informações sobre Set-AzureRmVMAEMExtension.
  
![Tela de resultado de uma execução bem-sucedida do cmdlet do Azure específico para SAP Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Uma execução bem-sucedida de set-AzureRmVMAEMExtension realizará todas as etapas necessárias para configurar a funcionalidade de monitoramento de host para SAP.

A saída fornecida pelo script deve ser semelhante a esta:

* Confirmação de que a configuração de monitoramento para o VHD Base (que contém o sistema operacional), bem como todos os VHDs adicionais montados na VM, foram configurados.
* As duas próximas mensagens confirmam a configuração das métricas de armazenamento para uma conta de armazenamento específica. 
* Uma linha da saída fornecerá um status sobre a atualização real da configuração de monitoramento.
* Outra será exibida para confirmar que a configuração foi implantada ou atualizada.
* A última linha da saída é informativa, mostrando a possibilidade de testar a configuração de monitoramento.
* Para verificar se todas as etapas do Monitoramento Avançado do Azure foram executadas com êxito e se a infraestrutura do Azure fornece os dados necessários, faça a verificação de preparação para a Extensão de Monitoramento Avançado do Azure para SAP, conforme descrito no capítulo [Verificação de preparação para o Monitoramento Avançado do Azure para SAP][deployment-guide-5.1] neste documento. 
* Para continuar fazendo isso, aguarde de 15 a 30 minutos até que o Diagnóstico do Azure colete os dados relevantes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI do Azure para VMs Linux
Siga as etapas [neste artigo][install-extension-cli] para instalar a Extensão de Monitoramento Avançado do Azure para SAP em uma VM Linux por meio de um desktop/laptop Linux.

 [comentário]: <> (MSSedusch TODO Verificar se o link ainda é válido)

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e solução de problemas de configuração de monitoramento de ponta a ponta para SAP no Azure
Depois de ter implantado a VM do Azure e ter configurado a infraestrutura relevante de monitoramento do Azure, verifique se todos os componentes do Monitoramento Avançado do Azure estão funcionando de forma adequada.

Sendo assim, execute a verificação de preparação para a Extensão de Monitoramento Avançado do Azure para SAP, conforme descrito no capítulo [Verificação de preparação do Monitoramento Avançado do Azure para SAP][deployment-guide-5.1]. Se o resultado dessa verificação for positivo e você obtiver todos os contadores de desempenho relevantes, o monitoramento do Azure foi configurado com êxito. Nesse caso, prossiga com a instalação do Agente de Host do SAP, conforme descrito nas Notas da SAP listadas no capítulo [Recursos de SAP][deployment-guide-2.2] deste documento. Se o resultado da verificação de preparação indicar que estão faltando contadores, continue executando a verificação de integridade da infraestrutura de monitoramento do Azure, conforme descrito no capítulo [Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure][deployment-guide-5.2]. Caso haja algum problema com a configuração de monitoramento do Azure, verifique o capítulo [Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3] para obter ajuda com a solução de problemas.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de preparação para o monitoramento aprimorado do Azure para SAP
Com essa verificação, você garante que as métricas que serão mostradas em seu aplicativo SAP sejam fornecidas completamente pela infraestrutura de monitoramento do Azure subjacente.

#### Executar a verificação de preparação em uma VM Windows
Para executar a verificação de preparação, faça logon na máquina virtual do Azure (não é necessária uma conta de administrador) e execute as seguintes etapas:

* Abra um prompt de comando do Windows e mude para a pasta de instalação da Extensão de Monitoramento do Azure para SAP C:\\Pacotes\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop

A parte da versão fornecida no caminho para a extensão de monitoramento acima pode variar. Se vir várias pastas da versão da extensão de monitoramento na pasta de instalação, verifique a configuração do serviço Windows "AzureEnhancedMonitoring" e alterne para a pasta indicada como "Caminho do executável".
 
![Propriedades do serviço que executa a Extensão de Monitoramento Avançado do para SAP][deployment-guide-figure-1000]

* Execute azperflib.exe na janela de comando sem parâmetros.

> [AZURE.NOTE] O azperflib.exe é executado em um loop e atualiza os contadores coletados a cada 60 segundos. Para encerrar o loop, feche a janela de comando.

Se a Extensão de Monitoramento Avançado do Azure não estiver instalada ou o serviço "AzureEnhancedMonitoring" não estiver em execução, a extensão não foi configurada corretamente. Nesse caso, siga o capítulo [Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3] para obter instruções detalhadas de como reimplantar a extensão.

##### Verifique a saída de azperflib.exe
A saída de azperflib.exe mostra que todos os contadores de desempenho do Azure para SAP preenchidos. Na parte inferior da lista de contadores coletados, você encontra um resumo e um indicador de integridade, que indica o status do monitoramento do Azure.
 
![Saída da verificação de integridade executando azperflib.exe indicando que não há problemas][deployment-guide-figure-1100] <a name="figure-11"></a>

Verifique o resultado retornado para a saída da quantidade de "Total de contadores" que são relatados como vazios e para a "Verificação de integridade", conforme mostrado na figura [acima][deployment-guide-figure-11].

Você pode interpretar os valores resultantes da seguinte maneira:

| Valores resultantes de Azperflib.exe | Status de preparação do monitoramento do Azure |
| ------------------------------|----------------------------------- |
| **Total de contadores: vazio** | Os dois contadores de armazenamento do Azure a seguir podem estar vazios: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Todos os outros contadores devem conter valores. |
| **Verificação de integridade** | OK somente se o status retornado mostrar OK |

Se os dois valores retornados por azperflib.exe não mostrarem que todos os contadores preenchidos foram retornados corretamente, siga as instruções de verificação de integridade da configuração de infraestrutura de monitoramento do Azure conforme descrito no capítulo [Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure][deployment-guide-5.2] abaixo.

#### Executar a verificação de preparação em uma VM Linux
Para executar a verificação de preparação, conecte-se via SSH à máquina virtual do Azure e execute as seguintes etapas:

* Verifique a saída da Extensão de Monitoramento Avançado do Azure
    * more /var/lib/AzureEnhancedMonitor/PerfCounters
        * Deve fornecer uma lista dos contadores de desempenho. O arquivo não deve estar vazio.
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
        * Deve retornar uma linha em que o erro é none, por exemplo, 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
    * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * Deve estar vazio ou não deve existir
* Se a primeira verificação anterior não tiver sido bem-sucedida, execute esses testes adicionais:
    * Certifique-se de que waagent esteja instalado seja iniciado
        * sudo ls -al /var/lib/waagent/
            * deve listar o conteúdo da pasta waagent
        * ps -ax | grep waagent
            * deve mostrar uma entrada semelhante a "python /usr/sbin/waagent-daemon"
    * Certifique-se de que a extensão de Diagnóstico do Linux esteja instalada e seja iniciada
        * sudo ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*
            * deve listar o conteúdo do diretório da Extensão de Diagnóstico do Linux
        * ps -ax | grep diagnostic
            * deve mostrar uma entrada semelhante a "python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py-daemon"
    * Certifique-se de que a Extensão de Monitoramento Avançado do Azure esteja instalada e seja iniciada
        * sudo ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/
    * deve listar o conteúdo do diretório da Extensão de Monitoramento Avançado do Azure
        * ps -ax | grep AzureEnhanced
            * deve mostrar uma entrada semelhante a "python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon"
* Instale o Agente de Host do SAP, conforme descrito na Nota SAP 1031096 e verifique a saída de saposcol
    * Execute /usr/sap/hostctrl/exe/saposcol -d
    * Execute dump ccm
    * Verifique se a métrica "Virtualization\_Configuration\\Enhanced Monitoring Access" é verdadeira
* Se você já tiver um servidor de aplicativos ABAP do SAP NetWeaver instalado, abra a transação ST06 e verifique se o monitoramento avançado está habilitado.

Se alguma das verificações acima falhar, siga o capítulo [Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3] para obter instruções detalhadas de como reimplantar a extensão.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure
Se algum dos dados de monitoramento não for entregue corretamente, conforme indicado pelo teste descrito no capítulo [Verificação de preparação para o Monitoramento Avançado do Azure para SAP][deployment-guide-5.1] acima, execute o cmdlet Test-AzureRmVMAEMExtension para testar se a configuração atual da infraestrutura de monitoramento do Azure e da extensão de monitoramento para SAP está correta.

Para testar a configuração de monitoramento, execute a seguinte sequência:

* Certifique-se de que você instalou a versão mais recente do cmdlet do Microsoft Azure PowerShell, conforme descrito no capítulo [Implantando cmdlets do Azure PowerShell][deployment-guide-4.1] deste documento.
* Execute o seguinte cmdlet do PowerShell. Para obter uma lista dos ambientes disponíveis, execute o comando Get-AzureRmEnvironment. Se você quiser usar o Azure público, seu ambiente será AzureCloud. Para o Azure na China, selecione AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Depois de você ter fornecido dados de sua conta e a Máquina Virtual do Azure, o script testará a configuração da máquina virtual que você escolher.

 
![Tela de entrada do cmdlet do Azure específico para SAP Test-VMConfigForSAP\_GUI][deployment-guide-figure-1200]

Depois de você ter inserido as informações sobre sua conta e a Máquina Virtual do Azure, o script testará a configuração da máquina virtual que você escolher.
 
![Saída de um teste bem-sucedido da infraestrutura de monitoramento do Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que todas as verificações estejam marcadas com OK. Se alguma das verificações não estiver ok, execute o cmdlet de atualização conforme descrito no capítulo [Configurar a Extensão de Monitoramento Avançado do Azure para SAP][deployment-guide-4.5] deste documento. Aguarde 15 minutos e execute novamente as verificações descritas nos capítulos [Verificação de preparação para o monitoramento aprimorado do Azure para SAP][deployment-guide-5.1] e [Verificação de integridade para a configuração de infraestrutura de monitoramento do Azure][deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, vá para o capítulo [Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Mais soluções de problemas da infraestrutura de monitoramento do Azure para SAP

#### ![Windows][Logo_Windows] Os contadores de desempenho do Azure não aparecem
A coleta de métricas de desempenho no Azure é feita pelo serviço Windows "AzureEnhancedMonitoring". Se o serviço não tiver sido instalado corretamente ou não estiver em execução na sua VM, nenhuma métrica de desempenho poderá ser coletada.

##### O diretório de instalação da Extensão de Monitoramento Avançado do Azure está vazio 

###### Problema
O diretório de instalação C:\\Pacotes\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`> \\drop está vazio.

###### Solução
A extensão não está instalada. Verifique se há um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reinicializar o computador e/ou executar novamente o script de configuração Set-AzureRmVMAEMExtension

##### O serviço de Monitoramento Avançado do Azure não existe 

###### Problema
O serviço Windows "AzureEnhancedMonitoring" não existe. Azperflib.exe: a saída de azperlib.exe gera um erro, conforme mostrado na [figura abaixo][deployment-guide-figure-14].
 
![A execução de azperflib.exe indica que o serviço da Extensão de Monitoramento Avançado do Azure para SAP não está em execução][deployment-guide-figure-1400] <a name="figure-14"></a>

###### Solução
Se o serviço não existir, conforme mostrado na [figura acima][deployment-guide-figure-14], a Extensão de Monitoramento do Azure para SAP não foi instalada corretamente. Reimplante a extensão de acordo com as etapas descritas para seu cenário de implantação no capítulo [Cenários de implantação de VMs para SAP no Microsoft Azure][deployment-guide-3].

Após a implantação da extensão, verifique novamente se os contadores de desempenho do Azure são fornecidos na VM do Azure após uma hora.

##### O serviço de Monitoramento Avançado do Azure existe, mas não pode ser iniciado 

###### Problema
O serviço Windows "AzureEnhancedMonitoring" existe e está habilitado, mas não pode ser iniciado. Verifique o log de eventos do aplicativo para obter mais informações.

###### Solução
Configuração incorreta. Habilite novamente a extensão de monitoramento para a VM, conforme descrito no capítulo [Configurar Extensão de Monitoramento Avançado do Azure para SAP][deployment-guide-4.5].

#### ![Windows][Logo_Windows] Faltam alguns contadores de desempenho do Azure
A coleta de métricas de desempenho no Azure é feita pelo serviço Windows "AzureEnhancedMonitoring", que obtém dados de diferentes fontes. Alguns dados de configuração são coletados localmente, as métricas de desempenho são lidas do Diagnóstico do Azure e os contadores de armazenamento são usados por meio de seu registro de log no nível de assinatura de armazenamento.

Se a solução de problemas usando a Nota SAP [1999351] não ajudar, execute novamente o script de configuração Set-AzureRmVMAEMExtension. Talvez você precise esperar por uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de terem sido habilitados. Se o problema persistir, abra uma mensagem de suporte do cliente SAP no componente BC-OP-NT-AZR.

#### ![Linux][Logo_Linux] Os contadores de desempenho do Azure não aparecem

A coleta de métricas de desempenho no Azure é feita por um deamon. Se o deamon não estiver em execução, nenhuma métrica de desempenho poderá ser coletada.

##### O diretório de instalação da Extensão de Monitoramento Avançado do Azure está vazio 

###### Problema
O diretório /var/lib/waagent/ não contém uma subpasta para a Extensão de Monitoramento Avançado do Azure.

###### Solução
A extensão não está instalada. Verifique se há um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reinicializar o computador e/ou executar novamente o script de configuração Set-AzureRmVMAEMExtension

#### ![Linux][Logo_Linux] Faltam alguns contadores de desempenho do Azure

A coleta de métricas de desempenho no Azure é feita por um daemon, que obtém dados de diferentes fontes. Alguns dados de configuração são coletados localmente, as métricas de desempenho são lidas do Diagnóstico do Azure e os contadores de armazenamento são usados por meio de seu registro de log no nível de assinatura de armazenamento.

Para ver uma completa e atualizada dos conhecidos problemas, consulte a Nota SAP [1999351], que contém informações adicionais de solução de problemas para o Monitoramento Avançado do Azure para SAP.

Se a solução de problemas usando Nota SAP [1999351] não ajudar, execute novamente o script de configuração Set-AzureRmVMAEMExtension conforme descrito no capítulo [Configurar a Extensão de Monitoramento Avançado do Azure para SAP][deployment-guide-4.5]. Talvez você precise esperar por uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de terem sido habilitados. Se o problema persistir, abra uma mensagem de suporte do cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

<!---HONumber=AcomDC_0608_2016-->