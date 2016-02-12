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
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# Escalar ou reduzir verticalmente um cluster do Service Fabric adicionando ou removendo máquinas virtuais

Você pode escalar ou reduzir verticalmente os clusters do Service Fabric do Azure para atender à demanda adicionando ou removendo máquinas virtuais.

>[AZURE.NOTE] Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que comporão esse cluster.

## Dimensionar manualmente um cluster do Service Fabric

### Escolher o tipo de nó a dimensionar

Se o cluster tiver vários tipos de nó, será necessário adicionar ou remover as VMs dos tipos específicos de nó. Faça assim:

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Navegue até **Clusters do Service Fabric**. ![Página Clusters do Service Fabric no Portal do Azure.][BrowseServiceFabricClusterResource]

3. Selecione o cluster que você deseja escalar ou reduzir verticalmente

4. Navegue até a folha **Configurações** no painel do cluster. Se você não vir a folha **Configurações**, clique em **Todas as Configurações** na parte essencial do painel do cluster.

5. Clique em **NodeTypes**, que abrirá a folha **Lista de NodeTypes**.

6. Clique no tipo de nó que você deseja escalar ou reduzir verticalmente. Isso abrirá a folha **Detalhes do NodeType**.

### Escalar verticalmente adicionando nós

Ajuste o número de VMs até o número que desejar e salve. Os nós/VMs serão adicionadas após a conclusão da implantação.

### Reduzir verticalmente removendo nós

A remoção de nós é um processo em duas etapas:

1. Ajuste o número de VMs até o desejado e salve. A extremidade inferior do controle deslizante indica o número mínimo de VMs necessárias para esse tipo de nó.

    >[AZURE.NOTE] Você deve manter um mínimo de cinco VMs para o tipo de nó principal.

2. Após a conclusão da implantação, você receberá uma notificação com os nomes de VM que agora podem ser excluídos. Em seguida, você precisará navegar até os recursos da VM e excluí-los:

    a. Retorne ao painel do cluster e clique em **Grupo de Recursos**. Isso abrirá a folha **Grupo de Recursos**.

    b. Procure em **Resumo** ou abra a lista de recursos clicando em “**...**”.

    c. Clique no nome da VM que o sistema indicou para exclusão.

    d. Clique no ícone **Excluir** para excluir a VM.

## Dimensionar automaticamente os clusters do Service Fabric

Neste momento, os clusters do Service Fabric não são compatíveis com dimensionamento automático. Em breve, os clusters serão construídos sobre conjuntos de escala de máquina virtual. Nesse momento, o dimensionamento automático se tornará possível e se comportará de maneira semelhante ao comportamento do dimensionamento automático disponível nos serviços de nuvem.

## Dimensionar os clusters usando PowerShell/CLI

Este artigo aborda o dimensionamento de clusters usando o portal. No entanto, você pode executar as mesmas ações na linha de comando usando os comandos do Gerenciador de Recursos do Azure no recurso do cluster. A resposta GET do recurso de cluster fornecerá a lista dos nós que foram desativados.

## Próximas etapas

- [Saiba mais sobre atualizações de cluster](service-fabric-cluster-upgrade.md)
- [Saiba mais sobre os serviços com estado de particionamento para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0204_2016-->