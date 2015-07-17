<properties
	pageTitle="Backup do Azure – Implantar e gerenciar o backup para o DPM usando o PowerShell do Azure | Microsoft Azure"
	description="Saiba como implantar e gerenciar o backup do Azure para o Data Protection Manager (DPM) usando o PowerShell do Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2015"
	ms.author="jimpark"/>


# Implantar e gerenciar o backup do Azure para servidores do Data Protection Manager (DPM) usando o PowerShell do Azure
Este artigo mostra como usar o PowerShell do Azure para configurar o Backup do Azure em um servidor DPM e para gerenciar backups e recuperações.

## Configurando o ambiente do PowerShell do Azure
Antes de usar o PowerShell do Azure para gerenciar backups do Data Protection Manager para o Azure, você precisará ter o ambiente certo no PowerShell do Azure. No início da sessão do PowerShell do Azure, certifique-se de executar o comando a seguir para importar os módulos corretos e permitir que você faça referência corretamente aos cmdlets do DPM:

```
PS C:\> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command Only DPM cmdlets: Get-DPMCommand Get general help: help Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -? Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax Sample DPM scripts: Get-DPMSampleScript
```

## Configuração e registro
### Instalando o agente de Backup do Azure em um Servidor de DPM
Antes de instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a versão mais recente do instalador no [Microsoft Download Center](http://aka.ms/azurebackup_agent). Salve o instalador em um local de fácil acesso, como *C:\Downloads*.

Para instalar o agente, execute o seguinte comando em um console do Azure PowerShell com privilégios elevados **no servidor do DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção */nu*, a janela do **Windows Update** será aberta no final da instalação para verificar se há atualizações.

O agente será exibido na lista de programas instalados. Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

#### Opções de instalação
Para ver todas as opções disponíveis por meio da linha de comando, use o seguinte comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Padrão |
| ---- | ----- | ----- |
| /q | Instalação silenciosa | - |
| /p:"local" | Caminho para a pasta de instalação do agente de Backup do Azure. | C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"local" | Caminho para a pasta de cache do agente de Backup do Azure. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m | Inscreva no Microsoft Update | - |
| /nu | Não verificar se há atualizações após a conclusão da instalação | - |
| /d | Desinstala o agente de Serviços de Recuperação do Microsoft Azure | - |
| /Ph | Endereço de Host do Proxy | - |
| /po | Número da porta do Host do Proxy | - |
| /pu | Nome de usuário do Host do Host | - |
| /pw | Senha do Proxy | - |

### Registrando-se no serviço de Backup do Azure
Antes de poder se registrar no serviço de Backup do Azure, você precisa garantir que os [pré-requisitos](backup-azure-dpm-introduction.md) sejam atendidos. Você deve:

- Ter uma assinatura válida do Azure
- Criar um cofre de backup
- Baixar as credenciais do cofre e armazená-las em um local conveniente (como *C:\Downloads*). As credenciais do cofre também podem ser renomeadas para sua conveniência. 
O registro da máquina no cofre é feito usando o cmdlet [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:\> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\Downloads\REGISTER.VaultCredentials"
```

Isso registrará o servidor DPM denominado “TestingServer” no Cofre do Microsoft Azure usando as credenciais do cofre especificadas.

> [AZURE.IMPORTANT]Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como entrada para o cmdlet.

### Definições de configuração iniciais
Depois que o servidor de DPM estiver registrado com o cofre de Backup do Azure, ele será iniciado com as configurações de assinatura padrão. Essas configurações de assinatura incluem Rede, Criptografia e Área de preparo. Para começar a alterar as configurações de assinatura, primeiro você precisa obter um identificador nas configurações existentes (padrão) usando o cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793):

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são feitas a este objeto local ```$setting``` do PowerShell do Azure e, em seguida, o objeto completo é confirmado no DPM e no Backup do Azure para salvá-los usando o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Você precisa usar o sinalizador ```–Commit``` para garantir que as alterações sejam mantidas. As configurações não serão aplicadas nem usadas pelo Backup do Azure, a menos que sejam confirmadas.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

### Rede
Se a conectividade do computador de DPM ao serviço de Backup do Azure na Internet é feita por meio de um servidor proxy, as configurações do servidor proxy devem ser fornecidas para que os backups sejam bem-sucedidos. Isso é feito usando os parâmetros ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` e ```ProxyPassword``` com o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Neste exemplo, não há nenhum servidor proxy, por isso estamos explicitamente omitindo todas as informações relacionadas a proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

O uso de largura de banda também pode ser controlado com as opções de ```-WorkHourBandwidth``` e ```-NonWorkHourBandwidth``` para um determinado conjunto de dias da semana. Neste exemplo, não definimos nenhuma limitação.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

### Configurando a Área de preparo
O agente de Backup do Azure em execução no servidor DPM precisa armazenar temporariamente os dados restaurados da nuvem (área de preparação local). Configure a área de preparo usando o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) e o parâmetro ```-StagingAreaPath```.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de preparo será definida como *C:\StagingArea* no objeto ```$setting``` do PowerShell do Azure. Verifique se a pasta especificada já existe, ou a confirmação final das configurações de assinatura falhará.


### Configurações de criptografia
Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração. É importante manter essas informações seguras depois de defini-las.

No exemplo abaixo, o primeiro comando converte a cadeia de caracteres ```passphrase123456789``` em uma cadeia de caracteres segura e atribui a sequência segura à variável chamada ```$Passphrase```. O segundo comando define a cadeia de caracteres segura em ```$Passphrase``` como a senha para a criptografia de backups.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [AZURE.IMPORTANT]Mantenha as informações de senha seguras e protegidas depois de defini-las. Você não poderá restaurar os dados do Azure sem essa senha.

Neste ponto, você deve fez todas as alterações necessárias ao objeto ```$setting```. Lembre-se de confirmar as alterações.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## Proteger dados no Backup do Azure
Nesta seção, você adicionará um servidor de produção ao DPM, protegerá os dados primeiro no armazenamento local do DPM e, em seguida, no Backup do Azure. Nos exemplos, demonstraremos como fazer backup de arquivos e pastas. A lógica pode ser facilmente estendida para fazer backup de qualquer fonte de dados para a qual há suporte no DPM. Todos os backups do DPM são regidos por um GP (Grupo de Proteção) com quatro partes:

1. **Membros do grupo** é uma lista de todos os objetos protegíveis (também conhecidos como *Fontes de dados* no DPM) que você deseja proteger no mesmo grupo de proteção. Por exemplo, convém proteger as VMs de produção em um grupo de proteção e os bancos de dados do SQL Server em outro grupo de proteção, pois eles podem ter requisitos de backup diferentes. Antes de poder fazer backup de qualquer fonte de dados em um servidor de produção, é necessário verificar se o agente do DPM está instalado no servidor e é gerenciado pelo DPM. Siga as etapas para a [instalação do Agente do DPM](https://technet.microsoft.com/library/bb870935.aspx) e vincule-o ao Servidor DPM apropriado.
2. O **Método de proteção de dados** especifica os locais de destino de backup – fita, disco e nuvem. Em nosso exemplo, protegeremos os dados no disco local e na nuvem.
3. Um **agendamento de backup** que especifica quando os backups precisam ser executados e a frequência com que os dados devem ser sincronizados entre o servidor DPM e o servidor de produção.
4. Um **cronograma de retenção** que especifica quanto tempo deve-se manter os pontos de recuperação no Azure.

### Criando um grupo de proteção
Comece criando um novo Grupo de Proteção usando o cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722).

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima criará um Grupo de Proteção chamado *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado posteriormente para adicionar o backup à nuvem do Azure. No entanto, para fazer alterações ao Grupo de Proteção – novo ou existente – precisamos obter um identificador em um objeto *modificável* usando o cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713).

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### Adicionando membros do grupo ao Grupo de Proteção
Cada Agente do DPM conhece a lista de fontes de dados no servidor em que está instalado. Para adicionar uma fonte de dados ao Grupo de Proteção, o Agente do DPM precisa enviar primeiro uma lista das fontes de dados de volta ao servidor DPM. Uma ou mais fontes de dados são então selecionadas e adicionadas ao Grupo de Proteção. As etapas do PowerShell do Azure necessárias para realizar isso são:

