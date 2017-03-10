# Visão geral
## [O que é a Site Recovery?](site-recovery-overview.md)
## [Como funciona o Site Recovery?](site-recovery-components.md)
## [Como funciona a replicação do Hyper-V para o Azure?](site-recovery-hyper-v-azure-architecture.md)
## [Quais cargas de trabalho você pode proteger?](site-recovery-workload.md)
## [Matriz de suporte do Site Recovery](site-recovery-support-matrix-to-azure.md)
## [PERGUNTAS FREQUENTES](site-recovery-faq.md)
## [Assistir a uma introdução](https://azure.microsoft.com/resources/videos/index/?services=recovery-manager)

# Introdução
## [Replicar VMs do VMware no Azure](site-recovery-vmware-to-azure.md)
## [Replicar VMs do VMware no Azure em uma implantação multilocatária (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Replicar VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md)
## [Replicar VMs do Hyper-V para o Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicar VMs VMware e servidores físicos para um site secundário](site-recovery-vmware-to-vmware.md)
## [Replicar VMs do Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm.md)

# Como:
## Plano
### [Pré-requisitos de implantação](site-recovery-prereq.md)
### [Considerações sobre infraestrutura de rede](site-recovery-network-design.md)
### [Executar o Planejador de Capacidade do Site Recovery para replicação do Hyper-V](site-recovery-capacity-planner.md)
### [Planejar a capacidade e dimensionamento de replicação do VMware no Azure](site-recovery-plan-capacity-vmware.md)
### [Executar o Planejador de Implantação para a replicação do VMware no Azure](site-recovery-deployment-planner.md)
## Configurar
### [Configure o ambiente de origem](site-recovery-set-up-vmware-to-azure.md)
### [Configure o ambiente de destino](site-recovery-prepare-target-vmware-to-azure.md)
### [Definir configurações de replicação](site-recovery-setup-replication-settings-vmware.md)
### [Implantar o serviço de mobilidade para replicação do VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Implante o serviço de Mobilidade usando o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Implante o serviço de Mobilidade com a DSC de Automação do Azure](site-recovery-automate-mobility-service-install.md)
## Failover e failback
### [Failover no Site Recovery](site-recovery-failover.md)
### [Configurar planos de recuperação](site-recovery-create-recovery-plans.md)
#### [Adicionar runbooks do Azure aos planos de recuperação](site-recovery-runbook-automation.md)
### [Executar um failover de teste para o Azure](site-recovery-test-failover-to-azure.md)
### [Executar um teste de failover entre dois sites do VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Fazer failback de VMs do VMware/servidores físicos do Azure](site-recovery-how-to-failback-azure-to-vmware.md)
### [Fazer failback de máquinas virtuais do Hyper-V a partir do Azure](site-recovery-failback-from-azure-to-hyper-v.md)

## Migrar
### [Migrar para o Azure](site-recovery-migrate-to-azure.md)
### [Migrar entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrar instâncias do Windows AWS para o Azure](site-recovery-migrate-aws-to-azure.md)
## Cargas de trabalho
### [Active Directory e DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Outras cargas de trabalho](site-recovery-workload.md#workload-summary)
## Automatizar a replicação
### [Automatizar a replicação do Hyper-V para o Azure (sem VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizar a replicação do Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizar a replicação do Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gerenciar
### [Remover servidores e desabilitar a proteção](site-recovery-manage-registration-and-protection.md)
### [Escalar horizontalmente servidores de processo](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Monitorar e solucionar problemas](site-recovery-monitoring-and-troubleshooting.md)

# Referência
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [Implantação clássica do PowerShell](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relacionados
## [Automação do Azure](/azure/automation/)

# Recursos
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Preços](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery)
