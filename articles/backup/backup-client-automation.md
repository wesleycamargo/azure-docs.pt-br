<properties
	pageTitle="Implantar e gerenciar o backup para Windows Server/Client usando o Windows PowerShell | Microsoft Azure"
	description="Aprenda a implantar e gerenciar o Backup do Azure usando o Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Implantar e gerenciar o backup no Azure para Windows Server/ Windows Client usando o Windows PowerShell
Este artigo mostra como usar a interface de linha de comando do Windows PowerShell® para configurar o Backup do Azure no Windows Server ou no Windows Client e para gerenciar backups e restaurações.

## Configurando o ambiente do Windows PowerShell
Antes de usar o Windows PowerShell para gerenciar o Backup do Azure, você precisará ter a versão certa do Windows PowerShell e as variáveis de ambiente configuradas.

## Configuração e registro
As seguintes tarefas de configuração e registro podem ser automatizadas com o Windows PowerShell - Instalando o agente de Backup do Azure - Registrando o serviço de Backup do Azure - Rede

### Instalando o agente de Backup do Azure
Para instalar o agente de Backup do Azure, você precisa ter o instalador baixado, já no Windows Server. Você pode obter a versão mais recente do instalador no [Microsoft Download Center](aka.ms/azurebackup_agent). Salve o instalador em um local de fácil acesso, como *C:\Downloads*. Para instalar o agente, execute o seguinte comando em um console do Windows PowerShell com privilégios elevados:

```
PS C:> MARSAgentInstaller.exe /q
```

O agente será instalado com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se você não especificar a opção **/nu**, a janela do **Windows Update** será aberta no final da instalação para verificar se há atualizações.

O agente será exibido na lista de programas instalados. Para ver a lista de programas instalados, vá para **Painel de controle** > **Programas** > **Programas e recursos**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

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
Antes de se registrar no serviço de Backup do Azure, certifique-se de que os [pré-requisitos](backup-try-azure-backup-in-10-mins.md) sejam atendidos: - Tenha uma assinatura válida do Azure - Crie um cofre de backup - Baixe as credenciais do cofre e armazene-as em um local conveniente (como *C:\Downloads*). As credenciais do cofre também podem ser renomeadas para sua conveniência.

O registro da máquina no cofre é feito usando o [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet:

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como entrada para o cmdlet.


### Rede
Quando a conectividade do computador Windows com a internet for através de um servidor proxy, as configurações de proxy também podem ser fornecidas para o agente. Neste exemplo, não há nenhum servidor proxy, por isso estamos explicitamente omitindo todas as informações relacionadas a proxy.

O uso de largura de banda também pode ser controlado com opções de *largura de banda em horário de trabalho* e *largura de banda fora do horário de trabalho* para um determinado conjunto de dias da semana.

A configuração dos detalhes de proxy e largura de banda é feita usando o [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet:

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Configurações de criptografia
Os dados de backup enviados para o Backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração. É importante manter essas informações seguras depois de defini-las.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Desinstalando o agente de Backup do Azure
A desinstalação do agente de Backup do Azure pode ser feita usando o seguinte comando:

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

A desinstalação dos binários do agente do computador tem outras consequências: - Remove o filtro de arquivo do computador e o rastreamento de alterações é interrompido. - Todas as informações de política são removidas do computador, mas as informações de política continuam a ser armazenadas no serviço. - Todos os agendamentos de backup são removidos e nenhum backup adicional é feito.

No entanto, os dados armazenados no Azure permanecem e são retidos de acordo com a política de retenção configurada por você. Os pontos mais antigos são desatualizados automaticamente.

## Gerenciamento remoto
Todo o gerenciamento relacionado ao agente, às políticas e as fontes de dados de Backup do Azure pode ser feito remotamente através do Windows PowerShell. A máquina que será gerenciada remotamente precisa ser preparada corretamente.

Por padrão, o serviço WinRM é configurado para inicialização manual. O tipo de inicialização deve ser definido como *Automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade Status deve ser *Em execução*.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

O computador agora pode ser gerenciado remotamente - começando na instalação do agente. Por exemplo, o script a seguir copia o agente para o computador remoto e o instala.

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Próximas etapas
Para obter mais informações sobre o Backup do Azure para Windows Server/Client, consulte [Introdução ao Backup do Azure](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->