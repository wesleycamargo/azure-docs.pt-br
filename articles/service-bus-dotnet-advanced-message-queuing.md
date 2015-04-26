<properties 
	pageTitle="Como usar os tópicos AMQP 1.0 com A API do Barramento de Serviço em .NET - Azure" 
	description="Saiba como usar o Advanced Message Queuing Protocol (AMQP) 1.0 com a API de Barramento de Serviço .NET do Azure". 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="sethm"/>






# Como usar os tópicos AMQP 1.0 com o Service Bus .NET API

## Introdução

O AMQP (Protocolo de Enfileiramento de Mensagens Avançadas) 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens robustos em plataformas cruzadas.

O suporte para o AMQP 1.0 no Barramento de Serviço significa que você pode usar o enfileiramento e publicar/assinar os recursos do sistema de mensagens agenciado de uma variedade de plataformas usando um protocolo binário eficiente.  Além disso, você pode criar aplicativos formados por componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

Este Guia de Instruções explica como usar os recursos do sistema de mensagens agenciado do Barramento de Serviço (tópicos sobre filas e publicação/assinatura) dos aplicativos do .NET que usam a API do .NET do Barramento de Serviço.  Existe um guia de instruções complementar que explica como fazer o mesmo usando a API do Java Message Service (JMS) padrão.  Você pode usar esses dois guias em conjunto para saber mais sobre mensagens em plataformas cruzadas usando o AMQP 1.0.

## Introdução ao Barramento de serviço

Este guia presume que você já tenha um namespace do Barramento de Serviço que contém uma fila denominada "queue1."  Caso contrário, você pode criar o namespace e a fila usando o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).  Para obter mais informações sobre como criar namespaces e filas do Service Bus, consulte o Guia de Instruções chamado "[Como usar filas do Service Bus.](https://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)

## Baixar o SDK do Barramento de Serviço

