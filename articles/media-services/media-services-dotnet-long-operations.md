<properties 
	pageTitle="Operações de sondagem de execução longa" 
	description="Este tópico mostra como sondar operações de execução longa." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="juliako"/>


# Oferecendo streaming ao vivo com os Serviços de Mídia do Azure

## Visão geral

Os Serviços de Mídia do Microsoft Azure oferecem APIs que enviam solicitações aos Serviços de Mídia para iniciar operações (por exemplo: criar, iniciar, interromper ou excluir um canal). Essas operações são de execução longa.

O SDK .NET de Serviços de Mídia fornece APIs que enviam a solicitação e aguardam a conclusão da operação (internamente, as APIs realizam a sondagem do andamento da operação em intervalos). Por exemplo, quando você chama o channel.Start(), o método retorna depois que o canal é iniciado. Você também pode usar a versão assíncrona: await channel.StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefa, consulte [TAP](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). "Métodos de sondagem" é como são chamadas as APIs que enviam uma solicitação de operação e então sondam o status até que a operação seja concluída. Esses métodos (especialmente a versão assíncrona) são recomendados para aplicativos cliente sofisticados e/ou serviços com monitoração de estado.

Existem cenários em que um aplicativo não pode esperar uma solicitação http de execução longa e desejar sondar o andamento da operação manualmente. Um exemplo típico seria um navegador interagindo com um serviço Web sem monitoração de estado: quando o navegador solicita a criação de um canal, o serviço Web inicia uma operação de execução longa e retorna a ID da operação para o navegador. O navegador pode pedir ao serviço Web para obter o status da operação com base na ID. O SDK .NET dos Serviços de Mídia fornece APIs úteis para esse cenário. Essas APIs são chamadas de "métodos sem sondagem".
Os "métodos sem sondagem" têm o padrão de nomenclatura a seguir: Enviar*OperationName*Operação (por exemplo, SendCreateOperation). Enviar*OperationName*Métodos de operação retornam o objeto **IOperation**; o objeto retornado contém informações que podem ser usadas para controlar a operação. Os métodos Send*OperationName*Métodos OperationAsync retornam **Task<IOperation>**.

Atualmente, as classes a seguir dão suporte a métodos sem sondagem:  **Channel**, **StreamingEndpoint** e **Program**.

Para monitorar o status da operação, use o método **GetOperation** na classe **OperationBaseCollection**. Use os seguintes intervalos para verificar o status da operação: para as operações **Channel** e **StreamingEndpoint**, use 30 segundos; para operações **Program**, use 10 segundos.


## Exemplo

O exemplo a seguir define uma classe chamada **ChannelOperations**. Essa definição de classe pode ser um ponto de partida para a definição de classe de serviço Web. Para simplificar, os exemplos a seguir usam as versões de métodos não assíncronas.

O exemplo também mostra como o cliente pode usar essa classe.

### Definição da classe ChannelOperations

	/// <summary> 
	/// A classe ChannelOperations implementa apenas 
	/// a operação de criação do canal. 
	/// </summary> 
	public class ChannelOperations
	{
	    // Leia os valores do arquivo App.config.
	    private static readonly string _mediaServicesAccountName =
	        ConfigurationManager.AppSettings["MediaServicesAccountName"];
	    private static readonly string _mediaServicesAccountKey =
	        ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	    // Campo para contexto de serviço.
	    private static CloudMediaContext _context = null;
	    private static MediaServicesCredentials _cachedCredentials = null;
	
	    public ChannelOperations()
	    {
	            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	            _context = new CloudMediaContext(_cachedCredentials);    }
	
	    /// <summary>  
	    /// Inicia a criação de um novo canal.  
	    /// </summary>  
	    /// <param name="channelName">Nome a ser dado ao novo canal </param>  
	    /// <returns>  
	    /// Id da operação para a operação de execução longa sendo executada pelos Serviços de Mídia. 
	    /// Use essa Id de operação para sondar o status de criação do canal. 
	    /// </returns> 
	    public string StartChannelCreation(string channelName)
	    {
	        var operation = _context.Channels.SendCreateOperation(
	            new ChannelCreationOptions
	            {
	                Name = channelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Output = CreateChannelOutput()
	            });
	
	        return operation.Id;
	    }
	
	    /// <summary> 
	    /// Verifica se a operação foi concluída. 
	    /// Se a operação tiver sido bem-sucedida, a Id do canal criado será retornada no parâmetro de saída.
	    /// </summary> 
	    /// <param name="operationId">A Id da operação.</param> 
	    /// <param name="channel">
	    /// Se a operação for bem-sucedida, 
	    /// a ID do canal criado é retornado no parâmetro de saída. </param>
	    /// <returns>Retorna false se a operação ainda estiver em andamento. Caso contrário, retorna true.</returns> 
	    public bool IsCompleted(string operationId, out string channelId)
	    {
	        IOperation operation = _context.Operations.GetOperation(operationId);
	        bool completed = false;
	
	        channelId = null;
	
	        switch (operation.State)
	        {
	            case OperationState.Failed:
	                // Tratar da falha. 
	                // Por exemplo, lança uma exceção. 
					// Use as seguintes informações na exceção: operationId, operation.ErrorMessage.
	                break;
	            case OperationState.Succeeded:
	                completed = true;
	                channelId = operation.TargetEntityId;
	                break;
	            case OperationState.InProgress:
	                completed = false;
	                break;
	        }
	        return completed;
	    }
	
	
	    private static ChannelInput CreateChannelInput()
	    {
	        return new ChannelInput
	        {
	            StreamingProtocol = StreamingProtocol.RTMP,
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelInput001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelPreview CreateChannelPreview()
	    {
	        return new ChannelPreview
	        {
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelPreview001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelOutput CreateChannelOutput()
	    {
	        return new ChannelOutput
	        {
	            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
	        };
	    }
	}

### O código do cliente

	ChannelOperations channelOperations = new ChannelOperations();
	string opId = channelOperations.StartChannelCreation("MyChannel001");
	
	string channelId = null;
	bool isCompleted = false;
	
	while (isCompleted == false)
	{
	    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	    isCompleted = channelOperations.IsCompleted(opId, out channelId);
	}
	
	// Se chegamos até aqui, devemos ter a Id do canal recém-criado.
	Console.WriteLine(channelId);


<!--HONumber=52--> 