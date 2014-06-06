<h2><a name="what-are-service-bus-topics"></a>O que são os tópicos e as assinaturas do Brramento de Serviço</h2>

Os tópicos e assinaturas do Brramento de Serviço oferecem suporte a um modelo de **comunicação de mensagens de publicação/assinatura**. Ao usar tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![Conceitos de tópico](./media/howto-service-bus-topics/sb-topics-01.png)

Ao contrário das filas do Brramento de Serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem um formulário de comunicação **um-para-muitos** usando um padrão de publicação/assinatura. É possível registrar várias assinaturas em um determinado tópico. Quando uma mensagem é enviada a um tópico, ele é então disponibilizado para cada assinatura para ser gerenciado/processado de forma independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, você pode registrar regras de filtro para um tópico em uma base por assinatura, o que lhe permite filtrar/restringir quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Brramento de Serviço permitem dimensionar para processar um grande número de mensagens em um grande número de usuários e aplicativos.

<h2><a name="create-a-service-namespace"></a>Criar um namespace de serviço</h2>

Para começar a usar assinaturas e tópicos do Brramento de Serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para endereçamento dos recursos do Brramento de Serviço em seu aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação à esquerda do Portal de Gerenciamento, clique em **Brramento de Serviço**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   
    ![][0]

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
    ![][2]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando os recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][6]


<h2><a name="obtain-default-credentials"></a>Obter as credenciais de gerenciamento padrão do namespace</h2>

Para realizar operações de gerenciamento, como a criação de fila no novo namespace, você precisa obter as credenciais de gerenciamento para o namespace, você precisa obter as credenciais de gerenciamento para o namespace. Você pode obter essas credenciais do Portal de Gerenciamento ou do Gerenciador de servidores do Visual Studio.

###Para obter as credenciais de gerenciamento do portal

1.  No painel de navegação esquerdo, clique no nó **Brramento de Serviço** para
    exibir a lista dos namespaces disponíveis:   
    ![][0]

2.  Selecione o namespace que você acabou de criar na lista abaixo:   
    ![][3]

3.  Clique em **Informações de Conexão**.   
    ![][4]

4.  Na caixa de diálogo **Informações de conexão de acesso**, localiza as entradas de **Emissor padrão** e **Chave padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace. 

###Para obter as credenciais de gerenciamento do Gerenciador de servidores

Para obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Brramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

Anote a o valor da **SharedAccessKey** ou copie-a na área de transferência:

![][34]

 
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png
  [34]: ./media/howto-service-bus-topics/VSProperties.png

