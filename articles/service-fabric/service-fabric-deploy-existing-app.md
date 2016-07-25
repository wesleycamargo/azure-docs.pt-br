<properties
   pageTitle="Implantar um executável existente à Service Fabric do Azure | Microsoft Azure"
   description="Instruções passo a passo sobre como empacotar um aplicativo existente como um executável convidado, para que ele possa ser implantado em um cluster do Azure Service Fabric"
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
   ms.workload="na"
   ms.date="06/20/2016"
   ms.author="bscholl;mikhegn"/>

# Implantar um executável convidado à Service Fabric

Você pode executar qualquer tipo de aplicativo, como o Node.js, o Java ou os aplicativos nativos no Service Fabric do Azure. A terminologia de Service Fabric se refere a esses tipos de aplicativos como executáveis convidados. Os executáveis convidados são tratados pela Service Fabric como serviços sem monitoração de estado. Como resultado, eles serão colocados em nós em um cluster, com base na disponibilidade e outras métricas. Este artigo descreve como empacotar e implantar um executável convidado em um cluster do Service Fabric usando o Visual Studio ou um utilitário de linha de comando.

## Benefícios de executar um executável convidado no Service Fabric

Há várias vantagens que vêm com a execução de um convidado executável em um cluster de Service Fabric:

- Alta disponibilidade. Os aplicativos executados no Service Fabric são altamente disponíveis de imediato. O Service Fabric assegura uma instância de um aplicativo esteja sempre funcionando.
- Monitoramento de integridade. O monitoramento de integridade pronto para uso do Service Fabric detecta se o aplicativo está em execução e fornece informações de diagnóstico em caso de falha.
- Gerenciamento do ciclo de vida do aplicativo. Além de fornecer atualizações sem tempo de inatividade, o Service Fabric também permite reverter para a versão anterior se houver um problema durante a atualização.
- Densidade. Você pode executar vários aplicativos no cluster, o que elimina a necessidade um hardware próprio para a execução de cada aplicativo.

Neste artigo, abordaremos as etapas básicas para empacotar um convidado executável e implantá-lo à Service Fabric.

## Visão geral resumida de arquivos de manifesto do serviço e do aplicativo

Como parte da implantação de um executável convidado, é útil entender o modelo de implantação e empacotamento do Service Fabric. O modelo de implantação de empacotamento do Service Fabric depende, principalmente, de dois arquivos XML: os manifestos do aplicativo e do serviço. A definição de esquema dos arquivos ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK e as ferramentas do Service Fabric em *C:\\Arquivos de Programas\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo**

  O manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que o compõem, além de outros parâmetros usados para definir como os serviços devem ser implantados (como o número de instâncias).

  No mundo do Service Fabric, um aplicativo é a “unidade atualizável”. Um aplicativo pode ser atualizado como uma única unidade em que falhas potenciais (e reversões potencias) são gerenciadas pela plataforma. A plataforma garante que o processo de atualização seja completamente bem-sucedido – ou que, se falhar, a plataforma não deixe o aplicativo em um estado desconhecido/instável.

* **Manifesto do serviço**

  O manifesto do serviço descreve os componentes de um serviço. Inclui dados, como o nome e o tipo de serviço (informações que o Service Fabric usa para gerenciar o serviço) e seus componentes de código, configuração e dados. O manifesto do serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação.

  Não vamos entrar em detalhes sobre todos os parâmetros diferentes que estão disponíveis no manifesto do serviço. Passaremos pelo subconjunto que é necessário para fazer um executável convidado ser executado no Service Fabric.

## Estrutura de arquivos do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, ele precisará seguir uma estrutura de diretórios predefinida. A seguir, um exemplo dessa estrutura.

