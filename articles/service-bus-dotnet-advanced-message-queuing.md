<properties linkid="develop-net-how-to-guides-service-bus-amqp" urlDisplayName="Service Bus AMQP" pageTitle="How to use AMQP 1.0 with the .NET Service Bus API - Azure" metaKeywords="" description="Learn how to use Advanced Message Queuing Protodol (AMQP) 1.0 with the Azure .NET Service Bus API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to use AMQP 1.0 with the Service Bus .NET API" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Como usar os tópicos AMQP 1.0 com o Service Bus .NET API

## <span class="short-header">Introdução</span>Introdução

O AMQP (Protocolo de Enfileiramento de Mensagens Avançadas) 1.0 é um protocolo de mensagens eficiente, confiável e conectado que pode ser usado para criar aplicativos de mensagens robustos em plataformas cruzadas.

O suporte para o AMQP 1.0 no Service Bus significa que você pode usar o enfileiramento e publicar/assinar os recursos de mensagens agenciadas a partir de uma variedade de plataformas usando um protocolo binário eficiente. Além disso, você pode criar aplicativos formados por componentes criados com o uso de uma mistura de linguagens, estruturas e sistemas operacionais.

Este Guia de Instruções explica como usar os recursos de mensagens agenciadas do Service Bus (tópicos sobre filas e publicação/assinatura) dos aplicativos do .NET que usam a API do .NET do Service Bus. Existe um guia de instruções complementar que explica como fazer o mesmo usando a API do Java Message Service (JMS) padrão. Você pode usar esses dois guias em conjunto para saber mais sobre mensagens em plataformas cruzadas usando o AMQP 1.0.

## <span class="short-header">Introdução</span>Introdução ao Service Bus

Este guia presume que você já tenha um namespace do Service Bus que contém uma fila denominada "queue1." Caso contrário, você pode criar o namespace e a fila usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] Para obter mais informações sobre como criar namespaces e filas do Service Bus, consulte o Guia de Instruções chamado "[Como usar filas do Service Bus.][Como usar filas do Service Bus.]

## <span class="short-header">Baixando o SDK</span>Baixando o SDK do Service Bus.

O suporte para o AMQP 1.0 está disponível na versão 2.1 ou posterior do SDK do Service Bus. Você pode baixar o SDK mais recente no site da NuGet em [][]<http://nuget.org/packages/WindowsAzure.ServiceBus/></a>

## <span class="short-header">Codificando aplicativos do .NET</span>Codificando aplicativos do .NET

Por padrão, a biblioteca de cliente do .NET do Service Bus se comunica com o serviço do Service Bus usando um protocolo dedicado baseado em SOAP. O uso do AMQP 1.0 em vez do protocolo padrão requer a configuração explícita na cadeia de conexão do Service Bus, conforme descrito na próxima seção. Além dessa alteração, o código do aplicativo permanece basicamente inalterado ao usar o AMQP 1.0.

Na versão atual, existem alguns outros recursos da API que não são suportados com o uso do AMQP. Esses recursos não suportados são listados posteriormente na seção "Recursos não suportados e restrições". Algumas das definições de configuração avançadas também apresentam um significado diferente com o uso do AMQP. Nenhuma dessas configurações são usadas neste breve guia de instruções, porém mais detalhes estão disponíveis no [Guia do Desenvolvedor do AMQP 1.0 do Service Bus][Guia do Desenvolvedor do AMQP 1.0 do Service Bus]

### Configuração por meio do App.config

É uma boa prática que os aplicativos usem o arquivo de configuração do App.config para armazenar as configurações. Para os aplicativos do Service Bus, você pode usar o App.config para armazenar as configurações de **ConnectionString** do Service Bus. Além disso, esta amostra do aplicativo armazena o nome da entidade das mensagens do Service Bus usada por ela.

