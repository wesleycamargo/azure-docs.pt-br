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
	ms.date="11/09/2015" 
	ms.author="raynew"/>


# Recuperação de desastres com o SQL Server e o Azure Site Recovery 

Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por meio da coordenação da replicação, do failover e da recuperação de máquinas virtuais e servidores físicos. A Recuperação de Site dá suporte a vários mecanismos de replicação a fim de proteger, replicar e realizar de forma consistente o failover de máquinas para o Azure ou para um armazenamento secundário. Obtenha uma visão geral de todos os cenários de implantação na [Visão geral do Azure Site Recovery](site-recovery-overview.md).

 Este artigo descreve como proteger o back-end do SQL Server de um aplicativo usando uma combinação de tecnologias BCDR do SQL Server e de Recuperação de Site. Você deve ter uma boa compreensão dos recursos de BCDR (cluster de failover, grupos de disponibilidade AlwaysOn, espelhamento de banco de dados, envio de log) e de Recuperação de Site antes de implantar os cenários descritos neste artigo.



## Visão geral

Muitas cargas de trabalho usam o SQL Server como base. Aplicativos como o SharePoint, Dynamics e SAP usam o SQL Server para implementar os serviços de dados. Recursos de alta disponibilidade e recuperação de desastres do SQL Server como, por exemplo, os grupos de disponibilidade do SQL Server, são usados para proteger e recuperar bancos de dados SQL Server. Os aplicativos implantam o SQL Server nas configurações a seguir


1. SQL Server Autônomo: o SQL Server e todos os bancos de dados são hospedados em um único computador (um computador físico ou máquina virtual). Quando virtualizado, o clustering do host não é usado para HA (alta disponibilidade) local e nenhuma HA de nível de convidado é implementada. 
2.	Instâncias de cluster de Failover (também conhecidas como FCI Sempre Ativo) do SQL Server: nessa configuração, 2 ou mais nós de instâncias do SQL Server com discos compartilhados são configurados em um cluster de Failover do Windows. Se qualquer uma das instâncias de cluster de Failover do SQL estiver inativa, o cluster pode realizar o failover do SQL para outra instância. Essa configuração é usada normalmente para HA no site primário. Isso não protege contra falhas ou interrupção na camada de armazenamento compartilhado. O disco compartilhado pode ser implementado usando iSCSI, Fiber Channel ou VHDX Compartilhado.
3.	Grupos de disponibilidade do SQL Always ON: nessa configuração, 2 nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.

O SQL Server também fornece tecnologias nativas para recuperação de desastre nas edições Enterprise para recuperar bancos de dados para um site remoto. Quando aplicável, vamos aproveitar as tecnologias nativas SQL de recuperação de desastre e integrar-nos com elas para a criação de um Plano de Recuperação de Desastres baseado no Azure Site Recovery.


1. Grupo de Disponibilidade do SQL Always On da Recuperação de Desastre para SQL edições Enterprise 2012 ou 2014 
2.	Espelhamento de banco de dados SQL no modo de segurança alta para SQL Server Standard (qualquer versão) ou SQL server 2008 R2



A Recuperação de Site pode proteger o SQL Server em execução em uma máquina virtual Hyper-V, uma máquina virtual VMware ou em um servidor físico.

 |**Local para o próprio local** | **Local para o Azure** 
---|---|---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim 
**Servidor físico** | Sim | Sim


## Suporte e integração

A seguir estão as versões e edições do SQL Server que são aplicáveis a este artigo:


- SQL Server 2014 Enterprise e Standard
- SQL Server 2012 Enterprise e Standard
- SQL Server 2008 R2 Enterprise e Standard


A Recuperação de Site pode ser integrada com tecnologias nativas de BCDR do SQL Server, resumidas na tabela abaixo a fim de fornecer uma solução de recuperação de desastre.