```
|-- ApplicationPackage
    |-- code
        |-- existingapp.exe
    |-- config
        |-- Settings.xml
    |-- data
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

A raiz contém o arquivo ApplicationManifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos exigidos pelo serviço – o ServiceManifest.xml e, normalmente, os três diretórios a seguir:

- *Código*. Esse diretório contém o código de serviço.
- *Config*. Esse diretório contém o arquivo settings.xml (e outros arquivos, se necessário) que o serviço pode acessar no tempo de execução para recuperar definições de configuração específicas.
- *Dados*. Esse é um diretório adicional para armazenar dados locais adicionais que podem ser necessário para o serviço. Observação: os dados devem ser usados para armazenar somente dados efêmeros. O Service Fabric não copia/replica alterações de diretório de dados se o serviço precisar ser realocado – por exemplo, durante o failover.

Observação: você não precisa criar os diretórios `config` e `data` se não precisar deles.

## Processo de empacotamento de um aplicativo existente

Ao empacotar um executável convidado, você pode optar por usar um modelo de projeto do Visual Studio ou criar o pacote de aplicativo manualmente. Usando o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados pelo assistente de novo projeto para você. Confira abaixo um guia passo a passo sobre como empacotar um executável convidado usando o Visual Studio.

O processo de empacotamento manual de um executável convidado baseia-se nas seguintes etapas:

1. Criar a estrutura de diretórios do pacote.
2. Adicionar os arquivos de configuração e código do aplicativo.
3. Editar o arquivo de manifesto do serviço.
4. Editar o arquivo de manifesto do aplicativo.

>[AZURE.NOTE] Forneceremos uma ferramenta de empacotamento que permite criar o ApplicationPackage automaticamente. No momento, a ferramenta está em visualização. Você pode baixá-lo [aqui](http://aka.ms/servicefabricpacktool).

### Criar a estrutura de diretórios do pacote
Você pode começar criando a estrutura de diretórios como descrito anteriormente.

### Adicionar os arquivos de configuração e código do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar os arquivos de configuração e código do aplicativo aos diretórios de code e config. Também é possível criar diretórios adicionais ou subdiretórios nos diretórios code ou config.

O Service Fabric faz uma cópia do conteúdo do diretório raiz do aplicativo, portanto, não há estrutura predefinida a usar que não criar dois diretórios principais, código e configurações. (Você pode escolher nomes diferentes, se desejar. Mais detalhes estão na próxima seção.)

>[AZURE.NOTE] Certifique-se de incluir todos os arquivos/dependências de que o aplicativo precisa. A Malha de Serviço copiará o conteúdo do pacote de aplicativos em todos os nós do cluster onde os serviços do aplicativo serão implantados. O pacote deve conter todos os códigos que o aplicativo precisa para ser executado. Não recomendamos supor que as dependências já estão instaladas.

### Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de manifesto do serviço para incluir as seguintes informações:

- O nome da fila do tipo de serviço. Esse é um ID que o Service Fabric usa para identificar um serviço.
- O comando a ser usado para iniciar o aplicativo (ExeHost).
- Qualquer script que precisa ser executado para configurar o aplicativo (SetupEntrypoint).

Veja abaixo um exemplo de um arquivo `ServiceManifest.xml`:

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

Vamos verificar as partes diferente do arquivo que você precisa atualizar:

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Você pode escolher qualquer nome que desejar para `ServiceTypeName`. O valor é usado no arquivo `ApplicationManifest.xml` para identificar o serviço.
- Você precisa especificar `UseImplicitHost="true"`. Esse atributo informa ao Service Fabric que o serviço se baseia em um aplicativo independente, de modo tudo o que o Service Fabric precisa fazer é iniciá-lo como um processo e monitorar a integridade.

### CodePackage
O elemento CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O elemento `Name` é usado para especificar o nome do diretório no pacote de aplicativos que contém o código do serviço. O `CodePackage` também tem o atributo `version`. Isso pode ser usado para especificar a versão do código – e também para atualizar o código do serviço usando a infraestrutura de gerenciamento de ciclo de vida de aplicativo do Service Fabric.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntrypoint é usado para especificar qualquer executável ou arquivo em lotes que deve ser executado antes da inicialização do código do serviço. Trata-se de um elemento opcional, de modo que ele não precisará ser incluído se não houver necessidade de inicialização/configuração. O SetupEntrypoint é executado toda vez que o serviço é reiniciado.

Há apenas um SetupEntrypoint, de modo que os scripts de instalação/configuração precisam ser reunidos em um único arquivo em lotes se a instalação/configuração do aplicativo exigir vários scripts. Assim como o elemento SetupEntryPoint, SetupEntrypoint pode executar qualquer tipo de arquivo: arquivos executáveis, arquivos em lotes e cmdlets do PowerShell. No exemplo anterior, o SetupEntrypoint baseia-se em um arquivo em lotes, LaunchConfig.cmd, que está localizado no subdiretório `scripts` do diretório de código (supondo que o elemento WorkingFolder esteja definido para código).

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

- O `Program` especifica o nome do executável que deve ser executado para iniciar o serviço.
- O `Arguments` especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
- `WorkingFolder` especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar dois valores:
	- O `CodeBase` especifica o diretório de trabalho que será definido no diretório de código no pacote de aplicativos (diretório `Code` na estrutura mostrada abaixo).
	- O `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`MyServicePkg`).
- O `WorkingFolder` é útil para definir o diretório de trabalho correto, de modo que os caminhos relativos possam ser usados pelos scripts de inicialização ou do aplicativo.

### Pontos de extremidade

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
O elemento `Endpoint` especifica os pontos de extremidade nos quais o aplicativo pode escutar. Neste exemplo, o aplicativo Node.js escuta na porta 3000.

## Editar o arquivo de manifesto do aplicativo

Depois de configurar o arquivo `Servicemanifest.xml`, você deve fazer algumas alterações ao arquivo `ApplicationManifest.xml` para garantir o uso do tipo e do nome de serviço corretos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

