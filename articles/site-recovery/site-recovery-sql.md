<properties 
	pageTitle="Recuperação de desastres com o SQL Server e o Azure Site Recovery" 
	description="O Azure Site Recovery coordena a replicação, failover e recuperação do SQL Server para um site secundário local ou para o Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="raynew"/>


# Recuperação de desastres com o SQL Server e o Azure Site Recovery 

Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por meio da coordenação da replicação, do failover e da recuperação de máquinas virtuais e servidores físicos. A Recuperação de Site dá suporte a vários mecanismos de replicação a fim de proteger, replicar e realizar de forma consistente o failover de máquinas para o Azure ou para um armazenamento secundário. Obtenha uma visão geral de todos os cenários de implantação na [Visão geral do Azure Site Recovery](site-recovery-overview.md).

 Este artigo descreve como proteger o back-end do SQL Server de um aplicativo usando uma combinação de tecnologias BCDR do SQL Server e de Recuperação de Site. Você deve ter uma boa compreensão dos recursos de BCDR (cluster de failover, grupos de disponibilidade AlwaysOn, espelhamento de banco de dados, envio de log) e de Recuperação de Site antes de implantar os cenários descritos neste artigo.



## Visão geral

Muitas cargas de trabalho usam o SQL Server como base. Aplicativos como o SharePoint, Dynamics e SAP usam o SQL Server para implementar os serviços de dados. Recursos de alta disponibilidade e recuperação de desastres do SQL Server, por exemplo, os grupos de disponibilidade do SQL Server, são usados para proteger e recuperar bancos de dados SQL Server.

A Recuperação de Site pode proteger o SQL Server em execução como uma máquina virtual Hyper-V, uma máquina virtual VMware ou como um servidor físico.

 |**Local para o próprio local** | **Local para o Azure** 
---|---|---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim 
**Servidor físico** | Sim | Sim


## Suporte e integração

A Recuperação de Site pode ser integrada com tecnologias nativas de BCDR do SQL Server, resumidas na tabela a fim de fornecer uma solução de recuperação de desastres.

**Recurso** |**Detalhes** | **Versão do SQL Server** 
---|---|---
**Grupo de disponibilidade AlwaysOn** | <p>Várias instâncias autônomas do SQL Server são executadas, cada uma delas, em um cluster de failover com vários nós.</p> <p>Os bancos de dados podem ser agrupados em grupos de failover, que podem ser copiados (espelhados) em instâncias do SQL Server, de modo que não exista a necessidade de armazenamento compartilhado.</p> <p>Fornece a recuperação de desastres entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.</p> | Edição SQL Server 2014/2012 Enterprise
**Clustering de failover (FCI AlwaysOn)** | <p>O SQL Server aproveita o clustering de failover do Windows para proporcionar a alta disponibilidade de cargas de trabalho local do SQL Server.</p><p>Os nós que executam instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância estiver inoperante, o cluster realizará o failover em outra.</p> <p>O cluster não protege contra falhas ou interrupções no armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, fiber channel ou VHDXs compartilhados.</p> | Edições do SQL Server Enterprise</p> <p>Edição SQL Server Standard (limitada a dois nós)
**Espelhamento de banco de dados (modo de alta segurança)** | Protege um único banco de dados para uma única cópia secundária. Disponível nos modos de replicação de alta segurança (síncrona) e de alto desempenho (assíncrono). Não requer um cluster de failover. | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise todas as edições</p>
**SQL Server autônomo** | O SQL Server e o banco de dados estão hospedados em um único servidor (físico ou virtual). O clustering de host é usado para alta disponibilidade, se o servidor for virtual. Sem alta disponibilidade no nível do convidado. | Edição Enterprise ou Standard



A tabela a seguir resume nossas recomendações para a integração de tecnologias de BCDR do SQL Server na implantação da Recuperação de Site.

**Versão** |**Edição** | **Implantação** | **Local para local** | **Local para o Azure** 
---|---|---|---|
SQL Server 2014 ou 2012 | Enterprise | Instância do cluster de failover | Grupos de disponibilidade AlwaysOn | Grupos de disponibilidade AlwaysOn
 | Enterprise | Grupos de disponibilidade AlwaysOn para alta disponibilidade | Grupo de disponibilidade AlwaysOn | Grupo de disponibilidade AlwaysOn
 | Padrão | Instância do cluster de failover | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
 | Enterprise ou Standard | Autônomo | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
SQL Server 2008 R2 | Enterprise ou Standard | Autônomo | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local


## Pré-requisitos de implantação

Veja o que você precisa antes de iniciar:


