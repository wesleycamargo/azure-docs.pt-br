<properties
   pageTitle="Solucionando problemas de implantações de grupos de recursos no Azure"
   description="Descreve os problemas comuns de implantação de recursos no Azure e mostra como usar o Portal do Azure, a CLI do azure (Interface de linha de comando do Azure para Mac, Linux e Windows) e o PowerShell para examinar as implantações e detectar problemas."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Solucionando problemas de implantações de grupos de recursos no Azure

É muito mais fácil evitar erros de implantação verificando algumas coisas com antecedência, mas implantações às vezes falham por várias razões diferentes. Este documento descreve ferramentas e operações para evitar erros simples, baixar arquivos de modelo e examinar logs de implantação. Ele também aborda as principais áreas de pensar ao examinar os logs de implantação para falhas.

## Ferramentas úteis para interagir com o Azure
O módulo AzureResourceManager inclui cmdlets que, ao trabalhar com seus recursos do Azure na linha de comando, permitem a você a coletar ferramentas que ajudam você a fazer seu trabalho. Modelos de grupo de recursos do Azure são documentos JSON, e a API de gerenciamento de recursos do Azure aceita e retorna JSON, portanto, ferramentas de análise de JSON são algumas das primeiras coisas que você usará para ajudá-lo a navegar pelas informações sobre seus recursos, bem como o criar ou interagir com modelos e arquivos de parâmetro de modelo.

### Ferramentas do Windows, Linux e Mac
Se você usa a Interface de linha de comando do Azure para Mac, Linux e Windows, provavelmente está familiarizado com ferramentas padrão para download como **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)** ou **[Resty](https://github.com/beders/Resty)**, e os utilitários JSON como **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** e as bibliotecas de linguagem que lidam bem com o JSON. \(Muitas dessas ferramentas também têm portas para o Windows, como o [wget](http://gnuwin32.sourceforge.net/packages/wget.htm); na verdade, existem várias maneiras de obter o Linux e outras ferramentas de software de código-fonte aberto em execução também no Windows.\)

Este tópico inclui alguns comandos da CLI do Azure que você pode usar com **jq** para obter exatamente as informações que deseja, com mais eficiência. Você deve escolher o conjunto de ferramentas com as quais você já está confortável para ajudá-lo a entender o uso de recursos do Azure.

### Windows PowerShell

O Windows PowerShell ten vários comandos básicos para executar os mesmos procedimentos.

- Use o cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** para baixar arquivos como modelos de grupo de recursos ou parâmetros JSON.
- Use o cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** para converter uma cadeia de caracteres JSON em um objeto personalizado \([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)\), que tem uma propriedade para cada campo na cadeia de caracteres JSON.

## Evitando erros na CLI do Azure para Mac, Linux e Windows

A CLI do Azure tem vários comandos para ajudar a evitar erros e detectar o que deu errado quando fazem isso.

- **azure location list**. Esse comando obtém os locais que dão suporte a cada tipo de recurso, como o provedor para máquinas virtuais. Antes de inserir um local para um recurso, use esse cmdlet para verificar se o local dá suporte ao tipo de recurso.

    Como a lista de locais pode ser longa e existem muitos provedores, você pode usar ferramentas para examinar os provedores e locais antes de usar um local que ainda não está disponível. O script a seguir usa **jq** para descobrir os locais onde está disponível o provedor de recursos para máquinas virtuais do Azure. \(\)

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. Este comando valida seu modelo e o parâmetro do modelo antes de você usá-los. Digite um modelo personalizado ou da galeria e os valores dos parâmetros do modelo que você pretende usar. Este cmdlet testa se o modelo é internamente consistente e se o conjunto de valores do parâmetro corresponde ao modelo.

    O exemplo a seguir mostra como validar um modelo e quaisquer parâmetros necessários; a CLI do Azure solicita os valores de parâmetro que são necessários.

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

## Obtendo informações para corrigir problemas de implantação da CLI do Azure

- **azure group log show <resource group>**: este comando obtém as entradas no log para cada implantação do grupo de recursos. Se algo der errado, comece examinando os logs de implantação.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

        Use the **--last-deployment** option to retrieve only the log for the most recent deployment. The following script uses the **--json** option and **jq** to search the log for deployment failures.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **Opções --verbose e -vv**: use a opção **--verbose** para definir o modo para verbose, para exibir as etapas que as operações devem seguir em `stdout`. Para um histórico completo de solicitações incluindo as etapas que **--verbose** habilita, use a opção **-vv**. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

- **Suas credenciais do Azure não foram configuradas ou expiraram**: para atualizar as credenciais em sua sessão da CLI do Azure, simplesmente digite `azure login`. Para obter ajuda com erros de autenticação, certifique-se de que você tenha [configurado a CLI do Azure corretamente](xplat-cli-connect.md).

## Evitando erros no Windows PowerShell

O módulo AzureResourceManager inclui cmdlets que ajudam você a evitar erros.


- **Get-AzureLocation**: este cmdlet obtém os locais que dão suporte a cada tipo de recurso. Antes de inserir um local para um recurso, use esse cmdlet para verificar se o local oferece suporte ao tipo de recurso.


- **Test-AzureResourceGroupTemplate**: teste o modelo e o parâmetro do modelo antes de usá-los. Digite um modelo personalizado ou da galeria e os valores dos parâmetros do modelo que você pretende usar. Este cmdlet testa se o modelo é internamente consistente e se o conjunto de valores do parâmetro corresponde ao modelo.

## Obtendo informações para corrigir problemas de implantação no Windows PowerShell

- **Get-AzureResourceGroupLog**: este cmdlet obtém as entradas no log para cada implantação do grupo de recursos. Se algo der errado, comece examinando os logs de implantação.

- **Verbose e Debug**: os cmdlets no módulo AzureResourceManager chamam APIs REST que executam o trabalho real. Para ver as mensagens que as APIs retornam, defina a variável $DebugPreference como "Continue" e use o parâmetro comum Verbose em seus comandos. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

- **Suas credenciais do Azure não foram configuradas ou expiraram**: para atualizar as credenciais em sua sessão do Windows PowerShell, use o cmdlet Add-AzureAccount. As credenciais em um arquivo de configurações de publicação não são suficientes para os cmdlets no módulo AzureResourceManager.

## Autenticação, assinatura, função e problemas de cota

Pode haver um ou mais dos diversos problemas impedindo a implantação bem-sucedida envolvendo o Active Directory do Azure, a autenticação e autorização. Independentemente de como você gerencia os grupos de recursos do Azure, a identidade que você usa para fazer logon em sua conta deve ser objetos do Active Directory do Azure ou entidades de serviço, o que também é chamado de contas de trabalho ou escola, ou IDs organizacionais.

Mas o Active Directory do Azure permite que você ou seu administrador controlem quais identidades podem acessar os recursos com um alto grau de precisão. Se suas implantações estão falhando, examine as próprias solicitações em busca de sinais de autenticação ou problemas de autorização, examinando também os logs de implantação de seu grupo de recursos. Você pode achar que embora você tenha permissões para alguns recursos, não tem permissões para outros. Usando a CLI do Azure, você pode examinar locatários e usuários do Active Directory do Azure que usam os comandos `azure ad`. \(Para uma lista completa de comandos da CLI do Azure, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](azure-cli-arm-commands.md).\)

