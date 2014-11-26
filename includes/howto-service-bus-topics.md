## <a name="what-are-service-bus-topics"></a>O que são os tópicos e as assinaturas do Barramento de Serviço

Os tópicos e assinaturas do Barramento de Serviço dão suporte a um modelo de **comunicação de mensagens
de publicação/assinatura**. Ao usar tópicos e assinaturas, os
componentes de um aplicativo distribuído não se comunicam diretamente uns
com os outros, mas compartilham mensagens por meio de um tópico, que atua como um
intermediário.

![Conceitos de tópico][Conceitos de tópico]

Ao contrário das filas do Barramento de Serviço, em que cada mensagem é processada por um
único consumidor, tópicos e assinaturas fornecem uma forma de comunicação **um-para-muitos**,
usando um padrão de publicação/assinatura. É possível
registrar várias assinaturas para um tópico. Quando uma mensagem é enviada a um
tópico, ele é disponibilizado para cada assinatura para ser manipulado/processado de forma
independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das
mensagens que foram enviadas para o tópico. Outra opção é registrar
regras de filtro para um tópico em uma base por assinatura, o que permite que você
filtre/restrinja quais mensagens para um tópico são recebidas por quais assinaturas
de tópico.

As assinaturas e os tópicos do Barramento de Serviço permitem o dimensionamento para processar um
grande número de mensagens em muitos usuários e
aplicativos.

## <a name="create-a-service-namespace"></a>Criar um namespace de serviço

Para começar a usar as assinaturas e os tópicos do Barramento de Serviço no Azure,
primeiro crie um namespace de serviço. Um namespace de serviço fornece
um contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu
aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em
    **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.
    ![][0]

4.  No diálogo **Adicionar um novo namespace**, insira um nome de namespace.
    O sistema imediatamente verifica se o nome está disponível.
    ![][1]

5.  Depois de verificar se o nome do namespace está disponível, escolha o
    país ou a região em que o namespace deve ser hospedado (certifique-se
    de usar o mesmo país/região em que você está implantando seus
    recursos de computação).

    IMPORTANTE: Selecione a **mesma região** que você pretende escolher para
    implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.  Clique na marca de seleção. Agora, o sistema cria o seu namespace
    de serviço e o habilita. Talvez você precise aguardar vários minutos, enquanto
    o sistema provisiona recursos para sua conta.

    ![][2]

## <a name="obtain-default-credentials"></a> Obter as credenciais do gerenciamento padrão do Namespace

A fim de executar operações de gerenciamento, como a criação de um tópico ou
assinatura no novo namespace, obtenha as credenciais de
gerenciamento para o namespace. Você pode obter essas credenciais do Portal de Gerenciamento ou do Gerenciador de servidores do Visual Studio.

### Para obter as credenciais de gerenciamento do portal

1.  No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para
    exibir a lista de namespaces disponíveis:
    ![][0]

2.  Selecione o namespace que você acabou de criar na lista abaixo:
    ![][3]

3.  Clique em **Informações de Conexão**.
    ![][4]

4.  Na caixa de diálogo **Informações de conexão de acesso**, localize as entradas de **Emissor Padrão** e **Chave Padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace.

### Para obter credenciais de gerenciamento do Gerenciador de Servidores

Para obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui][aqui], na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Brramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio.

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência:

![][5]

  [Conceitos de tópico]: ./media/howto-service-bus-topics/sb-topics-01.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [1]: ./media/howto-service-bus-topics/sb-queues-04.png
  [2]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  [aqui]: http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx
  [5]: ./media/howto-service-bus-topics/VSProperties.png
