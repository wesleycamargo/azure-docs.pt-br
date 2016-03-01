<properties 
	pageTitle="Proteger o SQL Server com a recuperação de desastre do SQL Server e o Azure Site Recovery | Microsoft Azure" 
	description="Este artigo descreve como replicar o SQL Server usando o Azure Site Recovery dos recursos de desastre do SQL Server." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/14/2016" 
	ms.author="raynew"/>


# Proteger o SQL Server com a recuperação de desastre do SQL Server e o Azure Site Recovery 


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

 Este artigo descreve como proteger o back-end do SQL Server de um aplicativo usando uma combinação de tecnologias BCDR do SQL Server e o Azure Site Recovery. Você deve ter uma boa compreensão dos recursos de recuperação de desastres do SQL Server (banco de dados de cluster de failover, grupos de disponibilidade do AlwaysOn, espelhamento, envio de log) e de Azure Site Recovery, antes de implantar os cenários descritos neste artigo.



## Visão geral

Muitas cargas de trabalho usam o SQL Server como base. Aplicativos como o SharePoint, Dynamics e SAP usam o SQL Server para implementar os serviços de dados. Aplicativos de implantar o SQL Server de maneiras diferentes:

- **SQL Server autônomo**: o SQL Server e todos os bancos de dados são hospedados em um único computador (físico ou virtual). Quando virtualizado, o cluster de host é usado para alta disponibilidade local. Nenhuma alta disponibilidade em nível de convidado é implementada.
- **Instâncias de cluster de Failover (sempre em FCI) do SQL Server**: dois ou mais nós de instâncias do SQL Server com discos compartilhados são configurados em um cluster de Failover do Windows. Se qualquer uma das instâncias de cluster estiver inativa, o cluster pode ter failover do SQL Server em outra instância. Essa configuração é usada normalmente para HA em um site primário. Isso não protege contra falhas ou interrupção na camada de armazenamento compartilhado. O disco compartilhado pode ser implementado usando ISCSI, Fiber Channel ou VHDx Compartilhado.
- **Grupos de Disponibilidade AlwaysOn do SQL**: nessa configuração, 2 nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.

O SQL Server também fornece tecnologias nativas para recuperação de desastre nas edições Enterprise para recuperar bancos de dados para um site remoto. Neste artigo, vamos aproveitar e integrar essas tecnologias de recuperação de desastres SQL nativas:

- Grupos de Disponibilidade AlwaysOn do SQL para recuperação de desastres para SQL Server 2012 ou 2014 Enterprise Editions.
- Espelhamento de banco de dados SQL no modo de alta segurança para SQL Server Standard Edition (qualquer versão), ou para o SQL Server 2008 R2.


O Site Recovery pode proteger o SQL Server, como resumido na tabela.

 |**Local para o próprio local** | **Local para o Azure** 
---|---|---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim 
**Servidor físico** | Sim | Sim


## Suporte e integração

Essas versões do SQL Server tem suporte nos cenários neste artigo:


- SQL Server 2014 Enterprise e Standard
- SQL Server 2012 Enterprise e Standard
- SQL Server 2008 R2 Enterprise e Standard


O Site Recovery pode ser integrado a tecnologias nativas de BCDR do SQL Server, resumidas na tabela abaixo a fim de fornecer uma solução de recuperação de desastre.