- Uma implantação local do SQL Server executando uma versão com suporte do SQL Server. Normalmente, você precisará também de um Active Directory para o servidor SQL.
- Os pré-requisitos para o cenário que você deseja implantar. Os pré-requisitos podem ser encontrados no artigo cada implantação. Eles estão listados e apresentam um link na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).
- Se você quiser configurar a recuperação no Azure, será necessário executar a ferramenta [Avaliação de preparação da máquina virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server a fim de verificar se são compatíveis com o Azure e com a Recuperação de Site.

## Configurar a proteção

Você precisará executar algumas etapas:

- Configurar o Active Directory
- Configurar a proteção para um de cluster de SQL Server ou servidor autônomo

### Configurar o Active Directory

Será necessário adicionar o Active Directory no site de recuperação secundário para que o SQL Server seja executado corretamente. Há duas opções:

- **Pequena empresa** — se você tiver uma quantidade pequena de aplicativos e um único controlador de domínio para o site local, e quiser realizar o failover de todo o site, recomendamos o uso da replicação da Recuperação de Site para replicar o controlador de domínio para o armazenamento de dados secundário ou para o Azure.

- **Empresa de médio e grande porte** — se você tiver uma grande quantidade de aplicativos, estiver executando uma floresta do Active Directory e quiser realizar o failover por aplicativo ou carga de trabalho, recomendamos a configuração de um controlador de domínio adicional no armazenamento de dados secundário ou no Azure. Observe que, se você estiver usando grupos de disponibilidade AlwaysOn para recuperar em um site remoto, recomendamos a configuração de outro controlador de domínio adicional no site secundário ou no Azure, a fim de usá-lo para a instância do SQL Server recuperada.

As instruções neste documento supõem que exista um controlador de domínio disponível no local secundário.

### Configurar a proteção para um SQL Server autônomo


Nessa configuração, recomendamos o uso da replicação de Recuperação de Site para proteger a máquina do SQL Server. As etapas exatas dependerão se o SQL Server está configurado como uma máquina virtual ou um servidor físico, e se você deseja replicar para o Azure ou para um site secundário local. Obtenha mais instruções sobre todos os cenários de implantação na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).


### Configurar a proteção para o cluster do SQL Server (2012 ou 2014 Enterprise)

Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade, ou uma instância do cluster de failover, recomendamos também o uso dos grupos de disponibilidade no site de recuperação. Observe que essa orientação serve para aplicativos que não usam transações distribuídas.

##### Local para o próprio local

1. [Configure bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma nova rede virtual no site secundário.
3. Configure uma VPN site a site entre a nova rede virtual e o site primário.
4. Crie uma máquina virtual no site de recuperação e instale nela o SQL Server.
5. Estenda os grupos de disponibilidade AlwaysOn existentes para a nova máquina virtual do SQL Server. Configure essa instância do SQL Server como uma cópia de réplica assíncrona.
6. Crie um ouvinte do grupo de disponibilidade ou atualize o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
7. Certifique-se de que o farm de aplicativos esteja configurado usando o ouvinte. Se a instalação tiver usado o nome de servidor do banco de dados, atualize-o para usar o ouvinte de modo que você não precise reconfigurá-lo após o failover.

#### Local para o Azure

Ao replicar para o Azure, a configuração de vários grupos de disponibilidade é um desafio, pois cada grupo de disponibilidade precisa de um ouvinte dedicado, e a configuração de cada ouvinte exige um serviço de nuvem separado. Recomendamos a configuração de um grupo de disponibilidade com todos os bancos de dados incluídos.

1. Crie uma rede virtual do Azure.
2. Configure uma VPN site a site entre o site local e esta rede.
3. Crie uma nova máquina virtual do Azure do SQL Server na rede e configure-a como uma réplica do grupo de disponibilidade assíncrona. Se você precisar de alta disponibilidade para a camada do SQL Server após o failover para o Azure, configure duas cópias da réplica assíncrona no Azure.
4. Configure uma réplica do controlador de domínio na rede virtual.
5. Certifique-se de que as extensões da máquina virtual estejam habilitadas na máquina virtual. Isso é necessário para enviar scripts específicos ao SQL Server em um plano de recuperação.
6. Configure um ouvinte de SQL Server para o grupo de disponibilidade usando o balanceador de carga interno do Azure.
7. Configure a camada de aplicativos para usar o ouvinte a fim de acessar a camada de bancos de dados. Para aplicativos que usam transações distribuídas, recomendamos o uso da Recuperação de Site com replicação SAN ou a replicação entre sites do VMWare.

### Configurar a proteção para o cluster do SQL Server (Standard ou 2008 R2)

Para um cluster executando o SQL Server Standard ou o SQL Server 2008 R2, recomendamos o uso da replicação da Recuperação de Site para proteger o SQL Server.

#### Local para o próprio local

- Para um ambiente Hyper-V, se o aplicativo usar transações distribuídas, recomendamos a implantação da [Recuperação de Site com replicação SAN](site-recovery-vmm-san.md).

- Para ambientes VMware, recomendamos a implantação da proteção [VMware para VMware](site-recovery-vmware-to-vmware.md).

#### Local para o Azure

A Recuperação de Site não dá suporte a clusters convidados ao replicar para o Azure. O SQL Server também não fornece uma solução de recuperação de desastres de baixo custo para a edição Standard. Recomendamos a proteção do cluster local do SQL Server para um SQL Server autônomo e sua recuperação no Azure.


1. Configure uma instância do SQL Server autônoma adicional no site local.
2. Configure essa instância para servir como um espelho para os bancos de dados que precisam de proteção. Configure o espelhamento no modo de alta segurança.
3.	Configure a Recuperação de Site no site local com base no ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).
4.	Use a replicação da Recuperação de Site para replicar a nova instância do SQL Server para o Azure. É uma cópia espelhada de alta segurança, portanto, será sincronizada com o cluster primário, mas será replicada para o Azure usando a replicação da Recuperação de Site.