**Recurso** |**Detalhes** | **Versão do SQL Server** 
---|---|---
**Grupo de disponibilidade AlwaysOn** | <p>Várias instâncias autônomas do SQL Server são executadas, cada uma delas, em um cluster de failover com vários nós.</p> <p>Os bancos de dados podem ser agrupados em grupos de failover, que podem ser copiados (espelhados) em instâncias do SQL Server, de modo que não exista a necessidade de armazenamento compartilhado.</p> <p>Fornece a recuperação de desastres entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.</p> | Edição SQL Server 2014/2012 Enterprise
**Clustering de failover (FCI AlwaysOn)** | <p>O SQL Server aproveita o clustering de failover do Windows para proporcionar a alta disponibilidade de cargas de trabalho local do SQL Server.</p><p>Os nós que executam instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância estiver inoperante, o cluster realizará o failover em outra.</p> <p>O cluster não protege contra falhas ou interrupções no armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, fiber channel ou VHDXs compartilhados.</p> | Edições do SQL Server Enterprise</p> <p>Edição SQL Server Standard (limitada a dois nós)
**Espelhamento de banco de dados (modo de alta segurança)** | Protege um único banco de dados para uma única cópia secundária. Disponível nos modos de replicação de alta segurança (síncrona) e de alto desempenho (assíncrono). Não requer um cluster de failover. | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise todas as edições</p>
**SQL Server autônomo** | O SQL Server e o banco de dados estão hospedados em um único servidor (físico ou virtual). O clustering de host é usado para alta disponibilidade, se o servidor for virtual. Sem alta disponibilidade no nível do convidado. | Edição Enterprise ou Standard




A tabela a seguir resume nossas recomendações para a integração de tecnologias de BCDR do SQL Server na implantação da Recuperação de Site.

**Versão** |**Edição** | **Implantação** | **Local para local** | **Local para o Azure** 
---|---|---|---|---
SQL Server 2014 ou 2012 | Enterprise | Instância do cluster de failover | Grupos de disponibilidade AlwaysOn | Grupos de disponibilidade AlwaysOn
 | Enterprise | Grupos de disponibilidade AlwaysOn para alta disponibilidade | Grupos de disponibilidade AlwaysOn | Grupos de disponibilidade AlwaysOn
 | Standard | FCI (instância do cluster de failover) | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site 
SQL Server 2008 R2 | Enterprise ou Standard | FCI (instância do cluster de failover) | Replicação de Recuperação de Site com espelhamento local | Replicação de Recuperação de Site com espelhamento local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site
SQL Server (qualquer versão) | Enterprise ou Standard | Instância do cluster de failover - aplicativo DTC | Replicação de recuperação de site | Sem suporte

## Pré-requisitos de implantação

Veja o que você precisa antes de iniciar:


