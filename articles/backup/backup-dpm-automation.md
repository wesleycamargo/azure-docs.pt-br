<properties
	pageTitle="Backup do Azure - Implantar e gerenciar o backup para DPM usando o Windows PowerShell | Microsoft Azure"
	description="Saiba como implantar e gerenciar o backup do Azure para Data Protection Manager (DPM) usando o Windows PowerShell"
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
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Implantar e gerenciar o backup do Azure para servidores do Data Protection Manager (DPM) usando o Windows PowerShell
Este artigo mostra como usar a interface de linha de comando do Windows PowerShell® para configurar o Backup do Azure em um servidor DPM e para gerenciar backups e restaurações.

## Configurando o ambiente do Windows PowerShell
Antes de usar o Windows PowerShell para gerenciar o Backup do Azure, você precisará ter a versão certa do Windows PowerShell e as variáveis de ambiente configuradas.

Verifique se você tem o Windows PowerShell versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows/DPM PowerShell.

```
$PSVersionTable
```

Você receberá o seguinte tipo de informações:

| Nome | Valor |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Caso não seja, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Configuração e registro
### Instalando o agente de Backup do Azure em um Servidor DPM
Para instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a versão mais recente do instalador no [Microsoft Download Center](aka.ms/azurebackup_agent). Salve o instalador em um local de fácil acesso, como *C:*.

Para instalar o agente, execute o seguinte comando em um console do Windows PowerShell de DPM com privilégios elevados:

```
PS C:> MARSAgentInstaller.exe /q
```

O agente será instalado com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção */nu*, a janela do Windows Update será aberta no final da instalação para verificar se há atualizações.

O agente será exibido na lista de programas instalados. Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

#### Opções de instalação
Para ver todas as opções disponíveis por meio da linha de comando, use o seguinte comando:

