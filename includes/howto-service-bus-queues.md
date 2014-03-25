<a id="what-are-service-bus-queues"></a>
##O que são as filas do Service Bus?

Filas do Service Bus oferecem suporte a um **orientado a mensagens de comunicação**
modelo. Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário. Um produtor de mensagem (remetente) transmite uma mensagem para a fila e, em seguida, continua o processamento.
De forma assíncrona, um consumidor de mensagem (receptor) recebe a mensagem da fila e a processa. O produtor não precisa esperar por uma resposta do consumidor para continuar a processar e enviar mais mensagens. Filas oferecem **primeiro a entrar, primeiro-Out (FIFO)** mensagem de entrega para um ou mais clientes de concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas por receptores na ordem em que elas foram adicionadas à fila, e cada mensagem é recebida e processada pelo consumidor de apenas uma mensagem.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Filas do Service Bus são uma tecnologia de uso geral que pode ser usada para uma grande variedade de cenários:

-A comunicação entre as funções web e de trabalho em um aplicativo do Windows Azure em várias camada
-A comunicação entre aplicativos locais e aplicativos do Windows Azure hospedado em uma solução híbrida
-A comunicação entre os componentes de um aplicativo distribuído em execução local em diferentes empresas ou departamentos de uma organização

Usando filas pode permitem dimensionar seus aplicativos melhores e permitir mais flexibilidade para sua arquitetura.

<a id="create-a-service-namespace"></a>
<h2>Criar um namespace de serviço</h2>

Para começar a usar assinaturas e tópicos do Service Bus no Windows Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para lidar com os recursos do Service Bus a partir do aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Windows Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**   
	![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você estiver implantando os recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

O namespace que você criou aparece no Portal de gerenciamento e reserva um tempo para ativar. Aguarde até que o status seja **ativos** antes de continuar.

<a id="obtain-default-credentials"></a>
<h2>Obter as credenciais de gerenciamento padrão do namespace</h2>

Para realizar operações de gerenciamento, como a criação de um tópico ou uma assinatura, no novo namespace, você precisa obter as credenciais de gerenciamento para o namespace.

1.  No painel de navegação esquerdo, clique no nó **Service Bus** para exibir a lista de namespaces disponíveis:   
       
	![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Selecione o namespace que você acabou de criar na lista abaixo:   
       
	![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Clique em **Informações de conexão**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Na caixa de diálogo **Acessar informações de conexão**, localize as entradas de **Emissor Padrão** e **Chave Padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace.

  [Portal de Gerenciamento do Windows Azure]: http://manage.windowsazure.com
  [Portal de Gerenciamento do Windows Azure]: http://manage.windowsazure.com



