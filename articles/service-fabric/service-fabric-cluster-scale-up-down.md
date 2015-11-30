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
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Escalar ou reduzir verticalmente um cluster do Service Fabric adicionando ou removendo Máquinas Virtuais (VMs) dele.

Você pode escalar ou reduzir verticalmente os clusters do Service Fabric para atender à demanda adicionando ou removendo máquinas virtuais.

>[AZURE.NOTE]Supõe-se que a sua assinatura tem a quantidade de núcleos suficiente para adicionar as novas VMs que comporão esse cluster.


## Dimensionar manualmente um cluster do Service Fabric

### Escolher o tipo de nó a dimensionar

Se o cluster tiver vários tipos de nó, você então precisará adicionar ou remover as VMs dos tipos específicos de nó.

1. Faça logon no Portal do Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Navegue até os Clusters do Service Fabric ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. Selecione o cluster que você deseja escalar ou reduzir verticalmente

4. Navegue até a folha de configurações no painel do cluster. Se você não vir a folha de configurações, então clique em "Todas as Configurações" na parte essencial do painel do cluster.

5. Clique em NodeTypes, que abrirá a folha de lista NodeTypes.

7. Clique no tipo de nó que você deseja escalar ou reduzir verticalmente. Ele abrirá em seguida a folha de detalhes Tipo de Nó.

### Escalar verticalmente adicionando nós.

Aumente o número de VMs até o desejado e salve. Os nós/as VMs serão então adicionados assim que a implantação for concluída.

### Reduzir verticalmente removendo nós

A remoção de nós é um processo em duas etapas:

1. Ajuste o número de VMs até o desejado e salve. A extremidade inferior do controle deslizante indicará o requisito mínimo de VM para esse NodeType.

  >[AZURE.NOTE]Você deve manter um mínimo de 5 VMs para o tipo de nó principal.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. Retorne ao painel do cluster e clique no grupo de recursos. Isso abrirá a folha Grupo de Recursos.

3. Procure em Resumo ou abra a lista de recursos clicando em "..."

4. Clique no nome da VM que o sistema tinha indicado que poderia ser excluída.

5. Clique no ícone Excluir para excluir a VM.

## Dimensionamento automático do cluster do Service Fabric

Neste momento, os clusters do Service Fabric não são compatíveis com dimensionamento automático. Em breve, os clusters serão construídos sobre conjuntos de escala de máquina virtual (VMSS). Nesse momento, o dimensionamento automático se tornará possível e se comportará de maneira semelhante ao comportamento do dimensionamento automático disponível nos Serviços de Nuvem.

## Dimensionamento usando PowerShell/CLI

Este artigo abordou o dimensionamento de clusters usando o portal. No entanto, você pode executar as mesmas ações na linha de comando usando os comandos do ARM no recurso do cluster. A resposta GET do ClusterResource fornecerá a lista dos nós que foram desativados.

## Próximas etapas

- [Saiba mais sobre atualizações de cluster](service-fabric-cluster-upgrade.md)
- [Saiba mais sobre os serviços com estado de particionamento para escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->