O suporte para o AMQP 1.0 está disponível na versão 2.1 ou posterior do SDK do Barramento de Serviço.  Você pode baixar o SDK mais recente no site da NuGet em [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## Codificando aplicativos .NET

Por padrão, a biblioteca de cliente do .NET do Barramento de Serviço se comunica com o serviço do Barramento de Serviço usando um protocolo dedicado baseado em SOAP.  O uso do AMQP 1.0 em vez do protocolo padrão requer a configuração explícita na cadeia de conexão do Service Bus, conforme descrito na próxima seção.  Além dessa alteração, o código do aplicativo permanece basicamente inalterado ao usar o AMQP 1.0.

Na versão atual, existem alguns outros recursos da API que não têm suporte com o uso do AMQP.  Esses recursos não suportados são listados posteriormente na seção "Recursos não suportados e restrições".  Algumas das definições de configuração avançadas também apresentam um significado diferente com o uso do AMQP.  Nenhuma dessas configurações são usadas neste breve guia de instruções, porém mais detalhes estão disponíveis no [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](http://msdn.microsoft.com/library/jj841071.aspx).

### Configuração por meio do App.config

É uma prática recomendada que os aplicativos usem o arquivo de configuração do App.config para armazenar as configurações.  Para os aplicativos do Barramento de Serviço, você pode usar o App.config para armazenar as configurações de **ConnectionString** do Barramento de Serviço.  Este aplicativo de exemplo também usa o App.config para armazenar o nome da entidade de mensagens do Barramento de Serviço que ele usa.

Uma amostra do arquivo App.config é mostrada abaixo:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### Configurando a cadeia de conexão do Barramento de Serviço

O valor da configuração **Microsoft.ServiceBus.ConnectionString** é a cadeia de conexão do Barramento de Serviço usada para configurar a conexão ao Service Bus. O formato é conforme segue:

	Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Onde [namespace] e [chave SAS] são obtidos no Portal de Gerenciamento do Azure.  Para obter mais informações, consulte [Como usar as Filas do Service Bus][].

Ao usar o AMQP, a cadeia de conexão é acrescentada com ";TransportType=Amqp", que informa à biblioteca de cliente para fazer sua conexão com o Service Bus usando o AMQP 1.0.

### Configurando o Nome da Entidade

Esta amostra do aplicativo usa a configuração de "EntityName" na seção **appSettings** do arquivo App.config para configurar o nome da fila com a qual o aplicativo troca mensagens.

### Um aplicativo simples do .NET que usa Filas do Service Bus

O exemplo a seguir envia e recebe mensagens em uma fila do Service Bus.

	// SimpleSenderReceiver.cs
	
	using System;
	using System.Configuration;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	
	namespace SimpleSenderReceiver
	{
	    class SimpleSenderReceiver
	    {
	        private static string connectionString;
	        private static string entityName;
	        private static Boolean runReceiver = true;
	        private MessagingFactory factory;
	        private MessageSender sender;
	        private MessageReceiver receiver;
	        private MessageListener messageListener;
	        private Thread listenerThread;
	
	        static void Main(string[] args)
	        {
	            try
	            {
	                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
	                    runReceiver = false;
	                
	                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
	                string entityNameKey = "EntityName";
	                entityName = ConfigurationManager.AppSettings[entityNameKey];
	                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
	                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
	                Console.WriteLine("Press [enter] to send a message. " +
	                    "Type 'exit' + [enter] to quit.");
	
	                while (true)
	                {
	                    string s = Console.ReadLine();
	                    if (s.ToLower().Equals("exit"))
	                    {
	                        simpleSenderReceiver.Close();
	                        System.Environment.Exit(0);
	                    }
	                    else
	                        simpleSenderReceiver.SendMessage();
	                }
	            }
	            catch (Exception e)
	            {
	                Console.WriteLine("Caught exception: " + e.Message);
	            }
	        }
	
	        public SimpleSenderReceiver()
	        {
	            factory = MessagingFactory.CreateFromConnectionString(connectionString);
	            sender = factory.CreateMessageSender(entityName);
	
	            if (runReceiver)
	            {
	                receiver = factory.CreateMessageReceiver(entityName);
	                messageListener = new MessageListener(receiver);
	                listenerThread = new Thread(messageListener.Listen);
	                listenerThread.Start();
	            }
	        }
	
	        public void Close()
	        {
	            messageListener.RequestStop();
	            listenerThread.Join();
	            factory.Close();
	        }
	
	        private void SendMessage()
	        {
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
	            sender.Send(message);
	            Console.WriteLine("Sent message with MessageID = " 
	                + message.MessageId);
	        }
	
	    }
	
	    public class MessageListener
	    {
	        private MessageReceiver messageReceiver;
	        public MessageListener(MessageReceiver receiver)
	        {
	            messageReceiver = receiver;
	        }
	
	        public void Listen()
	        {
	            while (!_shouldStop)
	            {
	                try
	                {
	                    BrokeredMessage message = 
	                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
	                    if (message != null)
	                    {
	                        Console.WriteLine("Received message with MessageID = " + 
	                            message.MessageId);
	                        message.Complete();
	                    }
	                }
	                catch (Exception e)
	                {
	                    Console.WriteLine("Caught exception: " + e.Message);
	                    break;
	                }
	            }
	        }
	
	        public void RequestStop()
	        {
	            _shouldStop = true;
	        }
	
	        private volatile bool _shouldStop;
	    }
	}

### Executando o aplicativo

A execução do aplicativo produz a saída do formulário:

	> SimpleSenderReceiver.exe
	Pressione [enter] para enviar uma mensagem.  Digite 'exit' + [enter] para sair.
	
	Mensagem enviada com MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Mensagem recebida com MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Mensagem enviada com MessageID = d00e2e088f06416da7956b58310f7a06
	Mensagem recebida com MessageID = d00e2e088f06416da7956b58310f7a06
	
	Mensagem recebida com MessageID = f27f79ec124548c196fd0db8544bca49
	Mensagem enviada com MessageID = f27f79ec124548c196fd0db8544bca49
	exit

## Mensagens em plataformas cruzadas entre JMS e .NET

Este guia mostrou como enviar mensagens para o Barramento de Serviço usando o .NET e também como receber essas mensagens usando o .NET.  No entanto, um dos principais benefícios do AMQP 1.0 é que ele permite que os aplicativos sejam criados por meio de componentes escritos em diferentes linguagens, com mensagens trocadas de forma confiável e com total fidelidade.

Ao usar a amostra do aplicativo do .NET descrito acima e um aplicativo do Java semelhante obtido de um guia complementar, [Como usar a API do Java Message Service (JMS) com o Barramento de Serviço e o AMQP 1.0](http://aka.ms/ll1fm3), , é possível trocar mensagens entre o .NET e o Java. 

Para obter mais informações sobre os detalhes de mensagens em plataformas cruzadas usando o Service Bus e o AMQP 1.0, consulte o [Guia do Desenvolvedor do Service Bus e AMQP 1.0](http://msdn.microsoft.com/library/jj841071.aspx).

### JMS para o .NET

Para demonstrar as mensagens do JMS para o .NET:

* Inicie o aplicativo de exemplo do .NET sem nenhum argumento de linha de comando.
* Inicie a amostra do aplicativo Java com o argumento de linha de comando "sendonly".  Nesse modo, o aplicativo não receberá mensagens da fila, ele somente as enviará.
* Pressione **Enter** algumas vezes no console do aplicativo Java; isso fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo .NET.

### Saída do aplicativo JMS

	> java SimpleSenderReceiver sendonly
	Pressione [enter] para enviar uma mensagem.  Digite 'exit' + [enter] para sair.
	Mensagem enviada com JMSMessageID = ID:4364096528752411591
	Mensagem enviada com JMSMessageID = ID:459252991689389983
	Mensagem enviada com JMSMessageID = ID:1565011046230456854
	exit

### Saída do aplicativo .NET

	> SimpleSenderReceiver.exe	
	Pressione [enter] para enviar uma mensagem.  Digite 'exit' + [enter] para sair.
	Mensagem recebida com MessageID = 4364096528752411591
	Mensagem recebida com MessageID = 459252991689389983
	Mensagem recebida com MessageID = 1565011046230456854
	exit

## Do .NET para o JMS

Para demonstrar as mensagens do .NET para o JMS:

* Inicie a amostra do aplicativo do .NET com o argumento de linha de comando "sendonly".  Nesse modo, o aplicativo não receberá mensagens da fila, ele somente as enviará.
* Inicie a amostra do aplicativo do Java sem nenhum argumento de linha de comando.
* Pressione **Enter** algumas vezes no console do aplicativo .NET, isso fará com que as mensagens sejam enviadas.
* Essas mensagens são recebidas pelo aplicativo Java.

#### Saída do aplicativo .NET

	> SimpleSenderReceiver.exe sendonly
	Pressione [enter] para enviar uma mensagem.  Digite 'exit' + [enter] para sair.
	Mensagem enviada com MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Mensagem enviada com MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Mensagem enviada com MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### Saída do aplicativo JMS

	> java SimpleSenderReceiver	
	Pressione [enter] para enviar uma mensagem.  Digite  'exit' + [enter] para sair.
	Mensagem recebida com JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Mensagem recebida com JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Mensagem recebida com JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

<h2>Restrições e recursos sem suporte</h2>

Os seguintes recursos da API do Service Bus do .NET não são atualmente suportados com o uso do AMQP:

* Transações
* Enviar por meio de destino da transferência
* Receber pelo número sequencial da mensagem
* Procurar mensagens e sessões
* Estado de sessão
* APIs baseadas em lote
* Recebimento dimensionado
* Manipulação do tempo de execução das regras de assinatura
* Renovação de bloqueio da sessão
* Algumas pequenas diferenças no comportamento

Para obter mais informações, consulte o [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](http://msdn.microsoft.com/library/jj841071.aspx).  Este tópico inclui uma lista detalhada de APIs não suportadas.

<h2>Resumo</h2>

Este Guia de Instruções mostrou como acessar os recursos de mensagens agenciadas do Service Bus (tópicos sobre filas e publicação/assinatura) do .NET que usam o AMQP 1.0 e a API do .NET do Barramento de Serviço.

Você também pode usar o AMQP 1.0 do Barramento de Serviço de outras linguagens, incluindo Java, C, Python e PHP.  Os componentes criados com essas linguagens podem trocar mensagens com segurança e com total fidelidade usando o AMQP 1.0 no Barramento de Serviço. Para obter mais informações, consulte o [Guia do Desenvolvedor AMQP 1.0 do Barramento de Serviço](http://msdn.microsoft.com/library/jj841071.aspx).

<h2>Mais informações</h2>

* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](http://aka.ms/pgr3dp)
* [Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0](http://aka.ms/ll1fm3)
* [Guia do desenvolvedor do AMQP 1.0 do Barramento de Serviço](http://msdn.microsoft.com/library/jj841071.aspx)
* [Como usar filas do Barramento de Serviço](http://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)

[Como usar filas do Barramento de Serviço]: http://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/

<!--HONumber=49-->