<properties
   pageTitle="Configurar a atualização de um aplicativo do Service Fabric | Microsoft Azure"
   description="Saiba como definir as configurações para atualizar um aplicativo do Service Fabric usando o Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# Configurar a atualização de um aplicativo do Service Fabric no Visual Studio

As ferramentas do Service Fabric no Visual Studio fornecem suporte à atualização da publicação em clusters locais ou remotos. Há duas vantagens em atualizar o seu aplicativo para uma versão mais recente em vez de substituir o aplicativo durante o teste e depuração: primeiro, os dados do aplicativo não serão perdidos durante a atualização; segundo, será possível obter alta disponibilidade, pois não haverá qualquer interrupção de serviço durante a atualização caso hajam instâncias de serviço suficientes espalhadas pelos domínios de atualização. Os testes podem ser executados em um aplicativo enquanto ele está sendo atualizado.

## Parâmetros necessários para atualizar

Você pode escolher entre dois tipos de implantação: normal ou atualização. Uma implantação normal apaga todas as informações e dados sobre a implantação anterior do cluster, enquanto uma implantação de atualização preserva-as. Ao atualizar um aplicativo do Service Fabric no Visual Studio, você precisa fornecer políticas de verificação de integridade e parâmetros de atualização de aplicativo. Os parâmetros de atualização de aplicativo ajudam a controlar a atualização, enquanto as políticas de verificação de integridade determinam se a atualização foi bem-sucedida ou não. Confira [Atualização do aplicativo Service Fabric: atualizar parâmetros](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Existem três modos de atualização: *Monitorada*, *Não monitorada/Automática*, e *Manual*.

  - Uma atualização *Monitorada* automatiza a atualização e a verificação de integridade do aplicativo.

  - Uma atualização *Não monitorada/Automática* automatiza a atualização, mas ignora a verificação de integridade do aplicativo.

  - Ao fazer uma atualização *Manual*, é necessário atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Confira [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## Atualizar um aplicativo do Service Fabric no Visual Studio

Se estiver usando as Ferramentas do Service Fabric no Visual Studio para atualizar um aplicativo do Service Fabric, você pode especificar um processo de publicação para uma atualização em vez de uma implantação normal marcando a caixa de seleção **Atualizar o aplicativo**.

### Para configurar os parâmetros de atualização

1. Clique no botão **Configurações** próximo à caixa de seleção. A caixa de diálogo **Editar Parâmetros de Atualização** é exibida. A caixa de diálogo **Editar parâmetros de atualização** dá suporte aos modos de atualização *Monitorada*, *Não monitorada/Automática* e *Não monitorada/Manual*.

2. Selecione o modo de atualização que você deseja usar e preencha a grade de parâmetros.

    Cada parâmetro tem valores padrão. O parâmetro opcional *DefaultServiceTypeHealthPolicy* tem uma entrada de tabela de hash. Aqui está um exemplo do formato de entrada de tabela de hash para *DefaultServiceTypeHealthPolicy*:

	```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
	```
	
    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que utiliza uma entrada de tabela de hash no seguinte formato:

	```    
	@ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
	```


    Aqui está um exemplo da vida real:

    ```
	@{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
	```

3. Se você selecionar o modo de atualização *Não monitorada/Manual*, precisará iniciar manualmente um console do PowerShell para continuar e concluir o processo de atualização. Confira [Atualização de aplicativos do Service Fabric: tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber como a atualização manual funciona

## Atualizar um aplicativo usando o PowerShell

Você pode usar os cmdlets do PowerShell para atualizar um aplicativo do Service Fabric. Confira [Tutorial da atualização de aplicativos do Service Fabric](service-fabric-application-upgrade-tutorial.md) e [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) para obter informações detalhadas.

## Especifique uma política de verificação de integridade no arquivo de Manifesto do Aplicativo

Cada serviço em um aplicativo do Service Fabric pode ter seus próprios parâmetros de política de integridade, que substituem os valores padrão. Você pode fornecer esses valores de parâmetro no Arquivo de Manifesto do Aplicativo.

O exemplo a seguir mostra como aplicar uma política de verificação de integridade exclusiva para cada serviço no manifesto do aplicativo.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## Próximas etapas
Para saber mais sobre como implantar um aplicativo, confira [Implantar um aplicativo existente no Service Fabric do Azure](service-fabric-deploy-existing-app.md).

<!---HONumber=Nov15_HO4-->