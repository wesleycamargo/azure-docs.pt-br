## Enviar mensagens ao Hub de Eventos
Nesta seção, iremos escrever um aplicativo de console Java para enviar eventos para o seu hub de eventos. Usaremos o provedor JMS AMQP do [projeto Apache Qpid](http://qpid.apache.org/). Isso é análogo a usar tópicos e filas do barramento de serviço com AMQP por meio de Java, como mostrado [aqui](../articles/service-bus-java-how-to-use-jms-api-amqp.md). Para obter mais informações, consulte a [documentação do Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) e [serviço de mensagens Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. No Eclipse, crie um novo projeto Java chamado **Remetente**.

2. Baixe a versão mais recente da biblioteca **Qpid JMS AMQP 1.0** [daqui](http://qpid.apache.org/components/qpid-jms/index.html).

3. Extraia os arquivos do arquivo morto e copie os seguintes jars do diretório `qpid-amqp-1-0-client-jms\<version>\lib`do arquivo morto em seu projeto Eclipse **Remetente**.

4. No Gerenciador de Pacotes do Eclipse, clique com botão direito do mouse no projeto **Remetente** e selecione **Propriedades**. No painel esquerdo da caixa de diálogo, clique em **Caminho de compilação Java**, clique na guia **Bibliotecas** e, em seguida, no botão **Adicionar JARs**. Selecione todos os jars anteriormente copiados e, em seguida, clique em **OK**.

	![][8]

5. Crie um arquivo chamado **servicebus.properties** na raiz do projeto **Remetente**, com o seguinte conteúdo. Lembre-se de substituir o valor para o nome do Hub de Evento e o nome de namespace (o último é geralmente `{event hub name}-ns`). Você também deve substituir uma versão codificada em URL da chave para o **SendRule** criado anteriormente. Você pode codificar a URL [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).

		# servicebus.properties - exemplo de configuração de JNDI

		# Registre um ConnectionFactory no JNDI usando o formato:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Registre um ConnectionFactory no JNDI usando o formato:
		# queue.[jndi_name] = [physical_name]
		# queue.[jndi_name] = [physical_name]
		queue.EventHub = {nome do hub de evento}

5. Crie uma nova classe chamada **Remetente**. Adicione o seguinte `import` instruções:

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

8. Em seguida, adicione o seguinte código a:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configurar o ambiente JNDI
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// Criar conexão
			Connection connection = cf.createConnection();
	
			// Criar sessão para o remetente e MessageProducer no lado do remetente
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);
	
			System.out.println("Pressione Ctrl-C para interromper o processo de remetente");
			System.out.println("Pressione Enter para iniciar agora");
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
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!--HONumber=52--> 
