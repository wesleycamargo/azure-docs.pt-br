<properties 
 pageTitle="Gerenciar extensões de máquina virtual | Microsoft Azure" 
 description="Descreve como adicionar, localizar, atualizar e remover extensões." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="squillace" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="03/10/2015" 
 ms.author="rasquill"/>
#Gerenciar extensões de máquina virtual
Descreve como localizar, adicionar, modificar ou remover extensões de VM com máquinas virtuais Windows ou Linux no Azure.

##Usando extensões de VM

As Extensões de VM do Azure implementam comportamentos ou recursos que ajudam outros programas a funcionarem em VMs do Azure (por exemplo, a extensão **WebDeployForVSDevTest** permite o Visual Studio para soluções de implantação da Web em sua VM do Azure) ou fornecer a capacidade de interagir com a máquina virtual para dar suporte a alguns outros comportamentos (por exemplo, você pode usar as extensões de acesso da máquina virtual do Powershell, o xplat-cli e clientes REST para redefinir ou modificar os valores de acesso remoto na sua VM do Azure).

>[AZURE.IMPORTANT]Para obter uma lista completa das extensões pelos recursos que oferecem suporte, consulte [Extensões de VM do Azure e recursos](https://msdn.microsoft.com/library/dn606311.aspx). Como cada extensão de VM dá suporte a um recurso específico, exatamente o que você pode e não pode fazer com uma extensão depende da extensão. Portanto, antes de modificar a sua VM, verifique se que você leu a documentação para a extensão de VM que deseja usar. Não há suporte para remover algumas extensões de VM; outras têm propriedades que podem ser definidas e que alteram radicalmente o comportamento da VM.

As tarefas mais comuns são:

1.  Localizando extensões disponíveis
    
2.  Atualizando extensões carregadas

3.  Adicionando extensões

4.  Removendo extensões

##Localizar extensões disponíveis

As Extensões de VM do Azure são (para obter uma lista completa das extensões pelos recursos que oferecem suporte, consulte [Extensões de VM do Azure e recursos](https://msdn.microsoft.com/library/dn606311.aspx).) Você pode localizar a extensão e as informações estendidas usando:

-   PowerShell
-   Interface de plataforma cruzada do Azure (xplat-cli)
-   API REST de gerenciamento de serviço

quaisquer cmdlets [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx) ou [APIs REST do Gerenciamento de serviço](https://msdn.microsoft.com/library/ee460799.aspx) para localizar informações sobre extensões disponíveis.

###PowerShell do Azure

Algumas extensões têm cmdlets do Powershell que são específicos delas, que podem facilitar suas configurações do PowerShell; mas os seguintes cmdlets funcionam para todas as extensões de VM.

Você pode usar os cmdlets a seguir para obter informações sobre as extensões disponíveis:

-   Para instâncias de funções web ou funções de trabalho, você pode usar o cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx).
-   Para instâncias de máquinas virtuais, você pode usar o cmdlet [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx).

     Por exemplo, o exemplo de código a seguir mostra como listar as informações para a extensão **IaaSDiagnostics** usando o PowerShell.
    
        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Interface de linha de comando do Azure (xplat-cli)

Algumas extensões têm os comandos xplat-cli que são específicos a eles (a extensão de VM Docker é um exemplo), que podem facilitar suas configurações; mas os comandos a seguir funcionam para todas as extensões de VM.

Você pode usar o comando **lista de extensão de VM do Azure** para obter informações sobre as extensões disponíveis e usar a opção **–-json** para exibir todas as informações disponíveis sobre uma ou mais extensões. Se você não usar um nome de extensão, o comando retorna uma descrição json de todas as extensões disponíveis.

Por exemplo, o exemplo de código a seguir mostra como listar as informações para a extensão **IaaSDiagnostics** usando o comando xplat-cli **lista de extensão de VM do Azure** e usa a opção **–-json** para retornar informações completas.


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###API REST do Gerenciamento de Serviços

Você pode usar as APIs REST a seguir para obter informações sobre as extensões disponíveis:

-   Para instâncias de funções web ou funções de trabalho, você pode usar a operação [Listar extensões disponíveis](https://msdn.microsoft.com/library/dn169559.aspx). Para listar as versões de extensões disponíveis, você pode usar [Listar versões da extensão](https://msdn.microsoft.com/library/dn495437.aspx).

-   Para instâncias de máquinas virtuais, você pode usar a operação [Listar extensões de recurso](https://msdn.microsoft.com/library/dn495441.aspx). Para listar as versões de extensões disponíveis, você pode usar [Listar versões da extensão de recurso](https://msdn.microsoft.com/library/dn495440.aspx).

##Adicionar, atualizar ou desabilitar extensões

As extensões podem ser adicionadas quando uma instância for criada ou podem ser adicionadas a uma instância em execução. As Extensões podem ser atualizadas, desabilitadas ou removidas. Você pode executar essas ações usando cmdlets do Azure PowerShell ou usando as operações de API de REST do Gerenciamento de serviço. São necessários parâmetros para instalar e configurar algumas extensões. Os parâmetros públicos e privados têm suporte para extensões.


###PowerShell do Azure

Usar cmdlets do Azure PowerShell é a maneira mais fácil de adicionar e atualizar extensões. Quando você usa os cmdlets de extensão, a maioria da configuração da extensão é feita para você. Às vezes, talvez seja necessário adicionar programaticamente uma extensão. Quando precisar fazer isso, você deverá fornecer a configuração da extensão.

Você pode usar os cmdlets a seguir para saber se uma extensão requer uma configuração de parâmetros públicos e privados:

-   Para instâncias de funções web ou funções de trabalho, você pode usar o cmdlet **Get-AzureServiceAvailableExtension**.

-   Para instâncias de máquinas virtuais, você pode usar o cmdlet **Get-AzureVMAvailableExtension**.

###API REST do Gerenciamento de Serviços

Ao recuperar uma lista de extensões disponíveis usando as APIs REST, você receberá informações sobre como a extensão deve ser configurada. As informações retornadas podem mostrar informações de parâmetro representadas por um esquema público e um esquema privado. Os valores de parâmetros públicos são retornados em consultas sobre as instâncias. Os valores dos parâmetros privados não são retornados.

Você pode usar as APIs REST a seguir para saber se uma extensão requer uma configuração de parâmetros públicos e privados:

-   Para instâncias de funções da web ou funções de trabalho, os elementos **PublicConfigurationSchema** e **PrivateConfigurationSchema** contêm as informações na resposta da operação [Listar extensões disponíveis](https://msdn.microsoft.com/library/dn169559.aspx).

-   Para instâncias de Máquinas Virtuais, os elementos **PublicConfigurationSchema** e **PrivateConfigurationSchema** contêm as informações na resposta da operação [Listar extensões de recurso](https://msdn.microsoft.com/library/dn495441.aspx).

>[AZURE.NOTE]As extensões também podem usar as configurações que são definidas com JSON. Quando esses tipos de extensões são usados, apenas o elemento **SampleConfig** é usado.

<!---HONumber=August15_HO7-->