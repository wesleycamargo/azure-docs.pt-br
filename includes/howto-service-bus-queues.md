<a id="what-are-service-bus-queues"></a>
##O que são as filas do Barramento de Serviço?

Filas do Barramento de Serviço suportam um modelo **orientado a mensagens de comunicação** m. Ao usar as filas, os componentes de um aplicativo distribuído não cse comunicam diretamente uns com os outros, em vez disso, eles trocam mensagens por meio de uma a fila, que atua como um intermediário (agente). Um produtor de mensagem (remetente) htransmite uma mensagem para a fila e, em seguida, continua o processamento.
De forma assíncrona, um consumidor de mensagem (receptor) recebe a mensagem da qfila e a processa. O produtor não precisa esperar uma resposta fdo consumidor para continuar a processar e enviar mais mmensagens. Filas oferecem entrega de mensagem do tipo **PEPS (primeiro a entrar, primeiro a sair)** t para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente rrecebidas e processadas pelos receptores na ordem em que foram aadicionadas à fila, sendo que cada mensagem é recebida e processada por apenas oum consumidor de mensagem.
 
![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Filas do Barramento de Serviço são uma tecnologia de uso geral que pode ser usada para a uma grande variedade de cenários:

-   Comunicação entre as funções web e de trabalho em um
    aplicativo multicamada do Azure
-   Comunicação entre aplicativos locais e aplicativos hospedados pelo Azure
    em uma solução híbrida
-   Comunicação entre os componentes de um aplicativo distribuído
    executado localmente em diferentes
    organizações ou departamentos de uma
    organização

O uso de filas permite uma maneira melhor de você escalar horizontalmente seus aplicativos e econcede mais resiliência à sua arquitetura.

## Criar um namespace de serviço

Para começar a usar as filas do barramento de serviço no Azure, você deve primeiro ccriar um namespace de serviço. Um namespace de serviço fornece um contêiner cpara controle de escopo, que permite endereçar recursos do barramento de serviço dentro de seu aplicativo.

Para criar um namespace de serviço:
1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em
    **Barramento de Serviço**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   

	![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Depois de verificar se o nome do namespace está disponível, escolha o
    país ou a região em que o namespace deve ser hospedado (certifique-se
    de usar o mesmo país/região em que você está implantando seus
    recursos de computação).

	IMPORTANTE: Selecione a **mesma região** que você pretende escolher para
    implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6. 	Deixe os outros campos na caixa de diálogo com seus valores padrão (**Mensagens** e **Camada padrão**), em seguida, clique na marca de seleção. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, enquanto o sistema provisiona recursos para sua conta.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

O namespace que você criou aparecerá no Portal de Gerenciamento e tleva um momento para ser ativado. Aguarde até que o status seja **Ativo** para ccontinuar.

## Obter as credenciais de gerenciamento padrão do namespace

Para realizar operações de gerenciamento como a criação de uma fila, no tnovo namespace, você deve obter as credenciais de gerenciamento para o nnamespace. Você pode obter essas credenciais do Portal de Gerenciamento ou do Gerenciador de servidores do Visual Studio.

###Para obter as credenciais de gerenciamento do portal

1.  No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para
    exibir a lista de namespaces disponíveis:   
	![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Selecione o namespace que você acabou de criar na lista abaixo:   
	![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Clique em **Informações de Conexão**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  No painel **Acessar as informações de conexão**, encontre a cadeia de conexão que contém a chave SAS e o nome da chave.   

	![](./media/howto-service-bus-queues/multi-web-45.png)
    

4.  Anote a chave ou copie-a na área de transferência.

###Para obter credenciais de gerenciamento do Gerenciador de Servidores

Para obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://msdn.microsoft.com/library/windowsazure/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Barramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência:

![][34]

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com

  [34]: ./media/howto-service-bus-queues/VSProperties.png

<!---HONumber=47-->
