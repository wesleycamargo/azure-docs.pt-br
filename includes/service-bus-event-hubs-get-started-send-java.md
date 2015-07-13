## Enviar mensagens ao Hub de Eventos
Nesta seção, vamos escrever um aplicativo de console Java para enviar eventos ao seu Hub de Eventos. Usaremos o provedor JMS AMQP do [projeto Apache Qpid](http://qpid.apache.org/). Isso é semelhante a usar tópicos e filas do Barramento de Serviço com AMQP por meio de Java, como mostrado [aqui](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Para saber mais, consulte a [documentação do Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) e o [Serviço de Mensagens Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. No Eclipse, instale o [Kit de Ferramentas do Azure para Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Isso inclui as bibliotecas de cliente do AMQP, Qpid JMS.

2. No Eclipse, crie um novo projeto Java chamado **Remetente**.

3. No Gerenciador de Pacotes do Eclipse, clique com botão direito do mouse no projeto **Remetente** e selecione **Propriedades**. No painel esquerdo da caixa de diálogo, clique em **Caminho de Compilação Java**, clique na guia **Bibliotecas** e, em seguida, no botão **Adicionar Biblioteca**. Selecione **Pacote para Bibliotecas de Cliente Apache Qpid para JMS (pela MS Open Tech)**, clique em **Avançar** e em **Concluir**.

	![][8]

4. Crie um arquivo chamado **servicebus.properties** na raiz do projeto **Remetente**, com o seguinte conteúdo. Lembre-se de substituir os valores do(a):
	- Nome do Hub de Eventos.
	- Nome do namespace (o último geralmente é `{event hub name}-ns`).
	- Chave **SendRule** codificada em URL (você anotou essa chave quando criou o Hub de Eventos). Você pode codificar a URL [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Crie uma nova classe chamada **Remetente**. Adicione as seguintes declarações de `import`:

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. Em seguida, adicione o seguinte código a:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=62-->