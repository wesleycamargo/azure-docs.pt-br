
# Visão geral
## [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md)

# Introdução
## [Fazer backup de arquivos e pastas](backup-try-azure-backup-in-10-mins.md)
## [Fazer backup de máquinas virtuais do Azure](backup-azure-vms-first-look.md)
## [Proteger VMs do Azure](backup-azure-vms-first-look-arm.md)

# Como
## Usar o PowerShell
### [VMs do Azure no portal do Azure](backup-azure-vms-automation.md)
### [VMs do Azure no portal clássico](backup-azure-vms-classic-automation.md)
### [DPM no portal do Azure](backup-dpm-automation.md)
### [DPM no portal clássico](backup-dpm-automation-classic.md)
### [Windows Server no portal do Azure](backup-client-automation.md)
### [Windows Server no portal clássico](backup-client-automation-classic.md)

## Servidor de Backup do Azure
### [Matriz de proteção do Servidor de Backup do Azure](backup-mabs-protection-matrix.md)
### Instalar ou atualizar
#### [Preparar cargas de trabalho do Servidor de Backup do Azure no portal do Azure](backup-azure-microsoft-azure-backup.md)
#### [Preparar cargas de trabalho do Servidor de Backup do Azure no portal clássico](backup-azure-microsoft-azure-backup-classic.md)
#### [Adicionar armazenamento ao Servidor de Backup do Azure](backup-mabs-add-storage.md)
#### [Atualizar Servidor de Backup do Azure para v.2](backup-mabs-upgrade-to-v2.md)
#### [Instalação autônoma do Servidor de Backup do Azure](backup-mabs-unattended-install.md)
### Proteger as cargas de trabalho
#### [Usar o Servidor de Backup do Azure para fazer backup de um servidor do VMware](backup-azure-backup-server-vmware.md)
#### [Usar o Servidor de Backup do Azure para fazer backup do Exchange](backup-azure-exchange-mabs.md)
#### [Usar o Servidor de Backup do Azure para fazer backup de um farm do SharePoint](backup-azure-backup-sharepoint-mabs.md)
#### [Usar o Servidor de Backup do Azure para fazer backup do SQL](backup-azure-sql-mabs.md)
#### [Proteger o estado do sistema e recuperação bare-metal](backup-mabs-system-state-and-bmr.md)
### Solucionar problemas
#### [Solucionar problemas de Servidor de Backup do Azure](backup-azure-mabs-troubleshoot.md)


## Data Protection Manager
### [Preparar as cargas de trabalho do DPM no portal do Azure](backup-azure-dpm-introduction.md)
### [Preparar as cargas de trabalho do DPM no portal clássico](backup-azure-dpm-introduction-classic.md)
### [Usar o DPM do System Center para fazer backup de um servidor do Exchange](backup-azure-backup-exchange-server.md)
### [Recuperar dados no cofre de Backup para um servidor DPM alternativo](backup-azure-alternate-dpm-server.md)
### [Usar o DPM para fazer backup de cargas de trabalho do SQL Server](backup-azure-backup-sql.md)
### [Usar o DPM para fazer backup de um farm do SharePoint](backup-azure-backup-sharepoint.md)

## VMs do Azure
### Preparar a VM
#### [Preparar máquinas virtuais do Azure](backup-azure-vms-prepare.md)
#### [Preparar as máquinas virtuais implantadas pelo Resource Manager](backup-azure-arm-vms-prepare.md)
### Planejar seu ambiente
#### [Planejar a infraestrutura de backup de VMs](backup-azure-vms-introduction.md)
### Fazer backup de VMs
#### [Fazer backup de máquinas virtuais do Azure no cofre de backup](backup-azure-vms.md)
#### [Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação](backup-azure-arm-vms.md)
#### [Fazer backup de máquinas virtuais criptografadas](backup-azure-vms-encryption.md)
### Gerenciar e monitorar VMs
#### [Gerenciar e monitorar os backups de VM do Azure no Portal Clássico](backup-azure-manage-vms-classic.md)
#### [Gerenciar backups de VM do Azure no Portal do Azure](backup-azure-manage-vms.md)
#### [Monitorar alertas de backups de VM do Azure no Portal do Azure](backup-azure-monitor-vms.md)
### Restaurar dados das VMs
#### [Recuperar arquivos de backups de VM do Azure](backup-azure-restore-files-from-vm.md)
#### [Restaurar máquinas virtuais no Azure](backup-azure-restore-vms.md)
#### [Restaurar VMs implantadas com o Resource Manager na Portal do Azure](backup-azure-arm-restore-vms.md)
#### [Restaurar chave e segredo do Key Vault para VMs criptografadas usando o Azure Backup](backup-azure-restore-key-secret.md)
#### [Restaurar máquinas virtuais criptografadas](backup-azure-vms-encryption.md)

## Banco de Dados SQL do Azure
### [Configurar retenção de backup de longo prazo](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Exibir backups em um cofre dos Serviços de Recuperação](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restaurar desde a retenção de backup de longo prazo](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Excluir backups de longo prazo do SQL do Azure](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Arquivos e pastas do Windows
### [Windows Server usando o modelo de implantação clássico](backup-configure-vault-classic.md)
### [Windows Server usando o modelo de implantação do Resource Manager](backup-configure-vault.md)
### [Gerenciar cofres de Backup usando o modelo de implantação clássico](backup-azure-manage-windows-server-classic.md)
### [Monitorar e gerenciar cofres dos Serviços de Recuperação](backup-azure-manage-windows-server.md)
### [Restaurar arquivos em um Windows Server usando o modelo de implantação do Resource Manager](backup-azure-restore-windows-server.md)
### [Recuperar arquivos em um Windows Server usando o modelo de implantação clássico](backup-azure-restore-windows-server-classic.md)

## [Perguntas frequentes](backup-azure-backup-faq.md)

## Solucionar problemas
### [Problemas de backup de VM do Azure no portal do Azure](backup-azure-vms-troubleshoot.md)
### [Problemas de backup de VM do Azure no portal clássico](backup-azure-vms-troubleshoot-classic.md)
### [Falha no backup de VM do Azure: não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Lentidão de backup de arquivos e pastas no Backup do Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Conceitos
## [Visão geral de cofres de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md)
## [Atualizando um cofre de Backup para um cofre de Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md)
## [Excluir um cofre de Backup do Azure](backup-azure-delete-vault.md)
## [Controle de acesso baseado em função](backup-rbac-rs-vault.md)
## [Segurança para backups híbridos](backup-azure-security-feature.md)
## [Configurar o backup offline](backup-azure-backup-import-export.md)
## [Substitua sua biblioteca de fitas](backup-azure-backup-cloud-as-tape.md)
## [Backups consistentes do aplicativo das VMs Linux](backup-azure-linux-app-consistent.md)

# Referência
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/backup/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=backup)
