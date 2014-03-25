<h2><a name="what-are-service-bus-topics"></a>O que são os tópicos e as assinaturas do Service Bus</h2>

Os tópicos e assinaturas do Service Bus oferecem suporte a um modelo de **comunicação de mensagens de publicação/assinatura**. Ao usar tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Ao contrário das filas do Service Bus, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem um formulário de comunicação **um-para-muitos** usando um padrão de publicação/assinatura. É possível registrar várias assinaturas em um determinado tópico. Quando uma mensagem é enviada a um tópico, ele é então disponibilizado para cada assinatura para ser gerenciado/processado de forma independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, você pode registrar regras de filtro para um tópico em uma base por assinatura, o que lhe permite filtrar/restringir quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Service Bus permitem dimensionar para processar um grande número de mensagens em um grande número de usuários e aplicativos.

<h2><a name="create-a-service-namespace"></a>Criar um namespace de serviço</h2>

Para começar a usar assinaturas e tópicos do Service Bus no Windows Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para lidar com os recursos do Service Bus a partir do aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Windows Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**   
    ![][0]

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
    ![][2]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você estiver implantando os recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][6]


<h2><a name="obtain-default-credentials"></a>Obter as credenciais de gerenciamento padrão do namespace</h2>

Para realizar operações de gerenciamento, como a criação de um tópico ou uma assinatura, no novo namespace, você precisa obter as credenciais de gerenciamento para o namespace.

1.  No painel de navegação esquerdo, clique no nó **Service Bus** para exibir a lista de namespaces disponíveis:   
    ![][0]

2.  Selecione o namespace que você acabou de criar na lista abaixo:   
    ![][3]

3.  Clique em **Informações de conexão**.   
    ![][4]

4.  Na caixa de diálogo **Acessar informações de conexão**, localize as entradas de **Emissor Padrão** e **Chave Padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace. 

 
  [Portal de Gerenciamento do Windows Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png