Uma amostra do arquivo App.config é mostrada abaixo:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net;SharedSecretIssuer=[issuer name];SharedSecretValue=[issuer key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
        </appSettings>
    </configuration>

### Configurando a cadeia de conexão do Service Bus

O valor da configuração **Microsoft.ServiceBus.ConnectionString** é a cadeia de conexão do Service Bus usada para configurar a conexão ao Service Bus. O formato é conforme segue:

    Endpoint=sb://[namespace].servicebus.windows.net;SharedSecretIssuer=[issuer name];SharedSecretValue=[issuer key];TransportType=Amqp

Onde [namespace], [nome do emissor] e [chave do emissor] são obtidos a partir do Portal de Gerenciamento do Azure. Para obter mais informações, consulte [Como usar as Filas do Service Bus][Como usar as Filas do Service Bus].

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
    Press [enter] to send a message. Type 'exit' + [enter] to quit.

    Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf

    Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
    Received message with MessageID = d00e2e088f06416da7956b58310f7a06

    Received message with MessageID = f27f79ec124548c196fd0db8544bca49
    Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
    exit

## <span class="short-header">Mensagens em plataformas cruzadas</span>Mensagens entre JMS e .NET em plataformas cruzadas

Este guia mostrou como enviar mensagens para o Service Bus usando o .NET e também como receber essas mensagens usando o .NET. No entanto, um dos principais benefícios do AMQP 1.0 é que ele permite que os aplicativos sejam criados a partir de componentes escritos em diferentes linguagens, com mensagens trocadas de forma confiável e com total fidelidade.

Ao usar a amostra do aplicativo do .NET descrito acima e um aplicativo do Java semelhante obtido de um guia complementar, [Como usar a API do Java Message Service (JMS) com o Service Bus e o AMQP 1.0][Como usar a API do Java Message Service (JMS) com o Service Bus e o AMQP 1.0], é possível trocar mensagens entre o .NET e o Java.

Para obter mais informações sobre os detalhes de mensagens em plataformas cruzadas usando o Service Bus e o AMQP 1.0, consulte o [Guia do Desenvolvedor do Service Bus e AMQP 1.0][Guia do Desenvolvedor do AMQP 1.0 do Service Bus].

### Do JMS para o .NET

Para demonstrar as mensagens do JMS para o .NET:

-   Inicie a amostra do aplicativo do .NET sem nenhum argumento de linha de comando.
-   Inicie a amostra do aplicativo Java com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila; ele somente as enviará.
-   Pressione **Enter** algumas vezes no console do aplicativo Java; isso fará com que as mensagens sejam enviadas.
-   Essas mensagens são recebidas pelo aplicativo .NET.

#### Saída do aplicativo JMS

    > java SimpleSenderReceiver sendonly
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Sent message with JMSMessageID = ID:4364096528752411591
    Sent message with JMSMessageID = ID:459252991689389983
    Sent message with JMSMessageID = ID:1565011046230456854
    exit

#### Saída do aplicativo .NET

    > SimpleSenderReceiver.exe  
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Received message with MessageID = 4364096528752411591
    Received message with MessageID = 459252991689389983
    Received message with MessageID = 1565011046230456854
    exit

### Do .NET para o JMS

Para demonstrar as mensagens do .NET para o JMS:

-   Inicie a amostra do aplicativo do .NET com o argumento de linha de comando "sendonly". Nesse modo, o aplicativo não receberá mensagens da fila; ele somente as enviará.
-   Inicie a amostra do aplicativo do Java sem nenhum argumento de linha de comando.
-   Pressione **Enter** algumas vezes no console do aplicativo .NET, isso fará com que as mensagens sejam enviadas.
-   Essas mensagens são recebidas pelo aplicativo Java.

#### Saída do aplicativo .NET

    > SimpleSenderReceiver.exe sendonly
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
    Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
    Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
    exit

#### Saída do aplicativo JMS

    > java SimpleSenderReceiver 
    Press [enter] to send a message. Type 'exit' + [enter] to quit.
    Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
    Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
    Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
    exit

## <span class="short-header">Recursos não suportados</span>Recursos não suportados e restrições

Os seguintes recursos da API do Service Bus do .NET não são atualmente suportados com o uso do AMQP:

-   Transações
-   Enviar por meio de destino da transferência
-   Receber pelo número sequencial da mensagem
-   Procurar mensagens e sessões
-   Estado de sessão
-   APIs baseadas em lote
-   Recebimento dimensionado
-   Manipulação do tempo de execução das regras de assinatura
-   Renovação de bloqueio da sessão
-   Algumas pequenas diferenças no comportamento

Para obter mais informações, consulte o [Guia do Desenvolvedor do AMQP 1.0 do Service Bus][Guia do Desenvolvedor do AMQP 1.0 do Service Bus]. Este tópico inclui uma lista detalhada de APIs não suportadas.

## <span class="short-header">Resumo</span>Resumo

Este Guia de Instruções mostrou como acessar os recursos de mensagens agenciadas do Service Bus (tópicos sobre filas e publicação/assinatura) do .NET que usam o AMQP 1.0 e a API do .NET do Service Bus.

Você também pode usar o AMQP 1.0 do Service Bus de outras linguagens, incluindo Java, C, Python e PHP. Os componentes criados com estas linguagens podem trocar mensagens de forma confiável e com total fidelidade usando o AMQP 1.0 no Service Bus. Para obter mais informações, consulte o [Guia do Desenvolvedor do AMQP 1.0 do Service Bus][Guia do Desenvolvedor do AMQP 1.0 do Service Bus].

## <span class="short-header">Mais informações</span>Mais informações

-   [Suporte para o AMQP 1.0 no Service Bus do Azure][Suporte para o AMQP 1.0 no Service Bus do Azure]
-   [Como usar a API do Serviço de Mensagem Java (JMS) com Service Bus e AMQP 1.0][Como usar a API do Java Message Service (JMS) com o Service Bus e o AMQP 1.0]
-   [Guia do desenvolvedor do AMQP 1.0 do Service Bus][Guia do Desenvolvedor do AMQP 1.0 do Service Bus]
-   [Como usar as filas do Service Bus][Como usar as Filas do Service Bus]

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Como usar filas do Service Bus.]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/service-bus-queues/
  []: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Guia do Desenvolvedor do AMQP 1.0 do Service Bus]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj841071.aspx
  [Como usar as Filas do Service Bus]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/service-bus-queues/
  [Suporte para o AMQP 1.0 no Service Bus do Azure]: http://aka.ms/pgr3dp