Você também pode ter problemas quando uma implantação atinge uma cota padrão, que pode ser por grupo de recursos, assinaturas, contas, bem como outros escopos. Confirme que você tenha os recursos disponíveis para implantar corretamente, conforme considerar adequado. Para obter informações completas sobre as cotas, consulte [Assinatura do Azure e limites de serviço, cotas e restrições](azure-subscription-service-limits.md).


## Problemas de modo PowerShell e CLI do Azure

Você pode ter a experiência de que recursos do Azure implantados usando a API de gerenciamento de serviço ou usando o portal clássico não estejam visíveis usando a API de gerenciamento de recursos ou o portal do Azure. É importante gerenciar recursos usando a mesma API de gerenciamento ou portal que você usou para criá-los. Se um recurso desapareceu, verifique se ele está disponível usando a outra API de gerenciamento ou portal.

## Problemas de registro de provedor de recursos do Azure

Os recursos são gerenciados por provedores de recursos, e uma conta ou assinatura pode ser habilitada para usar um provedor específico. Se você estiver habilitado para usar um provedor, ele também deve ser registrado para uso. A maioria dos provedores são automaticamente registrados pelo Portal do Azure ou a interface de linha de comando que você está usando, mas não todos.

Para ver se o provedor está registrado para uso com a CLI do Azure, use o comando `azure provider list` \(a seguir, um exemplo truncado da saída\).

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

    Again, if you want more information about providers, including their regional availability, type `azure provider list --json`. The following selects only the first one in the list to view:

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

## Noções básicas sobre quando uma implantação é bem-sucedida para modelos personalizados

Se você estiver usando modelos que criou, é importante entender que o sistema de gerenciamento de recursos do Azure relata com êxito em uma implantação quando todos os provedores retornam da implantação com êxito. Isso significa que todos os itens de modelo foram implantados para seu uso.

No entanto, isso não significa necessariamente que o grupo de recursos esteja **ativo e pronto para seus usuários**. Por exemplo, a maioria das implantações solicita que implantação para baixar atualizações, aguarde por outros recursos que não são de modelo ou instale scripts complexos, ou realize alguma outra atividade executável sobre a qual o Azure não sabe, sobre porque ela não é uma atividade que um provedor está rastreando. Nesses casos, pode levar algum tempo antes que os recursos estejam prontos para uso no mundo real. Como resultado, você deve esperar que o status da implantação seja bem-sucedida algum tempo antes que sua implantação possa ser usada.

Você pode impedir o Azure de relatar êxito da implantação, no entanto, ao criar um script personalizado para seu modelo personalizado - usando o [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), por exemplo - que sabe como monitorar toda a implantação para preparação de todo o sistema e retorna com êxito somente quando os usuários podem interagir com toda a implantação. Se você quiser garantir que sua extensão seja a última a ser executada, use a propriedade **dependsOn** em seu modelo. Um exemplo pode ser visto [aqui](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Mesclar modelos

Às vezes, você pode precisar mesclar dois modelos, ou talvez você precise iniciar um modelo filho em um modelo pai. Isso pode ser feito com o uso de um recurso de implantação no modelo mestre para implantar um modelo filho.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Navegando entre grupos de recursos

Muitas vezes, convém usar um recurso de fora do grupo de recursos atual, onde um modelo está sendo implantado. O caso mais comum para esse comportamento é o uso de uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. Isso geralmente é necessário para que a exclusão do grupo de recursos que contém as máquinas virtuais não resulte na exclusão de blobs vhd ou um de uma VNet que é usada por vários grupos de recursos. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }



## Próximas etapas

Para dominar a criação de modelos, leia [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md), e navegue pelo [Repositório do AzureRMTemplates](https://github.com/azurermtemplates/azurermtemplates) para obter exemplos de implantação. Um exemplo da propriedade **dependsOn** é o [Modelo de balanceador de carga com regra de NAT de entrada](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!--HONumber=52-->