O gráfico a seguir ilustra essa configuração.

![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Criar planos de recuperação

Os planos de recuperação agrupam as máquinas que devem passar por failover juntas. Saiba mais sobre os [planos de recuperação](site-recovery-create-recovery-plans.md) e [failover](site-recovery-failover.md) antes de iniciar.


### Criar um plano de recuperação para clusters do SQL Server (SQL Server 2012/2014 Enterprise)

#### Configurar scripts do SQL Server a fim de realizar o failover para o Azure

Neste cenário, aproveitamos os scripts personalizados e a automação do Azure para os planos de recuperação, a fim de configurar um failover com scripts dos grupos de disponibilidade do SQL Server.

1.	Crie um arquivo local para o script realizar um failover de um grupo de disponibilidade. Este exemplo de script especifica um caminho até o grupo de disponibilidade na réplica do Azure e realiza seu failover nessa instância de réplica. Esse script será executado na réplica de máquina virtual do SQL Server passando com a extensão de script personalizada.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Carregue o script em um blob em uma conta de armazenamento do Azure. Use este exemplo:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Crie um runbook de automação do Azure para invocar os scripts na réplica de máquina virtual do SQL Server no Azure. Use este exemplo de script para fazer isso. [Saiba mais](site-recovery-runbook-automation.md) sobre como usar runbooks de automação em planos de recuperação.

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o runbook de automação para realizar o failover de grupos de disponibilidade.

#### Configurar scripts do SQL Server a fim de realizar o failover para um site secundário

1. Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o script para realizar o failover de grupos de disponibilidade.

### Criar um plano de recuperação para clusters do SQL Server (Standard)

#### Configurar scripts do SQL Server a fim de realizar o failover para o Azure

1.	Crie um arquivo local para o script realizar o failover do espelhamento de banco de dados do SQL Server. Use este exemplo de script.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Carregue o script em um blob em uma conta de armazenamento do Azure. Use este exemplo de script.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Crie um runbook de automação do Azure para invocar o script na réplica de máquina virtual do SQL Server no Azure. Use este exemplo de script para fazer isso. [Saiba mais](site-recovery-runbook-automation.md) sobre como usar runbooks de automação em planos de recuperação. Verifique se o agente da máquina virtual está em execução na máquina virtual do SQL Server em estado de failover antes de fazer isso.

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. Adicione estas etapas ao plano de recuperação a fim de realizar o failover da camada do SQL Server:

	- Para um failover planejado, adicione um script no lado primário para desligar o cluster primário após "Desligamento do grupo".
	- Adicione a máquina virtual de espelhamento de banco de dados do SQL Server ao plano de recuperação, de preferência no primeiro grupo de inicialização.
3.	Adicione um script posterior ao failover para realizar o failover da cópia espelhada nessa máquina virtual usando o script de automação mencionado acima. Perceba que com a mudança do nome da instância do banco de dados, a camada de aplicativo deverá ser reconfigurada para usar o novo banco de dados.


#### Configurar scripts do SQL Server a fim de realizar o failover para um site secundário

1.	Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Adicione a máquina virtual de espelhamento de banco de dados do SQL Server ao plano de recuperação, de preferência no primeiro grupo de inicialização.
3.	Adicione um script posterior ao failover para realizar o failover da cópia espelhada nessa máquina virtual usando o script de VMM mencionado acima. Perceba que com a mudança do nome da instância do banco de dados, a camada de aplicativo deverá ser reconfigurada para usar o novo banco de dados.





## Considerações sobre failover de teste

Se você estiver usando grupos de disponibilidade AlwaysOn, não será possível fazer um failover de teste da camada do SQL Server. Considere estas opções como alternativas:

- Opção 1

	1. Execute um failover de teste das camadas de aplicativo e de front-end.
	2. Atualize a camada de aplicativos para acessar a cópia da réplica no modo somente leitura e executar um teste somente leitura do aplicativo.

- Opção 2
1.	Crie uma cópia da instância da réplica de máquina virtual do SQL Server (usando o clone do VMM para site a site ou Backup do Azure) e ative-a em uma rede de teste
2.	Execute o failover de teste usando o plano de recuperação.

## Considerações sobre failback

Para clusters padrão do SQL, a realização do failback após um failover não planejado exige um backup do SQL Server e restauração a partir da instância espelho para o cluster original e, em seguida, o restabelecimento do espelhamento.





 

<!---HONumber=August15_HO7-->