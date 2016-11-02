<properties
   pageTitle="Implantar um executável existente à Service Fabric do Azure | Microsoft Azure"
   description="Instruções passo a passo sobre como empacotar um aplicativo existente como um executável convidado, para que ele possa ser implantado em um cluster do Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-a-guest-executable-to-service-fabric"></a>Implantar um executável convidado à Service Fabric

Você pode executar qualquer tipo de aplicativo, como o Node.js, o Java ou os aplicativos nativos no Service Fabric do Azure. O Service Fabric se refere a esses tipos de aplicativos como executáveis convidados.
Os executáveis convidados são tratados pela Service Fabric como serviços sem monitoração de estado. Como resultado, eles são colocados em nós em um cluster, com base na disponibilidade e em outras métricas. Este artigo descreve como empacotar e implantar um executável convidado em um cluster do Service Fabric usando o Visual Studio ou um utilitário de linha de comando.

Neste artigo, abordaremos as etapas para empacotar um convidado executável e implantá-lo no Service Fabric.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios de executar um executável convidado no Service Fabric

Há várias vantagens que vêm com a execução de um convidado executável em um cluster de Service Fabric:

- Alta disponibilidade. Os aplicativos que são executados no Service Fabric estão no modo de alta disponibilidade. O Service Fabric garante que as instâncias de um aplicativo estão em execução.
- Monitoramento de integridade. O monitoramento de integridade do Service Fabric detecta se um aplicativo está em execução e fornece informações de diagnóstico, caso haja uma falha.   
- Gerenciamento do ciclo de vida do aplicativo. Além de oferecer atualizações sem tempo de inatividade, o Service Fabric fornecerá reversão automática da versão anterior, se houver um evento de integridade deficiente durante uma atualização.    
- Densidade. Você pode executar vários aplicativos no cluster, o que elimina a necessidade um hardware próprio para a execução de cada aplicativo.


## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral do aplicativo e dos arquivos de manifesto do serviço

Como parte da implantação de um executável convidado, é útil entender o modelo de implantação e empacotamento do Service Fabric, conforme descrito no [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento do Service Fabric depende de dois arquivos XML: o aplicativo e o manifesto do serviço. A definição de esquema dos arquivos ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK do Service Fabric em *C:\Arquivos de Programas\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** O manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que o compõem e os outros parâmetros usados para definir como os serviços devem ser implantados, como o número de instâncias.

  No Service Fabric, um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma única unidade em que falhas e reversões potencias são gerenciadas. O Service Fabric garante que o processo de atualização será completamente bem-sucedido ou, se a atualização falhar, ele não deixará o aplicativo em um estado desconhecido/instável.

* **Manifesto do serviço** O manifesto do serviço descreve os componentes de um serviço. Ele inclui informações, como o nome, o tipo e o código do serviço, a configuração e os dados. O manifesto do serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação.


## <a name="application-package-file-structure"></a>Estrutura de arquivos do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, o aplicativo precisa seguir uma estrutura de diretórios predefinida. Veja a seguir um exemplo dessa estrutura.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o arquivo ApplicationManifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos exigidos pelo serviço – o ServiceManifest.xml e, normalmente, os três diretórios a seguir:

- *Código*. Esse diretório contém o código de serviço.
- *Config*. Esse diretório contém o arquivo settings.xml (e outros arquivos, se necessário) que o serviço pode acessar no tempo de execução para recuperar definições de configuração específicas.
- *Dados*. Esse é um diretório adicional para armazenar dados locais adicionais que podem ser necessário para o serviço. Observação: os dados devem ser usados para armazenar somente dados efêmeros. O Service Fabric não copia/replica alterações de diretório de dados se o serviço precisar ser realocado – por exemplo, durante o failover.

Observação: você não precisa criar os diretórios `config` e `data` se não precisar deles.

## <a name="packaging-an-existing-executable"></a>Empacotamento de um executável existente

Ao empacotar um executável convidado, você pode optar por usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativos manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados pelo assistente de novo projeto para você.

>[AZURE.NOTE] A maneira mais fácil de empacotar um executável existente do Windows em um serviço é usar o Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Uso do Visual Studio para empacotar um aplicativo executável

O Visual Studio fornece um modelo de serviço do Service Fabric para ajudar você a implantar um executável convidado em um cluster do Service Fabric.

Siga as próximas etapas para concluir a publicação:

1. Escolha Arquivo -> Novo Projeto e crie um Aplicativo do Service Fabric.
2. Escolha Executável Convidado como o Modelo de Serviço.
3. Clique em Procurar para escolher a pasta com o executável e preencha o restante dos parâmetros para criar o serviço.
    - *Comportamento do Pacote de Códigos* pode ser definido para copiar todo o conteúdo da pasta para o Projeto do Visual Studio, o que será útil se o executável não for alterado. Se você espera que o executável mude e se quiser a capacidade de obter novas compilações dinamicamente, poderá optar por vincular para a pasta. Observe que você pode usar pastas vinculadas ao criar o projeto de aplicativo no Visual Studio. Este processo cria um vínculo com a localização de origem de dentro do projeto, tornando possível a atualização do convidado executável em seu destino de origem, fazendo com que essas atualizações se tornem parte do pacote de aplicativos no build.
    - *Programa* – Escolha o executável que deve ser executado para iniciar o serviço.
    - *Argumentos* – Especifique os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
    - *WorkingFolder* – Especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
        - `CodeBase` especifica o diretório de trabalho que será definido no diretório de código no pacote de aplicativos (diretório `Code` na estrutura de arquivos mostrada anteriormente).
        - `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` na estrutura de arquivos mostrada anteriormente).
        - `Work` especifica que os arquivos serão colocados em um subdiretório chamado trabalho