No elemento `ServiceManifestImport`, você pode especificar um ou mais serviços para incluí-los no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório em que o arquivo `ServiceManifest.xml` está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configurar registro em log
Para executáveis convidados, é muito útil poder ver logs do console para descobrir se os scripts de configuração e aplicativo mostram todos os erros. O redirecionamento do console pode ser configurado no arquivo `ServiceManifest.xml` usando o elemento `ConsoleRedirection`.

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

	* O `FileRetentionCount` determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log das cinco execução anteriores são armazenados no diretório de trabalho.
	* O `FileMaxSizeInKb` especifica o tamanho máximo dos arquivos de log.

Arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para determinar onde os arquivos estão localizados, use o Gerenciador do Service Fabric para determinar em qual nó o serviço está sendo executado e qual diretório de trabalho está sendo usado. Esse processo é abordado mais adiante neste artigo.

### Implantação
A última etapa será implantar seu aplicativo. O script do PowerShell abaixo mostra como implantar seu aplicativo no cluster de desenvolvimento local e iniciar um novo serviço do Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Um serviço do Service Fabric pode ser implantado em várias "configurações". Por exemplo, pode ser implantado como uma ou várias instâncias, ou pode ser implantado de forma que haja uma instância do serviço em cada nó do cluster do Service Fabric.

O parâmetro `InstanceCount` do cmdlet `New-ServiceFabricService` é usado para especificar quantas instâncias do serviço devem ser iniciadas no cluster do Service Fabric. Você pode definir o valor de `InstanceCount` dependendo do tipo de aplicativo que está implantando. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, somente uma instância do serviço será implantada no cluster. O agendador do Service Fabric determina em qual nó o serviço será implantado.

* `InstanceCount ="-1"`. Nesse caso, uma instância do serviço será implantada em cada nó do cluster do Service Fabric. O resultado final será ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Essa é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), pois aplicativos cliente precisam apenas se “conectar” a qualquer um dos nós do cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster do Service Fabric estão conectados a um balanceador de carga para que o tráfego do cliente possa ser distribuído pelo serviço em execução em todos os nós do cluster.

### Verificar seu aplicativo em execução

No Gerenciador do Service Fabric, identifique o nó em que o serviço está em execução. Neste exemplo, ele está executando no Node1:

![Nó em que o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se você navegar até o nó e procurar o aplicativo, verá as informações essenciais do nó, incluindo sua localização no disco.

![Local no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se você navegar até o diretório usando o Gerenciador de Servidores, poderá localizar o diretório de trabalho e a pasta de log do serviço, como mostrado abaixo.

![Local do log](./media/service-fabric-deploy-existing-app/loglocation.png)

## Uso do Visual Studio para empacotar um aplicativo existente

O Visual Studio fornece um modelo de serviço do Service Fabric para ajudar você a implantar um executável convidado em um cluster do Service Fabric. Você precisa passar pelo seguinte para concluir a publicação:

>[AZURE.NOTE] Este recurso requer o [SDK versão 2.1.150](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/13/release-of-service-fabric-sdk-2-1-150-and-runtime-5-1-150/)

1. Escolha Arquivo -> Novo Projeto e crie um novo Aplicativo do Service Fabric.
2. Escolha Executável Convidado como o Modelo de Serviço.
3. Clique em Procurar para escolher a pasta com o executável e preencha o restante dos parâmetros para criar o novo serviço.
  - O *Comportamento do Pacote de Códigos* pode ser definido para copiar todo o conteúdo da pasta para o projeto do Visual Studio, o que será útil se o executável não for alterado. Se você espera que o executável mude e se quiser a capacidade de obter novas compilações dinamicamente, poderá optar por vincular para a pasta.
  - *Programa* escolhe o nome do executável que deve ser executado para iniciar o serviço.
  - *Argumentos* especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
  - *WorkingFolder* especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar dois valores:
  	- *CodeBase* especifica o diretório de trabalho que será definido no diretório de código no pacote de aplicativos (diretório `Code` na estrutura mostrada abaixo).
    - *CodePackage* especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`MyServicePkg`).
4. Dê um nome ao seu serviço e clique em OK.
5. Se seu serviço precisar de um ponto de extremidade para comunicação, você poderá adicionar o Protocolo, a Porta e o Tipo ao arquivo ServiceManifest.xml (por exemplo): ```<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />```.
6. Agora você pode experimentar o pacote e publicar a ação em seu cluster local ao depurar a solução no Visual Studio. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para controle de origem.

>[AZURE.NOTE] Você pode usar pastas vinculadas ao criar o projeto de aplicativo no Visual Studio. Isso vinculará ao local de origem de dentro do projeto, tornando possível a atualização do convidado executável em seu destino de origem, fazendo com que essas atualizações se tornem parte do pacote de aplicativo na compilação.

## Próximas etapas
Neste artigo, você aprendeu como empacotar um executável convidado e implantá-lo à Service Fabric. Como uma próxima etapa, confira o conteúdo adicional deste tópico.

- [Amostra de empacotamento e implantação de um executável convidado no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluindo um link para o pré-lançamento da ferramenta de empacotamento
- [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)
- [Criar seu primeiro aplicativo do Service Fabric usando o Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0713_2016-->