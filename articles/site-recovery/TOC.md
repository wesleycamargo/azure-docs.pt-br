# Visão geral
## [O que é a Site Recovery?](site-recovery-overview.md)
## [Como funciona o Site Recovery?](site-recovery-components.md)
## [Como funciona a replicação do Hyper-V para o Azure?](site-recovery-hyper-v-azure-architecture.md)
## [Quais cargas de trabalho você pode proteger?](site-recovery-workload.md)
## [Matriz de suporte do Site Recovery](site-recovery-support-matrix-to-azure.md)
## [PERGUNTAS FREQUENTES](site-recovery-faq.md)
## [Assistir a uma introdução](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Introdução
## [Replicar VMs do VMware para o Azure](site-recovery-vmware-to-azure.md)
## [Replicar servidores físicos para o Azure](site-recovery-physical-servers-to-azure.md)
## [Replicar VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md)
## [Replicar VMs do Hyper-V para o Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicar VMs do Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm.md)
## [Replicar VMs VMware e servidores físicos para um site secundário](site-recovery-vmware-to-vmware.md)
## [Replicar VMs do VMware no Azure em uma implantação multilocatária (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Como
## Plano
### [Pré-requisitos para replicação do Azure](site-recovery-prereq.md)
### [Planejar infraestrutura de rede](site-recovery-network-design.md)
### [Planejar mapeamento de rede](site-recovery-network-mapping.md)
### [Planejar a capacidade e dimensionamento de replicação do VMware no Azure](site-recovery-plan-capacity-vmware.md)
### [Planejador de Implantação para a replicação do VMware no Azure](site-recovery-deployment-planner.md)
### [Planejador de Capacidade para replicação do Hyper-V](site-recovery-capacity-planner.md)
### [Controle a replicação de VM com acesso baseado em função](site-recovery-role-based-linked-access-control.md)

## Configurar
### [Configure o ambiente de origem](site-recovery-set-up-vmware-to-azure.md)
### [Configure o ambiente de destino](site-recovery-prepare-target-vmware-to-azure.md)
### [Definir configurações de replicação](site-recovery-setup-replication-settings-vmware.md)
### [Implantar o serviço de mobilidade para replicação do VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Implante o serviço de Mobilidade usando o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Implante o serviço de Mobilidade com a DSC de Automação do Azure](site-recovery-automate-mobility-service-install.md)
### [Habilitar a replicação](site-recovery-replicate-vmware-to-azure.md)
## Failover e failback
### [Failover de computadores protegidos](site-recovery-failover.md)
### [Configurar planos de recuperação](site-recovery-create-recovery-plans.md)
#### [Adicionar runbooks do Azure aos planos de recuperação](site-recovery-runbook-automation.md)
### [Executar um failover de teste](site-recovery-test-failover-to-azure.md)
### [Proteger os computadores novamente após o failover](site-recovery-how-to-reprotect.md)
### [Failback do Azure](site-recovery-failback-azure-to-vmware.md)

## Migrar
### [Migrar para o Azure](site-recovery-migrate-to-azure.md)
### [Migrar entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrar instâncias do Windows AWS para o Azure](site-recovery-migrate-aws-to-azure.md)
## Cargas de trabalho
### [Active Directory e DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Aplicativos Web baseados em IIS](site-recovery-iis.md)
### [Citrix XenApp e XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Outras cargas de trabalho](site-recovery-workload.md#workload-summary)
## Automatizar a replicação
### [Automatizar a replicação do Hyper-V para o Azure (sem VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizar a replicação do Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizar a replicação do Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gerenciar
### [Editar as configurações de replicação](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Gerenciar servidores de processo no Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Gerenciar o servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Gerenciar servidores de processo dimensionados](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Gerenciar servidores vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Remover servidores e desabilitar a proteção](site-recovery-manage-registration-and-protection.md)
## [Monitorar e solucionar problemas](site-recovery-monitoring-and-troubleshooting.md)

# Referência
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [Implantação clássica do PowerShell](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relacionados
## [Automação do Azure](/azure/automation/)

# Recursos
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Preços](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery)
