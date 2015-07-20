<properties 
   pageTitle="Tutorial do REST para Barramento de Serviço"
   description="Compile um aplicativo host simples de Barramento de Serviço que expõe uma interface baseada em REST."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/07/2015"
   ms.author="sethm" />

# Tutorial do REST para Barramento de Serviço

Este tópico descreve como compilar um aplicativo host simples de Barramento de Serviço que expõe uma interface baseada em REST. O REST permite que um cliente da Web, como, por exemplo, um navegador da Web, acesse a API de Barramento de Serviço por meio de solicitações HTTP.

Este tutorial usa o modelo de programação REST do Windows Communication Foundation (WCF) para construir um serviço REST no Barramento de Serviço. Para saber mais, confira [Modelo de programação REST WCF](https://msdn.microsoft.com/library/bb412169.aspx) e [Criando e implementando serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

## Etapa 1: inscrever-se para uma conta do Azure

A primeira etapa é criar um namespace de serviço e obter uma chave de Assinatura de Acesso Compartilhado (SAS). Um namespace de serviço fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS é automaticamente gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e a chave SAS fornece as credenciais para o Barramento de Serviço autenticar o acesso a um aplicativo.

### Para criar um namespace de serviço e obter uma chave SAS

1. Para criar um namespace no portal de gerenciamento do Azure, execute as etapas em [Como: criar ou modificar um namespace do Barramento de Serviço](https://msdn.microsoft.com/library/hh690931.aspx).

2. Na janela principal do portal de gerenciamento do Azure, clique no nome do namespace de serviço que você criou na etapa anterior.

3. Clique em **Configurar** para exibir as políticas de acesso compartilhado para o namespace.

4. Anote a chave primária para a política **RootManageSharedAccessKey** ou copie-a na área de transferência. Você a usará mais tarde neste tutorial.

## Etapa 2: definir um contrato de serviço WCF baseado em REST para usar com o Barramento de Serviço

Assim como ocorre com outros serviços de Barramento de Serviço, quando você cria um serviço no estilo REST, é preciso definir o contrato. O contrato especifica para quais operações o host oferece suporte. Uma operação de serviço pode ser considerada um método de serviço Web. Os contratos são criados pela definição de uma interface C++, C# ou Visual Basic. Cada método na interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve ser aplicado a cada interface, e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve ser aplicado a cada operação. Se um método em uma interface que tem o [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), tal método não será exposto. O código usado para essas tarefas é mostrado no exemplo logo após o procedimento.

A principal diferença entre um contrato básico de Barramento de Serviço e um contrato no estilo REST é a adição de uma propriedade para o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Esta propriedade permite mapear um método em sua interface para um método no outro lado da interface. Nesse caso, usaremos [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) para vincular um método a HTTP GET. Isso permite que o Barramento de Serviço recupere e interprete de forma precisa os comandos enviados à interface.

### Para criar um contrato de Barramento de Serviço com uma interface

1. Abra o Visual Studio como administrador: clique com o botão direito no programa no menu Iniciar e, em seguida, clique em **Executar como administrador**.

2. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo** e, em seguida, **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outros Idiomas**), selecione o modelo **Aplicativo de Console** e chame-o de **ImageListener**. Use o **Local** padrão. Clique em **OK** para criar o projeto.

3. Para um projeto C#, o Visual Studio cria um arquivo `Program.cs`. Essa classe contém um método `Main()` vazio, necessário para um projeto de aplicativo de console ser criado corretamente.

4. Adicione uma referência a **System.ServiceModel.dll** ao projeto:
	1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Referências** na pasta do projeto e clique em **Adicionar Referência**. 
	2. Clique na guia **.NET** na caixa de diálogo **Adicionar Referência** e role para baixo até ver **System.ServiceModel**. Selecione-o e clique em **OK**.

5. Repita a etapa anterior para adicionar uma referência ao assembly **System.ServiceModel.Web.dll**.

6. Adicione instruções `using` para os namespaces **System.ServiceModel**, **System.ServiceModel.Channels**, **System.ServiceModel.Web** e **System.IO**.

	```c
  	using System.ServiceModel;
  	using System.ServiceModel.Channels;
  	using System.ServiceModel.Web;
  	using System.IO;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o namespace que permite o acesso programático aos recursos básicos do WCF. O Barramento de Serviço usa vários dos objetos e atributos do WCF para definir contratos de serviço. Você usará este namespace na maioria dos seus aplicativos de retransmissão do Barramento de Serviço. Da mesma forma, [System.ServiceModel.Channels](https://msdn.microsoft.com/pt-br/library/system.servicemodel.channels.aspx) ajuda a definir o canal, que é o objeto por meio do qual você se comunica com o Barramento de Serviço e o navegador da Web do cliente. Por fim, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contém os tipos que permitem a criação de aplicativos baseados na Web.

7. Renomeie o namespace para o programa alterando o padrão do Visual Studio para **Microsoft.ServiceBus.Samples**.

 	```c
	namespace Microsoft.ServiceBus.Samples
	{
		...
	```

8. Imediatamente após a declaração do namespace, defina uma nova interface chamada **IImageContract** e aplique o atributo **ServiceContractAttribute** à interface com um valor de `http://samples.microsoft.com/ServiceModel/Relay/`. O valor do namespace é diferente do namespace que você usa em todo o escopo do seu código. O valor do namespace é usado como um identificador exclusivo para este contrato e deve ter informações sobre a versão. Para saber mais, confira [Controle de versão do serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Especificar o namespace de forma explícita impede a adição do valor de namespace padrão ao nome do contrato.

	```c
	[ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
	public interface IImageContract
	{
	}
	```

9. Dentro da interface `IImageContract`, declare um método para a operação individual exposta pelo contrato `IImageContract` na interface e aplique o atributo `OperationContractAttribute` ao método que você deseja expor como parte do contrato de Barramento de Serviço público.

	```c
	public interface IImageContract
	{
		[OperationContract]
		Stream GetImage();
	}
	```

10. Ao lado do atributo **OperationContract**, aplique o atributo **WebGet**.

	```c
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
	```

	Isso permite que o Barramento de Serviço encaminhe as solicitações HTTP GET para **GetImage**, e converta os valores de retorno de **GetImage** em uma resposta HTTP GETRESPONSE. Posteriormente no tutorial, você usará um navegador da Web para acessar esse método e exibir a imagem no navegador.

11. Logo após a definição `IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`:
		
	```c
	[ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
  
	public interface IImageChannel : IImageContract, IClientChannel { }
	```

	Um canal é o objeto WCF por meio do qual o serviço e o cliente trocam informações. Posteriormente, você criará o canal em seu aplicativo host. O Barramento de Serviço usa este canal para passar as solicitações HTTP GET do navegador para a implementação **GetImage**. O Barramento de Serviço também usa o canal para obter o valor de retorno de **GetImage** e convertê-lo em uma GETRESPONSE HTTP para o navegador do cliente.

12. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão de seu trabalho até o momento.

### Exemplo

O exemplo de código a seguir mostra uma interface básica que define um contrato de Barramento de Serviço.

```c
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
	
namespace Microsoft.ServiceBus.Samples
{
	
    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
	
    public interface IImageChannel : IImageContract, IClientChannel { }
	
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Etapa 3: implementar um contrato de serviço WCF baseado em REST para usar no Barramento de Serviço

A criação de um serviço de Barramento de Serviço no estilo REST exige primeiro a criação do contrato, que é definido por meio de uma interface. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe chamada **ImageService** que implementa a interface **IImageContract** definida pelo usuário. Depois de implementar o contrato, configure a interface usando um arquivo App.config. O arquivo de configuração contém as informações necessárias para o aplicativo, como o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o Barramento de Serviço. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

Assim como nas etapas anteriores, há pouca diferença entre a implementação de um contrato no estilo REST e um contrato de Barramento de Serviço básico.

### Para implementar um contrato de Barramento de Serviço no estilo REST

1. Crie uma nova classe chamada **ImageService** diretamente após a definição da interface **IImageContract**. A classe **ImageService** implementa a interface **IImageContract**. 

	```c
	class ImageService : IImageContract
	{
	}
	```
	Assim como outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação é exibida no mesmo arquivo de definição de interface e no método `Main()`.

2. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à classe **IImageService** para indicar que a classe é uma implementação de um contrato do WCF:

	```c
	[ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class ImageService : IImageContract
	{
	}
	```

	Conforme mencionado anteriormente, esse namespace não é um namespace tradicional. Em vez disso, ele faz parte da arquitetura do WCF que identifica o contrato. Para saber mais, confira o tópico [Nomes de contrato de dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação do WCF.

3. Adicione uma imagem .jpg ao seu projeto.

	Esta é uma imagem exibida pelo serviço no navegador receptor. Clique com o botão direito do mouse em seu projeto e clique em **Adicionar**. Em seguida, clique em **Item Existente**. Use a caixa de diálogo **Adicionar Item Existente** para navegar até um .jpg adequado e clique em **Adicionar**.
    
	Ao adicionar o arquivo, certifique-se de que a opção **Todos os Arquivos** esteja selecionada na lista suspensa ao lado do campo **Nome do arquivo:**. O restante deste tutorial supõe que o nome da imagem seja "image.jpg". Se você tiver um arquivo diferente, será necessário renomear a imagem ou alterar o código para compensar.

4. Para ter certeza de que o serviço em execução consegue encontrar o arquivo de imagem, no Gerenciador de Soluções, clique com o botão direito do mouse no arquivo de imagem. No painel **Propriedades**, defina **Copiar para o Diretório de Saída** como **Copiar se for mais recente**.

5. Adicione referências aos assemblies **System.Drawing.dll**, **System.Runtime.Serialization.dll** e **Microsoft.ServiceBus.dll** para o projeto, e adicione também as seguintes instruções `using` associadas:

	```c
	using System.Drawing;
	using System.Drawing.Imaging;
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Web;
	```

6. Na classe **ImageService**, adicione o seguinte construtor que carrega o bitmap e o prepara para envio ao navegador do cliente:

	```c
	class ImageService : IImageContract
	{
		const string imageFileName = "image.jpg";
  
		Image bitmap;
  
		public ImageService()
		{
			this.bitmap = Image.FromFile(imageFileName);
		}
	}
	```

7. Logo após o código anterior, adicione o seguinte método **GetImage** à classe **ImageService** para retornar uma mensagem HTTP que contém a imagem:

	```c
	public Stream GetImage()
	{
		MemoryStream stream = new MemoryStream();
		this.bitmap.Save(stream, ImageFormat.Jpeg);
  
		stream.Position = 0;
		WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
  
		return stream;
	}
	```
  
	Essa implementação usa **MemoryStream** para recuperar a imagem e prepará-la para transmissão por streaming para o navegador. Ela começa a posição da transmissão em zero, declara o conteúdo da transmissão como jpeg e transmite as informações.

8. No menu **Compilar**, clique em **Solução de Compilação**.

### Para definir a configuração a fim de executar o serviço da Web no Barramento de Serviço

1. Clique com o botão direito do mouse no projeto **ImageListener**. Em seguida, clique em **Adicionar** e em **Novo Item**.

2. No Gerenciador de Soluções, clique duas vezes em **App.config**, que atualmente contém os seguintes elementos XML:

	```xml
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	</configuration>
	```
  
	O arquivo de configuração é semelhante a um arquivo de configuração do WCF e inclui o nome do serviço, o ponto de extremidade (ou seja, o local que o Barramento de Serviço expõe para os clientes e hosts se comunicarem) e a associação (o tipo de protocolo usado para comunicação). A principal diferença é que o ponto de extremidade de serviço configurado refere-se a uma associação [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx), que não faz parte do .NET Framework. Para saber mais sobre como configurar um aplicativo do Barramento de Serviço, confira [Configurando um serviço WCF para registrar com o Barramento do Serviço](https://msdn.microsoft.com/library/ee173579.aspx).
  

3. Adicione um elemento XML `<system.serviceModel>` ao arquivo App.config. Este é um elemento do WCF que define um ou mais serviços. Aqui, ele é usado para definir o nome do serviço e o ponto de extremidade.
  
	```xml
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<system.serviceModel>
      
		</system.serviceModel>
    
	</configuration>
	```

4. No elemento `system.serviceModel`, adicione um elemento `<bindings>` que tem o seguinte conteúdo. Isso define as associações usadas no aplicativo. É possível definir várias associações, mas para este tutorial você definirá apenas uma.
  
	```xml
	<bindings>
		<!-- Application Binding -->
		<webHttpRelayBinding>
			<binding name="default">
				<security relayClientAuthenticationType="None" />
			</binding>
		</webHttpRelayBinding>
	</bindings>
	```
  
	Esta etapa define um associação [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) do Barramento de Serviço com **relayClientAuthenticationType** definido como **Nenhum**. Essa configuração indica que um ponto de extremidade usando essa associação não exige uma credencial de cliente.

5. Após o elemento `<bindings>`, adicione um elemento `<services>`. Assim como nas associações, você pode definir vários serviços em um único arquivo de configuração. No entanto, para este tutorial, você definirá apenas um.
  
	```xml
	<services>
		<!-- Application Service -->
		<service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
			<endpoint name="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IImageContract"
					binding="webHttpRelayBinding"
					bindingConfiguration="default"
					behaviorConfiguration="sbTokenProvider"
					address="" />
		</service>
	</services>
	```
  
	Esta etapa configura um serviço que usa o padrão previamente definido **webHttpRelayBinding**. Ele também usa o padrão **sbTokenProvider**, definido na próxima etapa.

6. Após o elemento `<services>`, crie um elemento `<behaviors>` com o conteúdo a seguir, substituindo "SAS_KEY" pela chave *Assinatura de Acesso Compartilhado* (SAS) obtida no portal de gerenciamento do Azure na Etapa 1.
  
	```xml
	<behaviors>
		<endpointBehaviors>
			<behavior name="sbTokenProvider">
				<transportClientEndpointBehavior>
					<tokenProvider>
						<sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
					</tokenProvider>
				</transportClientEndpointBehavior>
			</behavior>
			</endpointBehaviors>
			<serviceBehaviors>
				<behavior name="default">
					<serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
				</behavior>
			</serviceBehaviors>
	</behaviors>
	```
  
7. No menu **Compilar**, clique em **Compilar Solução** para compilar a solução inteira.

### Exemplo

O código a seguir mostra o contrato e a implementação do serviço para um serviço baseado em REST que está em execução no Barramento de Serviço usando a associação **WebHttpRelayBinding**.

```c
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{
    

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }    
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo a seguir mostra o arquivo App.config associado ao serviço.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      <!-- Application Binding -->
      <webHttpRelayBinding>
        <binding name="default">
          <!-- Turn off client authentication so that client does not need to present credential through browser or fiddler -->
          <security relayClientAuthenticationType="None" />
        </binding>
      </webHttpRelayBinding>
    </bindings>

    <services>
      <!-- Application Service -->
      <service name="Microsoft.ServiceBus.Samples.ImageService"
               behaviorConfiguration="default">
        <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
      </service>
    </services>

    <behaviors>
      <endpointBehaviors>
        <behavior name="sbTokenProvider">
          <transportClientEndpointBehavior>
            <tokenProvider>
              <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
            </tokenProvider>
          <transportClientEndpointBehavior>
        </behavior>
      </endpointBehaviors>
      <serviceBehaviors>
        <behavior name="default">
          <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
</configuration>
```

## Etapa 4: hospedar o serviço WCF baseado em REST para usar o Barramento de Serviço

Esta etapa descreve como executar um serviço Web usando um aplicativo de console no Barramento de Serviço. Confira uma lista completa com os códigos escritos nesta etapa logo após o procedimento.

### Para criar um endereço base para o serviço

1. Na declaração de função `Main()`, crie uma variável para armazenar o namespace de seu projeto de Barramento de Serviço.

	```c
	string serviceNamespace = "InsertServiceNamespaceHere";
	```
	O Barramento de Serviço usa o nome do namespace para criar um URI exclusivo.

2. Crie uma instância de `Uri` para o endereço básico do serviço que está baseado no namespace.

	```c
	Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
	```

### Para criar e configurar o host do serviço Web

1. Crie o host do serviço Web usando o endereço URI criado anteriormente nesta seção.

	```c
	WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
	```
	O host do serviço é o objeto do WCF que instancia o aplicativo host. Este exemplo passa o tipo de host que você deseja criar (um **ImageService**) e também o endereço no qual você deseja expor o aplicativo host.

### Para executar o host do serviço Web

1. Abra o arquivo serviço.

	```c
	host.Open();
	```
	O serviço está em execução.

2. É exibida uma mensagem indicando que o serviço está em execução e dizendo como parar o serviço.

	```c
	Console.WriteLine("Copy the following address into a browser to see the image: ");
	Console.WriteLine(address + "GetImage");
	Console.WriteLine();
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

3. Quando terminar, feche o host do serviço.

	```c
	host.Close();
	```

## Exemplo

O exemplo a seguir inclui o contrato e a implementação do serviço das etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console. Compile o código a seguir em um arquivo executável chamado **ImageListener.exe**:

```c
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{
    
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }    
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### Compilando o código

Após compilar a solução, faça o seguinte para executar o aplicativo:

1. Em um prompt de comando, execute o serviço (ImageListener\\bin\\Debug\\ImageListener.exe).

2. Copie e cole o endereço do prompt de comando em um navegador para ver a imagem.

## Próximas etapas

Agora que você compilou um aplicativo que usa o serviço de retransmissão do Barramento de Serviço, confira os seguintes tópicos para saber mais sobre as mensagens retransmitidas:

- [Visão geral da arquitetura de Barramento de Serviço do Azure](fundamentals-service-bus-hybrid-solutions.md#relays)

- [Como usar o serviço de Retransmissão do Barramento de Serviço](service-bus-dotnet-how-to-use-relay.md)

<!---HONumber=July15_HO2-->