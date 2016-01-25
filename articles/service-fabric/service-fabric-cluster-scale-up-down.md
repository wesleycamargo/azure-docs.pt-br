<properties
   pageTitle="Escalar ou reduzir verticalmente um cluster do Service Fabric | Microsoft Azure"
   description="Escale ou reduza verticalmente um cluster do Service Fabric para atender à demanda adicionando ou removendo nós de máquinas virtuais"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# Escalar ou reduzir verticalmente um cluster do Service Fabric adicionando ou removendo Máquinas Virtuais (VMs) dele.

Você pode escalar ou reduzir verticalmente os clusters do Service Fabric para atender à demanda adicionando ou removendo máquinas virtuais.

>[AZURE.NOTE]Supõe-se que a sua assinatura tem a quantidade de núcleos suficiente para adicionar as novas VMs que comporão esse cluster.


## Dimensionar manualmente um cluster do Service Fabric

Se o cluster tiver vários tipos de Nó, será necessário adicionar ou remover as VMs dos tipos específicos de nó. Você pode adicionar VMs a um tipo de nó e remover VMs dos outros, na mesma implantação.

### Atualizar um cluster que você já implantou usando o portal

Como esse é um processo complicado, temos um módulo do PowerShell carregado em um Repositório Git que faz isso para você.

**Etapa 1**: copiar esta pasta para o computador desde este [repositório Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Etapa 2**: verificar se o SDK do Azure 1.0 ou superior está instalado em seu computador.

**Etapa 3**: abrir uma janela do Powershell e importar o ServiceFabricRPHelpers.psm

```
Remove-Module ServiceFabricRPHelpers
```

Importe o módulo do PowerShell que você acabou de copiar. Basta copiar o seguinte cmd e alterar o caminho para o .psm1 de modo que seja o que está em sua máquina.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **Etapa 4**: fazer logon em sua conta do Azure

```
Login-AzureRmAccount
```

Execute o comando Invoke-ServiceFabricRPClusterScaleUpgrade e verifique se o nome do grupo de recursos e a assinatura estão corretos.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

Veja um exemplo preenchido do mesmo comando PS

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **Etapa 5**: agora, o comando recuperará as informações do cluster e percorrerá com você todos os tipos de nó, informando primeiro a contagem atual de VM/Nó para esse tipo de nó e, em seguida, perguntando qual deveria ser a nova contagem de VM/Nó.

 **Para ampliar esse tipo de nó**, especifique um número maior do que a contagem atual de VMs.

**Para reduzir esse tipo de nó**, especifique um número menor do que a contagem atual de VMs.

Agora, esses prompt percorrerá todos os tipos de nó. Se o cluster tiver apenas um tipo de nó, você receberá apenas uma solicitação.
 
  **Etapa 6**: se você estiver adicionando novas VMs, receberá um prompt para fornecer a identificação do usuário e senha da área de trabalho remota das VMs que você está adicionando.
 
**Etapa 7**: na janela de saída, você verá mensagens informando o andamento da implantação. Após a conclusão da implantação, o cluster deverá ter o número de nós especificado na Etapa 5.


![ScaleupDownPSOut][ScaleupDownPSOut]


**Etapa 8**: se essa foi uma solicitação de redução, ainda restará uma etapa de exclusão das VMs. O script desativa todas as VMs das quais você solicitou a remoção, ou seja, nesses nós/VMs não há réplicas do aplicativo ou do sistema. Portanto, agora é seguro excluir essas VMs.

**OBSERVAÇÃO**: embora os nós desativados não sejam mais usados pelo seu cluster SF, você deve excluir as VMs desativadas para que não seja cobrado por elas.

**Etapa 8.1**: faça logon no Portal do Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

**Etapa 8.2**: navegue até o recurso de Cluster que você estava reduzindo e clique em "Todas as Configurações" na web part Essentials.

**Etapa 8.3**: clique nos Tipos de Nó e você verá uma lista com os nós desativados. Nesta foto, chackodnt15, chackodnt24, chackodnt25 e chackodnt26 são as VMs que eu preciso excluir agora.

![DeactivatedNodeList][DeactivatedNodeList]

**Etapa 8.4**: exclua as VMs por meio do Portal ou PS. Após a exclusão, você terá concluído a redução de seu cluster.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
Veja um exemplo preenchido do mesmo comando

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### Atualizar um cluster que você já implantou usando o PowerShell/CLI do ARM

Se você já implantou inicialmente o cluster usando um modelo de ARM, será necessário modificar a contagem das VMs de um tipo de nó específico e todos os recursos que oferecem suporte à VM. O número mínimo permitido de VMs para o tipo de nó primário é cinco (para clusters que não são de teste), para clusters de teste o mínimo é de três.

Quando você tiver o novo modelo, poderá implantá-lo por meio do ARM usando o mesmo grupo de recursos do que o cluster que está atualizando.


## Dimensionamento automático do cluster do Service Fabric

Neste momento, os clusters do Service Fabric não são compatíveis com dimensionamento automático. Em breve, os clusters serão construídos sobre conjuntos de escala de máquina virtual (VMSS). Nesse momento, o dimensionamento automático se tornará possível e se comportará de maneira semelhante ao comportamento do dimensionamento automático disponível nos Serviços de Nuvem.


## Próximas etapas

- [Saiba mais sobre atualizações de cluster](service-fabric-cluster-upgrade.md)
- [Saiba mais sobre os serviços com estado de particionamento para escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->