```
PS C:> MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Padrão |
| ---- | ----- | ----- |
| /q | Instalação silenciosa | - | | /p:"local" | Caminho para a pasta de instalação do agente de Backup do Azure. | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"local" | Caminho para a pasta de cache do agente de Backup do Azure. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Inscreva no Microsoft Update | - | | /nu | Não verificar se há atualizações após a conclusão da instalação | - | | /d | Desinstala o agente de Serviços de Recuperação do Microsoft Azure | - | | /Ph | Endereço de Host do Proxy | - | | /po | Número da porta do Host do Proxy | - | | /pu | Nome de usuário do Host do Host | - | | /pw | Senha do Proxy | - |

### Registrando-se no serviço de Backup do Azure
Antes de se registrar no serviço de Backup do Azure, certifique-se de que os seguintes pré-requisitos sejam atendidos: - Tenha uma assinatura válida do Azure - Crie um cofre de backup - Baixe as credenciais do cofre e armazene-as em um local conveniente (como *C:\Downloads*). As credenciais do cofre também podem ser renomeadas para sua conveniência.

O registro da máquina no cofre é feito usando o cmdlet [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

Isso registrará o servidor DPM denominado "TestingServer" com o cofre do Microsoft Azure usando as credenciais do cofre localizadas em *C:*.

> [AZURE.IMPORTANT]Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como entrada para o cmdlet.

### Configurando as opções de backup do DPM (configurações de assinatura) para o Online Backup
Depois que o servidor DPM for registrado para a proteção online, ele será definido com configurações de assinatura padrão, que especificam várias opções de backup. Se quiser alterar as configurações de assinatura, é necessário obter a configuração existente e, em seguida, modificá-la de acordo com as suas necessidades.

#### Obtendo as configurações de assinatura existentes para o servidor DPM
Para definir as configurações de assinatura online do DPM, primeiro você precisa obter as configurações existentes usando o cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx):

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Você precisa executar esse comando para obter o objeto de configuração de assinatura que pode ser usado para configurar várias opções e, em seguida, salvar as configurações no final. A execução do comando para um novo servidor DPM retornará as configurações padrão.

#### Modificando as configurações de assinatura
Você pode configurar as seguintes configurações da assinatura de backup online do DPM usando o cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791):

##### Área de preparação
O agente de Backup do Azure em execução no servidor DPM precisa armazenar temporariamente os dados restaurados da nuvem (área de preparação local). Você pode usar o comando abaixo para configurar a área de preparação. Isso definirá a área de preparação como "C:\StagingArea" para **$setting**, mas a configuração ainda não terá sido aplicada ao servidor DPM a menos que você confirme as configurações.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Certifique-se de que a pasta especificada no comando acima já exista. Caso contrário, você receberá uma mensagem de erro ao salvar as configurações de assinatura

##### Rede
A conectividade do computador do DPM com a internet é realizada através de um servidor proxy. As configurações de proxy podem ser fornecidas para o agente. Neste exemplo, não há nenhum servidor proxy, por isso estamos explicitamente omitindo todas as informações relacionadas a proxy.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

O uso de largura de banda também pode ser controlado com as opções de *largura de banda em horário de trabalho* e *largura de banda fora do horário de trabalho* para um determinado conjunto de dias da semana. Neste exemplo, não definimos nenhuma limitação

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Configurações de criptografia
Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração.

> [AZURE.IMPORTANT]Mantenha essas informações seguras depois de defini-las.

No exemplo abaixo, o primeiro comando converte a cadeia de caracteres passphrase123456789 em uma cadeia de caracteres segura e atribui a sequência segura à variável chamada de $Passphrase. O segundo comando define a cadeia de caracteres segura em $Passphrase como a senha de criptografia de backups

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Salvando as configurações de assinatura
Você precisa confirmar as alterações do servidor DPM usando a opção *–Confirmar*

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]As configurações não serão aplicadas a menos que confirmadas.

## Protegendo dados para o Backup do Azure
As próximas etapas mostrarão como se adiciona um servidor de produção ao DPM e, em seguida, como se protege os dados em disco e online para o Azure. Nos exemplos, demonstraremos como fazer backup de arquivos e pastas. A lógica pode ser facilmente estendida para fazer backup de qualquer fonte de dados com suporte. Como pré-requisito, é necessário configurar e registrar o servidor DPM para proteção online.

Todos os backups do DPM são regidos por um grupo de proteção (PG) que consiste de quatro partes

1. **Membros do grupo** é uma lista de todos os objetos protegíveis que você deseja proteger no mesmo PG. Por exemplo, convém proteger as VMs de produção em um PG e as VMs de teste em outro PG, pois elas podem ter requisitos de backup diferentes.
2. **Método de proteção de dados** é usado para especificar onde você deseja proteger os dados. Neste exemplo, protegeremos os dados em disco e proteção online para a nuvem do Azure.
3. Um **agendamento de backup** que especifica quando os backups precisam ser tomados e com que frequência os dados devem ser sincronizados entre a cópia do servidor DPM e o servidor de produção.
4. Um **cronograma de retenção** que especifica quanto tempo deve-se manter os pontos de recuperação no Azure.

### Criar um grupo de proteção e fazer backup de dados em intervalos agendados
#### Adicionando um servidor de produção a um servidor DPM
Antes de poder fazer backup de dados em um servidor de produção, é necessário verificar se o agente do DPM está instalado no servidor de produção e é gerenciado pelo DPM. Siga as etapas [aqui](https://technet.microsoft.com/library/bb870935.aspx) para instalar o agente do DPM e configurá-lo para fazer backup pelo servidor DPM apropriado.

#### Criando um PG
Neste artigo, já que estamos automatizando o backup, vamos pressupor que nada foi configurado. Começamos criando um novo PG usando o cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx).

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Antes de fazermos qualquer alteração no PG, é necessário colocá-lo em modo modificável usando o cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx).

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

Os cmdlets acima criarão um PG chamado de "ProtectGroup01", mas eles não fazem nenhum backup ainda. É necessário definir do que será feito backup, quando os backups serão executados e onde os backups serão armazenados.

### Adicionando membros do grupo no PG
Há várias etapas para adicionar uma fonte de dados no PG

#### Obtendo o servidor de produção (PS) que queremos fazer backup
Você pode buscar todos os PS nos quais o agente do DPM está instalado e sendo gerenciado pelo servidor DPM usando o cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx). Neste exemplo, filtraremos e configuraremos apenas o PS com o nome "productionserver01" para o backup.

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Execute uma consulta para obter uma lista de fontes de dados no PS
Você pode executar uma consulta na máquina do PS para obter uma lista de todas as fontes de dados que o DPM pode fazer backup usando o cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx). Neste exemplo, você pode filtrar a fonte de dados "D:\" no PS para o qual queremos configurar a proteção.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Adicionando a fonte de dados para proteção
Você pode adicionar a fonte de dados ao PG "ProtectGroup01" criado no exemplo anterior usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx)

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Selecionando o método de proteção de dados
Depois de adicionar a fonte de dados ao PG, é necessário especificar o método de proteção. Neste caso, configuramos o PG para proteção de curto prazo em disco e proteção de longo prazo em fita.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Especifique os objetivos do ponto de recuperação
#### Definindo o intervalo de retenção
Você pode definir o período de retenção para o PG usando [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx). O comando abaixo define o *retentionrange* e a *frequência de sincronização* para o ponto de recuperação baseado em disco.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

Para pontos de recuperação online, você pode configurar intervalos de retenção para vários esquemas de GFS (ou seja, diário, semanal, mensal e anual). Neste exemplo, criamos um objeto com vários intervalos de retenção e, em seguida, configuramos o intervalo de retenção online usando o objeto, como mostra o exemplo abaixo.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Definindo agendamentos de Backup
O DPM define os agendamentos padrão automaticamente se você especificar o objetivo de proteção usando o cmdlet **Set-PolicyObjective**. Para alterar os agendamentos padrão, use o cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) seguido do cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx).

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Criação de réplica
Se você estiver fazendo backup de fonte de dados pela primeira vez, o DPM precisa criar uma réplica inicial que criará uma cópia da fonte de dados que será protegida no volume de réplica do DPM. Você pode programar a criação de uma réplica automaticamente em um momento específico, ou manualmente, usando o cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx) .

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Confirmando o PG
Finalmente, é necessário confirmar as alterações antes que o DPM possa obter o backup de acordo com a configuração do PG usando o cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx).

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Obtendo uma lista de todos os pontos de recuperação online
Você pode usar o cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) para obter uma lista de todos os pontos de recuperação de fonte de dados. Neste exemplo: - buscamos todos os PGs no servidor DPM que serão armazenados em uma matriz $PG - Obtemos as fontes de dados que correspondem a $PG[0] - Obtemos todos os pontos de recuperação de uma fonte de dados.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Restaurar dados protegidos no Azure
Neste exemplo, demonstramos como restaurar uma VM Hyper-V de um ponto de recuperação online, mas os comandos podem ser facilmente estendidos a qualquer tipo de fonte de dados.

Primeiro, você precisa criar uma opção de recuperação usando o cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx). Para o exemplo abaixo, recuperaremos uma fonte de dados do Hyper-V em um local alternativo.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

Depois de configurar a opção de recuperação, será restaurado o ponto de recuperação online obtido na seção [Obtendo uma lista de todos os pontos de recuperação online](#Getting-a-list-of-all-Online-Recovery-Points) acima.

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Próximas etapas
Para obter mais informações sobre o Backup do Azure para DPM, consulte [Introdução ao Backup de DPM do Azure](backup-azure-dpm-introduction.md)

<!---HONumber=62-->