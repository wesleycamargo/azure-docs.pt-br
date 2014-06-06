<a id="what-are-service-bus-queues"></a>
##O que são as filas do Brramento de Serviço?

Filas do Brramento de Serviço oferecem suporte a um **orientado a mensagens de comunicação**
modelo. Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário. Um produtor de mensagem (remetente) transmite uma mensagem para a fila e, em seguida, continua o processamento.
De forma assíncrona, um consumidor de mensagem (receptor) recebe a mensagem da fila e a processa. O produtor não precisa esperar por uma resposta do consumidor para continuar a processar e enviar mais mensagens. Filas oferecem **primeiro a entrar, primeiro-Out (FIFO)** mensagem de entrega para um ou mais clientes de concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas por receptores na ordem em que elas foram adicionadas à fila, e cada mensagem é recebida e processada pelo consumidor de apenas uma mensagem.

![Conceitos de fila](./media/howto-service-bus-queues/sb-queues-08.png)

Filas do Brramento de Serviço são uma tecnologia de uso geral que pode ser usada para uma grande variedade de cenários:

-   A comunicação entre as funções web e de trabalho em um aplicativo do Azure em várias camadas
-   A comunicação entre aplicativos locais e aplicativos do Azure hospedado em uma solução híbrida
-   A comunicação entre os componentes de um aplicativo distribuído em execução local em diferentes empresas ou departamentos de uma organização

Usando filas pode permitem dimensionar seus aplicativos melhores e permitir mais flexibilidade para sua arquitetura.

<a id="create-a-service-namespace"></a>
<h2>Criar um namespace de serviço</h2>

Para começar a usar filas do Brramento de Serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para endereçamento dos recursos do Brramento de Serviço em seu aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação à esquerda do Portal de Gerenciamento, clique em **Brramento de Serviço**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   
	![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando os recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

O namespace que você criou aparece no Portal de gerenciamento e reserva um tempo para ativar. Aguarde até que o status seja **ativos** antes de continuar.

<a id="obtain-default-credentials"></a>
<h2>Obter as credenciais de gerenciamento padrão do namespace</h2>

Para realizar operações de gerenciamento, como a criação de fila no novo namespace, você precisa obter as credenciais de gerenciamento para o namespace. Você pode obter essas credenciais do Portal de Gerenciamento ou do Gerenciador de servidores do Visual Studio.

###Para obter as credenciais de gerenciamento do portal

1.  No painel de navegação esquerdo, clique no nó **Brramento de Serviço** para
    exibir a lista dos namespaces disponíveis:   
	![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Selecione o namespace que você acabou de criar na lista abaixo:   
	![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Clique em **Informações de Conexão**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Na caixa de diálogo **Informações de conexão de acesso**, localiza as entradas de **Emissor padrão** e **Chave padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace.

###Para obter as credenciais de gerenciamento do Gerenciador de servidores

Para obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Brramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

Anote a o valor da **SharedAccessKey** ou copie-a na área de transferência:

![][34]

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com

  [34]: ./media/howto-service-bus-queues/VSProperties.png

