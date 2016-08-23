<properties
	pageTitle="Replicar as máquinas virtuais VMware no Azure usando o Site Recovery com a DSC de Automação do Azure | Microsoft Azure"
	description="Descreve como usar a DSC de Automação do Azure para implantar automaticamente o Serviço de Mobilidade do ASR e o agente do Azure para as máquinas virtuais/físicas no Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Replicar as VMs do VMware para o Azure usando a Recuperação de Site com a DSC de Automação do Azure

No OMS, oferecemos uma solução abrangente de Backup e Recuperação de Desastres que pode ser usada como parte de seu plano de continuidade de negócios.

Começamos esta jornada junto com o Hyper-V usando a Réplica do Hyper-V, mas expandimos para oferecer suporte a uma configuração heterogênea, pois sabemos que os clientes têm vários hipervisores e plataformas em suas nuvens.

Se você estiver executando cargas de trabalho do VMware e/ou servidores físicos hoje, contaremos com um servidor de gerenciamento que executa todos os componentes da Recuperação de Site em execução no seu ambiente para lidar com a replicação dos dados e a comunicação com o Azure quando ele for seu destino.

## Implantar o Serviço de Mobilidade do ASR usando a DSC de automação do OMS
Vamos começar fazendo uma análise rápida do que este servidor de gerenciamento realmente faz:

O servidor de gerenciamento executa várias funções do servidor, como **configuração** – que coordena a comunicação e gerencia os processos de replicação e recuperação dos dados.

Além disso, a função do **processo** atua como um gateway de replicação onde recebe os dados de replicação das máquinas de origem protegidas, otimiza-os com o cache, compactação e criptografia, antes de enviá-los para a conta de armazenamento do Azure. Uma das funções da função do processo também é a instalação do Serviço de Mobilidade por push nas máquinas protegidas e a execução da descoberta automática das VMs VMware.

No caso de um failback do Azure, a função do **destino mestre** será responsável e lidará com os dados de replicação como parte dessa operação.

Para as máquinas protegidas, podemos contar com o **Serviço de Mobilidade** – que é um componente implantado em cada máquina (VM VMware ou servidor físico) que você deseja replicar para o Azure. Sua função é a captura dos dados gravados na máquina e encaminhá-los para o servidor de gerenciamento (função de processo).

Ao lidar com a continuidade de negócios, é importante compreender suas cargas de trabalho, infraestrutura e também os componentes envolvidos para atender aos requisitos de RTO e RPO. Nesse contexto, o serviço de mobilidade é a chave para garantir que suas cargas de trabalho sejam protegidas conforme o esperado.

Assim, como podemos assegurar, de uma forma otimizada, que temos uma configuração protegida confiável com a ajuda de alguns componentes do OMS?

Neste artigo, forneceremos um exemplo de como você pode aproveitar a DSC de Automação do OMS junto com a Recuperação de Site do OMS para garantir que o Serviço de Mobilidade e o agente de VM do Azure sejam implantados nas máquinas do Windows que você deseja proteger e sempre estejam em execução quando o Azure for o destino da replicação.

## Pré-requisitos

- Um repositório para armazenar a configuração necessária
- Um repositório para armazenar a senha necessária para se registrar no servidor de gerenciamento
- Windows Management Framework 5.0 instalado nos computadores que você deseja habilitar para a proteção (requisito para a DSC de Automação do OMS)

Se você quiser usar a DSC para suas máquinas do Windows usando o WMF 4.0, consulte a seção 'Usando a DSC em ambientes desconectados'

(Uma senha exclusiva é gerada para cada servidor de gerenciamento. Se você for implantar vários servidores de gerenciamento, precisará garantir que a senha correta seja armazenada no arquivo passphrase.txt.)

O Serviço de Mobilidade pode ser instalado por meio da linha de comando e aceita vários argumentos.

É por isso que precisamos ter binários (após extraí-los de nossa configuração) e armazená-los em algum lugar onde possamos recuperá-los usando uma configuração DSC.

## Etapa 1: Extraindo os binários