**Recurso** |**Detalhes** | **Versão do SQL Server** 
---|---|---
**Grupo de disponibilidade AlwaysOn** | Várias instâncias autônomas do SQL Server cada uma executando em um cluster de failover com vários nós.<br/><br/>Os bancos de dados podem ser agrupados em grupos de failover que podem ser copiados (espelhados) em instâncias do SQL Server para que nenhum armazenamento compartilhado seja necessário.<br/><br/>Fornece recuperação de desastres entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático. | SQL Server 2014 & 2012 Enterprise Edition
**Clustering de failover (FCI AlwaysOn)** | O SQL Server aproveita o Clustering de Failover do Windows para proporcionar a alta disponibilidade de cargas de trabalho local do SQL Server.<br/><br/>Os nós que executam instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância estiver inoperante, o cluster realiza o failover para outro.<br/><br/>O cluster não protege contra falhas ou interrupções no armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, fiber channel ou VHDXs compartilhados. | Edições do SQL Server Enterprise<br/><br/>Edição SQL Server Standard (limitada somente a dois nós)
**Espelhamento de banco de dados (modo de alta segurança)** | Protege um único banco de dados para uma única cópia secundária. Disponível nos modos de replicação de alta segurança (síncrona) e de alto desempenho (assíncrono). Não requer um cluster de failover. | SQL Server 2008 R2<br/><br/>Todas as edições do SQL Server Enterprise
**SQL Server autônomo** | O SQL Server e o banco de dados estão hospedados em um único servidor (físico ou virtual). O clustering de host é usado para alta disponibilidade, se o servidor for virtual. Sem alta disponibilidade no nível do convidado. | Edição Enterprise ou Standard

## Recomendações de implantação


Esta tabela resume nossas recomendações para a integração de tecnologias de BCDR do SQL Server com a Recuperação de Site.

**Versão** |**Edição** | **Implantação** | **Local para local** | **Local para o Azure** 
---|---|---|---|---
SQL Server 2014 ou 2012 | Enterprise | Instância do cluster de failover | Grupos de disponibilidade AlwaysOn | Grupos de disponibilidade AlwaysOn
| Enterprise | Grupos de disponibilidade AlwaysOn para alta disponibilidade | Grupos de disponibilidade AlwaysOn | Grupos de disponibilidade AlwaysOn
 | Padrão | FCI (instância do cluster de failover) | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site
SQL Server 2008 R2 | Enterprise ou Standard | FCI (instância do cluster de failover) | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site
SQL Server (qualquer versão) | Enterprise ou Standard | Instância do cluster de failover – aplicativo DTC | Replicação de recuperação de site | Sem suporte


## Pré-requisitos de implantação

Veja o que você precisa antes de iniciar:

