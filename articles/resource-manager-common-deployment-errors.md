<properties
   pageTitle="Erros comuns de implantação do Azure | Microsoft Azure"
   description="Descreve como resolver erros comuns durante a implantação com o Azure Resource Manager"
   services="azure-resource-manager"
   documentationCenter=""
   tags=""
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/24/2016"
   ms.author="tomfitz"/>

# Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager

Este tópico descreve como você pode resolver alguns dos erros comuns que pode encontrar ao implantar recursos no Azure. Para saber mais sobre como solucionar problemas de implantação, consulte [Solucionando problemas de implantações de grupo de recursos](resource-manager-troubleshoot-deployments-portal.md).

É possível evitar alguns erros validando o modelo e os parâmetros antes da implantação. Para obter exemplos de validação de modelo, consulte [Implantar recursos com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

## O nome do recurso já existe

Para alguns recursos, especialmente contas de armazenamento, servidores de banco de dados e sites da Web, você deve fornecer um nome para o recurso que seja exclusivo em todo o Azure. Você pode criar um nome exclusivo concatenando a convenção de nomenclatura com o resultado da função [uniqueString](./resource-group-template-functions/#uniquestring).
 
    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 

## Não é possível localizar o recurso durante a implantação

O Gerenciador de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisa usar o elemento **dependsOn** em seu modelo para criar uma dependência do outro recurso. Por exemplo, ao implantar um aplicativo Web, o plano do Serviço de Aplicativo deve existir. Se você não tiver especificado que o aplicativo Web é dependente do plano do Serviço de Aplicativo, o Gerenciador de Recursos criará os dois recursos ao mesmo tempo. Você receberá um erro informando que o recurso do plano do Serviço de Aplicativo não pode ser encontrado porque ele ainda não existe quando tentar definir uma propriedade no aplicativo Web. Você pode evitar esse erro configurando a dependência no aplicativo Web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## O local não está disponível para o recurso

Ao especificar um local para um recurso, você deve usar um dos locais que dá suporte ao recurso. Antes de inserir um local para um recurso, use um dos comandos a seguir para verificar se o local dá suporte ao tipo de recurso.

### PowerShell

Use **Get-AzureRmResourceProvider** para obter os tipos e locais com suporte para um provedor de recursos específico.

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

Você obterá uma lista dos tipos de recursos para o provedor de recursos.

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Você pode se concentrar nos locais para um determinado tipo de recurso com:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Que retorna os locais com suporte:

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
    
Que retorna os locais com suporte:
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST

Para a API REST, consulte [Obter informações sobre um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Cota excedida

Você pode ter problemas quando a implantação ultrapassar uma cota, que pode ser por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se tentar implantar uma máquina virtual com mais núcleos do que o valor permitido, você receberá um erro informando que a cota foi excedida. Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](./azure-subscription-service-limits.md).

Para examinar as cotas de núcleos da assinatura, você deve usar o comando `azure vm list-usage` na CLI do Azure. O exemplo a seguir ilustra que a cota de núcleo de uma conta de avaliação gratuita é 4:

    azure vm list-usage
    
Que retorna:
    
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

Ou, no PowerShell, você pode usar o cmdlet **Get-AzureRmVMUsage**.

    Get-AzureRmVMUsage
    
Que retorna:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Nesses casos, você deve ir para o portal e abrir um problema de suporte para aumentar a cota para a região na qual você deseja implantar.

> [AZURE.NOTE] Lembre-se de que, para grupos de recursos, a cota é para cada região individual, não para a assinatura inteira. Se você precisar implantar 30 núcleos no Oeste dos EUA, será necessário pedir 30 núcleos do Gerenciador de Recursos no Oeste dos EUA. Se precisar implantar 30 núcleos em qualquer uma das regiões às quais tenha acesso, você deverá solicitar 30 núcleos do Gerenciador de recursos em todas as regiões.


## Falha na autorização

Você pode receber um erro durante a implantação porque a conta ou a entidade de serviço que está tentando implantar os recursos de serviço não tem acesso para executar essas ações. O Azure Active Directory permite que você ou seu administrador controlem quais identidades podem acessar os recursos com um alto grau de precisão. Por exemplo, se sua conta está atribuída à função Leitor, ela não poderá criar novos recursos. Nesse caso, você deve ver uma mensagem de erro indicando que houve falha na autorização.

Para obter mais informações sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

Além de controle de acesso baseado em função, suas ações de implantação podem ser limitadas por políticas na assinatura. Por meio de políticas, o administrador pode impor convenções a todos os recursos implantados na assinatura. Por exemplo, um administrador pode exigir que um valor de marca específico seja fornecido para um tipo de recurso. Se não tiver cumprido os requisitos da política, você receberá um erro durante a implantação. Para obter mais informações sobre políticas, consulte [Usar a política para gerenciar recursos e controlar o acesso](./resource-manager-policy.md).

## Verificar o registro do provedor de recursos

Os recursos são gerenciados por provedores de recursos, e uma conta ou assinatura deve ser registrada para usar um provedor específico. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos.

### PowerShell

Para ver o status do registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Que retorna todos os provedores de recursos disponíveis e o status do registro:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Para registrar um provedor, use **registro AzureRmResourceProvider** e forneça o nome do provedor de recursos que deseja registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Você é solicitado a confirmar o registro e um status é retornado.

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### CLI do Azure

Para ver se o provedor está registrado para uso com a CLI do Azure, use o comando `azure provider list` (a seguir, um exemplo truncado da saída).

    azure provider list
        
Que retorna todos os provedores de recursos disponíveis e o status do registro:
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

Para registrar um provedor de recursos, use o comando `azure provider register` e especifique o *namespace* para registrar.

    azure provider register Microsoft.Cdn

### API REST

Para obter o status do registro, consulte [Obter informações sobre um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Para registrar um provedor, consulte [Registrar uma assinatura com um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

## Erros de extensão de script personalizado

Se você encontrar um erro com uma extensão de script personalizado ao implantar uma máquina virtual, consulte [Solucionando problemas de falhas da extensão da VM do Windows no Azure](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md) ou [Solucionando problemas de falhas de extensões de VM do Linux](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).

## Entenda quando uma implantação está pronta 

O Azure Resource Manager informa o êxito de uma implantação quando todos os provedores são retornados da implantação com êxito. No entanto, isso não significa necessariamente que seu grupo de recursos está "ativo e pronto para seus usuários". Por exemplo, uma implantação pode precisar baixar atualizações, aguardar recursos que não são de modelo ou instalar scripts complexos, ou realizar alguma outra atividade executável sobre a qual o Azure não sabe, sobre porque ela não é uma atividade que um provedor está acompanhando. Nesses casos, pode levar algum tempo antes que os recursos estejam prontos para uso no mundo real. Como resultado, você deve esperar que o status da implantação seja bem-sucedida algum tempo antes que sua implantação possa ser usada.

Você pode impedir o Azure de relatar êxito da implantação, no entanto, ao criar um script personalizado para seu modelo personalizado - usando o [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), por exemplo - que sabe como monitorar toda a implantação para preparação de todo o sistema e retorna com êxito somente quando os usuários podem interagir com toda a implantação. Se você quiser garantir que sua extensão seja a última a ser executada, use a propriedade **dependsOn** em seu modelo.

## Próximas etapas

- Para saber mais sobre ações de auditoria, consulte [Operações de auditoria com o Gerenciador de Recursos](./resource-group-audit.md).
- Para saber mais sobre ações para determinar os erros durante a implantação, consulte [Solução de problemas de implantação de grupo de recursos](./resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0330_2016-->