- Uma implantação local do SQL Server executando uma versão com suporte do SQL Server. Normalmente, você precisará também de um Active Directory para o servidor SQL.
- Os pré-requisitos para o cenário que você deseja implantar. Os pré-requisitos podem ser encontrados no artigo cada implantação. Eles estão listados e apresentam um link na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).
- Se você quiser configurar a recuperação no Azure, será necessário executar a ferramenta [Avaliação de preparação da máquina virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server a fim de verificar se são compatíveis com o Azure e com a Recuperação de Site.


## Configurar a proteção do AD

Será necessário adicionar o Active Directory no site de recuperação secundário para que o SQL Server seja executado corretamente. Há duas opções:

- **Pequena empresa** — se você tiver uma quantidade pequena de aplicativos e um único controlador de domínio para o site local, e quiser realizar o failover de todo o site, recomendamos o uso da replicação da Recuperação de Site para replicar o controlador de domínio para o armazenamento de dados secundário ou para o Azure.

- **Empresa de médio e grande porte** — se você tiver uma grande quantidade de aplicativos, estiver executando uma floresta do Active Directory e quiser realizar o failover por aplicativo ou carga de trabalho, recomendamos a configuração de um controlador de domínio adicional no armazenamento de dados secundário ou no Azure. Observe que, se você estiver usando grupos de disponibilidade AlwaysOn para recuperar em um site remoto, recomendamos a configuração de outro controlador de domínio adicional no site secundário ou no Azure, a fim de usá-lo para a instância do SQL Server recuperada.

As instruções neste documento supõem que exista um controlador de domínio disponível no local secundário. Você pode consultar as diretrizes de solução do AD DR [aqui](http://aka.ms/asr-ad).

##Proteção de configuração de grupos de disponibilidade do SQL AlwaysOn

### Local para o Azure

O Azure Site Recovery (ASR) oferece suporte nativo ao SQL AlwaysOn. Se você tiver criado um Grupo de Disponibilidade SQL com uma máquina virtual do Azure configurada como "Secundária", poderá usar o ASR para gerenciar o failover dos Grupos de Disponibilidade.

No momento, esse recurso está no modo de visualização e fica disponível quando o datacenter principal é gerenciado pelo System Center Virtual Machine Manager (VMM).

#### Ambientes gerenciados pelo Servidor VMM
Se você entrar em um cofre do ASR, verá uma guia para SQL Servers na guia Itens Protegidos.

![Itens Protegidos](./media/site-recovery-sql/protected-items.png)

A seguir, as etapas para integrar o SQL AlwaysOn ao ASR.

##### Pré-requisitos
- Um SQL Server local em um servidor autônomo ou em um cluster de failover 
- Uma ou mais máquinas virtuais do Azure com o SQL Server instalado
- Configuração do Grupo de Disponibilidade do SQL entre o SQL Server local e o SQL Server em execução no Azure
- O PowerShell remoto deve ser habilitado no SQL Server local. O Servidor VMM deve ser capaz de fazer chamadas remotas do PowerShell para o SQL Server
- No SQL Server local, será necessário adicionar aos grupos de usuários do SQL uma conta de usuário com pelo menos as permissões a seguir
	- ALTER AVAILABILITY GROUP - [referência 1](https://msdn.microsoft.com/pt-BR/library/hh231018.aspx), [referência 2](https://msdn.microsoft.com/pt-BR/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - [referência 1](https://msdn.microsoft.com/pt-BR/library/ff877956.aspx#Security)
- Uma conta Executar como deve ser criada no Servidor VMM para a conta da etapa anterior
- O módulo PS do SQL deve ser instalado em SQL Servers em execução no local e em máquinas virtuais do Azure
- O Agente de VM deve ser instalado nas máquinas virtuais em execução no Azure
- NTAUTHORITY\\System deve ter as seguintes permissões no SQL Server em execução em máquinas virtuais no Azure
	- ALTER AVAILABILITY GROUP - [referência 1](https://msdn.microsoft.com/pt-BR/library/hh231018.aspx), [referência 2](https://msdn.microsoft.com/pt-BR/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE - [referência 1](https://msdn.microsoft.com/pt-BR/library/ff877956.aspx#Security)

##### Adicionando um SQL Server

Clique em Adicionar SQL para adicionar um novo SQL Server.

![Adicionar SQL](./media/site-recovery-sql/add-sql.png)

Forneça os detalhes do SQL Server, do VMM e as credenciais que serão usadas para gerenciar o SQL Server.

![Adicionar Caixa de Diálogo do SQL](./media/site-recovery-sql/add-sql-dialog.png)

###### Parâmetros
1. Nome: um nome amigável que você deseja fornecer para fazer referência a esse SQL Server
2. SQL Server (FQDN): um nome de domínio totalmente qualificado (FQDN) do SQL Server de origem que você deseja adicionar. Caso o SQL Server esteja instalado em um Cluster de Failover, forneça o FQDN do cluster e não de qualquer um dos nós do cluster. 
3. Instância do SQL Server: escolha a instância SQL padrão ou forneça o nome da instância SQL personalizada.
4. Servidor VMM: selecione um dos servidores VMM que já foram registrados no Azure Site Recovery (ASR). O ASR usará esse servidor VMM para se comunicar com o SQL Server
5. CONTA EXECUTAR COMO: forneça o nome de uma Conta Executar como criada no Servidor VMM selecionado acima. Esta conta Executar como seria usada para acessar o SQL Server e deve ter permissões de Leitura e Failover nos Grupos de Disponibilidade no SQL Server. 

Depois de adicionar o SQL Server, ele aparecerá na guia SQL Servers.

![Lista do SQL Server](./media/site-recovery-sql/sql-server-list.png)

##### Adicionando um Grupo de Disponibilidade do SQL

Após a adição do SQL Server, a próxima etapa é adicionar os Grupos de Disponibilidade ao ASR. Para fazer isso, faça uma busca detalhada dentro do SQL Server adicionado na etapa anterior e clique em Adicionar Grupo de Disponibilidade do SQL.

![Adicionar AG do SQL](./media/site-recovery-sql/add-sqlag.png)

O Grupo de Disponibilidade do SQL pode ser replicado para uma ou mais máquinas virtuais no Azure. Ao adicionar o grupo de disponibilidade do sql, você precisa fornecer o nome e a assinatura da máquina virtual do Azure na qual deseja aplicar o failover do grupo de disponibilidade pelo ASR.

![Adicionar Caixa de Diálogo AG do SQL](./media/site-recovery-sql/add-sqlag-dialog.png)

No exemplo anterior, o Grupo de Disponibilidade DB1-AG seria o Primário na máquina virtual SQLAGVM2 em execução dentro da assinatura DevTesting2 em um failover.

>[AZURE.NOTE]Somente os Grupos de Disponibilidade Primários no SQL Server adicionado na etapa anterior estarão disponíveis para adição ao ASR. Se você tiver tornado um Grupo de Disponibilidade Primário no SQL Server, ou se tiver adicionado mais Grupos de Disponibilidade no SQL Server após sua adição, atualize-o usando a opção Atualizar disponível no SQL Server.

#### Criando um plano de recuperação

A próxima etapa é criar um plano de recuperação usando as máquinas virtuais e os grupos de disponibilidade. Selecione o mesmo servidor VMM usado na Etapa 1 como a origem e o Microsoft Azure como destino.

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp1.png)

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp2.png)

No exemplo, o aplicativo do Sharepoint é composto por três máquinas virtuais que usam um Grupo de Disponibilidade do SQL como seu back-end. Neste plano de recuperação, podemos selecionar o grupo de disponibilidade e também a máquina virtual que forma o aplicativo.

Você pode personalizar ainda mais o plano de recuperação movendo máquinas virtuais para grupos de failover diferentes a fim de sequenciar a ordem do failover. O grupo de disponibilidade sempre sofre failover primeiro, pois deverá ser usado como um back-end de qualquer aplicativo.

![Personalizar o Plano de Recuperação](./media/site-recovery-sql/customize-rp.png)

#### Failover

Há outras opções de failover disponíveis após a adição de um Grupo de Disponibilidade a um Plano de recuperação.

##### Failover planejado

Failover planejado implica um failover sem perda de dados. Para conseguir isso, o Modo de Disponibilidade do Grupo de Disponibilidade do SQL é definido primeiro como Síncrono e um failover é disparado para tornar o grupo de disponibilidade Primário na máquina virtual fornecida durante a adição do grupo de disponibilidade ao ASR. Quando o failover estiver concluído, o Modo de Disponibilidade será definido com o mesmo valor definido antes do disparo do failover planejado.

##### Failover não planejado

Um failover não planejado pode resultar em perda de dados. Durante o disparo de um failover não planejado, o Modo de Disponibilidade do Grupo de Disponibilidade não é alterado. Em seguida, torna-se Primário na máquina virtual fornecida durante a adição do grupo de disponibilidade ao ASR. Após a conclusão do failover não planejado, e o servidor local que executa o SQL Server estar disponível novamente, a Replicação Inversa deverá ser disparada no Grupo de Disponibilidade. Observe que essa ação não está disponível no plano de recuperação e pode ser executada no Grupo de Disponibilidade do SQL na guia SQL Servers

##### Failover de Teste
Não há suporte para o failover de teste no Grupo de Disponibilidade do SQL. Se você disparar o Failover de Teste de um Plano de Recuperação que contém o Grupo de Disponibilidade do SQL, o failover será ignorado para o Grupo de Disponibilidade.

Considere estas opções como alternativas:

######Opção 1



1. Execute um failover de teste das camadas de aplicativo e de front-end.

2. Atualize a camada de aplicativos para acessar a cópia da réplica no modo somente leitura e executar um teste somente leitura do aplicativo.

######Opção 2

1.	Crie uma cópia da instância da réplica de máquina virtual do SQL Server (usando o clone do VMM para site a site ou Backup do Azure) e ative-a em uma rede de teste
2.	Execute o failover de teste usando o plano de recuperação.

##### Failback

Se você quiser transformar o Grupo de Disponibilidade no SQL Server local novamente em Primário, faça isso disparando o Failover Planejado no Plano de Recuperação e escolhendo a direção do Microsoft Azure para o Servidor VMM local

#### Replicação inversa

Após um failover não planejado, será necessário disparar a replicação inversa no Grupo de Disponibilidade para retomar a replicação. Até que isso seja feito, a replicação permanecerá suspensa.


### Ambientes não gerenciados pelo Servidor VMM

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

###Local para o próprio local
Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade, ou uma instância do cluster de failover, recomendamos também o uso dos grupos de disponibilidade no site de recuperação. Observe que essa orientação serve para aplicativos que não usam transações distribuídas.


1. [Configure bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma nova rede virtual no site secundário.
3. Configure uma VPN site a site entre a nova rede virtual e o site primário.
4. Crie uma máquina virtual no site de recuperação e instale nela o SQL Server.
5. Estenda os grupos de disponibilidade AlwaysOn existentes para a nova máquina virtual do SQL Server. Configure essa instância do SQL Server como uma cópia de réplica assíncrona.
6. Crie um ouvinte do grupo de disponibilidade ou atualize o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
7. Certifique-se de que o farm de aplicativos esteja configurado usando o ouvinte. Se a instalação tiver usado o nome de servidor do banco de dados, atualize-o para usar o ouvinte de modo que você não precise reconfigurá-lo após o failover.

Para aplicativos que usam transações distribuídas, recomendamos o uso da [Recuperação de Site com replicação SAN](site-recovery-vmm-san.md) ou a [replicação entre sites do VMWare](site-recovery-vmware-to-vmware.md).

####Considerações sobre o Plano de Recuperação

1. Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o script para realizar o failover de grupos de disponibilidade.



## Configurar a proteção para um SQL Server autônomo


Nessa configuração, recomendamos o uso da replicação de Recuperação de Site para proteger a máquina do SQL Server. As etapas exatas dependerão se o SQL Server está configurado como uma máquina virtual ou um servidor físico, e se você deseja replicar para o Azure ou para um site secundário local. Obtenha mais instruções sobre todos os cenários de implantação na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).


## Configurar a proteção para o cluster do SQL Server (Standard ou 2008 R2)

Para um cluster executando o SQL Server Standard ou o SQL Server 2008 R2, recomendamos o uso da replicação da Recuperação de Site para proteger o SQL Server.

#### Local para o próprio local

- Se o aplicativo usar transações distribuídas, recomendamos a implantação da [Recuperação de Site com replicação SAN](site-recovery-vmm-san.md) para um ambiente Hyper-V e [VMware para VMware](site-recovery-vmware-to-vmware.md) para um ambiente VMware.

- Para aplicativos não DTC, tire vantagem da abordagem anterior para recuperar o cluster como um servidor autônomo, aproveitando um espelho de banco de dados local de segurança alta.

#### Local para o Azure

A Recuperação de Site não dá suporte a clusters convidados ao replicar para o Azure. O SQL Server também não fornece uma solução de recuperação de desastres de baixo custo para a edição Standard. Recomendamos a proteção do cluster local do SQL Server para um SQL Server autônomo e sua recuperação no Azure.


1. Configure uma instância do SQL Server autônomo adicional no site local.
2. Configure essa instância para servir como um espelho para os bancos de dados que precisam de proteção. Configure o espelhamento no modo de alta segurança.
3.	Configure a Recuperação de Site no site local com base no ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).
4.	Use a replicação da Recuperação de Site para replicar a nova instância do SQL Server para o Azure. É uma cópia espelhada de segurança alta, portanto, será sincronizada com o cluster primário, mas será replicada para o Azure usando a replicação da Recuperação de Site.

O gráfico a seguir ilustra essa configuração.

![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Considerações sobre failback

Para clusters padrão do SQL, a realização do failback após um failover não planejado exige um backup do SQL e restauração da instância do Espelho para o cluster original e o restabelecimento do espelho.










 

<!---HONumber=Nov15_HO3-->