<properties
   pageTitle="Solução de erros comuns de implantação do Azure | Microsoft Azure"
   description="Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="erro de implantação, implantação do azure, implante no azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/06/2016"
   ms.author="tomfitz"/>

# Solução de erros comuns de implantação do Azure com o Azure Resource Manager

Este tópico descreve como é possível resolver alguns erros de implantação comuns do Azure que você pode encontrar. Se você precisa de mais informações sobre o que deu errado com sua implantação, primeiro confira [Exibir operações de implantação](resource-manager-troubleshoot-deployments-portal.md) e depois volte a este artigo para obter ajuda sobre como resolver o erro.

## Modelo ou recurso inválido

Se você receber um erro informando que o modelo ou uma propriedade em um recurso é inválida, isso possivelmente indicará a ausência de um caractere em seu modelo. Esse erro é fácil de cometer ao usar expressões de modelo porque a expressão fica entre aspas, então o JSON valida-a mesmo assim e seu editor pode não detectar o erro. Por exemplo, a atribuição de nome a seguir para uma conta de armazenamento contém um par de colchetes, três funções, três pares de parênteses, um par de aspas simples e uma propriedade:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se você não fornecer toda a sintaxe correspondente, o modelo produzirá um valor muito diferente de sua intenção.

Dependendo da localização do caractere ausente em seu modelo, você receberá um erro informando que o modelo ou então que um recurso é inválido. O erro também pode indicar que o processo de implantação não pôde processar a expressão de linguagem de modelo. Quando você receber esse tipo de erro, examine cuidadosamente a sintaxe da expressão.

## O nome do recurso já existe ou já está sendo usado por outro recurso

