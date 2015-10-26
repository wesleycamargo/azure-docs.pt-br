<properties 
   pageTitle="Integração de máquinas físicas e virtuais para o gerenciamento pelo DSC de Automação do Azure | Microsoft Azure" 
   description="Como configurar computadores para o gerenciamento com o DSC de Automação do Azure" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="10/07/2015"
   ms.author="coreyp"/>

# Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure

## Por que gerenciar máquinas com o DSC de Automação do Azure?

Como a [Configuração de Estado Desejado do PowerShell](https://technet.microsoft.com/library/dn249912.aspx), a Configuração de Estado Desejado da Automação do Azure é um serviço de gerenciamento de configuração simples, mas poderoso, para nós DSC (máquinas físicas e virtuais) em qualquer datacenter de nuvem ou no local. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode facilmente integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios mostrando a conformidade de cada máquina para o estado desejado especificado. A camada de gerenciamento do DSC de Automação do Azure é para o DSC o que a camada de gerenciamento de Automação do Azure é para o script do PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do Powershell, ela também ajuda a gerenciar configurações de DSC. Para saber mais sobre os benefícios de usar o DSC de Automação do Azure, confira [Visão geral do DSC de Automação do Azure](automation-dsc-overview/).

O DSC de Automação do Azure pode ser usado para gerenciar uma variedade de máquinas:

*    Máquinas virtuais do Azure (clássico)
*    Máquinas virtuais do Azure
*    Máquinas físicas / virtuais locais do Windows, ou em uma nuvem diferente do Azure
*    Máquinas físicas / virtuais locais do Linux , no Azure, ou em uma nuvem diferente do Azure

As seções a seguir descrevem como você pode integrar cada tipo de máquina à DSC de Automação do Azure.

## Máquinas virtuais do Azure (clássico)

Com o DSC de Automação do Azure, você pode facilmente carregar máquinas virtuais do Azure (clássico) para gerenciamento de configuração usando o portal do Azure ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com o DSC de Automação do Azure. Como a extensão da Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, as etapas para acompanhar o progresso ou a solução de problemas são fornecidas na seção [Solução de problemas da integração de máquina virtual do Azure](#Troubleshooting-Azure-virtual-machine-onboarding) abaixo.


### Máquinas virtuais do Azure

No [Portal de visualização do Azure](http://portal.azure.com/), clique em **Navegar** -> **Máquinas virtuais (clássico)**. Selecione a VM do Windows que você deseja integrar. Na folha do painel da máquina virtual, clique em **Todas as configurações** -> **Extensões** -> **Adicionar** -> **DSC de Automação do Azure** -> **Criar**. Insira os [Valores do Gerenciador de Configuração Local do DSC do PowerShell](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) necessários para o caso de uso, chave de registro da sua conta de Automação e URL de registro e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


Para localizar a URL de registro e a chave para a conta de Automação para integrar a máquina, confira a seção [Registro seguro](#Secure-registration) abaixo.

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Login-AzureRmAccount
    
    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRMAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRMAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these DSC agent Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
       RegistrationKey = @{
         UserName = 'notused'
         Password = 'PrivateSettingsRef:RegistrationKey'
    }
      RegistrationUrl = $RegistrationInfo.Endpoint
      NodeConfigurationName = $NodeConfigName
      ConfigurationMode = "ApplyAndMonitor"
      ConfigurationModeFrequencyMins = 15
      RefreshFrequencyMins = 30
      RebootNodeIfNeeded = $False
      ActionAfterReboot = "ContinueConfiguration"
      AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension
     -VM $vm
     -Publisher Microsoft.Powershell
     -ExtensionName DSC
     -Version 2.6
     -PublicConfiguration $PublicConfiguration
     -PrivateConfiguration $PrivateConfiguration
     -ForceUpdate

    $VM | Update-AzureVM

## Máquinas virtuais do Azure

O DSC de Automação do Azure permite que você integre facilmente máquinas virtuais do Azure para o gerenciamento de configuração, usando o portal do Azure, os modelos do Gerenciador de Recursos do Azure ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com o DSC de Automação do Azure. Como a extensão da Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, as etapas para acompanhar o progresso ou a solução de problemas são fornecidas na seção [Solução de problemas da integração de máquina virtual do Azure](#Troubleshooting-Azure-virtual-machine-onboarding) abaixo.


### Portal do Azure

No [Portal de visualização do Azure](http://portal.azure.com/), navegue até a conta de Automação do Azure onde você deseja carregar as máquinas virtuais. No painel da conta de Automação, clique em **Nós DSC** -> **Adicionar VM do Azure**.

Em **Selecionar máquinas virtuais para integrar**, selecione uma ou mais máquinas virtuais do Azure para carregar.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


Em **Configurar dados de registro**, digite os [Valores do Gerenciador de Configuração Local do DSC do PowerShell](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) necessários para o caso de uso e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Modelos do Gerenciador de Recursos do Azure

Máquinas virtuais do Azure podem ser implantadas e integradas ao DSC de Automação do Azure por meio de modelos do Gerenciador de Recursos do Azure. Confira [Configurar uma VM por meio da extensão DSC e DSC de Automação do Azure](http://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) para um modelo de exemplo que carrega uma VM existente ao DSC de Automação do Azure. Para localizar a chave de registro e a URL de registro feitas como entrada neste modelo, confira a seção [Registro seguro](#Secure-registration) abaixo.

### PowerShell

O cmdlet [Register-AzureAutomationDscNode](https://msdn.microsoft.com/library/mt244097.aspx?f=255&MSPPError=-2147217396) pode ser usado para carregar máquinas virtuais no portal de visualização do Azure por meio do PowerShell.

#### Máquinas físicas / virtuais locais do Windows, ou em uma nuvem diferente do Azure

As máquinas do Windows locais e as máquinas do Windows em nuvens que não são do Azure (como o Amazon Web Services) também podem ser integradas ao DSC de Automação do Azure, desde que eles tenham acesso de saída à Internet, através de algumas etapas simples:

1. Verifique se a versão mais recente do [WMF 5](http://www.microsoft.com/pt-BR/download/details.aspx?id=48729) está instalada nas máquinas que você deseja carregar ao DSC de Automação do Azure.

2. Abra o console do PowerShell ou o PowerShell ISE como administrador em uma máquina no seu ambiente local. Este computador também deve ter a versão mais recente do WMF 5 instalado.

3. Conecte-se ao Gerenciador de Recursos do Azure usando o módulo Azure PowerShell: `Login-AzureRMAccount`

4. Baixe, da conta de Automação da qual você deseja carregar nós, as metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:

	`Get-AzureRMAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName      		MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop`

5. Opcionalmente, exiba e atualize as metaconfigurações na pasta de saída, conforme necessário para corresponder aos [Campos e valores do Gerenciador de Configuração Local do DSC do PowerShell](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) desejados, se os padrões não coincidirem com o caso de uso.

6. Aplicam-se remotamente às metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

7. Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta de saída da etapa 4 em cada máquina para carregar. Ligue para Set-DscLocalConfigurationManager localmente em cada máquina para carregar.

8. Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

#### Máquinas físicas / virtuais locais do Linux , no Azure, ou em uma nuvem diferente do Azure

Os computadores Linux locais e os computadores Linux em nuvens que não são do Azure também podem ser integrados ao DSC de Automação do Azure, desde que eles tenham acesso de saída à Internet, através de algumas etapas simples:

1. Verifique se a versão mais recente do [Agente Linux do DSC](http://www.microsoft.com/pt-BR/download/details.aspx?id=49150) está instalada nas máquinas que você deseja carregar ao DSC de Automação do Azure.

2. Se os [Padrões do Gerenciador de Configuração Local do DSC do PowerShell](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) corresponder ao caso de uso:

*    Em cada computador Linux para carregar ao DSC de Automação do Azure, use Register.py para carregar usando os padrões do Gerenciador de Configuração Local do DSC do PowerShell:

	`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

*    Para localizar a chave de registro e a URL de registro para a sua conta de Automação, confira a seção [Registro seguro](#Secure-registration) abaixo.

	Se os padrões do Gerenciador de Configuração Local do DSC do PowerShell **não** corresponderem ao caso de uso, siga as etapas de 3 a 9. Caso contrário, vá diretamente para a etapa 9.

3.  Abra o console do PowerShell ou o PowerShell ISE como administrador em um computador Windows no seu ambiente local. Este computador também deve ter a versão mais recente do [WMF 5](http://www.microsoft.com/pt-BR/download/details.aspx?id=48729) instalada.

4.  Conecte-se ao Gerenciador de Recursos do Azure usando o módulo Azure PowerShell:

	`Login-AzureRMAccount`

5.  Baixe, da conta de Automação da qual você deseja carregar nós, as metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:
	
	`Get-AzureRMAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop_`

6.  Opcionalmente, exiba e atualize as metaconfigurações na pasta de saída, conforme necessário para corresponder aos [Campos e valores do Gerenciador de Configuração Local do DSC do PowerShell](http://https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) desejados, se os padrões não coincidirem com o caso de uso.

7.  Aplicam-se remotamente às metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:
    	
    	$SecurePass = ConvertTo-SecureString -string "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurPass
        $Opt = New-CimSessionOption -UseSs1:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential:$Cred -ComputerName:<your Linux machine> -Port:5986 -Authentication:basic -SessionOption:$Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs

8.  Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, para cada computador Linux para carregar, copie a metaconfiguração correspondente a esse computador a partir da pasta na etapa 5 no computador Linux. Em seguida, chame `SetDscLocalConfigurationManager.py` localmente em cada computador Linux desejado para carregar ao DSC de Automação do Azure:

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

9.  Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

## Proteger o registro

Os computadores podem ser carregados com segurança a uma conta de Automação do Azure por meio do protocolo de registro de WMF 5 DSC, que permite a um nó DSC se autenticar a um servidor de Recepção ou de Relatórios do DSC V2 do PowerShell (incluindo o DSC de Automação do Azure). Registra o nó ao servidor em uma **URL de registro**, autenticando usando uma **Chave do registro**. Durante o registro, o nó de DSC e o servidor de Recepção/Relatórios DSC negociam um certificado exclusivo para este nó a ser usado para a autenticação do servidor após o registro. Esse processo impede que nós integrados representem um ao outro, por exemplo, se um nó for comprometido e estiver se comportando de maneira mal-intencionada. Após o registro, a Chave de registro não é usada para autenticação novamente e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro do DSC na folha **Gerenciar chaves** no portal de visualização do Azure. Abra a folha clicando no ícone de chave no painel **Fundamentos** da conta de Automação.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    A URL de registro é o campo de URL na folha Gerenciar chaves.
*    A chave de registro é a Chave de Acesso Primária ou Chave de Acesso Secundária na folha Gerenciar chaves. Qualquer chave pode ser usada.

Para aumentar a segurança, as chaves de acesso primária e secundária de uma conta de Automação podem ser regeneradas a qualquer momento (na folha **Gerenciar chaves**) para impedir que os registros de nós futuros usem chaves anteriores.

## Solução de problemas de integração de máquina virtual do Azure

O DSC de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com o DSC de Automação do Azure. Como a extensão de Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, acompanhar o seu progresso e solucionar os problemas de sua execução pode ser importante.

>[AZURE.NOTE]Qualquer método de integração de uma VM do Azure ao DSC de Automação do Azure que usa a extensão de Configuração de Estado Desejado da VM do Azure pode levar até uma hora para o nó mostrar como registrado na Automação do Azure. Isso se deve à instalação do Windows Management Framework 5.0 na VM pela extensão DSC da VM do Azure, que precisa integrar a VM à DSC da Automação do Azure.

Para solucionar problemas ou exibir o status da extensão de Configuração de Estado Desejado da VM do Azure, no portal de visualização do Azure, navegue para a VM que está sendo integrada, clique em -> **Todas as configurações** -> **Extensões** -> **DSC**. Para obter mais detalhes, você pode clicar em **Exibir o status detalhado**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## Expiração do certificado e um novo registro

Após o registro, cada nó negocia automaticamente um certificado exclusivo para autenticação, que expira depois de um ano. Neste momento, o protocolo de registro DSC do PowerShell não pode renovar automaticamente certificados quando eles estão prestes a expirar, então você precisa registrar novamente os nós após o período de um ano. Antes de registrar novamente, certifique-se de que cada nó está executando o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expirar e o nó não estiver registrado, o nó não será capaz de se comunicar com a Automação do Azure e será marcado como "Sem resposta". O novo registro é feito da mesma maneira como você registrou o nó inicialmente. A realização de um novo registro a 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, vai resultar na geração e uso de um novo certificado.

## Artigos relacionados
* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [cmdlets da DSC de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [preço da DSC de Automação do Azure](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Oct15_HO3-->