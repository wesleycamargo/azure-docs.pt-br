---
title: Replicar aplicativos com o SQL Server e o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como replicar o SQL Server usando o Azure Site Recovery dos recursos de desastre do SQL Server.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery

Este artigo descreve como proteger o back-end do SQL Server de um aplicativo usando uma combinação de tecnologias BCDR (continuidade dos negócios e recuperação de desastre) do SQL Server e o [Azure Site Recovery](site-recovery-overview.md).

Antes de começar, verifique se você entendeu os recursos de recuperação de desastres do SQL Server, incluindo o clustering de failover, grupos de disponibilidade do AlwaysOn, espelhamento de banco de dados e envio de log.


## <a name="sql-server-deployments"></a>Implantações do SQL Server

Muitas cargas de trabalho usam o SQL Server como base, e ele pode ser integrado com aplicativos como o SharePoint, Dynamics e SAP, para implementar os serviços de dados.  O SQL Server pode ser implantado de várias maneiras:

* **SQL Server autônomo**: o SQL Server e todos os bancos de dados são hospedados em um único computador (físico ou virtual). Quando virtualizado, o cluster de host é usado para alta disponibilidade local. A alta disponibilidade no nível de convidado não é implementada.
* **Instâncias de cluster de Failover (FCI do AlwaysOn) do SQL Server**: dois ou mais nós executando o SQL Server com instâncias de discos compartilhados são configurados em um cluster de Failover do Windows. Se um nó estiver inativo, o cluster poderá realizar o failover do SQL Server em outra instância. Essa configuração é usada normalmente para implementar a alta disponibilidade em um site primário. Essa implantação não protege contra falhas ou interrupção na camada de armazenamento compartilhada. Um disco compartilhado pode ser implementado usando ISCSI, Fiber Channel ou vhdx compartilhado.
* **Grupos de Disponibilidade AlwaysOn do SQL**: dois ou mais nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.

 Este artigo utiliza as seguintes tecnologias de recuperação de desastre nativas do SQL para recuperar bancos de dados em um local remoto:

* Grupos de Disponibilidade AlwaysOn do SQL, a fim de fornecer recuperação de desastres para SQL Server 2012 ou 2014 edições Enterprise.
* Espelhamento de banco de dados SQL no modo de alta segurança para SQL Server Standard Edition (qualquer versão), ou para o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

### <a name="supported-scenarios"></a>Cenários com suporte
O Site Recovery pode proteger o SQL Server, como resumido na tabela.

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

### <a name="supported-sql-server-versions"></a>Versões do SQL Server com suporte
Essas versões do SQL Server tem suporte nos cenários com suporte:

* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise e Standard

### <a name="supported-sql-server-integration"></a>Integração do SQL Server com suporte

O Site Recovery pode ser integrado com tecnologias nativas de BCDR do SQL Server, resumidas na tabela a fim de fornecer uma solução de recuperação de desastres.

**Recurso** | **Detalhes** | **SQL Server** |
--- | --- | ---
**Grupo de disponibilidade AlwaysOn** | Várias instâncias autônomas do SQL Server cada uma executando em um cluster de failover com vários nós.<br/><br/>Os bancos de dados podem ser agrupados em grupos de failover, que podem ser copiados (espelhados) em instâncias do SQL Server, de modo que não exista a necessidade de armazenamento compartilhado.<br/><br/>Fornece a recuperação de desastres entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático. | SQL Server 2014 & 2012 Enterprise Edition
**Clustering de failover (FCI AlwaysOn)** | O SQL Server aproveita o Clustering de Failover do Windows para proporcionar a alta disponibilidade de cargas de trabalho local do SQL Server.<br/><br/>Os nós que executam instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância estiver inoperante, o cluster realiza o failover para outro.<br/><br/>O cluster não protege contra falhas ou interrupções no armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, fiber channel ou VHDXs compartilhados. | Edições do SQL Server Enterprise<br/><br/>Edição SQL Server Standard (limitada somente a dois nós)
**Espelhamento de banco de dados (modo de alta segurança)** | Protege um único banco de dados para uma única cópia secundária. Disponível nos modos de replicação de alta segurança (síncrona) e de alto desempenho (assíncrono). Não requer um cluster de failover. | SQL Server 2008 R2<br/><br/>Todas as edições do SQL Server Enterprise
**SQL Server autônomo** | O SQL Server e o banco de dados estão hospedados em um único servidor (físico ou virtual). O clustering de host é usado para alta disponibilidade, se o servidor for virtual. Sem alta disponibilidade no nível do convidado. | Edição Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recomendações de implantação