4. Dê um nome ao seu serviço e clique em OK.
5. Se seu serviço precisar de um ponto de extremidade para comunicação, agora você poderá adicionar o Protocolo, a Porta e o Tipo ao arquivo ServiceManifest.xml. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora você pode usar o pacote e publicar a ação em seu cluster local ao depurar a solução no Visual Studio. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para controle de origem.
7. Vá para o final deste artigo para conferir como exibir seu serviço executável de convidado em execução no Service Fabric Explorer.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Empacotamento e implantação manual de um executável existente
O processo de empacotamento manual de um executável convidado baseia-se nas seguintes etapas:

1. Criar a estrutura de diretórios do pacote.
2. Adicionar os arquivos de configuração e código do aplicativo.
3. Editar o arquivo de manifesto do serviço.
4. Editar o arquivo de manifesto do aplicativo.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretórios do pacote
Você pode começar criando a estrutura de diretórios como descrito anteriormente.

### <a name="add-the-application's-code-and-configuration-files"></a>Adicionar os arquivos de configuração e código do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar os arquivos de configuração e código do aplicativo aos diretórios de code e config. Também é possível criar diretórios adicionais ou subdiretórios nos diretórios code ou config.

O Service Fabric faz uma cópia do conteúdo do diretório raiz do aplicativo, portanto, não há estrutura predefinida a usar que não criar dois diretórios principais, código e configurações. (Você pode escolher nomes diferentes, se desejar. Mais detalhes estão na próxima seção.)

>[AZURE.NOTE] Certifique-se de incluir todos os arquivos/dependências de que o aplicativo precisa. O Service Fabric copia o conteúdo do pacote de aplicativos em todos os nós do cluster nos quais os serviços do aplicativo serão implantados. O pacote deve conter todos os códigos que o aplicativo precisa para ser executado. Não recomendamos supor que as dependências já estão instaladas.

### <a name="edit-the-service-manifest-file"></a>Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de manifesto do serviço para incluir as seguintes informações:

- O nome da fila do tipo de serviço. Esse é um ID que o Service Fabric usa para identificar um serviço.
- O comando a ser usado para iniciar o aplicativo (ExeHost).
- Qualquer script que precisa ser executado para configurar o aplicativo (SetupEntrypoint).

Segue um exemplo de um arquivo `ServiceManifest.xml` :

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

#### <a name="updating-the-servicetypes"></a>Atualização dos ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Você pode escolher qualquer nome que desejar para `ServiceTypeName`. O valor é usado no arquivo `ApplicationManifest.xml` para identificar o serviço.
- Você precisa especificar `UseImplicitHost="true"`. Esse atributo informa ao Service Fabric que o serviço se baseia em um aplicativo independente, de modo tudo o que o Service Fabric precisa fazer é iniciá-lo como um processo e monitorar a integridade.

#### <a name="updating-the-codepackage"></a>Atualização do CodePackage
O elemento CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O elemento `Name` é usado para especificar o nome do diretório no pacote de aplicativos que contém o código do serviço. `CodePackage` também tem o atributo `version`. Isso pode ser usado para especificar a versão do código – e também para atualizar o código do serviço usando a infraestrutura de gerenciamento de ciclo de vida de aplicativo do Service Fabric.
#### <a name="optional:-updating-the-setupentrypoint"></a>Opcional: atualização do SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntrypoint é usado para especificar qualquer executável ou arquivo em lotes que deve ser executado antes da inicialização do código do serviço. Trata-se de uma etapa opcional, de modo que ela não precisará ser incluída, se não houver necessidade de inicialização/configuração. O SetupEntrypoint é executado toda vez que o serviço é reiniciado.

Há apenas um SetupEntrypoint, de modo que os scripts de instalação/configuração precisarão ser agrupados em um único arquivo em lotes, se a instalação/configuração do aplicativo exigir vários scripts. O SetupEntrypoint pode executar qualquer tipo de arquivo: arquivos executáveis, arquivos em lotes e cmdlets do PowerShell. Para obter mais detalhes, confira [Configure SetupEntryPoint](service-fabric-application-runas-security.md)(Configurar SetupEntryPoint).

