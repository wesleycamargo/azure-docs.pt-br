<properties
   pageTitle="Node.js e atores confiáveis | Microsoft Azure"
	description="Uma introdução passo a passo sobre como criar um aplicativo express do Node.js que usa Atores Confiáveis e é executado na plataforma da Service Fabric do Azure."
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
	ms.date="08/17/2015"
	ms.author="claudioc"/>


# Node.js e atores confiáveis: uma combinação vencedora
Este artigo é uma visão geral de como você pode criar um aplicativo que usa o Node.js e atores confiáveis. A solução final é uma mistura do código javascript usado principalmente para fornecer o lado de front-end do aplicativo (APIs Rest/Web) e C# para computação mais complexa. Aproveitando o modelo de programação deda Service Fabric, o aplicativo é escalonável e confiável pronto para uso. O processo consta das seguintes etapas:

1. criar um novo projeto de ator com ou sem estado da Service Fabric usando as ferramentas da Service Fabric para Visual Studio
2. criar um projeto de Node.js usando, por exemplo [ferramentas do Node.js para Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC) 
3. Adicionar um projeto do Node.js (por exemplo, um aplicativo do basic express 4) para a solução da Service Fabric. Você pode simplesmente usar a solução/Adicionar projeto existente para incluir o projeto do Node.js. 
4. Empacotar o aplicativo do Node.js para que possa ser implantado usando ferramentas VS para a Service Fabric

## criar o projeto do Node.js
Após criar o projeto do Node.js e adicionar as dependências, você precisa alterar a estrutura do diretório do projeto de modo que ele siga a estrutura que as ferramentas da Service Fabric para o Visual Studio requerem para empacotar e implantar o aplicativo como qualquer outro serviço da Service Fabric. O objetivo é fazer as alterações na estrutura de diretório somente uma vez, de modo que você possa beneficiar-se de ser capaz de usar o processo de implantação no Visual Studio e, portanto, implante seu aplicativo do Node.js junto com outros serviços na solução. As alterações que precisam ser feitas para a estrutura do diretório são:

1. Criar um diretório PackageRoot
2. Criar um diretório de código em PackageRoot
3. Mover todos os arquivos e diretórios sob o diretório do código

Após concluir, a estrutura do projeto no Visual Studio deve ser semelhante ao seguinte:

![][8]

Como você pode ver, todo o código do Node.js está no diretório PackageRoot/Code. O Service Fabric não pressupõe nada sobre os aplicativos/bibliotecas instaladas no nó onde o aplicativo será implantado, de modo que você precisa incluir todas as dependências. No caso do Node.js, você precisa incluir o node.exe para que a Service Fabric possa executar o código (você pode encontrar o node.exe no diretório arquivo de programas\\nodejs).

![][3]

## Adicionar o arquivo de metadados servicemanifest.xml
Para implantar o aplicativo do Node.js, precisamos adicionar um arquivo de metadados que é necessário para especificar algumas propriedades que serão usadas pela Service Fabric para determinar como implantar e iniciar o aplicativo.

Este é um exemplo de como deve ser a aparência do servicemanifest.xml, consulte [neste artigo](service-fabric-deploy-existing-app.md) para obter mais detalhes sobre quais são os elementos importantes que precisam ser atualizados mas normalmente você precisa atualizar:

* Nome (elemento ServiceManifest)
* ServiceTypeName (elemento StatelessServiceType)
* Argumentos (elemento ExeHost) para especificar o arquivo js que deve ser usado para iniciar o aplicativo.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
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
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Observe que uma das opções do servicemanifest.xml pode não funcionar com o Node.js. Em alguns casos, `ConsoleRedirection` não funciona se um módulo do Node.js (por exemplo, Express) pressupõe que o fd para o stdout e stderr são 1 e 2.

Após adicionar o arquivo `servicemanifest.xml` ao projeto do Node.js, a estrutura do projeto deve ter esta aparência:

![][4]

