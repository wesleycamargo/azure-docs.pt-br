<properties
   pageTitle="Implantar um aplicativo personalizado no Service Fabric do Azure | Microsoft Azure"
   description="Instruções passo a passo sobre como empacotar um aplicativo existente para que ele possa ser implantado em um cluster da Malha de Serviço do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Implantar um aplicativo personalizado no Service Fabric

Você pode executar qualquer tipo de aplicativo existente, como o Node.js, o Java ou os aplicativos nativos no Service Fabric. O Service Fabric trata esses aplicativos como serviços sem estado e os coloca em nós em um cluster com base na disponibilidade e em outras métricas. Este artigo descreve como empacotar e implantar um aplicativo existente em um cluster do Service Fabric.

## Benefícios da execução de um aplicativo personalizado no Service Fabric

Há algumas vantagens de executar o aplicativo no Cluster do Service Fabric:

- Alta disponibilidade: os aplicativos executados no Service Fabric são altamente disponíveis de imediato. O Service Fabric assegura uma instância de um aplicativo esteja sempre em execução
- Monitoramento de integridade: o monitoramento de integridade nativo do Service Fabric detecta se o aplicativo está em execução e fornece informações de diagnóstico no caso de uma falha   
- Gerenciamento de ciclo de vida do aplicativo: além de atualizações sem qualquer tempo de inatividade, o Service Fabric também permite a reversão para a versão anterior se houver um problema durante a atualização.    
- Densidade: você pode executar vários aplicativos no cluster, o que elimina a necessidade um hardware próprio para a execução de cada aplicativo

Neste artigo, abordaremos as etapas básicas para empacotar um aplicativo existente e implantá-lo no Service Fabric.


## Visão geral resumida de arquivos de manifesto do serviço e do aplicativo

Antes de entrar em detalhes sobre a implantação de um aplicativo existente, é útil entender o modelo de empacotamento e implantação do Service Fabric. O modelo de implantação de empacotamento do Service Fabric depende basicamente de dois arquivos:


* **Manifesto do aplicativo**

  O manifesto do aplicativo é usado para descrever o aplicativo e listar os serviços que o compõem, além de outros parâmetros, como o número de instâncias, que são usados para definir como os serviços devem ser implantados. No mundo do Service Fabric, um aplicativo é ‘unidade atualizável’. Um aplicativo pode ser atualizado como uma única unidade, em que as falhas potenciais (e reversões potenciais) são gerenciadas pela plataforma para garantir que o processo de atualização seja completamente bem-sucedido ou, no caso de falha, que ele não deixe o aplicativo em um estado desconhecido/instável.


* **Manifesto do serviço**

  O manifesto do serviço descreve os componentes de um serviço. Ele inclui dados como nome e tipo do serviço (informações que a Malha de Serviço usa para gerenciar o serviço), seu código, configuração e componentes de dados, além de alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação. Não vamos entrar em detalhes de todos os diferentes parâmetros disponíveis no manifesto do serviço; vamos observar o subconjunto que é exigido para executar um aplicativo existente na Malha de Serviço.


## Estrutura de arquivos do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, ele precisará seguir uma estrutura de diretórios predefinida. A seguir, um exemplo dessa estrutura.

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

A raiz contém o arquivo ApplicationManifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos exigidos pelo serviço: o ServiceManifest.xml e normalmente 3 diretórios:

- *code*: contém o código do serviço
- *config*: contém um arquivo settings.xml (e outros arquivos, se forem necessários) que o serviço pode acessar no tempo de execução para recuperar definições específicas da configuração.
- *data*: um diretório adicional para armazenar dados locais adicionais que podem ser necessário para o serviço. Observação: data deve ser usado para armazenar somente dados efêmeros. A Malha de Serviço não vai copiar/replicar alterações no diretório data se o serviço precisar ser realocado, por exemplo, durante o failover.

Observação: você não precisa criar os diretórios `config` e `data`, caso eles não sejam necessários.

## O processo de empacotamento de um aplicativo existente

O processo de empacotamento de um aplicativo existente baseia-se nas seguintes etapas:

- criar a estrutura de diretórios do pacote
- adicionar arquivos de configuração e de código do aplicativo
- atualizar o arquivo de manifesto do serviço
- atualizar o manifesto do aplicativo