Para alguns recursos, especialmente as contas de armazenamento, servidores do banco de dados e sites da Web, você deve fornecer um nome para o recurso que seja exclusivo em todo o Azure. Você pode criar um nome exclusivo concatenando a convenção de nomenclatura com o resultado da função [uniqueString](resource-group-template-functions.md#uniquestring).

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

## Não é possível localizar o recurso durante a implantação

O Gerenciador de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisará usar o elemento **dependsOn** em seu modelo para criar uma dependência do outro recurso. Por exemplo, ao implantar um aplicativo Web, o plano do Serviço de Aplicativo deve existir. Se você não tiver especificado que o aplicativo Web é dependente do plano do Serviço de Aplicativo, o Gerenciador de Recursos criará os dois recursos ao mesmo tempo. Você receberá um erro informando que o recurso do plano do Serviço de Aplicativo não pode ser encontrado porque ele ainda não existe quando tentar definir uma propriedade no aplicativo Web. Você pode evitar esse erro configurando a dependência no aplicativo Web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## Não foi possível encontrar o membro 'copy' no objeto

Você encontrou esse erro quando aplicou o elemento **copy** em uma parte do modelo que não dá suporte a esse elemento. Só é possível aplicar o elemento copy em um tipo de recurso. Não é possível aplicar a cópia em uma propriedade dentro de um tipo de recurso. Por exemplo, você aplica a cópia em uma máquina virtual, mas não pode aplicá-la nos discos do SO para uma máquina virtual. Em alguns casos, você pode converter um recurso-filho em um recurso-pai para criar um loop de cópia. Para obter mais informações sobre como usar a cópia, consulte [Criar várias instâncias de recursos Azure Resource Manager](resource-group-create-multiple.md).

## SKU não disponível

Ao implantar um recurso (normalmente uma máquina virtual), você pode receber o seguinte código de erro e a mensagem de erro:

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

Você recebe esse erro quando o recurso SKU selecionado (como o tamanho da VM) não está disponível para o local escolhido. Você tem duas opções para resolver esse problema:

1.	Faça logon no portal e comece a adicionar um novo recurso por meio da interface do usuário. Quando você definir os valores, verá os SKUs disponíveis desse recurso.

    ![skus disponíveis](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	Se não é possível encontrar um SKU adequado na região ou uma região alternativa que atenda às suas necessidades de negócios, entre em contato o [Suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).


## Nenhum provedor registrado encontrado

Ao implantar recursos, você pode receber o seguinte código de erro e a mensagem:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

Você recebe esse erro por um dos três motivos:

1. Local não suportado para o tipo de recurso
2. Versão da API não suportada para o tipo de recurso
3. O provedor de recursos não foi registrado para sua assinatura

A mensagem de erro deve fornecer sugestões para os locais com suporte e as versões da API. Você pode alterar o modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos. Se você não usou um provedor de recursos específico antes, precisará registrá-lo. Você pode descobrir mais sobre os provedores de recursos com os seguintes comandos.

### PowerShell

Para ver o status do registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registrar um provedor, use **Register-AzureRmResourceProvider** e forneça o nome do provedor de recursos que deseja registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para obter os locais com suporte para um determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para obter as versões da API com suporte para um determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### CLI do Azure

Para ver se o provedor está registrado, use o comando `azure provider list`.

    azure provider list

Para registrar um provedor de recursos, use o comando `azure provider register` e especifique o *namespace* para registrar.

    azure provider register Microsoft.Cdn

Para ver os locais com suporte e as versões da API para um provedor de recursos, use:

    azure provider show -n Microsoft.Compute --json > compute.json

## Cota excedida

Você pode ter problemas quando a implantação ultrapassar uma cota, que pode ser por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se tentar implantar uma máquina virtual com mais núcleos do que o valor permitido, você receberá um erro informando que a cota foi excedida. Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](azure-subscription-service-limits.md).

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

Ou no PowerShell, você pode usar o cmdlet **Get-AzureRmVMUsage**.

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

Além de controle de acesso baseado em função, suas ações de implantação podem ser limitadas por políticas na assinatura. Por meio de políticas, o administrador pode impor convenções a todos os recursos implantados na assinatura. Por exemplo, um administrador pode exigir que um valor de marca específico seja fornecido para um tipo de recurso. Se não tiver cumprido os requisitos da política, você receberá um erro durante a implantação. Para obter mais informações sobre as políticas, consulte [Usar a política para gerenciar os recursos e controlar o acesso](resource-manager-policy.md).

## Solucionando problemas das máquinas virtuais

| Erro | Artigos |
| -------- | ----------- |
| Erros de extensão de script personalizado | [Falhas de extensão da VM do Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Falhas de extensão da VM do Linux](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Erros de provisionamento de imagem do SO | [Novos erros da VM do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Novos erros da VM do Linux](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| Falhas na alocação | [Falhas de alocação da VM do Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Falhas de alocação da VM do Linux](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Erros do Secure Shell (SSH) ao tentar conectar | [Conexões do Secure Shell com a VM do Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Erros ao conectar o aplicativo em execução na VM | [Aplicativo em execução em uma VM do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Aplicativo em execução em uma VM do Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erros de conexão da Área de Trabalho Remota | [Conexões da Área de Trabalho Remota com a VM do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Erros de conexão resolvidos com a reimplantação | [Reimplantar Máquina Virtual em um novo nó do Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erros do serviço de nuvem | [Problemas de implantação do serviço de nuvem](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## Solucionando problemas de outros serviços

A tabela a seguir não é uma lista completa de tópicos de solução de problemas do Azure. Pelo contrário, ela foca nos problemas relacionados à implantação ou à configuração dos recursos. Se você precisar de ajuda para solucionar os problemas de execução com um recurso, confira a documentação desse serviço do Azure.

| O Barramento de | Artigo |
| -------- | -------- |
| Automação | [Dicas de solução de problemas para erros comuns na Automação do Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Solução de problemas do Microsoft Azure Stack](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Aplicativos Web e Azure Stack](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Data Factory | [Solucionar problemas da Data Factory](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [Solucionar problemas comuns quando você implanta serviços no Azure Service Fabric](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperação de Site | [Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Armazenamento | [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Solucionar problemas de implantação do dispositivo StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Banco de Dados SQL | [Solucionar problemas de conexão no Banco de Dados SQL do Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [Solução de problemas do Azure SQL Data Warehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## Entenda quando uma implantação está pronta

O Azure Resource Manager informa o êxito de uma implantação quando todos os provedores são retornados da implantação com êxito. No entanto, isso não significa necessariamente que seu grupo de recursos está "ativo e pronto para seus usuários". Por exemplo, uma implantação pode precisar baixar atualizações, aguardar recursos que não são de modelo ou instalar scripts complexos, ou realizar alguma outra atividade executável sobre a qual o Azure não sabe, sobre porque ela não é uma atividade que um provedor está acompanhando. Nesses casos, pode levar algum tempo antes que os recursos estejam prontos para uso no mundo real. Como resultado, você deve esperar que o status da implantação seja bem-sucedida algum tempo antes que sua implantação possa ser usada.

Você pode impedir o Azure de relatar êxito da implantação, no entanto, ao criar um script personalizado para seu modelo personalizado - usando o [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), por exemplo - que sabe como monitorar toda a implantação para preparação de todo o sistema e retorna com êxito somente quando os usuários podem interagir com toda a implantação. Se você quiser garantir que sua extensão seja a última a ser executada, use a propriedade **dependsOn** em seu modelo.

## Próximas etapas

- Para saber mais sobre as ações de auditoria, consulte [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para saber mais sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0713_2016-->