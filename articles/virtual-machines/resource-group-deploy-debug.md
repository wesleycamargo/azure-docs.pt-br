<properties
   pageTitle="Solucionando problemas com implantações de grupo de recursos | Microsoft Azure"
   description="Descreve problemas comuns com a implantação de recursos criados usando o modelo de implantação do Gerenciador de Recursos, além de mostrar como detectar e corrigir esses problemas."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="10/14/2015"
   ms.author="tomfitz;rasquill"/>

# Solucionando problemas de implantações de grupos de recursos no Azure

Quando você encontra um problema durante a implantação, é preciso descobrir o que deu errado. O Gerenciador de Recursos fornece duas maneiras de descobrir o que aconteceu e por que isso aconteceu. É possível usar comandos de implantação para recuperar informações sobre implantações específicas de um grupo de recursos. Ou você pode usar os logs de auditoria para recuperar informações sobre todas as operações executadas em um grupo de recursos. Com essas informações, você pode corrigir o problema e retomar as operações em sua solução.

Este tópico se concentra principalmente em como usar os comandos de implantação para solucionar problemas com implantações. Para obter informações sobre como usar os logs de auditoria para acompanhar todas as operações de seus recursos, consulte [Auditar operações com o Gerenciador de Recursos](../resource-group-audit.md).

