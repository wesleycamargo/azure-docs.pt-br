<properties
   pageTitle="Gerenciar vários ambientes no Service Fabric | Microsoft Azure"
   description="Os aplicativos do Service Fabric podem ser executados em clusters que variam de tamanho de um computador para milhares de computadores. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferentes por ambiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="seanmck"/>

# Gerenciando parâmetros do aplicativo para vários ambientes

Os clusters do Service Fabric podem ser formados usando qualquer lugar de milhares de computadores. Embora os binários de aplicativo possam ser executados sem modificação em um amplo espectro de ambientes, com frequência você desejará configurar o aplicativo de forma diferente, dependendo do número de computadores em que ele estiver sendo implantado.

Como um exemplo simples, considere a `InstanceCount` para um serviço sem monitoração de estado. Ao executar no Azure, geralmente você vai querer definir esse parâmetro como o valor especial -1, garantindo que o serviço seja executado em todos os nós do cluster. No entanto, essa configuração não é adequada para um cluster de uma caixa, uma vez que você não pode ter vários processos escutando no mesmo ponto de extremidade em um único computador. Em vez disso, você normalmente define a `InstanceCount` como 1.

## Especificando parâmetros específicos do ambiente

A solução para isso é um conjunto de serviços padrão parametrizados e arquivos de parâmetros do aplicativo que preencham os valores de parâmetro para um determinado ambiente.

### Serviços padrão

Os aplicativos do Service Fabric são compostos de uma coleção de instâncias de serviço. Embora seja possível criar um aplicativo vazio e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria dos aplicativos tem um conjunto de serviços principais que sempre deverá ser criado quando o aplicativo for instanciado. Esses são chamados de "serviços padrão" e são especificadas no manifesto do aplicativo, com espaços reservados para configuração por ambiente incluídos entre colchetes adicionais:

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

Os atributos DefaultValue especificam o valor a ser usado na ausência de um parâmetro mais específico para um determinado ambiente.

>[AZURE.NOTE]Nem todos os parâmetros da instância de serviço são adequados para a configuração por ambiente. No exemplo acima, os valores LowKey e HighKey para o esquema de particionamento do serviço são definidos explicitamente para todas as instâncias do serviço, já que o intervalo de partição é uma função do seu domínio de dados, não do ambiente.

### Definições de configuração de serviço por ambiente

O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) permite que os serviços incluam pacotes de configuração com pares de chave-valor personalizados legíveis em tempo de execução. Os valores dessas configurações também podem ser diferenciados pelo ambiente por meio da especificação de uma `ConfigOverride` no manifesto do aplicativo.

Suponha que você tenha a seguinte configuração no manifesto de serviço para o serviço `Stateful1`:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para substituir esse valor por um par de aplicativo/ambiente específico, crie uma `ConfigOverride` ao importar o manifesto de serviço no manifesto do aplicativo.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Esse parâmetro poderá então ser configurado por ambiente, como mostrado acima, declarando-o na seção de parâmetros do manifesto do aplicativo e especificando valores específicos nos arquivos de parâmetros do aplicativo.

>[AZURE.NOTE]No caso de definições de configuração de serviço, há três locais onde o valor de uma chave pode ser definido: o pacote de configuração de serviço, o manifesto do aplicativo e o arquivo de parâmetros do aplicativo. O Service Fabric sempre escolherá o arquivo de parâmetros de aplicativo primeiro (se especificado), o manifesto do aplicativo e, por fim, o pacote de configuração.

### Arquivos de parâmetros de aplicativo

O projeto de aplicativo do Service Fabric pode incluir um ou mais arquivos de parâmetros de aplicativo, cada um deles define os valores específicos para os parâmetros definidos no manifesto do aplicativo:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Por padrão, um novo aplicativo inclui dois arquivos de parâmetros, chamados Local.xml e Cloud.xml:

![Arquivos de parâmetros de aplicativo no Gerenciador de Soluções][app-parameters-solution-explorer]

Para criar um novo arquivo de parâmetros, basta copiar e colar um existente e dar um novo nome a ele.

## Identificando parâmetros específicos do ambiente durante a implantação

No momento da implantação, você precisa escolher o arquivo de parâmetros adequado para aplicar ao seu aplicativo. Você pode fazer isso por meio da caixa de diálogo Publicar no Visual Studio ou no PowerShell.

### Implantando do Visual Studio

Você pode escolher na lista de arquivos de parâmetros disponíveis ao publicar seu aplicativo no Visual Studio.

![Escolher um arquivo de parâmetros na caixa de diálogo Publicar][publishdialog]

### Implantando do PowerShell

O script `DeployCreate-FabricApplication.ps1` do PowerShell aceita um arquivo de parâmetros como um parâmetro.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Próximas etapas

Para saber mais sobre alguns dos principais conceitos discutidos neste tópico, consulte a [Visão geral técnica do Service Fabric](service-fabric-technical-overview.md). Para obter informações sobre outros recursos de gerenciamento de aplicativos disponíveis no Visual Studio, consulte [Gerenciando seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=Nov15_HO4-->