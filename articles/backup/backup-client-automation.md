<properties
	pageTitle="Implantar e gerenciar o backup para Windows Server/Client usando o PowerShell do Azure | Microsoft Azure"
	description="Saiba como implantar e gerenciar o Backup do Azure usando o PowerShell do Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="aashishr"; "jimpark"/>


# Implantar e gerenciar o backup no Azure para Windows Server/Windows Client usando o Azure PowerShell
Este artigo mostra como usar o Azure PowerShell para configurar o Backup do Azure no Windows Server ou no Windows Client e para gerenciar backups e recuperações.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Configuração e registro
As seguintes tarefas de configuração e registro podem ser automatizadas com o Azure PowerShell:

- Instalando o agente de Backup do Azure
- Registrando-se no serviço de Backup do Azure
- Rede

### Instalando o agente de Backup do Azure
Antes de instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a versão mais recente do instalador no [Microsoft Download Center](http://aka.ms/azurebackup_agent). Salve o instalador em um local de fácil acesso, como *C:\\Downloads*.

Para instalar o agente, execute o seguinte comando em um console do Azure PowerShell com privilégios elevados:

```
PS C:\> MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção */nu*, a janela do **Windows Update** será aberta no final da instalação para verificar se há atualizações. Uma vez instalado, o agente será exibido na lista de programas instalados.

Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

#### Opções de instalação

Para ver todas as opções disponíveis por meio da linha de comando, use o seguinte comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Padrão |
| ---- | ----- | ----- |
| /q | Instalação silenciosa | - | | /p:"local" | Caminho para a pasta de instalação do agente de Backup do Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent | | /s:"local" | Caminho para a pasta de cache do agente de Backup do Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent\\Scratch | | /m | Inscreva no Microsoft Update | - | | /nu | Não verificar se há atualizações após a conclusão da instalação | - | | /d | Desinstala o agente de Serviços de Recuperação do Microsoft Azure | - | | /Ph | Endereço de Host do Proxy | - | | /po | Número da porta do Host do Proxy | - | | /pu | Nome de usuário do Host do Host | - | | /pw | Senha do Proxy | - |


### Registrando-se no serviço de Backup do Azure
Antes de poder se registrar no serviço de Backup do Azure, você precisa garantir que os [pré-requisitos](backup-try-azure-backup-in-10-mins.md) sejam atendidos. Você deve:

- Ter uma assinatura válida do Azure
- Criar um cofre de backup
- Baixe as credenciais do cofre e armazene-as em um local conveniente (como *C:\\Downloads*). As credenciais do cofre também podem ser renomeadas para sua conveniência.

O registro da máquina no cofre é feito usando o [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet:

```
PS C:\> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como entrada para o cmdlet.

### Rede
Quando a conectividade do computador Windows com a internet for através de um servidor proxy, as configurações de proxy também podem ser fornecidas para o agente. Neste exemplo, não há nenhum servidor proxy, por isso estamos explicitamente omitindo todas as informações relacionadas a proxy.

O uso de largura de banda também pode ser controlado com as opções de ```work hour bandwidth``` e ```non-work hour bandwidth``` para um determinado conjunto de dias da semana.

A configuração dos detalhes de proxy e largura de banda é feita usando o [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Configurações de criptografia
Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT]Mantenha as informações de senha seguras e protegidas depois de defini-las. Você não poderá restaurar os dados do Azure sem essa senha.

## Fazer backup de arquivos e pastas
Todos os seus backups de servidores e clientes Windows para o Azure Backup são controlados por uma política. A política consiste em três partes:

1. Um **agendamento de backup** que especifica quando backups precisam ser efetuados e sincronizados com o serviço.
2. Um **cronograma de retenção** que especifica quanto tempo deve-se manter os pontos de recuperação no Azure.
3. Uma **especificação de inclusão/exclusão de arquivo** que determina de que conteúdo deve-se realizar o backup.

Neste documento, já que estamos automatizando o backup, vamos pressupor que nada foi configurado. Começamos criando uma nova política de backup por meio do cmdlet [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) e usando-a.

```
PS C:\> $newpolicy = New-OBPolicy
```

Neste momento, a política está vazia e outros cmdlets são necessários para definir quais itens serão incluídos ou excluídos, quando backups serão executados e onde os backups serão armazenados.

### Configurando o agendamento de backup
A primeira das três partes de uma política é o agendamento de backup, que é criado usando o cmdlet [New-OBSchedule](https://technet.microsoft.com/library/hh770401). O agendamento de backup define quando os backups precisam ser executados. Ao criar um agendamento, você precisa especificar dois parâmetros de entrada:

- Os **dias da semana** nos quais o backup deve ser executado. Você pode executar o trabalho de backup em apenas um dia ou em todos os dias da semana, ou qualquer combinação entre essas opções.
- Os **horários do dia** quando o backup deve ser executado. Você pode definir até três horários do dia diferentes quando o backup será disparado.

Por exemplo, você poderia configurar uma política de backup que é executada às 16h todo sábado e domingo.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

O agendamento de backup deve ser associado a uma política, e isso pode ser realizado usando o cmdlet [Set-OBSchedule](https://technet.microsoft.com/library/hh770407).

```
PS C:\> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### Configurando uma política de retenção
A política de retenção define por quanto tempo os pontos de recuperação criados por meio de trabalhos de backup são mantidos. Ao criar uma nova política de retenção usando o cmdlet [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425), você pode especificar o número de dias durante os quais os pontos de recuperação de backup precisam ser mantidos com o Backup do Azure. O exemplo a seguir define uma política de retenção de sete dias.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

> [AZURE.NOTE]Atualmente não há suporte, nos cmdlets do PowerShell, para a configuração de políticas de retenção de longo prazo. Use o console da interface do usuário do Backup do Azure para definir políticas de retenção de longo prazo.

A política de retenção deve ser associada à política principal usando o cmdlet [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### Incluindo e excluindo arquivos destinados a backup
Um objeto ```OBFileSpec``` define os arquivos a serem incluídos e excluídos em um backup. Este é um conjunto de regras que definem o escopo dos arquivos e pastas protegidos em um computador. Você pode ter muitas regras de inclusão ou exclusão de arquivos conforme necessário e associá-las a uma política. Ao criar um novo objeto OBFileSpec, você pode:

- Especificar os arquivos e pastas a serem incluídos
- Especificar os arquivos e pastas a serem excluídos
- Especificar o backup recursivo de dados em uma pasta ou o backup apenas dos arquivos de nível superior na pasta especificada.

A última opção é obtida usando o sinalizador -NonRecursive no comando New-OBFileSpec.

No exemplo a seguir, vamos fazer backup dos volumes C: e D: e excluir os binários do sistema operacional na pasta do Windows e em quaisquer pastas temporárias. Para isso, vamos criar duas especificações de arquivo usando o cmdlet [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) – uma para inclusão e outra para exclusão. Depois que as especificações de arquivo são criadas, elas são associadas à política usando o cmdlet [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424).

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:", "D:")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### Aplicando a política
Agora, o objeto de política está concluído e tem um agendamento de backup associado, política de retenção e uma lista de inclusão/exclusões de arquivos. Esta política agora pode ser confirmada para uso pelo Backup do Azure. Antes de aplicar a política recém-criada, verifique se não há nenhuma política de backup existente associada ao servidor, por meio do cmdlet [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415). Remover a política gerará uma solicitação de confirmação. Para ignorar a confirmação, use o sinalizador ```-Confirm:$false``` com o cmdlet.

```
PS C:\> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A confirmação do objeto de política é realizada usando o cmdlet [Set-OBPolicy](https://technet.microsoft.com/library/hh770421). Isso também gerará uma solicitação de confirmação. Para ignorar a confirmação, use o sinalizador ```-Confirm:$false``` com o cmdlet.

```
PS C:\> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : {DataSource DatasourceId:4508156004108672185 Name:C:\ FileSpec:FileSpec FileSpec:C:\ IsExclude:False IsRecursive:True ,FileSpec FileSpec:C:\windows IsExclude:True IsRecursive:True ,FileSpec FileSpec:C:\temp IsExclude:True IsRecursive:True
              , DataSource
              DatasourceId:4508156005178868542
              Name:D:\
              FileSpec:FileSpec
              FileSpec:D:\
              IsExclude:False
              IsRecursive:True

              }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Você pode exibir os detalhes da política de backup existente usando o cmdlet [Get-OBPolicy](https://technet.microsoft.com/library/hh770406). É possível fazer uma busca mais detalhada usando o cmdlet [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) para o agendamento de backup e o cmdlet [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) para as políticas de retenção.

```
PS C:\> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a ScheduleRunDays : {Saturday, Sunday} ScheduleRunTimes : {16:00:00} State : Existing
PS C:\> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7 RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e State : Existing
PS C:\> Get-OBPolicy | Get-OBFileSpec
FileName : * FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ FileSpec : D:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\ FileSpec : C:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows FileSpec : C:\windows IsExclude : True IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp FileSpec : C:\temp IsExclude : True IsRecursive : True
```

### Executando um backup ad hoc
Depois de definir uma política de backup, os backups ocorrerão de acordo com o agendamento. Disparar um backup ad hoc também é possível usando o cmdlet [Start-OBBackup](https://technet.microsoft.com/library/hh770426):

```
PS C:\> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes... Preparing storage... Estimating size of backup items... Estimating size of backup items... Transferring data... Verifying backup... Job completed. The backup operation completed successfully.
```

## Restaurar dados por meio do Backup do Azure
Esta seção o orientará pelas etapas para automatizar a recuperação de dados por meio do Backup do Azure. Isso envolve as seguintes etapas:

1. Selecionar o volume de origem
2. Escolher um ponto de backup para restaurar
3. Escolha um item para restaurar
4. Disparar o processo de restauração

### Selecionar o volume de fonte
Para restaurar um item por meio do Backup do Azure, é necessário primeiro identificar a fonte do item. Como estamos executando os comandos no contexto de um Windows Server ou um cliente Windows, o computador já foi identificado. A próxima etapa para identificação da origem é identificar o volume que a contém. É possível recuperar uma lista de volumes ou fontes cujo backup está sendo executado neste computador pela execução do cmdlet [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410). Esse comando retorna uma matriz de todas as fontes desse servidor/cliente cujo backup foi executado.

```
PS C:\> $source = Get-OBRecoverableSource
PS C:\> $source FriendlyName : C:\ RecoverySourceName : C:\ ServerName : myserver.microsoft.com
FriendlyName : D:\ RecoverySourceName : D:\ ServerName : myserver.microsoft.com
```

### Escolhendo um ponto de backup para restaurar
A lista de pontos de backup pode ser recuperada executando o cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) com os parâmetros apropriados. Em nosso exemplo, escolheremos o último ponto de backup para o volume de fonte *D:* e o usaremos para recuperar um arquivo específico.

```
PS C:\> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 17-Jun-15 6:31:31 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
```
O objeto ```$rps``` é uma matriz de pontos de backup. O primeiro elemento é o ponto mais recente e o enésimo elemento é o ponto mais antigo. Para escolher o último ponto, usaremos ```$rps[0]```.

### Escolhendo um item para restaurar
Para identificar o arquivo ou pasta exato a ser restaurado, use recursivamente o cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx). Desse modo, a hierarquia de pastas pode ser pesquisada usando somente o ```Get-OBRecoverableItem```.

Neste exemplo, se quisermos restaurar o arquivo *finances.xls*, podemos fazer referência a ele usando o objeto ```$filesFolders[1]```.

```
PS C:\> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:\> $filesFolders IsDir : True ItemNameFriendly : D:\MyData\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\ LocalMountPoint : D:\ MountPointName : D:\ Name : MyData PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
PS C:\> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:\> $filesFolders IsDir : False ItemNameFriendly : D:\MyData\screenshot.oxps ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps LocalMountPoint : D:\ MountPointName : D:\ Name : screenshot.oxps PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 228313 ItemLastModifiedTime : 21-Jun-14 6:45:09 AM
IsDir : False ItemNameFriendly : D:\MyData\finances.xls ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls LocalMountPoint : D:\ MountPointName : D:\ Name : finances.xls PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 96256 ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Você também pode procurar itens a serem restaurados usando o cmdlet ```Get-OBRecoverableItem```. Em nosso exemplo, para procurar *finances.xls*, poderíamos obter um identificador no arquivo executando esse comando:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### Disparando o processo de restauração
Para disparar o processo de restauração, primeiro precisamos especificar as opções de recuperação. Isso pode ser feito usando o cmdlet [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx). Para este exemplo, vamos pressupor que queremos restaurar os arquivos para *C:\\temp*. Vamos pressupor também que queremos ignorar os arquivos já existentes na pasta de destino *C:\\temp*. Para criar uma opção de recuperação desse tipo, use o seguinte comando:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora dispare a restauração usando o comando [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) no ```$item``` selecionado da saída do cmdlet ```Get-OBRecoverableItem```:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Job completed. The recovery operation completed successfully.
```


## Desinstalando o agente de Backup do Azure
A desinstalação do agente de Backup do Azure pode ser feita usando o seguinte comando:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Antes de desinstalar os binários do agente do computador, é necessário considerar algumas consequências:

- A desinstalação remove o filtro de arquivo do computador e interrompe o acompanhamento de alterações.
- Todas as informações de política são removidas do computador, mas as informações de política continuam sendo armazenadas no serviço.
- Todos os agendamentos de backup são removidos e nenhum backup posterior é realizado.

No entanto, os dados armazenados no Azure permanecem e são mantidos de acordo com a política de retenção configurada por você. Os pontos mais antigos são desatualizados automaticamente.

## Gerenciamento remoto
Todo o gerenciamento relacionado ao agente, às políticas e às fontes de dados do Backup do Azure pode ser feito remotamente por meio do PowerShell do Azure. A máquina que será gerenciada remotamente precisa ser preparada corretamente.

Por padrão, o serviço WinRM é configurado para inicialização manual. O tipo de inicialização deve ser definido como *Automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade Status deve ser *Em execução*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

O PowerShell do Azure deve ser configurado remotamente.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

O computador agora pode ser gerenciado remotamente - começando na instalação do agente. Por exemplo, o script a seguir copia o agente para o computador remoto e o instala.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## Próximas etapas
Para obter mais informações sobre o Backup do Azure para Windows Server/Client, consulte [Introdução ao Backup do Azure](backup-introduction-to-azure-backup.md)

<!---HONumber=August15_HO6-->