Este tópico mostra como recuperar informações para solução de problemas por meio do Azure PowerShell, da CLI do Azure e da API REST. Para obter informações sobre como usar o portal de visualização para solucionar problemas de implantações, consulte [Usando o Portal de Visualização do Azure para gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

Também são descritas neste tópico as soluções para erros comuns que os usuários encontram.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássica. Não é possível criar grupos de recursos com o modelo de implantação clássica.


## Solucionar problemas com o PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Você pode obter o status geral de uma implantação com o comando **Get-AzureRmResourceGroupDeployment**. No exemplo abaixo, a implantação falhou.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

Geralmente, cada implantação é composta por várias operações, e cada operação representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, geralmente você precisa ver os detalhes sobre as operações de implantação. É possível ver o status das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

O comando mostra duas operações na implantação. O estado de provisionamento de uma é "com falha (Failed)" e da outra é "êxito (Succeeded)".

Você pode recuperar a mensagem de status com o seguinte comando:

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Solucionar problemas com a CLI do Azure

Você pode obter o status geral de uma implantação com o comando **azure group deployment show**. No exemplo abaixo, a implantação falhou.

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


Você pode encontrar mais informações sobre por que a implantação falhou nos logs de auditoria. Para ver os logs de auditoria, execute o comando **azure group log show**. É possível incluir a opção **--last-deployment** para recuperar apenas o log da implantação mais recente.

    azure group log show ExampleGroup --last-deployment

O comando **azure group log show** pode retornar muitas informações. Na solução de problemas, normalmente você se concentra nas operações que falharam. O script a seguir usa a opção **--json** e **jq** para pesquisar o log em busca de falhas na implantação. Para saber mais sobre ferramentas, como **jq**, consulte [Ferramentas úteis para interagir com o Azure](#useful-tools-to-interact-with-azure)

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name 
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

Você pode ver que **properties** inclui informações em json sobre a operação que falhou.

Você pode usar as opções **-verbose** e **- vv** para ver mais informações dos logs. Use a opção **-verbose** para exibir as etapas das operações em `stdout`. Para ver um histórico completo de solicitações, use a opção **- vv**. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

## Solucionar problemas com a API REST

A API REST do Gerenciador de Recurso fornece URIs para recuperar informações sobre uma implantação, operações da implantação e detalhes sobre uma operação específica. Para obter descrições completas desses comandos, consulte:

- [Obter informações sobre uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Listar todas as operações de implantação de modelo](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Obter informações sobre uma operação de implantação de modelo](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Atualizando credenciais expiradas

A implantação falhará se suas credenciais do Azure tiverem expirado ou se você não tiver entrado em sua conta do Azure. Suas credenciais poderão expirar se a sessão ficar aberta por muito tempo. Você pode atualizar as credenciais com as seguintes opções:

- Para o PowerShell, use o cmdlet **AzureRmAccount Login** (ou **Add-AzureAccount** para versões do PowerShell antes da visualização 1.0). As credenciais em um arquivo de configurações de publicação não são suficientes para os cmdlets no módulo AzureResourceManager.
- Com a CLI do Azure, use **azure login**. Para obter ajuda com erros de autenticação, certifique-se de que você tenha [configurado a CLI do Azure corretamente](../xplat-cli-connect.md).

## Verificando o formato dos modelos e parâmetros

Se o arquivo de modelo ou parâmetro não estiver no formato correto, a implantação falhará. Antes de executar uma implantação, você pode testar a validade do modelo e dos parâmetros.

### PowerShell

Para o PowerShell, use **Test-AzureRmResourceGroupDeployment** (ou **Test-AzureResourceGroupTemplate** para versões do PowerShell antes da visualização 1.0).

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### CLI do Azure

Na CLI do Azure, use **azure group template validate <resource group>**

O exemplo a seguir mostra como validar um modelo e os parâmetros necessários. A CLI do Azure solicita os valores de parâmetro que são necessários.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### API REST

Para a API REST, consulte [Validar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Verificando quais locais dão suporte ao recurso

Ao especificar um local para um recurso, você deve usar um dos locais que dá suporte ao recurso. Antes de inserir um local para um recurso, use um dos comandos a seguir para verificar se o local dá suporte ao tipo de recurso.

### PowerShell

Para versões do PowerShell antes da visualização 1.0, você pode ver a lista completa de recursos e locais usando o comando **Get-AzureLocation**.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

É possível especificar um tipo específico de recuso com:

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

Para visualização do PowerShell 1.0, use **Get-AzureRmResourceProvider** para obter os locais com suporte.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

É possível especificar um tipo específico de recuso com:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### CLI do Azure

Para a CLI do Azure, você pode usar a **lista local do Azure**. Como a lista de locais pode ser longa e existem muitos provedores, você pode usar ferramentas para examinar os provedores e locais antes de usar um local que ainda não está disponível. O script a seguir usa **jq** para descobrir os locais nos quais está disponível o provedor de recursos para máquinas virtuais do Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST
        
Para a API REST, consulte [Obter informações sobre um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Criando nomes de recurso exclusivos

Para alguns recursos, especialmente contas de armazenamento, servidores de banco de dados e sites da Web, você deve fornecer um nome para o recurso que seja exclusivo em todo o Azure. Atualmente, não há nenhuma maneira de testar se um nome é exclusivo. É recomendável usar uma convenção de nomenclatura que seja improvável de ser usada por outras organizações.

## Problemas de autenticação, assinatura, função e cota

Pode haver um ou mais dos diversos problemas impedindo a implantação bem-sucedida envolvendo o Active Directory do Azure, a autenticação e autorização. Independentemente de como gerenciar os grupos de recursos do Azure, a identidade que você usa para entrar em sua conta deve ser um objeto do Active Directory do Azure. Essa identidade pode ser um trabalho ou conta de escola que você criou ou foi atribuída a você, ou você pode criar uma Entidade de Serviço para aplicativos.

Mas o Active Directory do Azure permite que você ou seu administrador controlem quais identidades podem acessar os recursos com um alto grau de precisão. Se suas implantações estiverem falhando, examine as próprias solicitações em busca de sinais de problemas de autenticação ou autorização, bem como os logs de implantação de seu grupo de recursos. Você pode descobrir que embora tenha permissões para alguns recursos, não tem permissões para outros. Usando a CLI do Azure, você pode examinar locatários e usuários do Active Directory do Azure que usam os comandos `azure ad`. (Para ver uma lista completa de comandos da CLI do Azure, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure](azure-cli-arm-commands.md).)

Você também pode ter problemas quando uma implantação atinge uma cota padrão, que pode ser por grupo de recursos, assinaturas, contas e outros escopos. Para sua satisfação, certifique-se de que você tenha os recursos disponíveis para a implantação correta. Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../azure-subscription-service-limits.md).

Para examinar suas próprias cotas de assinatura de núcleos, você deve usar o comando `azure vm list-usage` na CLI do Azure e o cmdlet **Get-AzureVMUsage** no PowerShell. A seguir, é mostrado o comando na CLI do Azure e ilustrado que a cota de núcleo de uma conta de avaliação gratuita é 4:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se você tentar implantar um modelo que crie mais de 4 núcleos para a região Oeste dos EUA na assinatura acima, obterá um erro de implantação que pode ser semelhante ao seguinte (no portal ou investigando os logs de implantação):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

Nesses casos, você deve ir para o portal e abrir um problema de suporte para aumentar a cota para a região na qual você deseja implantar.

> [AZURE.NOTE]Lembre-se de que, para grupos de recursos, a cota é para cada região individual, não para a assinatura inteira. Se você precisar implantar 30 núcleos no Oeste dos EUA, será necessário pedir 30 núcleos do Gerenciador de Recursos no Oeste dos EUA. Se precisar implantar 30 núcleos em qualquer uma das regiões às quais tenha acesso, você deverá solicitar 30 núcleos do Gerenciador de recursos em todas as regiões. <!-- --> Para ser específico sobre núcleos, por exemplo, você pode verificar as regiões para as quais deve solicitar o valor da cota apropriado usando o comando a seguir, que resulta em **jq** para análise json. <!-- --> azure provider show Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }


## Verificando o registro do provedor de recursos

Os recursos são gerenciados por provedores de recursos, e uma conta ou assinatura pode ser habilitada para usar um provedor específico. Se você estiver habilitado para usar um provedor, ele também deve ser registrado para uso. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos.

### PowerShell

Para obter uma lista de provedores de recursos e o status do registro, use **Get-AzureProvider** para versões do PowerShell antes da visualização 1.0.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Para registrar um provedor, use **Register-AzureProvider**.

Para visualização do PowerShell 1.0, use **Get-AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Para registrar um provedor, use **Register-AzureRmResourceProvider**.

### CLI do Azure

Para ver se o provedor está registrado para uso com a CLI do Azure, use o comando `azure provider list` (a seguir, um exemplo truncado da saída).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Repetindo, se quiser mais informações sobre provedores, incluindo sua disponibilidade regional, digite `azure provider list --json`. O seguinte seleciona somente o primeiro na lista para exibir:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

Se um provedor requer o registro, use o comando `azure provider register <namespace>`, em que o valor *namespace* é proveniente da lista anterior.

### API REST

Para obter o status do registro, consulte [Obter informações sobre um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Para registrar um provedor, consulte [Registrar uma assinatura com um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Noções básicas sobre quando uma implantação é bem-sucedida para modelos personalizados

Se você estiver usando modelos que criou, é importante entender que o sistema do Gerenciador de Recursos do Azure relata êxito em uma implantação quando todos os provedores retornam da implantação com êxito. Isso significa que todos os itens de modelo foram implantados para seu uso.

No entanto, observe que isso não significa necessariamente que seu grupo de recursos está "ativo e pronto para seus usuários". Por exemplo, a maioria das implantações solicita que implantação para baixar atualizações, aguarde por outros recursos que não são de modelo ou instale scripts complexos, ou realize alguma outra atividade executável sobre a qual o Azure não sabe, sobre porque ela não é uma atividade que um provedor está rastreando. Nesses casos, pode levar algum tempo antes que os recursos estejam prontos para uso no mundo real. Como resultado, você deve esperar que o status da implantação seja bem-sucedida algum tempo antes que sua implantação possa ser usada.

Você pode impedir o Azure de relatar êxito da implantação, no entanto, ao criar um script personalizado para seu modelo personalizado - usando o [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), por exemplo - que sabe como monitorar toda a implantação para preparação de todo o sistema e retorna com êxito somente quando os usuários podem interagir com toda a implantação. Se você quiser garantir que sua extensão seja a última a ser executada, use a propriedade **dependsOn** em seu modelo. Um exemplo pode ser visto [aqui](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Ferramentas úteis para interagir com o Azure
Ao trabalhar com recursos do Azure na linha de comando, você coletará ferramentas que lhe ajudam a fazer seu trabalho. Os modelos de grupo de recursos do Azure são documentos JSON, e a API do Gerenciador de Recursos do Azure aceita e retorna JSON. Sendo assim, ferramentas de análise de JSON são algumas das primeiras coisas que ajudarão você a navegar pelas informações sobre seus recursos, bem como a criar ou interagir com modelos e arquivos de parâmetro de modelo.

### Ferramentas do Windows, Linux e Mac
Se você usa a Interface de Linha de Comando do Azure para Mac, Linux e Windows, provavelmente está familiarizado com ferramentas padrão para download como **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)** ou **[Resty](https://github.com/beders/Resty)**, e os utilitários JSON como **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** e as bibliotecas de linguagem que lidam bem com o JSON. (Muitas dessas ferramentas também têm portas para o Windows, como o [wget](http://gnuwin32.sourceforge.net/packages/wget.htm); na verdade, existem várias maneiras de obter o Linux e outras ferramentas de software livre em execução também no Windows.)

Este tópico inclui alguns comandos da CLI do Azure que você pode usar com **jq** para obter exatamente as informações que deseja, com mais eficiência. Você deve escolher o conjunto de ferramentas com as quais você já está acostumado para ajudá-lo a entender o uso de recursos do Azure.

### PowerShell

O PowerShell tem vários comandos básicos para executar os mesmos procedimentos.

- Use o cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** para baixar arquivos como modelos de grupo de recursos ou parâmetros JSON.
- Use o cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** para converter uma cadeia de caracteres JSON em um objeto personalizado ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)), que tem uma propriedade para cada campo na cadeia de caracteres JSON.


## Próximas etapas

Para dominar a criação de modelos, leia [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md) e navegue pelo [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) para obter exemplos implantáveis. Um exemplo da propriedade **dependsOn** é [Criar uma máquina virtual com várias NICs e RDP acessíveis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=Oct15_HO3-->