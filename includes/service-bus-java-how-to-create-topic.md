## O que são os tópicos e as assinaturas do Barramento de Serviço?

Os tópicos e assinaturas do Barramento de Serviço dão suporte a um modelo de comunicação de mensagens de *publicação/assinatura*. Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![Conceitos de tópico](./media/service-bus-java-how-to-create-topic/sb-topics-01.png)

Em contraste com as filas do Barramento de Serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem uma forma de comunicação de um para muitos usando um padrão de publicação/assinatura. É possível registrar várias assinaturas para um tópico. Quando uma mensagem é enviada a um tópico, é disponibilizada para cada assinatura para ser manipulada/processada de forma independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. Outra opção é registrar regras de filtro para um tópico por assinatura, o que permite que você filtre/restrinja quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Barramento de Serviço permitem o dimensionamento para processar um grande número de mensagens em muitos usuários e aplicativos.

## Criar um namespace de serviço

Para começar a usar as assinaturas e os tópicos do Barramento de Serviço no Azure, primeiro crie um namespace de serviço. Um namespace de serviço fornece um contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em**Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**. ![][0]

4.  No diálogo **Adicionar um novo namespace**, digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível. ![][2]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando seus recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Deixe os outros campos na caixa de diálogo com seus valores padrão (**Mensagens** e **Camada padrão**), em seguida, clique na marca de seleção. Agora, o sistema cria o seu namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][6]


## Obter as credenciais de gerenciamento padrão do namespace

A fim de executar operações de gerenciamento, como a criação de um tópico ou assinatura no novo namespace, obtenha as credenciais de gerenciamento para o namespace. Você pode obter essas credenciais no portal de gerenciamento do Azure.

### Para obter as credenciais de gerenciamento do portal

1.  No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para exibir a lista de namespaces disponíveis: ![][0]

2.  Na lista mostrada, clique no namespace que acabou de criar: ![][3]

3.  Clique em **Configurar** para exibir as políticas de acesso compartilhado para seu namespace. ![](./media/service-bus-java-how-to-create-topic/sb-queues-14.png)

4.  Anote a chave primária ou copie-a na área de transferência.


  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [0]: ./media/service-bus-java-how-to-create-topic/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-create-topic/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-create-topic/sb-queues-09.png
  [4]: ./media/service-bus-java-how-to-create-topic/sb-queues-06.png

  [6]: ./media/service-bus-java-how-to-create-topic/getting-started-multi-tier-27.png
  [34]: ./media/service-bus-java-how-to-create-topic/VSProperties.png

<!---HONumber=August15_HO6-->