>[AZURE.NOTE]\: forneceremos uma ferramenta de empacotamento, permitindo que você crie o ApplicationPackage automaticamente. No momento, a ferramenta está em visualização. Você pode baixá-lo [aqui](http://aka.ms/servicefabricpacktool).

### Criar a estrutura de diretórios do pacote
Você pode começar criando a estrutura de diretórios como descrito acima.

### Adicionar os arquivos de configuração e código do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar os arquivos de configuração e código do aplicativo nos diretórios code e config. Também é possível criar diretórios adicionais ou subdiretórios nos diretórios code ou config. O Service Fabric faz uma cópia do conteúdo do diretório raiz do aplicativo, de modo que não há estrutura predefinida a ser usada, a não ser criar dois diretórios principais, code e settings (mas você pode escolher nomes diferentes se desejar; mais detalhes na próxima seção).

>[AZURE.NOTE]\: Certifique-se de incluir todos os arquivos/dependências que o aplicativo precisa. A Malha de Serviço copiará o conteúdo do pacote de aplicativos em todos os nós do cluster onde os serviços do aplicativo serão implantados. O pacote deve conter todos os códigos que o aplicativo precisa para ser executado. Não é recomendável supor que as dependências já estejam instaladas.

### Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de Manifesto do Serviço para incluir as seguintes informações:

- O nome da fila do tipo de serviço. Isso é um 'Id' que o Service Fabric usa para identificar um serviço
- O comando a ser usado para iniciar o aplicativo (ExeHost)
- Qualquer script que precisa ser executado para configurar o aplicativo (SetupEntrypoint)

Veja abaixo um exemplo de um `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Vamos verificar a parte diferente do arquivo que você precisa atualizar:

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Você pode escolher qualquer nome desejado para `ServiceTypeName`. O valor será usado no `ApplicationManifest.xml` para identificar o serviço.
- Você deve especificar `UseImplicitHost="true"`. Esse atributo informa à Malha de Serviço que o serviço se baseia em um aplicativo independente, de modo que ele só precisa iniciá-lo como um processo e monitorar a integridade.

### CodePackage
O CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O elemento `Name` é usado para especificar o nome do diretório no Pacote de Aplicativos que contém o código do serviço. O `CodePackage` também tem o atributo `version` que pode ser usado para especificar a versão do código e, possivelmente, ser usado para atualizar o código do serviço, com a infraestrutura do ALM do Service Fabric.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O SetupEntrypoint é usado para especificar qualquer executável ou arquivo em lotes que deve ser executado antes da inicialização do código do serviço. Trata-se de um elemento opcional, de modo que ele não precisará ser incluído se não houver inicialização/configuração obrigatória. O SetupEntrypoint é executado toda vez que o serviço é reiniciado. Há apenas um SetupEntrypoint, de modo que os scripts de instalação/configuração precisam ser associados em um único arquivo em lotes se a instalação/configuração do aplicativo exigir vários scripts. Assim como o elemento Entrypoint, SetupEntrypoint pode executar qualquer tipo de arquivo: executável, arquivos em lotes e cmdlet do PowerShell. No exemplo anterior, o SetupEntrypoint se baseia em um arquivo em lotes, launchConfig.cmd, que está localizado no subdiretório `scripts` do diretório Code (supondo que o elemento WorkingDirectory esteja definido como Code).

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O elemento `Entrypoint` no arquivo de manifesto do serviço é usado para especificar como iniciar o serviço. O elemento `ExeHost` especifica o executável e os argumentos que devem ser usados para iniciar o serviço.

- `Program`: especifica o nome do executável que deve ser executado para iniciar o serviço.
- `Arguments`: especifica os argumentos que devem ser transmitidos para o executável. Pode ser uma lista de parâmetros com argumentos.
- `WorkingFolder`: especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar dois valores:
	- `CodeBase`: o diretório de trabalho será definido no diretório Code no pacote de aplicativos (diretório `Code` na estrutura mostrada abaixo)
	- `CodePackage`: o diretório de trabalho será definido como a raiz do pacote de aplicativos (`MyServicePkg`)
- O elemento `WorkingDirectory` é útil para definir o diretório de trabalho correto, de modo que os caminhos relativos possam ser usados pelos scripts de inicialização ou do aplicativo.

### Pontos de extremidade

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
O elemento `Endpoint` especifica os pontos de extremidade nos quais o aplicativo pode escutar. Neste exemplo, o aplicativo Node.js escuta na porta 3000.

## Arquivo de manifesto do aplicativo

Depois de configurar o arquivo `servicemanifest.xml`, você deve fazer algumas alterações no arquivo `ApplicationManifest.xml` para garantir que o tipo e o nome de Serviço corretos sejam usados.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

No `ServiceManifestImport`, você pode especificar um ou mais serviços para incluí-los no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório em que o arquivo `ServiceManifest.xml` está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configurando o log
Para um aplicativo existente, é muito útil poder ver logs do console para descobrir se os scripts de configuração e aplicativo não mostram algum erro. O redirecionamento do console pode ser configurado no arquivo `ServiceManifest.xml` usando o elemento `ConsoleRedirection`

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` pode ser usado para redirecionar as saídas do console (stdout e stderr) para um diretório de trabalho. Dessa forma, elas podem ser usadas para verificar se não há erros durante a instalação ou execução do aplicativo no cluster do Service Fabric.

	* `FileRetentionCount` determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log das 5 execução anteriores são armazenados no diretório de trabalho.
	* `FileMaxSizeInKb` especifica o tamanho máximo dos arquivos de log.

Os arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para determinar onde os arquivos estão localizados, use o Gerenciador da Malha de Serviço e descubra em qual nó o serviço está em execução e qual é o diretório de trabalho que está sendo usado no momento. Esse procedimento será abordado posteriormente neste artigo.

### Implantação
A última etapa será implantar seu aplicativo. O script do PowerShell abaixo mostra como implantar seu aplicativo no cluster de desenvolvimento local e iniciar um novo serviço do Service Fabric.

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Um serviço da Malha de Serviço pode ser implantado em várias 'configurações'. Por exemplo, ele pode ser implantado como uma única ou várias instâncias, ou pode ser implantado de modo que haja uma instância do serviço em cada nó do cluster da Malha de Serviço.

O parâmetro `InstanceCount` do cmdlet `New-ServiceFabricService` é usado para especificar quantas instâncias do serviço devem ser iniciadas no cluster do Service Fabric. Você pode definir o valor de `InstanceCount` dependendo do tipo de aplicativo que está implantando. Os dois cenários mais comuns são: * `InstanCount = "1"`: nesse caso, somente uma instância do serviço será implantada no cluster. O agendador da Malha de Serviço determina em qual nó o serviço será implantado.

* `InstanceCount ="-1"`: nesse caso, uma instância do serviço será implantada em cada nó do cluster do Service Fabric. O resultado final será ter uma (e apenas uma) instância do serviço para cada nó no cluster. Essa é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), pois aplicativos cliente precisam apenas se 'conectar' a qualquer um dos nós do cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster da Malha de Serviço estiverem conectados a um balanceador de carga para que o tráfego do cliente possa ser distribuído pelo serviço em execução em todos os nós do cluster.

### Verificar seu aplicativo em execução

No Gerenciador do Service Fabric, identifique o nó em que o serviço está em execução. Neste exemplo, ele está executando no Node1:

![aplicativo em execução](./media/service-fabric-deploy-existing-app/runningapplication.png)

Se você navegar até o nó e procurar o aplicativo, verá as informações essenciais do nó, incluindo sua localização no disco.

![local no disco](./media/service-fabric-deploy-existing-app/locationondisk.png)

Se você navegar até o diretório usando o Gerenciador de Servidores, poderá localizar o diretório de trabalho e a pasta de logs do serviço, como mostrado abaixo.

![local no disco](./media/service-fabric-deploy-existing-app/loglocation.png)


## Próximas etapas
Neste artigo, você aprendeu as etapas básicas de empacotamento e implantação de um aplicativo existente no Service Fabric. Como uma próxima etapa, confira o conteúdo adicional deste tópico.

- Exemplo de empacotamento e implantação de um aplicativo personalizado no [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication), incluindo um link para a versão de pré-lançamento da ferramenta de empacotamento.
- Saiba como [implantar vários aplicativos personalizados](service-fabric-deploy-multiple-apps.md).
- Introdução à [criação do seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

<!---HONumber=AcomDC_1125_2015-->