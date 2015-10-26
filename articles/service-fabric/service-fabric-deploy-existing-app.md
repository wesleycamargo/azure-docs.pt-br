<properties
   pageTitle="Implantar um aplicativo existente no Service Fabric do Azure | Microsoft Azure"
   description="Instruções passo a passo sobre como empacotar um aplicativo existente para que ele possa ser implantado em um cluster da Malha de Serviço do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Implantar um aplicativo existente na Malha de Serviço do Azure

O Service Fabric do Azure pode ser usada para implantar aplicativos existentes, permitindo que eles se beneficiem de monitoramento de integridade e gerenciamento de ciclo de vida de aplicativos (ALM).

Este tutorial explica o processo e os conceitos básicos envolvidos no empacotamento de um aplicativo existente para implantação em um cluster do Service Fabric.


## Visão geral resumida de arquivos de manifesto do serviço e do aplicativo

Antes de entrar em detalhes sobre a implantação de um aplicativo existente, é útil entender o modelo de implantação do Service Fabric. O modelo de implantação do Service Fabric depende basicamente de dois arquivos:


* **Manifesto do aplicativo**

  O manifesto do aplicativo é usado para descrever o aplicativo e listar os serviços que o compõem, além de outros parâmetros, como o número de instâncias, que são usados para definir como os serviços devem ser implantados no mundo da Malha de Serviço, um aplicativo é a 'unidade atualizável'. Um aplicativo pode ser atualizado como uma única unidade, em que as falhas potenciais (e reversões potenciais) são gerenciadas pela plataforma para garantir que o processo de atualização seja completamente bem-sucedido ou, no caso de falha, que ele não deixe o aplicativo em um estado desconhecido/instável.

  Este é um exemplo de manifesto do aplicativo:

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest> ```

* **Manifesto do serviço**

  O manifesto do serviço descreve os componentes de um serviço. Ele inclui dados como nome e tipo do serviço (informações que a Malha de Serviço usa para gerenciar o serviço), seu código, configuração e componentes de dados, além de alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação. Não vamos entrar em detalhes de todos os diferentes parâmetros disponíveis no manifesto do serviço; vamos observar o subconjunto que é exigido para executar um aplicativo existente na Malha de Serviço.

  Este é um exemplo de manifesto do serviço:

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest> ```

## Estrutura de arquivo do pacote de aplicativos
Para implantar um aplicativo usando os cmdlets do PowerShell, por exemplo, o aplicativo precisa seguir uma estrutura de diretório predefinida.

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

A raiz contém o arquivo applicationmanifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos exigidos pelo serviço: o servicemanifest.xml e, normalmente, 3 diretórios:

- *code*: contém o código do serviço
- *config*: contém um arquivo settings.xml (e outros arquivos, se necessário) que o serviço pode acessar no tempo de execução para recuperar definições específicas da configuração.
- *data*: um diretório adicional para armazenar dados locais adicionais que o serviço possa precisar. Observação: data deve ser usado para armazenar somente dados efêmeros. A Malha de Serviço não vai copiar/replicar alterações no diretório data se o serviço precisar ser realocado, por exemplo, durante o failover.

Observação: você pode usar qualquer nome de diretório arbitrário para Code, Config e Data. Certifique-se somente de usar o mesmo valor no arquivo ApplicationManifest.

## O processo de empacotamento de um aplicativo existente

O processo de empacotamento de um aplicativo existente baseia-se nas seguintes etapas:

- Criar a estrutura de diretório do pacote
- Adicionar arquivos de configuração e código do aplicativo
- Atualizar o arquivo de manifesto do serviço
- Atualizar o manifesto do aplicativo


### Criar a estrutura de diretório do pacote
Você pode começar criando a estrutura de diretório conforme descrito acima. Criei um diretório e copiei o manifesto do aplicativo e do serviço de um projeto existente que tinha criado anteriormente no Visual Studio.

![][1] ![][2]


### Adicionar os arquivos de configuração e código do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar os arquivos de configuração e código do aplicativo nos diretórios Code e Config. Também é possível criar diretórios adicionais ou subdiretórios nos diretórios Code ou Config. A Malha de Serviço faz uma cópia do conteúdo do diretório raiz do aplicativo, de modo que não há estrutura predefinida a ser usada, a não ser criar dois diretórios principais, Code e Settings (mas você pode escolher nomes diferentes se desejar; mais detalhes na próxima seção).

