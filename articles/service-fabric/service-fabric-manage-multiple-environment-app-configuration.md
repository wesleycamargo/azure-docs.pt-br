<properties
   pageTitle="Gerenciar vários ambientes no Service Fabric | Microsoft Azure"
   description="Os aplicativos do Service Fabric podem ser executados em clusters que variam de tamanho de um computador para milhares de computadores. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferentes por ambiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Gerenciar parâmetros do aplicativo para vários ambientes

Você pode criar clusters do Service Fabric usando em qualquer lugar de um a milhares de computadores. Embora os binários de aplicativo possam ser executados sem modificação em um amplo espectro de ambientes, com frequência você desejará configurar o aplicativo de forma diferente, dependendo do número de computadores em que ele estiver sendo implantado.

Como um exemplo simples, considere a `InstanceCount` para um serviço sem estado. Quando você estiver executando aplicativos no Azure, geralmente desejará definir esse parâmetro como o valor especial "-1". Isso garante que o serviço esteja em execução em todos os nós no cluster. No entanto, essa configuração não é adequada para um cluster de um computador, uma vez que você não pode ter vários processos escutando no mesmo ponto de extremidade em um único computador. Em vez disso, normalmente você definirá `InstanceCount` como “1”.

## Especificando parâmetros específicos do ambiente

A solução para esse problema de configuração é um conjunto de serviços padrão parametrizados e arquivos de parâmetros do aplicativo que preencham os valores de parâmetro para um determinado ambiente.

### Serviços padrão

Os aplicativos do Service Fabric são compostos de uma coleção de instâncias de serviço. Embora seja possível para você criar um aplicativo vazio e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria dos aplicativos tem um conjunto de serviços principais que sempre deverá ser criado quando o aplicativo for instanciado. Eles são chamados de "serviços padrão". Eles são especificados no manifesto do aplicativo, com espaços reservados para configuração por ambiente incluídos entre colchetes adicionais:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Cada um dos parâmetros nomeados deve ser definido dentro do elemento Parameters do manifesto do aplicativo:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

O atributo DefaultValue especifica o valor a ser usado na ausência de um parâmetro mais específico para um determinado ambiente.

>[AZURE.NOTE] Nem todos os parâmetros da instância de serviço são adequados para a configuração por ambiente. No exemplo acima, os valores LowKey e HighKey para o esquema de particionamento do serviço são definidos explicitamente para todas as instâncias do serviço, já que o intervalo de partição é uma função do domínio de dados, não do ambiente.


### Definições de configuração de serviço por ambiente

O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) permite que os serviços incluam pacotes de configuração com pares de chave e valor personalizados legíveis em tempo de execução. Os valores dessas configurações também podem ser diferenciados pelo ambiente por meio da especificação de uma `ConfigOverride` no manifesto do aplicativo.

Suponha que você tenha a seguinte configuração no arquivo Config\\Settings.xml para o serviço `Stateful1`:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para substituir esse valor por um par de aplicativo/ambiente específico, crie uma `ConfigOverride` ao importar o manifesto do serviço no manifesto do aplicativo.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Esse parâmetro pode então ser configurado pelo ambiente como mostrado acima. Você pode fazer isso declarando-o na seção de parâmetros do manifesto do aplicativo e especificando valores específicos nos arquivos de parâmetro do aplicativo.

>[AZURE.NOTE] No caso de definições de configuração de serviço, há três locais onde o valor de uma chave pode ser definido: o pacote de configuração de serviço, o manifesto do aplicativo e o arquivo de parâmetro do aplicativo. O Service Fabric sempre escolherá o arquivo de parâmetro de aplicativo primeiro (se especificado), o manifesto do aplicativo e, por fim, o pacote de configuração.


### Arquivos de parâmetros de aplicativo

O projeto de aplicativo do Service Fabric pode incluir um ou mais arquivos de parâmetro de aplicativo. Cada um deles define os valores específicos para os parâmetros definidos no manifesto do aplicativo:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Por padrão, um novo aplicativo inclui dois arquivos de parâmetro de aplicativo, chamados Local.xml e Cloud.xml:

![Arquivos de parâmetros de aplicativo no Gerenciador de Soluções][app-parameters-solution-explorer]

Para criar um novo arquivo de parâmetro, bastará copiar e colar um existente e dar um novo nome a ele.

## Identificando parâmetros específicos do ambiente durante a implantação

No momento da implantação, você precisa escolher o arquivo de parâmetro adequado para aplicar ao seu aplicativo. Você pode fazer isso por meio da caixa de diálogo Publicar no Visual Studio ou por meio do PowerShell.

### Implantar com o Visual Studio

Você pode escolher na lista de arquivos de parâmetro disponíveis ao publicar seu aplicativo no Visual Studio.

![Escolher um arquivo de parâmetro na caixa de diálogo Publicar][publishdialog]

### Implantar do PowerShell

O script `DeployCreate-FabricApplication.ps1` do PowerShell aceita um arquivo de parâmetros como um parâmetro.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Próximas etapas

Para saber mais sobre alguns dos principais conceitos discutidos neste tópico, veja a [Visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para obter informações sobre outros recursos de gerenciamento de aplicativos disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_0309_2016-->