- Uma implantação local do SQL Server executando uma versão com suporte do SQL Server. Normalmente, você precisará também de um Active Directory para o servidor SQL.
- Os pré-requisitos para o cenário que você deseja implantar. Os pré-requisitos podem ser encontrados no artigo cada implantação. São fornecidos links para eles na [Visão geral do Site Recovery](site-recovery-overview.md).
- Se você quiser configurar a recuperação no Azure, será necessário executar a ferramenta [Avaliação de preparação da máquina virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server a fim de verificar se são compatíveis com o Azure e com a Recuperação de Site.


## Configurar o Active Directory

Será necessário adicionar o Active Directory no site de recuperação secundário para que o SQL Server seja executado corretamente. Há duas opções:

- **Pequena empresa** — se você tiver uma quantidade pequena de aplicativos e um único controlador de domínio para o site local, e quiser realizar o failover de todo o site, recomendamos o uso da replicação da Recuperação de Site para replicar o controlador de domínio para o armazenamento de dados secundário ou para o Azure.

- **Empresa de médio e grande porte** — se você tiver uma grande quantidade de aplicativos, estiver executando uma floresta do Active Directory e quiser realizar o failover por aplicativo ou carga de trabalho, recomendamos a configuração de um controlador de domínio adicional no armazenamento de dados secundário ou no Azure. Observe que, se você estiver usando grupos de disponibilidade AlwaysOn para recuperar em um site remoto, recomendamos a configuração de outro controlador de domínio adicional no site secundário ou no Azure, a fim de usá-lo para a instância do SQL Server recuperada.

As instruções neste documento supõem que exista um controlador de domínio disponível no local secundário. [Ler mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com o Site Recovery.

## Integrar proteção com o SQL Server Always-On (local no Azure)

### Protegendo máquinas virtuais Hyper-V em nuvens do VMM

O Site Recovery dá suporte nativamente ao AlwaysOn do SQL. Se você tiver criado um grupo de disponibilidade do SQL com uma máquina virtual do Azure configurada como 'Secundária', você pode usar o Site Recovery para gerenciar o failover dos Grupos de disponibilidade.

>[AZURE.NOTE] Essa funcionalidade está atualmente em visualização e disponível quando os servidores de host do Hyper-V no datacenter principal são gerenciados em nuvens do VMM.

#### Pré-requisitos

Aqui está o que você precisa para integrar o SQL AlwaysOn com a Recuperação de Site quando você estiver replicando do VMM:

- Um SQL Server local (servidor autônomo ou em um cluster de failover).
- Uma ou mais máquinas virtuais do Azure com o SQL Server instalado
- Uma configuração do Grupo de Disponibilidade do SQL entre o SQL Server local e o SQL Server em execução no Azure
- A comunicação remota do PowerShell deve ser habilitada no computador do SQL Server local. O Servidor VMM deve ser capaz de fazer chamadas remotas do PowerShell para o SQL Server.
- Uma conta de usuário deve ser adicionada no SQL Server local, esses grupos de usuário do SQL com pelo menos essas permissões:
	- ALTER AVAILABILITY GROUP: permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx) e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - permissões[aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Uma conta RunAs deve ser criada no Servidor VMM para a conta da etapa anterior
- O módulo PS do SQL deve ser instalado em SQL Servers em execução local e em máquinas virtuais do Azure
- O Agente de VM deve ser instalado nas máquinas virtuais em execução no Azure
- NTAUTHORITY\\System deve ter as seguintes permissões no SQL Server em execução em máquinas virtuais no Azure:
	- ALTER AVAILABILITY GROUP - permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx) e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - permissões [aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  Etapa 1: Adicionar um SQL Server


1. Clique em **Adicionar SQL** para adicionar um novo SQL Server. 

	![Adicionar SQL](./media/site-recovery-sql/add-sql.png)

2. Em **Definir configurações de SQL** > **Nome**, forneça um nome amigável para referir-se ao SQL Server.
3. **No SQL Server (FQDN)**, especifique o FQDN do SQL Server de origem que você deseja adicionar. Caso o SQL Server esteja instalado em um Cluster de Failover, forneça o FQDN do cluster e não de qualquer um dos nós do cluster.  
4. Em **Instância do SQL Server**, escolha a instância padrão ou forneça o nome da instância personalizada.
5. Em **Servidor VMM**, selecione um servidor VMM registrado no cofre de Recuperação de Site. O Site Recovery usa esse servidor do VMM para se comunicar com o SQL Server.
6. Em **Executar como Conta**, forneça o nome de uma conta RunAs que foi criada no servidor VMm especificado. Essa conta é usada para acessar o SQL Server e deve ter permissões de leitura e de Failover em grupos de disponibilidade no computador do SQL Server.

	![Adicionar Caixa de Diálogo do SQL](./media/site-recovery-sql/add-sql-dialog.png)

Depois de adicionar o SQL Server, ele aparecerá na guia **SQL Servers**.

![Lista do SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### Etapa 2: Adicionar um grupo de disponibilidade do SQL

1. Depois que o computador SQL Server for adicionado, a próxima etapa será adicionar os grupos de disponibilidade para a Recuperação de Site. Para fazer isso, faça uma busca detalhada dentro do SQL Server adicionado na etapa anterior e clique em Adicionar Grupo de Disponibilidade do SQL. 

	![Adicionar AG do SQL](./media/site-recovery-sql/add-sqlag.png)

2. O Grupo de Disponibilidade do SQL pode ser replicado para uma ou mais máquinas virtuais no Azure. Ao adicionar o grupo de disponibilidade do sql, você precisa fornecer o nome e a assinatura da máquina virtual do Azure na qual deseja aplicar o failover do grupo de disponibilidade pela Recuperação de Site.

	![Adicionar Caixa de Diálogo AG do SQL](./media/site-recovery-sql/add-sqlag-dialog.png)

3. No exemplo anterior, o Grupo de Disponibilidade DB1-AG seria o Primário na máquina virtual SQLAGVM2 em execução dentro da assinatura DevTesting2 em um failover.

>[AZURE.NOTE] Somente os Grupos de Disponibilidade Primários no SQL Server adicionado na etapa anterior estarão disponíveis para adição ao Site Recovery. Se você tiver tornado um Grupo de Disponibilidade Primário no SQL Server, ou se tiver adicionado mais Grupos de Disponibilidade no SQL Server após sua adição, atualize-o usando a opção Atualizar disponível no SQL Server.

#### Etapa 3: criar um plano de recuperação

A próxima etapa é criar um plano de recuperação usando as máquinas virtuais e os grupos de disponibilidade. Selecione o mesmo servidor VMM usado na Etapa 1 como a origem e o Microsoft Azure como destino.

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp1.png)

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp2.png)

No exemplo, o aplicativo do Sharepoint é composto por três máquinas virtuais que usam um Grupo de Disponibilidade do SQL como seu back-end. Neste plano de recuperação, podemos selecionar o grupo de disponibilidade e também a máquina virtual que forma o aplicativo.

Você pode personalizar ainda mais o plano de recuperação movendo máquinas virtuais para grupos de failover diferentes a fim de sequenciar a ordem do failover. O grupo de disponibilidade sempre sofre failover primeiro, pois deverá ser usado como um back-end de qualquer aplicativo.

![Personalizar o Plano de Recuperação](./media/site-recovery-sql/customize-rp.png)

### Etapa 4: Failover

Há outras opções de failover disponíveis após a adição de um Grupo de Disponibilidade a um Plano de recuperação.

Failover | Detalhes
--- | ---
**Failover planejado** | Failover planejado implica um failover sem perda de dados. Para conseguir isso, o Modo de Disponibilidade do Grupo de Disponibilidade do SQL é definido primeiro como Síncrono e um failover é disparado para disponibilizar o grupo Primário na máquina virtual fornecida durante a adição do grupo de disponibilidade ao Site Recovery. Quando o failover estiver concluído, o Modo de Disponibilidade será definido com o mesmo valor definido antes do disparo do failover planejado.
**Failover não planejado** | Um failover não planejado pode resultar em perda de dados. Durante o disparo de um failover não planejado, o Modo de Disponibilidade do Grupo de Disponibilidade não é alterado. Em seguida, torna-se Primário na máquina virtual fornecida durante a adição do grupo de disponibilidade ao Site Recovery. Após a conclusão do failover não planejado, e o servidor local que executa o SQL Server estar disponível novamente, a Replicação Inversa deverá ser disparada no Grupo de Disponibilidade. Observe que essa ação não está disponível no plano de recuperação e pode ser executada no Grupo de Disponibilidade do SQL na guia SQL Servers
**Failover de teste** | Não há suporte para o failover de teste no Grupo de Disponibilidade do SQL. Se você disparar o Failover de Teste de um Plano de Recuperação que contém o Grupo de Disponibilidade do SQL, o failover será ignorado para o Grupo de Disponibilidade.


Considere estas opções de failover.

Opção | Detalhes
--- | ---
**Opção 1** | 1\. Execute um failover de teste das camadas de aplicativo e de front-end.<br/><br/>2. Atualize a camada de aplicativos para acessar a cópia da réplica no modo somente leitura e executar um teste somente leitura do aplicativo.
**Opção 2** | 1\. Crie uma cópia da instância da réplica da máquina virtual do SQL Server (usando o clone do VMM para site a site ou Backup do Azure) e ative-a em uma rede de teste<br/><br/> 2. Execute o failover de teste usando o plano de recuperação.

Etapa 5: Failback

Se você quiser transformar o Grupo de Disponibilidade no SQL Server local novamente em Primário, faça isso disparando o Failover Planejado no Plano de Recuperação e escolhendo a direção do Microsoft Azure para o Servidor VMM local.

>[AZURE.NOTE] Após um failover não planejado, será necessário disparar a replicação inversa no Grupo de Disponibilidade para retomar a replicação. Até que isso seja feito, a replicação permanecerá suspensa.



### Proteger as máquinas sem o VMM

Para os ambientes que não são gerenciados por um Servidor VMM, é possível usar Runbooks de Automação do Azure para configurar um failover com script dos Grupos de Disponibilidade do SQL. A seguir, as etapas dessa configuração:

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

## Integrar proteção AlwaysOn do SQL (local para local)

Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade, ou uma instância do cluster de failover, recomendamos também o uso dos grupos de disponibilidade no site de recuperação. Observe que essa orientação serve para aplicativos que não usam transações distribuídas.

1. [Configure bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma nova rede virtual no site secundário.
3. Configure uma VPN site a site entre a nova rede virtual e o site primário.
4. Crie uma máquina virtual no site de recuperação e instale nela o SQL Server.
5. Estenda os grupos de disponibilidade AlwaysOn existentes para a nova máquina virtual do SQL Server. Configure essa instância do SQL Server como uma cópia de réplica assíncrona.
6. Crie um ouvinte do grupo de disponibilidade ou atualize o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
7. Certifique-se de que o farm de aplicativos esteja configurado usando o ouvinte. Se a instalação tiver usado o nome de servidor do banco de dados, atualize-o para usar o ouvinte de modo que você não precise reconfigurá-lo após o failover.

Para aplicativos que usam transações distribuídas, recomendamos o uso da [Recuperação de Site com replicação SAN](site-recovery-vmm-san.md) ou a [Replicação entre sites do VMWare/servidor físico](site-recovery-vmware-to-vmware.md).

### Considerações sobre o Plano de Recuperação

1. Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o script para realizar o failover de grupos de disponibilidade.



## Proteger um SQL Server autônomo

Nessa configuração, recomendamos o uso da replicação de Recuperação de Site para proteger a máquina do SQL Server. As etapas exatas dependerão se o SQL Server está configurado como uma máquina virtual ou um servidor físico, e se você deseja replicar para o Azure ou para um site secundário local. Obtenha mais instruções sobre todos os cenários de implantação na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).


## Proteger um cluster do SQL Server (Standard ou 2008 R2)

Para um cluster executando o SQL Server Standard ou o SQL Server 2008 R2, recomendamos o uso da replicação da Recuperação de Site para proteger o SQL Server.

### Local para o próprio local

- Caso o aplicativo use transações distribuídas, recomendamos implantar a [Recuperação de Site com replicação SAN](site-recovery-vmm-san.md) para um ambiente Hyper-V e [VMware/servidor físico para VMware](site-recovery-vmware-to-vmware.md) para um ambiente VMware.

- Para aplicativos não DTC, tire vantagem da abordagem anterior para recuperar o cluster como um servidor autônomo, aproveitando um espelho de banco de dados local de segurança alta.

### Local para o Azure

A Recuperação de Site não dá suporte a clusters convidados ao replicar para o Azure. O SQL Server também não fornece uma solução de recuperação de desastres de baixo custo para a edição Standard. Recomendamos a proteção do cluster local do SQL Server para um SQL Server autônomo e sua recuperação no Azure.


1. Configure uma instância do SQL Server Autônomo adicional no site local.
2. Configure essa instância para servir como um espelho para os bancos de dados que precisam de proteção. Configure o espelhamento no modo de alta segurança.
3.	Configure a Recuperação de Site no site local com base no ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware/servidor físico](site-recovery-vmware-to-azure-classic.md).
4.	Use a replicação da Recuperação de Site para replicar a nova instância do SQL Server para o Azure. É uma cópia espelhada de segurança alta, portanto, será sincronizada com o cluster primário, mas será replicada para o Microsoft Azure usando a replicação da Recuperação de Site.

O gráfico a seguir ilustra essa configuração.

![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Considerações sobre failback

Para clusters padrão do SQL, a realização do failback após um failover não planejado exige um backup do SQL, a restauração da instância do Espelho para o cluster original e o restabelecimento do espelho.

## Próximas etapas
[Saiba mais](site-recovery-best-practices.md) sobre a preparação para implantar a Recuperação de Site.










 

<!---HONumber=AcomDC_0218_2016-->