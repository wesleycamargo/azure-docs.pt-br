<properties
   pageTitle="Comunicação segura para serviços no Service Fabric | Microsoft Azure"
   description="Visão geral de como proteger a comunicação para serviços confiáveis executados em um cluster do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# Comunicação segura para serviços no Service Fabric
A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar. Este artigo abordará como configurar a segurança ao usar a comunicação remota do serviço e a pilha de comunicação do WCF.

## Protegendo o serviço ao usar a comunicação remota do serviço
A configuração da comunicação remota segura para um serviço é feita usando as seguintes etapas:

1. Vamos usar um [exemplo](service-fabric-reliable-services-communication-remoting.md) anterior que fala sobre como configurar a comunicação remota de serviço confiável. Começamos com uma interface `IHelloWorldStateful` que define os métodos que serão disponibilizados para uma chamada de procedimento remoto no seu serviço. E o serviço usará um `FabricTransportServiceRemotingListener`, que é declarado no namespace `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Esta é uma implementação de `ICommunicationListener` que fornece recursos de comunicação remota.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Adição de credenciais de segurança e configurações de ouvinte.

    Espera-se que o certificado que você deseja usar para proteger a comunicação do serviço seja instalado em todos os nós do cluster. Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança.

    1. Diretamente no código do serviço.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Usando o [Pacote de Configuração](service-fabric-application-model.md)

        Adicione uma seção `TransportSettings` em settings.xml

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Nesse caso, o método `CreateServiceReplicaListeners` terá esta aparência.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Se você adicionar uma seção `TransportSettings` a settings.xml sem nenhum prefixo, `FabricTransportListenerSettings` carregará por padrão todas as configurações dessa seção.

         ```xml
         <!--"TransportSettings" section without any prefix-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Nesse caso, o método `CreateServiceReplicaListeners` terá esta aparência.

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Ao chamar métodos em um serviço seguro usando a pilha de comunicação remota, em vez de usar a classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` para criar um proxy de serviço, usamos `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` e passamos `FabricTransportSettings`, que contém `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o código do cliente estiver sendo executado como parte de um serviço, você poderá carregar `FabricTransportSettings` de settings.xml. Crie uma seção TransportSettings semelhante ao código de serviço, conforme mostrado acima. Faça as alterações a seguir no código do cliente.

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não estiver sendo executado como parte de um serviço, você poderá criar um arquivo client\_name.settings.xml no mesmo local em que client\_name.exe está e criar uma seção TransportSettings nesse arquivo.

    Semelhante ao serviço, em settings.xml/client\_name.settings.xml do cliente, se você adicionar uma seção `TransportSettings` sem qualquer *prefixo*, `FabricTransportSettings` carregará por padrão todas as configurações dessa seção.

    Nesse caso, o código acima é ainda mais simplificado.

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## Protegendo o serviço ao usar a pilha de comunicação baseada no WCF

Vamos usar um [exemplo](service-fabric-reliable-services-communication-wcf.md) anterior que explica como configurar uma comunicação baseada no WCF para serviços confiáveis e estendê-la para torná-la segura.

1. Para o serviço, precisamos criar um `WcfCommunicationListener` que seja seguro. Para isso, precisamos modificar o método `CreateServiceReplicaListeners`.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. No cliente, a classe `WcfCommunicationClient` que criamos no [exemplo](service-fabric-reliable-services-communication-wcf.md) anterior permanece inalterada. Mas precisamos substituir o método `CreateClientAsync` de `WcfCommunicationClientFactory`.

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Use `SecureWcfCommunicationClientFactory` para criar um `WcfCommunicationClient`. Use o cliente para invocar métodos de serviço.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## Próximas etapas

* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!-----------HONumber=AcomDC_0330_2016-->