Esta tabela resume nossas recomendações para a integração de tecnologias de BCDR do SQL Server com a Recuperação de Site.

| **Versão** | **Edição** | **Implantação** | **Local para local** | **Local para o Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Enterprise |Instância do cluster de failover |Grupos de disponibilidade AlwaysOn |Grupos de disponibilidade AlwaysOn |
|| Enterprise |Grupos de disponibilidade AlwaysOn para alta disponibilidade |Grupos de disponibilidade AlwaysOn |Grupos de disponibilidade AlwaysOn | |
|| Standard |FCI (instância do cluster de failover) |Replicação de Recuperação de Site com espelhamento local |Replicação de Recuperação de Site com espelhamento local | |
|| Enterprise ou Standard |Autônomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server 2008 R2 |Enterprise ou Standard |FCI (instância do cluster de failover) |Replicação de Recuperação de Site com espelhamento local |Replicação de Recuperação de Site com espelhamento local |
|| Enterprise ou Standard |Autônomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server (qualquer versão) |Enterprise ou Standard |Instância do cluster de failover – aplicativo DTC |Replicação de recuperação de site |Sem suporte |

## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação

* Uma implantação local do SQL Server executando uma versão com suporte do SQL Server. Normalmente, também é necessário ter um Active Directory para o SQL Server.
* Os requisitos para o cenário que você deseja implantar. Saiba mais sobre os requisitos de suporte para [replicação no Azure](site-recovery-support-matrix-to-azure.md) e [no local](site-recovery-support-matrix.md) e [os pré-requisitos de implantação](site-recovery-prereq.md).
* Para configurar a recuperação no Azure, será necessário executar a ferramenta [Avaliação de preparação da máquina virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server a fim de verificar se são compatíveis com o Azure e com o Site Recovery.

## <a name="set-up-active-directory"></a>Configurar o Active Directory

Configure o Active Directory no site de recuperação secundário para que o SQL Server seja executado corretamente.

* **Pequena empresa**— Com uma quantidade pequena de aplicativos e um único controlador de domínio para o site local, se você quiser realizar o failover de todo o site, recomendamos o uso da replicação do Site Recovery para replicar o controlador de domínio para o armazenamento de dados secundário ou para o Azure.
* **Empresa de médio e grande porte**— Se você tiver uma grande quantidade de aplicativos, uma floresta do Active Directory e quiser realizar o failover por aplicativo ou carga de trabalho, recomendamos a configuração de um controlador de domínio adicional no armazenamento de dados secundário ou no Azure. Se você estiver usando os grupos de disponibilidade AlwaysOn para recuperar em um site remoto, recomendamos a configuração de outro controlador de domínio adicional no site secundário ou no Azure, a fim de usá-lo para a instância do SQL Server recuperada.

As instruções neste artigo supõem que exista um controlador de domínio disponível no local secundário. [Ler mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com o Site Recovery.

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Integrar com o SQL Server AlwaysOn para replicação no Azure (Portal Clássico com um servidor VMM/de configuração)


O Site Recovery dá suporte nativamente ao AlwaysOn do SQL. Se você tiver criado um grupo de disponibilidade do SQL com uma máquina virtual do Azure configurada como um local secundário, você pode usar o Site Recovery para gerenciar o failover dos Grupos de disponibilidade.

> [!NOTE]
> Este recurso está na versão preview no momento. Ele fica disponível quando o site primário tem servidores host do Hyper-V gerenciados em nuvens do System Center VMM, ou quando você configura a [replicação do VMware](site-recovery-vmware-to-azure.md). A funcionalidade não está disponível no momento no novo Portal do Azure. Siga as etapas [desta seção](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server) se você estiver usando o novo Portal do Azure.
>
>


#### <a name="before-you-start"></a>Antes de começar

Para integrar o SQL AlwaysOn com o Site Recovery, você precisa:

* Um SQL Server local (servidor autônomo ou em um cluster de failover).
* Uma ou mais máquinas virtuais do Azure com o SQL Server instalado.
* Uma configuração do Grupo de Disponibilidade do SQL Server entre o SQL Server local e o SQL Server em execução no Azure.
* O PowerShell remoto habilitado no SQL Server local. O servidor do VMM ou o servidor de configuração deve ser capaz de fazer chamadas remotas do PowerShell para a máquina com SQL Server.
* Uma conta de usuário deve ser adicionada à máquina local do SQL Server. Adicione um grupo do SQL Server com pelo menos estas permissões:
  * ALTER AVAILABILITY GROUP: permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx) e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTERAR BANCO DE DADOS - permissões [aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* Se você estiver executando o VMM, a conta Executar como deve ser criada no servidor VMM
- Se você estiver executando o VMware, uma conta deverá ser criada no servidor de configuração usando CSPSConfigtool.exe
* O módulo PS do SQL deve ser instalado em SQL Servers em execução local e em VMs do Azure.
* O agente de VM deve ser instalado em VMs do Azure.
* NTAUTHORITY\System deve ter as seguintes permissões no SQL Server em execução em VMs do Azure.
  * ALTER AVAILABILITY GROUP: permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx) e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTERAR BANCO DE DADOS - permissões [aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

### <a name="add-a-sql-server"></a>Adicionar um SQL Server
1. Clique em **Adicionar SQL** para adicionar um novo SQL Server.

    ![Adicionar SQL](./media/site-recovery-sql/add-sql.png)
2. Em **Definir configurações de SQL** > **Nome**, forneça um nome amigável para o SQL Server.
3. **No SQL Server (FQDN)** , especifique o FQDN do SQL Server de origem que você deseja adicionar. Se o SQL Server estiver instalado em um cluster de failover, forneça o FQDN do cluster e não dos nós do cluster.  
4. Em **Instância do SQL Server**, escolha a instância padrão ou forneça o nome personalizado.
5. Em **Servidor de Gerenciamento**, selecione um servidor VMM ou um servidor de configuração registrado no cofre. O Site Recovery usa esse servidor para se comunicar com o SQL Server.
6. Em **Conta Executar como**, forneça o nome da conta Executar como do VMM ou a conta do servidor de configuração. Essa conta é usada para acessar o SQL Server e deve ter permissões de leitura e de Failover em grupos de disponibilidade na máquina do SQL Server.

    ![Adicionar Caixa de Diálogo do SQL](./media/site-recovery-sql/add-sql-dialog.png)

Depois de adicionar o SQL Server, ele aparecerá na guia **SQL Servers** .

![Lista do SQL Server](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>Adicionar um grupo de disponibilidade do SQL

1. No SQL Server adicionado, clique em **Adicionar Grupo de Disponibilidade do SQL**.

    ![Adicionar AG do SQL](./media/site-recovery-sql/add-sqlag.png)
2. O Grupo de Disponibilidade pode ser replicado em uma ou mais VMs do Azure. Ao adicionar o grupo, você precisa fornecer o nome e a assinatura da VM do Azure à qual você deseja realizar o failover do grupo usando o Site Recovery.

    ![Adicionar Caixa de Diálogo AG do SQL](./media/site-recovery-sql/add-sqlag-dialog.png)
3. Nesse exemplo, o Grupo de Disponibilidade DB1-AG seria o primário na máquina virtual SQLAGVM2 em execução dentro da assinatura DevTesting2 em um failover.

> [!NOTE]
> Somente os Grupos de Disponibilidade primários no SQL Server adicionado poderão ser adicionados ao Site Recovery. Se você tiver tornado um Grupo de Disponibilidade primário no SQL Server, ou se tiver adicionado mais Grupos de Disponibilidade no SQL Server após sua adição, atualize-o no SQL Server.
>
>

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Crie um plano de recuperação usando as máquinas virtuais e os grupos de disponibilidade. Selecione o servidor VMM ou o servidor de configuração como a origem, e o Azure como o destino.

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp1.png)

![Criar Plano de Recuperação](./media/site-recovery-sql/create-rp2.png)

No exemplo, o aplicativo do Sharepoint é composto por três máquinas virtuais que usam um Grupo de Disponibilidade do SQL como seu back-end. No plano de recuperação, podemos selecionar o grupo de disponibilidade e o aplicativo de VMs. Você pode personalizar ainda mais o plano de recuperação movendo VMs para grupos de failover diferentes a fim de sequenciar a ordem do failover. O Grupo de Disponibilidade sempre passa pelo failover primeiro, pois ele é usado como o back-end do aplicativo.

![Personalizar o Plano de Recuperação](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>Failover

Após a adição do Grupo de Disponibilidade a um plano de recuperação, outras opções de failover ficam disponíveis.

**Failover** | **Detalhes**
--- | ---
**Failover planejado** | O failover planejado implica um failover sem perda de dados. Para conseguir isso, o modo do Grupo de Disponibilidade do SQL é definido como síncrono e, depois, um failover é disparado para tornar o grupo de disponibilidade primário na máquina virtual fornecida, adicionando o grupo ao Site Recovery. Após a conclusão do failover, o Modo de Disponibilidade é definido com o mesmo valor definido antes do disparo do failover planejado.
**Failover não planejado** | Um failover não planejado pode resultar em perda de dados. Ao disparar um failover não planejado, o modo de disponibilidade do grupo não é alterado. Ele é transformado em primário na máquina virtual fornecida, adicionando o grupo de disponibilidade ao Site Recovery. Após a conclusão do failover não planejado, e o servidor local que executa o SQL Server estar disponível novamente, a Replicação Inversa deverá ser disparada no Grupo de Disponibilidade. Essa ação está disponível em **SQL Servers** > **Grupo de Disponibilidade do SQL** e não no plano de recuperação.
**Failover de teste** |Não há suporte para o failover de teste no Grupo de Disponibilidade do SQL. Se você acionar um failover de teste, o failover será ignorada para o Grupo de Disponibilidade.

Experimente estas opções de failover.

**Opção** | **Detalhes**
--- | ---
**Opção 1** | 1. Execute um failover de teste das camadas de aplicativo e de front-end.<br/><br/>2. Atualize a camada de aplicativos para acessar a cópia da réplica no modo somente leitura e executar um teste somente leitura do aplicativo.
**Opção 2** | 1. Crie uma cópia da instância da réplica de máquina virtual do SQL Server (usando o clone do VMM para site a site ou Backup do Azure) e ative-a em uma rede de teste<br/><br/> 2. Execute o failover de teste usando o plano de recuperação.

### <a name="fail-back"></a>Failback

Se você quiser transformar o Grupo de Disponibilidade primário novamente no SQL Server local, dispare um failover planejado no plano de recuperação e selecione a direção do Microsoft Azure para o servidor local.

> [!NOTE]
> Após um failover não planejado, será necessário disparar a replicação inversa no Grupo de Disponibilidade para retomar a replicação.  Até que isso seja feito, a replicação permanecerá suspensa.
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Integrar com o SQL Server AlwaysOn para replicação no Azure (Portal Clássico/Portal do Azure sem um servidor VMM/de configuração)

Essas instruções são relevantes se você estiver integrando com Grupos de Disponibilidade do SQL Server no novo Portal do Azure ou no Portal Clássico, se você não estiver usando um servidor VMM ou um servidor de configuração. Nesse cenário, é possível usar Runbooks de Automação do Azure para configurar um failover com script dos Grupos de Disponibilidade do SQL.

Você precisa saber do seguinte:

1. Crie um arquivo local para um script que realiza failover em um grupo de disponibilidade. Este exemplo de script especifica um caminho até o grupo de disponibilidade na réplica do Azure e realiza seu failover nessa instância de réplica. Esse script é executado na réplica de máquina virtual do SQL Server passando com a extensão de script personalizada.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Carregue o script em um blob em uma conta de armazenamento do Azure. Use este exemplo:

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Crie um runbook de automação do Azure para invocar os scripts na réplica de máquina virtual do SQL Server no Azure. Use este exemplo de script para fazer isso. [Saiba mais](site-recovery-runbook-automation.md) sobre como usar runbooks de automação em planos de recuperação.

4. Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o runbook de automação para realizar o failover de grupos de disponibilidade.


5. O AlwaysOn do SQL não oferece suporte nativo ao failover de teste. Portanto, recomendamos o seguinte:
    1. Configure o [Backup do Azure](../backup/backup-azure-vms.md) na máquina virtual que hospeda a réplica do grupo de disponibilidade no Azure.
    1. Antes de disparar o failover de teste do plano de recuperação, recupere a máquina virtual no backup feito na etapa anterior.
    1. Execute o failover de teste do plano de recuperação.


> [!NOTE]
> O script abaixo pressupõe que o Grupo de Disponibilidade SQL está hospedado em uma VM do Azure clássico e que o nome da máquina virtual restaurada na Etapa&2; é SQLAzureVM-Test. Modifique o script com base no nome que você usa para a máquina virtual recuperada.
>
>


     ``workflow SQLAvailabilityGroupFailover
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
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>Integração com o SQL Server AlwaysOn para replicação em um site local secundário

Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade (ou uma FCI), recomendamos também o uso dos grupos de disponibilidade no site de recuperação. Observe que isso se aplica a aplicativos que não usam transações distribuídas.

1. [Configure bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma rede virtual no site secundário.
3. Configure uma conexão de VPN site a site entre a rede virtual e o site primário.
4. Crie uma máquina virtual no site de recuperação e instale nela o SQL Server.
5. Estenda os grupos de disponibilidade AlwaysOn existentes para a nova VM do SQL Server. Configure essa instância do SQL Server como uma cópia de réplica assíncrona.
6. Crie um ouvinte do grupo de disponibilidade ou atualize o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
7. Certifique-se de que o farm de aplicativos esteja configurado usando o ouvinte. Se a instalação tiver usado o nome de servidor do banco de dados, atualize-o para usar o ouvinte de modo que você não precise reconfigurá-lo após o failover.

Para aplicativos que usam transações distribuídas, recomendamos a implantação do Site Recovery com [replicação SAN](site-recovery-vmm-san.md) ou a [Replicação entre sites do VMware/servidor físico](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações sobre o Plano de Recuperação
1. Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. Ao criar um plano de recuperação para o aplicativo, adicione uma etapa com script "inicialização pré-Grupo 1", que invoca o script para realizar o failover de grupos de disponibilidade.

## <a name="protect-a-standalone-sql-server"></a>Proteger um SQL Server autônomo

Nesse cenário, recomendamos o uso da replicação de Site Recovery para proteger a máquina do SQL Server. As etapas exatas dependerão se o SQL Server é uma VM ou um servidor físico, e se você deseja replicar para o Azure ou para um site secundário local. Saiba mais sobre [cenários do Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteger um cluster do SQL Server (edição standard/Windows Server 2008 R2)

Para um cluster executando o SQL Server Standard ou o SQL Server 2008 R2, recomendamos o uso da replicação do Site Recovery para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>Local para o próprio local

* Caso o aplicativo use transações distribuídas, recomendamos implantar o [Site Recovery com replicação SAN](site-recovery-vmm-san.md) para um ambiente Hyper-V ou [VMware/servidor físico para VMware](site-recovery-vmware-to-vmware.md) para um ambiente VMware.
* Para aplicativos não DTC, use a abordagem anterior para recuperar o cluster como um servidor autônomo, aproveitando um espelho de banco de dados local de segurança alta.

### <a name="on-premises-to-azure"></a>Local para o Azure

O Site Recovery não dá suporte a clusters convidados ao replicar para o Azure. O SQL Server também não fornece uma solução de recuperação de desastres de baixo custo para a edição Standard. Nesse cenário, recomendamos a proteção do cluster local do SQL Server para um SQL Server autônomo e sua recuperação no Azure.

1. Configure uma instância do SQL Server Autônomo adicional no site local.
2. Configure a instância para servir como um espelho para os bancos de dados que você quer proteger. Configure o espelhamento no modo de alta segurança.
3. Configure o Site Recovery no site local para [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMs VMware/servidores físicos](site-recovery-vmware-to-azure-classic.md).
4. Use a replicação da Recuperação de Site para replicar a nova instância do SQL Server para o Azure. Como é uma cópia espelhada de alta segurança, ela será sincronizada com o cluster primário, mas será replicada para o Microsoft Azure usando a replicação do Site Recovery.


![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Considerações sobre failback

Para clusters do SQL Server Standard, a realização do failback após um failover não planejado exige um backup e restauração do SQL Server, a partir da instância de espelho para o cluster original com o restabelecimento do espelhamento.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](site-recovery-components.md) sobre a arquitetura do Site Recovery.

