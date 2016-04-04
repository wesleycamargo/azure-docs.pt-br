## Enviar mensagens ao Hub de Eventos

A biblioteca de cliente Java para os Hubs de Eventos está disponível para uso em projetos do Maven por meio do [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22), e pode ser referenciada usando a seguinte declaração de dependência dentro do arquivo de projeto do Maven:

``` XML
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>azure-eventhubs-clients</artifactId> 
    <version>0.6.0</version> 
</dependency>   
 ```
 
Para diferentes tipos de ambientes de build, é possível obter explicitamente os arquivos JAR liberados mais recentemente no [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou no [ponto de distribuição de versão no GitHub](https://github.com/Azure/azure-event-hubs/releases).

Para um editor de eventos simples, importe o pacote *com.microsoft.azure.eventhubs* para as classes de cliente dos Hubs de Eventos e o pacote *com.microsoft.azure.servicebus* para as classes de utilitário como exceções comuns que são compartilhadas com o cliente de mensagens do Barramento de Serviço do Azure.

Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. A classe será chamada ```Send```.

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

Substitua o namespace e os nomes do Hub de Eventos pelos valores usados durante a criação do Hub de Eventos. `sasKeyName` e `sasKey` correspondem ao nome e à chave da regra de Envio criada anteriormente. Com essas informações, crie uma cadeia de conexão.

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Em seguida, crie um evento singular transformando uma cadeia de caracteres em sua codificação de bytes UTF-8. Daí, criamos uma nova instância de cliente dos Hubs de Eventos usando a cadeia de conexão e enviamos a mensagem.

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0323_2016-->