1. Para extrair os arquivos necessários para esta instalação, navegue até o seguinte diretório no servidor de gerenciamento: **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    Nessa pasta, você deve ver um arquivo MSI denominado:

    **Microsoft-ASR\_UA\_<versão>_Windows\_GA_<data>\_Release.exe**

    Use o seguinte cmdlet para extrair o instalador:

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. Selecione todos os arquivos e envie para uma pasta compactada (zipada).

É isso! Agora, você tem os binários necessários para automatizar a instalação do serviço de mobilidade usando a DSC de Automação do OMS.

### Senha
Em seguida, você precisa determinar onde deseja colocar essa pasta compactada. No meu caso, posteriormente mostrarei que estou usando uma conta de armazenamento no Azure, onde também coloquei a senha necessária para a instalação, portanto, o agente será registrado no servidor de gerenciamento como parte do processo.

A senha que você recebeu ao implantar o servidor de gerenciamento pode ser salva em um arquivo txt, como passphrase.txt.

Eu coloquei a pasta compactada e a senha em um contêiner dedicado em minha conta de armazenamento do Azure

![local da pasta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se você preferir manter esses arquivos em um compartilhamento na rede, poderá fazer isso perfeitamente. Você só precisa garantir que o recurso de DSC que usaremos posteriormente realmente tenha acesso e possa obter a configuração e a senha.

## Etapa 2: Criando a configuração DSC
Minha configuração depende do WMF 5.0, portanto, isso significa que para a máquina aplicar com êxito a configuração por meio da DSC de Automação do OMS, o WMF 5.0 deve estar presente.

A seguinte configuração DSC é usada em meu ambiente:

```
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
```
A configuração fará o seguinte:

- As variáveis informarão a configuração sobre onde obter os binários para o Serviço de Mobilidade, o Agente de VM do Azure e a senha, bem como onde armazenar a saída.
- Importe o xPSDesiredStateConfiguration DscResource para que possamos usar 'xRemoteFile' para baixar os arquivos do repositório.
- Crie um diretório onde deseja armazenar os binários.
- O recurso de Arquivamento extrairá os arquivos da pasta compactada.
- O recurso 'Instalar' do Pacote instalará o Serviço de Mobilidade do instalador UNIFIEDAGENT. EXE com os argumentos específicos (as variáveis que constroem os argumentos precisam ser alteradas para refletir seu ambiente).
- O Pacote 'AzureAgent' instalará o Agente de VM do Azure que é recomendado em todas as VMs executadas no Azure e também torna possível adicionar extensões ao failover de postagem da VM.
- O(s) recurso(s) do Serviço garantirão que os serviços de Mobilidade relacionados e os serviços do Azure estejam sempre em execução.

Salvei a configuração como **ASRMobilityService** em uma pasta em meu computador.

(Lembre-se de substituir o CSIP em sua configuração para refletir o servidor de gerenciamento real, para que o agente seja conectado corretamente, também usando a senha correta).

## Etapa 3: Carregando a DSC de Automação do OMS

Como a configuração DSC que fizemos importará um módulo de recursos DSC necessário (xPSDesiredStateConfiguration), você precisa importar esse módulo na Automação do OMS antes de carregar a configuração DSC.

Faça logon na conta de automação e navegue até AssetsàModules e clique em Procurar Galeria.

Aqui, você pode procurar o módulo e importá-lo para sua conta.

![importar módulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Feito isso, vá até o computador onde os módulos do RM do Azure estão instalados e prossiga para importar a configuração DSC recém-criada.

### Importar cmdlets

No PowerShell, faça logon em sua assinatura do Azure e modifique os cmdlets para refletir seu ambiente.

Primeiro, quero carregar a configuração para a DSC de Automação do OMS usando o seguinte cmdlet:

```
Import-AzureRmAutomationDscConfiguration `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
                                        -Published `
                                        -Description "DSC Config for Mobility Service"
```

Em seguida, é necessário compilar a configuração na DSC de Automação do OMS para podermos começar a registrar os nós.

### Compilar a configuração na DSC de Automação do OMS

Podemos fazer isso executando o seguinte cmdlet:

```
Start-AzureRmAutomationDscCompilationJob `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -ConfigurationName ASRMobilityService
```

Isso pode levar alguns minutos, pois basicamente estamos implantando a configuração para o serviço hospedado de Pull da DSC.

Depois de concluído, você pode recuperar as informações do trabalho usando o PowerShell (Get-AzureRmAutomationDscCompilationJob) ou use portal.azure.com

![Recuperar trabalho](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Agora, publicamos e carregamos com êxito nossa configuração DSC para a DSC de Automação do OMS.

## Etapa 4: Integrando as máquinas para a DSC de Automação do OMS
*Um dos pré-requisitos para concluir esse cenário é que suas máquinas do Windows sejam atualizadas com a última versão do WMF. Você pode baixar e instalar a versão correta para sua plataforma seguindo esta URL: https://www.microsoft.com/download/details.aspx?id=50395*

Agora, criaremos um metaconfig para a DSC que aplicaremos em nossos nós. Para ter êxito com isso, você precisa recuperar a URL do ponto de extremidade e a chave primária de sua conta de Automação selecionada no Azure.

Esses valores podem ser localizados em 'Chaves' na folha 'Todas as configurações' da conta de Automação.

![Valores da chave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Em meu ambiente, tenho um servidor físico do Windows Server 2012 R2 que desejo proteger com a Recuperação de Site do OMS.

Antes de começar a associar o servidor ao ponto de extremidade do DSC de Automação, recomenda-se verificar quaisquer operações de renomeação pendentes no registro, pois isso pode impedir que a instalação seja concluída devido a uma reinicialização pendente.

### Verificar quaisquer operações de renomeação de arquivo pendentes no registro

Execute o seguinte cmdlet para verificar se não há nenhuma reinicialização pendente no servidor:

```
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations
```
Se isso não mostrar nada, você estará OK para continuar. Caso contrário, deverá lidar com isso reinicializando o servidor durante uma janela de manutenção.

Para aplicar a configuração no servidor, inicio o PowerShell ISE e executo o script a seguir. Isso é basicamente uma definição da Configuração Local do DSC que instruirá o mecanismo do gerenciador de configuração local para se registrar no serviço DSC de Automação do OMS e recuperar a configuração específica (ASRMobilityService.localhost)

```
[DSCLocalConfigurationManager()]

Configuration metaconfig
{
    node localhost
    {

        Settings
        {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
                ResourceRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }
                ConfigurationRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
                ConfigurationNames = 'ASRMobilityService.localhost'
            }
                ReportServerWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }

    }
}
metaconfig

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Essa configuração definirá o gerenciador de configuração local para se registrar na DSC de Automação do OMS e instruirá basicamente como o mecanismo deve operar, o que ele deve fazer no caso de um descompasso na configuração (ApplyAndAutoCorrect) e no caso de haver uma reinicialização necessária, continuará com a configuração posteriormente.