>[AZURE.NOTE]\: Certifique-se de incluir todos os arquivos/dependências que o aplicativo precisa. A Malha de Serviço copiará o conteúdo do pacote de aplicativos em todos os nós do cluster onde os serviços do aplicativo serão implantados. O pacote deve conter todos os códigos que o aplicativo precisa para ser executado. Não é recomendável supor que as dependências já estejam instaladas.

### Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de Manifesto do Serviço para incluir as seguintes informações:

- O nome da fila do tipo de serviço. Isso é um 'Id' que o Service Fabric usa para identificar um serviço
- O comando a ser usado para iniciar o aplicativo (ExeHost)
- Qualquer script que precisa ser executado para configurar o aplicativo (SetupEntrypoint

Este é um exemplo de um arquivo `servicemanifest.xnml` que “empacota” um aplicativo do node.js:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

Vamos verificar a parte diferente do arquivo que você precisa atualizar:

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- Você pode escolher qualquer nome desejado para `ServiceTypeName`; o valor é usado no `applicationmanifest.xml` para identificar o serviço.
- Você precisa especificar `UserImplicitHost = "true"`. Esse atributo informa à Malha de Serviço que o serviço se baseia em um aplicativo independente, de modo que ele só precisa iniciá-lo como um processo e monitorar a integridade.

### CodePackage
O CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O elemento `Name` é usado para especificar o nome do diretório no Pacote de Aplicativos que contém o código do serviço. `CodePackage` também tem o atributo `version`, que pode ser usado para especificar a versão do código e, possivelmente, ser usado para atualizar o código do serviço, utilizando a infraestrutura do ALM do Service Fabric.


### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


O elemento `Entrypoint` no arquivo de manifesto do serviço é usado para especificar como iniciar o serviço. O elemento `ExeHost` especifica o executável (e os argumentos) que deve ser usado para iniciar o serviço.

- `Program`: especifica o nome do executável que deve ser executado para iniciar o serviço.
- `Arguments`: especifica os argumentos que devem ser transmitidos para o executável. Pode ser uma lista de parâmetros com argumentos.
- `WorkingFolder`: especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar dois valores:
	- `CodeBase`: o diretório de trabalho será definido no diretório Code no pacote de aplicativos (diretório `Code` na estrutura mostrada abaixo)
	- `CodePackage`: o diretório de trabalho será definido como a raiz do pacote de aplicativos (`MyServicePkg`)
- O elemento `WorkingDirectory` é útil para definir o diretório de trabalho correto para que caminhos relativos possam ser usados pelos scripts de inicialização ou do aplicativo.

Também há outro valor que pode ser especificado para o elemento `WorkingFolder` (`Work`), mas ele não é muito útil para o cenário de execução de um aplicativo existente.


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### O ponto de entrada da configuração

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

O `SetupEntrypoint` é usado para especificar qualquer executável ou arquivo em lotes que deve ser executado antes da inicialização do código do serviço. Trata-se de um elemento opcional, de modo que ele não precisará ser incluído se não houver inicialização/configuração obrigatória. O ponto de entrada é executado toda vez que o serviço é reiniciado. Há apenas um SetupEntrypoint, de modo que os scripts de instalação/configuração precisam ser associados em um único arquivo em lotes se a instalação/configuração do aplicativo exigir vários scripts. Assim como o elemento `Entrypoint`, `SetupEntrypoint` pode executar qualquer tipo de arquivo: executável, arquivos em lotes e cmdlet do PowerShell. No exemplo acima, `SetupEntrypoint` se baseia em um arquivo em lotes, myAppsetup.cmd, que está localizado no subdiretório dos scripts do diretório Code (supondo que o elemento `WorkingDirectory` esteja definido como `Code`).

## Arquivo de manifesto do aplicativo

Depois de configurar o arquivo `servicemanifest.xml`, você precisa fazer algumas alterações ao arquivo `applicationmanifest.xml` para garantir que o tipo e o nome de Serviço corretos sejam usados.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

No `ServiceManifestImport`, é possível especificar um ou mais serviços que você deseja incluir no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório em que o arquivo `servicemanifest.xml` está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

O elemento `DefaultServices` no arquivo de manifesto do aplicativo é usado para definir algumas propriedades do serviço.

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName` é usado como uma “ID” para o serviço. No contexto de portabilidade de um aplicativo existente, `ServiceTypeName` precisa apenas ser um identificador exclusivo para o serviço.
* `StatelessService`: o Service Fabric dá suporte a dois tipos de serviço: com e sem estado. No caso de portabilidade de um aplicativo existente, o serviço é um serviço sem estado, portanto, `StatelessService` sempre deve ser usado.

Um serviço da Malha de Serviço pode ser implantado em várias 'configurações'. Por exemplo, ele pode ser implantado como uma única ou várias instâncias, ou pode ser implantado de modo que haja uma instância do serviço em cada nó do cluster da Malha de Serviço. No arquivo `applicationmanifest.xml`, é possível especificar como você deseja que o aplicativo seja implantado.

* `InstanceCount`: é usado para especificar quantas instâncias do serviço devem ser iniciadas no cluster do Service Fabric. É possível definir o valor de `InstanceCount` de acordo com o tipo de aplicativo que está sendo implantado. Os dois cenários mais comuns são:

	* `InstanCount = "1"`: nesse caso, somente uma instância do serviço será implantada no cluster. O agendador da Malha de Serviço determina em qual nó o serviço será implantado. A contagem de uma única instância também faz sentido para aplicativos que exigem uma configuração diferente, caso sejam executados em várias instâncias. Nesse caso, é mais fácil definir vários serviços no mesmo arquivo de manifesto do aplicativo e usar `InstanceCount = "1"`. Assim, o resultado final terá várias instâncias do mesmo serviço, mas cada uma com uma configuração específica. Um valor de `InstanceCount` maior que um fará sentido apenas se a meta for ter várias instâncias exatamente da mesma configuração.

	* `InstanceCount ="-1"`: nesse caso, uma instância do serviço será implantada em cada nó do cluster do Service Fabric. O resultado final será ter uma (e apenas uma) instância do serviço para cada nó no cluster. Essa é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), pois aplicativos cliente precisam apenas se 'conectar' a qualquer um dos nós do cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster da Malha de Serviço estiverem conectados a um balanceador de carga para que o tráfego do cliente possa ser distribuído pelo serviço em execução em todos os nós do cluster.


### Testando
Para um aplicativo existente, é muito útil poder ver logs do console para descobrir se os scripts de configuração e aplicativo não mostram algum erro. O redirecionamento do console pode ser configurado no arquivo `servicemanifest.xml` usando o elemento `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` pode ser usado para redirecionar as saídas do console (stdout e stderr) para um diretório de trabalho, para que elas possam ser usadas para verificar se não há nenhum erro durante a instalação ou a execução do aplicativo no cluster do Service Fabric.

	* `FileRetentionCount` determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log das 5 execução anteriores são armazenados no diretório de trabalho.
	* `FileMaxSizeInKb` especifica o tamanho máximo dos arquivos de log.

Os arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para determinar onde os arquivos estão localizados, use o Gerenciador da Malha de Serviço e descubra em qual nó o serviço está em execução e qual é o diretório de trabalho que está sendo usado no momento.

No Gerenciador da Malha de Serviço, identifique o nó em que o serviço está em execução.

![][3]

Depois de descobrir em qual nó o serviço está em execução no momento, você poderá descobrir qual é o diretório de trabalho usado.

![][4]

Quando você seleciona o nome do serviço, no painel direito, é possível ver onde o código e as configurações do serviço estão armazenados.

![][5]

Ao clicar no link do campo 'Disk Location', você pode acessar o diretório onde o serviço está em execução.

![][6]

O diretório log contém todos os arquivos de log.

Observação: esse exemplo mostra o caso de uma única instância do serviço em execução no cluster. Se houver várias instâncias, talvez você precise verificar o arquivo de log em todos os nós em que o serviço está em execução.


## O que vem a seguir
Estamos trabalhando em uma ferramenta que pode ser usada para empacotar um aplicativo existente simplesmente apontando para ele na raiz da estrutura de diretório do aplicativo. A ferramenta gerencia os arquivos de manifesto e configura as definições básicas que são exigidas para 'transformar' o aplicativo em um serviço da Malha de Serviço.

Veja [isso](service-fabric-develop-your-service-index.md) se desejar obter mais informações sobre como desenvolver um aplicativo tradicional do Service Fabric.

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO3-->