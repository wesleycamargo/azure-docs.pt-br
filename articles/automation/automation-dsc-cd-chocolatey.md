<properties
   pageTitle="Implantação Contínua de DSC de Automação do Azure com Chocolatey | Microsoft Azure"
   description="Implantação contínua de DevOps usando DSC de Automação do Azure e o gerenciador de pacotes Chocolatey. Exemplo com modelo ARM JSON completo e fonte do PowerShell."
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="golive"/>

# Implantação contínua em VMs de IaaS usando o DSC de Automação do Azure e o Chocolatey

Em um mundo de DevOps, há várias ferramentas para ajudá-lo em vários pontos no pipeline de Integração Contínua. A DSC (Configuração do Estado Desejado) da Automação do Azure é uma nova adição bem-vinda para as opções que podem ser utilizadas por equipes de DevOps. Este artigo demonstra a configuração da CD (Implantação Contínua) para um computador com Windows. Você pode facilmente ampliar a técnica para incluir tantos computadores com Windows quantos forem necessários na função (um site, por exemplo) e também para funções adicionais.

Meta: este artigo foi escrito em setembro de 2015. A versão do Visual Studio é 2015. As [Ferramentas PowerShell para Visual Studio](http://adamdriscoll.github.io/poshtools/) são instaladas e facilitam o trabalho com o PowerShell, adicionando realce de sintaxe e Intellisense. Atualmente, a DSC de Automação do Azure está em Visualização Pública. Portanto, poderão ocorrer alterações quando ela mudar para o status GA. O [código-fonte completo](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) para o exemplo está no GitHub.

![Implantação contínua para VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## Em um alto nível

Há bem pouco acontecendo aqui, mas, felizmente, é possível dividir tudo em dois processos principais:

  - Escrever código e testá-lo e, depois, criar e publicar pacotes de instalação para as versões principais e secundárias do sistema. 
  - Criar e gerenciar VMs que vão instalar e executar o código nos pacotes.  

Depois que esses dois processos principais são realizados, leva pouco tempo para atualizar automaticamente o pacote em execução em qualquer VM específica, à medida que novas versões são criadas e implantadas.

## Visão geral do componente

Gerenciadores de pacotes como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são muito conhecidas no mundo do Linux, mas nem tanto no mundo do Windows. [Chocolatey](https://chocolatey.org/) é um deles, e o [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tópico é uma excelente introdução. Resumindo, o Chocolatey permite instalar pacotes de um repositório central de pacotes em um sistema do Windows usando a linha de comando. Você pode criar e gerenciar seu próprio repositório, e o Chocolatey pode instalar pacotes de qualquer repositório que você designar.

A DSC (Configuração de Estado Desejado) ([visão geral](https://technet.microsoft.com/pt-BR/library/dn249912.aspx)) é uma ferramenta do PowerShell que permite declarar a configuração que você deseja usar para uma máquina. Por exemplo, você pode dizer: "Quero o Chocolatey instalado, quero o IIS instalado, quero a porta 80 aberta, quero a versão 1.0.0 de meu site instalada". O LCM (Gerenciador de configuração Local) da DSC implementa essa configuração. Um Servidor de recepção da DSC mantém um repositório de configurações para seus computadores. O LCM em cada computador verifica periodicamente se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar realinhar o computador com a configuração armazenada. Você pode editar a configuração armazenada no servidor de recepção para alinhar um computador ou um conjunto de computadores com a configuração alterada.

A Automação do Azure é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks e outros ativos, como nós, credenciais e recursos. A DSC de Automação do Azure amplia esse recurso de automação para incluir ferramentas da DSC do PowerShell. Aqui está uma ótima [Visão geral](automation-dsc-overview.md).

Um Recurso DSC é um módulo de código que tem recursos específicos, como gerenciar redes, o Active Directory ou o SQL Server. O Recurso DSC do Chocolatey sabe como acessar um Servidor do NuGet (entre outros), baixar e instalar pacotes e assim por diante. Há muitos outros Recursos DSC na [Galeria do PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Esses módulos são instalados no Servidor de Recepção da DSC de Automação do Azure (por você) para que possam ser usados por suas configurações.

Os modelos ARM fornecem uma maneira declarativa de gerar sua infraestrutura (itens como redes, sub-redes, roteamento e segurança de rede, balanceadores de carga, NICs, VMs e assim por diante). Aqui está uma [artigo](../resource-manager-deployment-model.md) que compara o modelo de implantação ARM (declarativo) ao modelo de implantação do Gerenciamento de Serviço do Azure (ASM ou clássico) (imperativo). Aqui está outro [artigo](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md) sobre os provedores de recursos principais, computação, armazenamento e rede.

Um recurso principal de um modelo ARM é sua capacidade de instalar uma extensão de VM na VM quando ela é provisionada. Uma extensão de VM tem recursos específicos, como executar um script personalizado, instalar software antivírus ou executar um script de configuração de DSC. Há muitos outros tipos de extensões de VM.

## Explicação rápida do diagrama

Começando pela parte superior, você escreve o código, compila e testa e, depois, cria um pacote de instalação. O Chocolatey pode manipular vários tipos de pacotes de instalação, como MSI, MSU e ZIP. Você conta com toda a capacidade do PowerShell para realizar a instalação real, caso os recursos nativos do Chocolatey não sejam suficientes. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Uso uma pasta pública em minha conta de armazenamento de blobs do Azure, mas pode ser qualquer outro lugar. O Chocolatey funciona nativamente com servidores do NuGet e alguns outras para o gerenciamento de metadados de pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. Este exemplo usa o NuGet. Um Nuspec consiste em metadados sobre seus pacotes. O Nuspec é "compilado" no NuPkg e armazenado em um servidor do NuGet. Quando a configuração solicita um pacote por nome e faz referência a um servidor do NuGet, o Recurso DSC do Chocolatey (agora na VM) obtém o pacote e o instala para você. Você também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo do ARM (Gerenciador de Recursos do Azure). Neste exemplo, a extensão de VM registra a VM no Servidor de Recepção de DSC de Automação do Azure (ou seja, um servidor de recepção) como um Nó. A configuração é armazenada no servidor de recepção. Na verdade, ela é armazenada duas vezes: uma vez como texto sem formatação e uma vez compilada como um arquivo MOF (para aqueles que os conhecem.) No portal, o MOF é uma "configuração de nó" (em vez de simplesmente uma "configuração"). É o artefato associado a um nó para que o nó saiba sua configuração. Os detalhes a seguir mostram como atribuir a configuração de nó ao nó.

Provavelmente, você já está realizando a ação na parte superior, ou a maior parte dela. Criar o nuspec, compilá-lo e armazená-lo em um servidor do NuGet é uma tarefa pequena. E você já está gerenciando VMs. O próximo passo para a implantação contínua requer a configuração do servidor de recepção (uma vez), o registro dos nós (uma vez) e a criação e o armazenamento da configuração neles (inicialmente). Em seguida, à medida que os pacotes forem atualizados e implantados no repositório, atualize a configuração no servidor de recepção (repita conforme necessário).

Se você não estiver começando com um modelo do ARM, isso também está OK. Há cmdlets do PowerShell projetados para ajudá-lo a registrar suas VMs no servidor de recepção e todo o restante.


## Etapa 1: configurar o servidor de recepção e a conta de automação

Em uma linha de comando do PowerShell (Add-AzureAccount) autenticada: (pode demorar alguns minutos enquanto o servidor de recepção é configurado)

    Switch-AzureMode -Name AzureResourceManager 
    Register-AzureProvider –ProviderNamespace Microsoft.Automation 
    Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation 
    New-AzureResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Você pode colocar sua conta de automação em qualquer uma das seguintes regiões (também conhecidas como localizações): Leste do Japão, Leste dos EUA 2, Europa Ocidental, Sudeste da Ásia, Centro-Sul dos EUA. Naturalmente, verifique se o grupo de recursos de automação está na mesma região que sua conta de automação.

## Etapa 2: ajustes da extensão de VM para o modelo ARM

Detalhes do registro de VM (usando a extensão de VM de DSC do PowerShell) são fornecidos neste [Modelo de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver). Esta etapa registra sua nova VM no servidor de recepção na lista de Nós DSC.

## Etapa 3: adicionar recursos de DSC necessários para o servidor de recepção

A Galeria do PowerShell é instrumentada para instalar recursos de DSC em sua conta da Automação do Azure. Navegue até o recurso desejado e clique no botão "Implantar na Automação do Azure".

![Exemplo da Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/cchoco.png)

Ou então, há a abordagem manual. A estrutura de pastas de um Módulo de Integração do PowerShell para um computador com o Windows é um pouco diferente da estrutura de pastas esperada pelo Servidor de Recepção de DSC de Automação do Azure. Isso exige que você faça alguns ajustes. Porém, não é difícil, e isso só precisa ser feito uma vez por recurso (a menos que você deseje atualizá-lo no futuro).

-   Instale o módulo necessário na estação de trabalho, da seguinte maneira:
    -   Instale o [Windows Management Framework, v5](http://aka.ms/wmf5latest) 
    -   `Install-Module  –ModuleName MODULENAME` < — captura o módulo da galeria do PowerShell 
-   Copie a pasta de módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` para uma pasta temporária 
-   Exclua os exemplos e a documentação da pasta principal 
-   Compacte a pasta principal, nomeando o arquivo zip exatamente como a pasta 
-   Coloque o arquivo zip em um local http acessível 
-   Execute este PowerShell:

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

O exemplo incluído executa estas etapas para cChoco e xNetworking.

## Etapa 4: adicionar a configuração de nó ao servidor de recepção

Não há nada de especial na primeira vez que você importa a configuração para o servidor de recepção e realiza a compilação. Todas as importações/compilações subsequentes da mesma configuração têm exatamente a mesma aparência. Sempre que atualiza o pacote e precisa enviá-lo para produção, você realiza esta etapa após garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote. Aqui estão o arquivo de configuração e o PowerShell:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   <— node must be named, $AllNodes.NodeName won’t work.
    
            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }
    
            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }
    
            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }
    
            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

    Import-AzureAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Essas etapas resultam em uma nova configuração de nó chamada "ISVBoxConfig.isvbox". O nome é compilado como "configurationName.nodeName".

## Etapa 5: atribuir a configuração de nó ao nó

Para essa etapa, você precisa consultar o servidor de recepção para obter a Id da nova VM depois que ela é registrada. O cmdlet para fazer isso é Get-AzureAutomationDscNode. Ele tem alguns filtros que podem funcionar, mas, no caso geral, convém canalizar a saída por meio de um filtro que vai capturar VMs com um padrão de nome. Se só cria VMs de uma única função, você pode obter todas no local em que o NodeConfigurationName é ConfigureLCMforAAPull.isvbox. Esse é o nome de configuração de qualquer VM recém-registrada, se você seguir esse exemplo. Depois que você obtiver a lista de IDs, aqui está o cmdlet:

    Set-AzureAutomationDscNode ` 
        -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -NodeConfigurationName ISVBoxConfig.isvbox -Id $theId

## Etapa 6: onde colocar todos esses itens do PowerShell?

Quando você escrever modelos personalizados do ARM, há um script do PowerShell fornecido pelo Visual Studio que invoca o modelo. Os cmdlets que importam sua configuração, a compilam, obtêm as ids das VMs e assim por diante podem ser acrescentados a esse script. Consulte o código-fonte para obter um exemplo no GitHub. Para as partes que são executadas sempre que o pacote é atualizado, integre os comandos necessários a seu pipeline de Integração Contínua.

## Etapa 7: criar e manter metadados de pacote

Para cada pacote que você coloca no repositório de pacotes, é necessário um nuspec que o descreve. Esse nuspec deve ser compilado e armazenado em seu servidor do NuGet. Esse processo é descrito [aqui](http://docs.nuget.org/create/creating-and-publishing-a-package). Você pode usar MyGet.org como um servidor do NuGet. Eles vendem este serviço, mas há uma SKU inicial que é gratuita. Em NuGet.org, você encontrará instruções sobre como instalar seu próprio servidor do NuGet para seus pacotes privados.

## Observações

Este exemplo começa com uma VM de uma imagem genérica do Windows 2012 R2 da galeria do Azure. Você poderá iniciá-lo por meio de qualquer imagem armazenada e, em seguida, ajustá-lo com a configuração de DSC.

Você não precisa usar um modelo ARM e a extensão de VM para usar essa técnica com suas VMs. E suas VMs não precisam estar no Azure para estar no gerenciamento de CD. Basta que o Chocolatey seja instalado e o LCM seja configurado na máquina virtual para que ele saiba onde está o servidor de recepção.

É claro que, ao atualizar um pacote em uma VM que está em produção, você precisa colocar a VM fora de rotação enquanto a atualização é instalada. A maneira de fazer isso varia muito. Por exemplo, com uma VM por trás de um Balanceador de Carga do Azure, você pode adicionar uma Sonda Personalizada. Ao atualizar a VM, faça com que o ponto de extremidade da sonda retorne um 400. O ajuste necessário para fazer essa alteração pode ser dentro de sua configuração, da mesma forma como o ajuste para que 200 volte a ser retornado depois que a atualização for concluída.

O código-fonte completo para esse exemplo está [neste projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

<!---HONumber=Oct15_HO3-->