## Adicionar os binários da Service Fabric
A próxima etapa é adicionar os binários da Service Fabric que são usados para conectar-se com os Atores em execução no cluster da Service Fabric. Como você pode ver no exemplo Edge.js, há algumas referências para assemblies. Para disponibilizar esses assemblies para edge.js, eles precisam ser copiados com o código do Node.js. A maneira mais fácil é copiar os binários de um projetos existente, os arquivos que precisam ser incluídos no diretório do código (e serão usados pelo edge.js) são os seguintes:

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

Após adicionar os binários ao projeto, a estrutura do projeto deve ter a aparência a seguir:

![][5]

## Adicione uma referência ao projeto Node.js no arquivo applicationmanifest
A próxima etapa é adicionar o serviço do Node.js ao manifesto do aplicativo, de modo que possa ser implantado usando as ferramentas do Visual Studio para Service Fabric. Isso é necessário porque não há nenhuma integração nas ferramentas da Service Fabric para o Visual Studio com o projeto do Node.js, portanto, ele precisa ser adicionado manualmente.

No arquivo `applicationmanifest.xml`, você precisa adicionar os seguintes elementos:

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Verifique se está usando os mesmos nomes que você usou no `servicemanifest.xml` para especificar `ServiceName` e `ServiceTypeName`. o arquivo `applicationmanifest.xml` deverá ser semelhante ao seguinte:

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Uso de atores confiáveis no aplicativo do Node.js
Agora que a estrutura do projeto está definida, podemos nos concentrar no código que permite que o aplicativo do Node.js se conecte aos Atores Confiáveis no cluster como você pode fazer em um aplicativo .NET. O cenário que desejamos habilitar é para compilar um aplicativo do Node.js que aja como front-end/gateway para o aplicativo cliente e expor um aplicativo baseado na web ou um conjunto de APIs rest que um aplicativo possa usar. O Node.js fornece o front-end do aplicativo enquanto Atores Confiáveis fornecem camadas escalonáveis e confiáveis do 'servidor de aplicativos' do aplicativo. Na Service Fabric, os Atores Confiáveis podem ser invocados por meio da [classe ActorProxy](service-fabric-reliable-actors-introduction.md#actor-communication). Por sorte, há um módulo Node.js excelente que pode ser usado para chamar código .NET: [Edge.js](https://github.com/tjanczuk/edge). Você pode usar as instruções no repositório github para descobrir como instalar a borda na sua máquina.

 
![][2]

Após instalar a borda usando NPM ou IU NPM no Visual Studio, você precisa mover o novo módulo instalado no diretório node\_modules no subdiretório do Código (a estrutura do projeto Node.js foi alterada e todos o códigos no diretório PackageRoot/Code foram movidos). É possível encontrar bons exemplos no repositório edge.js no Github sobre como pode usar a borda para chamar o código .NET, a seguir há um exemplo simples de como é a aparência do código quando você precisa chamar Atores Confiáveis usando a classe ActorProxy.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]O ActorProxy usa a interface de Ator para saber qual é o ator com o qual deve-se conectar. Para que o código gerenciado em execução no processo do Node.js possa instanciar essa classe, o assembly da interface de Ator deve ser copiado no diretório do código como os outros dlls da Service Fabric. Observação: você precisará copiar (ou definir uma ação de pós-compilação no VS) o dll sempre que você faça alterações a métodos/parâmetros na interface.

![][6]

## Implantação
Neste ponto, o projeto pode ser implantado usando as ferramentas da Service Fabric para o Visual Studio. A última etapa do processo é referenciar o projeto no projeto de aplicativo da Service Fabric de modo que possa ser incluído no pacote de aplicativos e seja implantado no cluster.

![][9]
 
Você pode implantar o aplicativo (que incluirá o aplicativo do Node.js), usando, por exemplo, o menu de contexto da solução.

![][10]

## Próximas etapas
* Saiba mais sobre as [Atores Confiáveis](service-fabric-reliable-actors-introduction.md)
* Leia mais sobre o ciclo de vida do aplicativo da [Service Fabric](service-fabric-application-lifecycle.md).
* Leia mais sobre a atualização do [aplicativo da Service Fabric](service-fabric-application-upgrade.md). 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=August15_HO9-->