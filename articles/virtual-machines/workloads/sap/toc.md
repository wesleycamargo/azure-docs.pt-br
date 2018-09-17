# Visão geral
## [Introdução](get-started.md)
## [Certificações](sap-certifications.md)
# SAP HANA no Azure (Instâncias Grandes)
## Visão geral
### [O que é SAP HANA no Azure (Instâncias Grandes)?](hana-overview-architecture.md)
### [Conhecer os termos](hana-know-terms.md)
### [Certificação](hana-certification.md)
### [SKUs disponíveis para HLI](hana-available-skus.md)
### [Dimensionamento](hana-sizing.md)
### [Requisitos de integração](hana-onboarding-requirements.md)
### [Nós de extensão e camadas de dados do SAP HANA](hana-data-tiering-extension-nodes.md)
### [Responsabilidades e modelo de operações](hana-operations-model.md)
## Arquitetura
### [Arquitetura geral](hana-architecture.md)
### [Arquitetura de rede](hana-network-architecture.md)
### [Arquitetura de armazenamento](hana-storage-architecture.md)
### [Cenários HLI com suporte](hana-supported-scenario.md)
## Infraestrutura e conectividade
### [Implantação do HLI](hana-overview-infrastructure-connectivity.md)
### [Conectar VMs do Azure para Instâncias Grandes do HANA](hana-connect-azure-vm-large-instances.md)
### [Conectar uma VNET ao ExpressRoute de Instância Grande do HANA](hana-connect-vnet-express-route.md)
### [Requisitos de rede adicionais](hana-additional-network-requirements.md)
## Instalar SAP HANA
### [Validar a configuração](hana-installation.md)
### [Instalação de exemplo do HANA](hana-example-installation.md)
## Alta disponibilidade e recuperação de desastres
### [Opções e considerações](hana-overview-high-availability-disaster-recovery.md)
### [Backup e restauração](hana-backup-restore.md)
### [Princípios e preparação](hana-concept-preparation.md)
### [Procedimento de failover de recuperação de desastre](hana-failover-procedure.md)
## Solucionar problemas e monitorar
### [HLI de Monitoramento](troubleshooting-monitoring.md)
### [Monitoramento e solução de problemas no lado do HANA](hana-monitor-troubleshoot.md)
## Como
### [Configuração de alta disponibilidade com STONITH](ha-setup-with-stonith.md)
### [Backup do sistema operacional para SKUs do tipo II](os-backup-type-ii-skus.md)
### [Atualização do sistema operacional para grandes instâncias do HANA](os-upgrade-hana-large-instance.md)
### [Configurando o servidor SMT para o SUSE Linux](hana-setup-smt.md)
# SAP HANA em Máquinas Virtuais do Azure
## [Instalação da única instância SAP HANA](hana-get-started.md)
## [Guia de implantação do HANA S/4 ou HANA BW/4 SAP CAL](cal-s4h.md)
## [Configurações de infraestrutura do SAP HANA e operações no Azure](hana-vm-operations.md)
## Disponibilidade do SAP HANA em Máquinas Virtuais do Azure
### [SAP HANA na visão geral de disponibilidade do Azure](sap-hana-availability-overview.md)
### [SAP HANA na disponibilidade do Azure dentro de uma região do Azure](sap-hana-availability-one-region.md)
### [SAP HANA na disponibilidade do Azure em regiões do Azure](sap-hana-availability-across-regions.md)
## [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
## [Visão geral de backup do SAP HANA](sap-hana-backup-guide.md)
## [Backup de nível de arquivo do SAP HANA](sap-hana-backup-file-level.md)
## [Backups de instantâneos de armazenamento do SAP HANA](sap-hana-backup-storage-snapshots.md)
# SAP NetWeaver e Business One em Máquinas Virtuais do Azure
## [SAP Business One em Máquinas Virtuais do Azure](business-one-azure.md)
## [SAP IDES no guia de implantação do Windows/SQL Server SAP CAL](cal-ides-erp6-erp7-sp3-sql.md)
## [SAP NetWeaver em VMs Linux do Azure](suse-quickstart.md)
## [Planejar e implementar o SAP NetWeaver no Azure](planning-guide.md)
## [Guia de Implantação do SAP NetWeaver](deployment-guide.md)
## [Conector SAP LaMa para o Azure](lama-installation.md)
## Guias de implantação de DBMS para carga de trabalho do SAP
### [Implantação do DBMS de Máquinas Virtuais do Azure geral para carga de trabalho do SAP](dbms_guide_general.md)
### [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP](dbms_guide_sqlserver.md)
### [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](dbms_guide_oracle.md)
### [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](dbms_guide_ibm.md)
### [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](dbms_guide_sapase.md)
### [SAP MaxDB, liveCache e implantação do Servidor de Conteúdo no Azure](dbms_guide_maxdb.md)
### Disponibilidade do SAP HANA em Máquinas Virtuais do Azure
### [SAP HANA na visão geral de disponibilidade do Azure](sap-hana-availability-overview.md)
### [SAP HANA na disponibilidade do Azure dentro de uma região do Azure](sap-hana-availability-one-region.md)
### [SAP HANA na disponibilidade do Azure em regiões do Azure](sap-hana-availability-across-regions.md)
## Alta disponibilidade (AD) no Windows e Linux
### [Visão geral](sap-high-availability-guide-start.md)
### Arquitetura da Alta Disponibilidade
#### [Arquitetura e Cenários de AD](sap-high-availability-architecture-scenarios.md)
#### [Arquitetura e Cenários de Mais Alta Disponibilidade](sap-higher-availability-architecture-scenarios.md)
#### [AD no Windows com Disco Compartilhado para a Instância do (A)SCS](sap-high-availability-guide-wsfc-shared-disk.md)
#### [AD no Windows com o Compartilhamento de Arquivos SOFS para a Instância do (A)SCS](sap-high-availability-guide-wsfc-file-share.md)
#### [AD no SUSE Linux para a Instância do (A)SCS](high-availability-guide-suse.md)
### Preparação da Infraestrutura do Azure
#### [Windows com Disco Compartilhado para a Instância do (A)SCS](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [Windows com Compartilhamento de Arquivos SOFS para a Instância do (A)SCS](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [Alta disponibilidade do NFS em VMs do Azure no SLES](high-availability-guide-suse-nfs.md)
#### [Pacemaker em SLES](high-availability-guide-suse-pacemaker.md)
### Instalação do SAP
#### [Windows com Disco Compartilhado para a Instância do (A)SCS](sap-high-availability-installation-wsfc-shared-disk.md)
#### [Windows com Compartilhamento de Arquivos SOFS para a Instância do (A)SCS](sap-high-availability-installation-wsfc-file-share.md)
#### [SUSE Linux com NFS para a Instância do (A)SCS](high-availability-guide-suse.md)
### MultiSID do SAP
#### [Windows com Disco Compartilhado para a Instância do (A)SCS](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [Windows com Compartilhamento de Arquivos SOFS para a Instância do (A)SCS](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [Azure Site Recovery da Recuperação de Desastre do SAP](../../../site-recovery/site-recovery-workload.md#protect-sap)
# Integração de Identidade do AAD SAP e Logon Único
## [Integração com o SAP Cloud](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com SAP Cloud Platform Identity Authentication](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Configurar Logon Único com a Plataforma de Nuvem do SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP HANA DBMS](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Logon Único de SAML do SAP Fiori Launchpad com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# Integração dos Serviços do Azure com o SAP
## [Usar SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [Usar o Conector do SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/)