1. Obter uma lista de todos os servidores gerenciados pelo DPM por meio do Agente do DPM.
2. Escolher um servidor específico.
3. Obter uma lista de todas as fontes de dados no servidor.
4. Escolher uma ou mais fontes de dados e adicioná-las ao Grupo de Proteção

A lista dos servidores nos quais o Agente do DPM está instalado e sendo gerenciado pelo servidor DPM é obtida usando o cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600). Neste exemplo, filtraremos e configuraremos apenas o PS com o nome *productionserver01* para o backup.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

Agora obtenha a lista de fontes de dados em ```$server``` usando o cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605). Neste exemplo, estamos filtrando para o volume *D:*, que desejamos configurar para o backup. Esta fonte de dados é adicionada ao Grupo de Proteção usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732). Lembre-se de usar o objeto ```$MPG``` *modificável* do grupo de proteção para fazer as inclusões.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita essa etapa quantas vezes for necessário, até adicionar todas as fontes de dados escolhidas ao grupo de proteção. Você também pode começar com apenas uma fonte de dados e concluir o fluxo de trabalho para criar o Grupo de Proteção e, posteriormente, adicionar mais fontes de dados ao Grupo de Proteção.

### Selecionando o método de proteção de dados
Depois de adicionar as fontes de dados ao Grupo de Proteção, a próxima etapa é especificar o método de proteção usando o cmdlet [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725). Neste exemplo, o Grupo de Proteção será configurado para backup no disco local e na nuvem.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Configurando o intervalo de retenção
Defina a retenção dos pontos de backup usando o cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762). Embora possa parecer estranho definir a retenção antes de definir o agendamento de backup, usar o cmdlet ```Set-DPMPolicyObjective``` define automaticamente um agendamento de backup padrão que pode então ser modificado. Sempre é possível definir o agendamento de backup primeiro e a política de retenção posteriormente.