Depois de executar isso, o nó deve começar a se registrar na DSC de Automação

![Nó do registro](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se voltarmos para o portal.azure.com, poderemos ver que o nó recém-registrado agora apareceu no portal.

![Nó do registro](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

No servidor, podemos pode executar o seguinte cmdlet do PowerShell para verificar se que ele foi registrado corretamente:

```
Get-DscLocalConfigurationManager
```

Depois da configuração ter sido extraída e aplicada no servidor, você pode verificar isso executando:

```
Get-DscConfigurationStatus
```

A saída mostra que o servidor extraiu com êxito sua configuração:

![Nó do registro](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Além disso, a instalação do Serviço de Mobilidade tem seu próprio log, que pode ser encontrado em ‘<UnidadeSistema>\\ProgramData\\ASRSetupLogs’.

É isso. Agora, implantamos e registramos com êxito o Serviço de Mobilidade no computador que queremos proteger com a Recuperação de Site e podemos contar com a DSC para que os serviços necessários sempre estejam em execução.

![Nó do registro](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Depois que isso foi detectado pelo servidor de gerenciamento, você poderá configurar a proteção e habilitar a replicação na máquina com a Recuperação de Site.

## Usando a DSC em ambientes desconectados

Se suas máquinas não estiverem conectadas à Internet, você ainda poderá contar com a DSC para implantar e configurar o Serviço de Mobilidade nas cargas de trabalho que gostaria de proteger.

Você pode instanciar seu próprio Servidor Pull da DSC no ambiente que fornecerá basicamente os mesmos recursos obtidos na DSC de Automação do OMS – onde os clientes extrairão a configuração assim que registrados no ponto de extremidade da DSC. No entanto, temos outra opção, que é usar o push, onde você está enviando manualmente a configuração DSC para suas máquinas, local ou remotamente.

Observe que, neste exemplo, adicionamos um parâmetro para nomecomputador e os arquivos remotos, agora localizados em um compartilhamento remoto que deve ser acessível pelas máquinas que você deseja proteger. No final do script, executamos a configuração e começamos a aplicar a configuração DSC no computador de destino.

### Pré-requisitos

· Módulo instalado xPSDesiredStateConfiguration do PowerShell

Para as máquinas do Windows onde o WMF 5.0 está instalado, você pode simplesmente instalar o módulo xPSDesiredStateConfiguration executando o seguinte cmdlet no(s) computador(es) de destino:

```
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Você também pode baixar e salvar o módulo, caso precise distribuí-lo para as máquinas do Windows usando o WMF 4.0, executando este cmdlet em um computador onde o PowerShellGet (WMF 5.0) está presente:

```
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Também para o WMF 4.0, verifique se a seguinte atualização está instalada na(s) máquina(s):

https://www.microsoft.com/download/details.aspx?id=40749

A configuração a seguir pode ser enviada para as duas máquinas do Windows com WMF 5.0 e 4.0

```
configuration ASRMobilityService {

    param (
    [Parameter(Mandatory=$true)]
    $computername
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $computername {      

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
ASRMobilityService

Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose
```

Se você quiser criar seu próprio servidor pull da DSC na rede corporativa, para imitar os mesmos recursos que você pode obter na DSC de Automação do OMS, consulte o guia a seguir: https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## Opcional: Implantar a Configuração DSC usando o Modelo do Azure Resource Manager

Neste artigo, até agora estamos focando em como você pode criar sua própria configuração DSC para implantar automaticamente o Serviço de Mobilidade e o Agente de VM do Azure – e assegurar que eles estejam em execução nas máquinas que você gostaria de proteger. Como bônus, também temos um modelo do Azure Resource Manager## ## que implantará essa configuração DSC em uma conta de automação do Azure nova ou existente, criando ativos de automação que conterá as variáveis do ambiente por meio dos parâmetros de entrada no modelo.

Uma vez implantada, você pode simplesmente consultar a Etapa 4 deste guia para integrar suas máquinas.

O modelo fará o seguinte:

· Use uma existente ou crie uma nova conta de Automação do OMS

· Obter parâmetros de entrada para:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


Todas as etapas acima serão executadas na ordem correta para que você comece facilmente a integrar suas máquinas para ter proteção.

O modelo com instruções para a implantação está localizado em:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

Implantar usando o PowerShell:

```
$OMSAutomationRGname = 'KNOMS'

$DSCJobGuid = (New-Guid).Guid

New-AzureRmResourceGroupDeployment `
                                  -Name "DSC3" `
                                  -ResourceGroupName $OMSAutomationRGname `
                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
                                  -OMSAutomationAccountName KNOMSAA `
                                  -ASRRemoteFile "https://knrecstor01.blob.core.windows.net/asr/ASR.zip" `
                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.windows.net/asr/passphrase.txt" `
                                  -ASRCSEndpoint '10.0.0.115' `
                                  -DSCJobGuid $DSCJobGuid `
                                  -Verbose
```

## Próximas etapas:

Depois de implantar os Agentes do Serviço de Mobilidade, você pode continuar a [habilitar a replicação](site-recovery-vmware-to-azure.md#step-6-replicate-applications) para as máquinas virtuais.

<!---HONumber=AcomDC_0810_2016-->