No exemplo anterior, o SetupEntrypoint executa um arquivo em lote chamado `LaunchConfig.cmd`, que está localizado no subdiretório `scripts` do diretório de código (supondo que o elemento WorkingFolder esteja definido como CodeBase).

#### <a name="updating-the-entrypoint"></a>Atualização do Entrypoint

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

- `Program` especifica o nome do executável que deve ser executado para iniciar o serviço.
- `Arguments` especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
- `WorkingFolder` especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
    - `CodeBase` especifica que o diretório de trabalho será definido no diretório de código no pacote de aplicativos (diretório `Code` na estrutura de arquivos anterior).
    - `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` na estrutura de arquivos anterior).
  - `Work` especifica que os arquivos serão colocados em um subdiretório chamado trabalho

A WorkingFolder é útil para definir o diretório de trabalho correto, de modo que os caminhos relativos possam ser usados pelo aplicativo ou pelos scripts de inicialização.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Atualização dos pontos de extremidade e registro no Serviço de Cadastramento para comunicação

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior, o elemento `Endpoint` especifica os pontos de extremidade nos quais o aplicativo pode escutar. Neste exemplo, o aplicativo Node.js escuta ao http na porta 3000.

Além disso, você pode solicitar ao Service Fabric para publicar este ponto de extremidade no Serviço de Cadastramento, de modo que outros serviços possam descobrir o endereço do ponto de extremidade deste serviço. Isso permite que você se comunique entre os serviços que são executáveis de convidado.
O endereço do ponto de extremidade publicado está no formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN` é o Endereço IP ou o Nome de Domínio Totalmente Qualificado do nó em que este executável será colocado e calculado para você.

No exemplo a seguir, quando o serviço for implantado no Service Fabric Explorer, você verá um ponto de extremidade semelhante ao `http://10.1.4.92:3000/myapp/` publicado na instância de serviço ou, se for um computador local, você verá `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Você pode usar estes endereços com o [proxy reverso](service-fabric-reverseproxy.md) para se comunicar entre os serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o arquivo de manifesto do aplicativo

Depois de configurar o arquivo `Servicemanifest.xml`, você deve fazer algumas alterações ao arquivo `ApplicationManifest.xml` para garantir o uso do tipo e do nome de serviço corretos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

No elemento `ServiceManifestImport` , você pode especificar um ou mais serviços para incluí-los no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório em que o arquivo `ServiceManifest.xml` está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar registro em log
Para executáveis convidados, é útil poder ver os logs do console para descobrir se o aplicativo e os scripts de configuração mostram algum erro.
O redirecionamento do console pode ser configurado no arquivo `ServiceManifest.xml` usando o elemento `ConsoleRedirection`.

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
    * `FileMaxSizeInKb` especifica o tamanho máximo dos arquivos de log.

Arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para indicar o local em que os arquivos estão localizados, você precisa usar o Service Fabric Explorer para determinar em qual nó o serviço está sendo executado e qual diretório de trabalho está sendo usado. Esse processo é abordado mais adiante neste artigo.

## <a name="deployment"></a>Implantação
A última etapa será implantar seu aplicativo. O script do PowerShell a seguir mostra como implantar seu aplicativo no cluster de desenvolvimento local e iniciar um novo serviço do Service Fabric.

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

* `InstanceCount = "1"`. Neste caso, somente uma instância do serviço é implantada no cluster. O agendador do Service Fabric determina em qual nó o serviço será implantado.

* `InstanceCount ="-1"`. Neste caso, uma instância do serviço é implantada em cada nó do cluster do Service Fabric. O resultado é ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), pois os aplicativos cliente precisam se “conectar” com qualquer um dos nós do cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster do Service Fabric estão conectados a um balanceador de carga para que o tráfego do cliente possa ser distribuído pelo serviço em execução em todos os nós do cluster.

## <a name="check-your-running-application"></a>Verificar seu aplicativo em execução

No Gerenciador do Service Fabric, identifique o nó em que o serviço está em execução. Neste exemplo, ele está executando no Node1:

![Nó em que o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar até o nó e procurar o aplicativo, você verá as informações essenciais do nó, incluindo sua localização no disco.

![Local no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se navegar até o diretório usando o Gerenciador de Servidores, você poderá localizar o diretório de trabalho e a pasta de log do serviço, como mostra a figura a seguir.

![Local do log](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como empacotar um executável convidado e implantá-lo à Service Fabric. Como uma próxima etapa, confira o conteúdo adicional deste tópico.

- [Amostra de empacotamento e implantação de um executável convidado no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluindo um link para o pré-lançamento da ferramenta de empacotamento
- [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)
- [Criar seu primeiro aplicativo do Service Fabric usando o Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)



<!--HONumber=Oct16_HO2-->