No exemplo abaixo, o cmdlet define os parâmetros de retenção para backups em disco. Isso manterá backups por 10 dias e sincronizará dados entre o servidor de produção e o servidor DPM a cada 6 horas. O ```SynchronizationFrequencyMinutes``` não define a frequência com que um ponto de backup é criado, mas sim a frequência com que os dados são copiados para o servidor DPM; isso impede que os backups fiquem grandes demais.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para backups destinados ao Azure (o DPM refere-se a eles como backups Online), os períodos de retenção podem ser configurados para a [retenção de longo prazo usando um esquema GFS (Avô-Pai-Filho)](backup-azure-backup-cloud-as-tape.md). Ou seja, você pode definir uma política de retenção combinada que envolva políticas de retenção diárias, semanais, mensais e anuais. Neste exemplo, criamos uma matriz que representa o esquema de retenção complexo que queremos e, em seguida, configuramos o intervalo de retenção usando o cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762).

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### Definir o agendamento de backup
O DPM define um agendamento padrão automaticamente se você especificar o objetivo de proteção usando o cmdlet ```Set-DPMPolicyObjective```. Para alterar os agendamentos padrão, use o cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido do cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723).

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` é uma matriz com quatro elementos que contém o agendamento de proteção online existente para o Grupo de Proteção no esquema GFS:

1. ```$onlineSch[0]``` conterá o agendamento diário
2. ```$onlineSch[1]``` conterá o agendamento semanal
3. ```$onlineSch[2]``` conterá o agendamento mensal
4. ```$onlineSch[3]``` conterá o agendamento anual

Portanto, se precisar modificar o agendamento semanal, você precisa se referir a ```$onlineSch[1]```.

### Backup inicial
Ao fazer backup de uma fonte de dados pela primeira vez, o DPM precisa criar uma réplica inicial que criará uma cópia da fonte de dados que será protegida no volume de réplica do DPM. Essa atividade pode ser agendada para um horário específico ou pode ser disparada manualmente usando o cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) com o parâmetro ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### Confirmando as alterações ao Grupo de Proteção
Por último, as alterações precisam ser confirmadas antes que o DPM possa fazer o backup de acordo com a nova configuração do Grupo de Proteção. Isso é feito usando o cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758).

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Exibir os pontos de backup
Você pode usar o cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) para obter uma lista de todos os pontos de recuperação para uma fonte de dados. Neste exemplo, vamos: buscar todos os GPs no servidor DPM que serão armazenados em uma matriz ```$PG```; obter as fontes de dados que correspondem a ```$PG[0]```; obter todos os pontos de recuperação para uma fonte de dados.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Restaurar dados protegidos no Azure
A restauração de dados é uma combinação de um objeto ```RecoverableItem``` e um objeto ```RecoveryOption```. Na seção anterior, obtivemos uma lista de pontos de backup para uma fonte de dados.

No exemplo a seguir, demonstramos como restaurar uma máquina virtual Hyper-V por meio do Backup do Azure, combinando os pontos de backup com o destino para recuperação. Isso inclui:

- Criar uma opção de recuperação usando o cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592).
- Obter a matriz de pontos de backup usando o cmdlet ```Get-DPMRecoveryPoint```.
- Escolher um ponto de backup por meio do qual fazer a restauração.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de fonte de dados.

## Próximas etapas
Para obter mais informações sobre o Backup do Azure para DPM, consulte [Introdução ao Backup de DPM do Azure](backup-azure-dpm-introduction.md)

<!---HONumber=62-->