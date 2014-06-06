# Usando a CDN para Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure oferece aos desenvolvedores uma solução global para entrega de conteúdo de largura de banda alta armazenando em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul. Para obter uma lista atual dos locais de nós CDN, consulte [Locais de nós CDN para Azure]

Esta tarefa inclui as seguintes etapas:

* [Etapa 1: Criar uma conta de armazenamento](#Step1)
* [Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento](#Step2)
* [Etapa 3: Acessar seu conteúdo CDN](#Step3)
* [Etapa 4: Excluir seu conteúdo CDN](#Step4)

Os benefícios do uso da CDN para armazenar dados do Azure em cache incluem:

-   Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos onde muitas 'viagens de internet' são necessárias para carregar conteúdo
-   Grande escala distribuída para lidar melhor com alta carga instantânea, digamos, no início de um evento, como o lançamento de um produto

Os clientes atuais da CDN agora podem usar a CDN do Azure no [Portal de Gerenciamento do Azure]. A CDN é um recurso suplementar à sua assinatura e tem um [plano de faturamento] separado.

<a id="Step1"> </a>
<h2>Etapa 1: Criar uma conta de armazenamento</h2>

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura do Azure. A conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o mais alto nível do namespace para acessar cada um dos componentes do serviço de armazenamento do Azure: serviços Blob, serviços Fila e serviços Tabela. Para obter mais informações sobre os serviços de armazenamento do Azure, consulte [Usando os Serviços de Armazenamento do Azure (a página pode estar em inglês)].

Para criar uma conta de armazenamento, você deve ser o administrador de serviço ou um coadministrador da assinatura associada.

<div class="dev-callout">
<strong>Observação</strong>
<p>Para obter informações sobre como executar esta operação usando a API de Gerenciamento de Serviços do Azure, consulte o tópico de referência <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx">Criar conta de armazenamento</a>.</p>
</div>

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Faça logon no [Portal de Gerenciamento do Azure].
2.  No canto inferior esquerdo, clique em **Novo**e, em seguida, clique em **Armazenamento**.
3.  Clique em **Criação Rápida**.

    A caixa de diálogo **Criar Conta de Armazenamento** é exibida.

    ![Criar Conta de Armazenamento][create-new-storage-account]

4. No campo **URL**, digite um nome de subdomínio. Essa entrada pode conter de 3 a 24 letras minúsculas e números.

    Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura. Para atender a um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, onde *&lt;StorageAccountLabel&gt;* refere-se ao valor digitado em **Inserir uma URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** o rótulo da URL forma o subdomínio do URI da conta de armazenamento e deve ser exclusivo entre todos os serviços hospedados no Azure.

	Esse valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5.  Na lista suspensa **Região/Grupo de Afinidade**, selecione uma localização geográfica para a conta de armazenamento. Como alternativa, use um grupo de afinidade. Para obter instruções sobre como criar um grupo de afinidade, consulte [Como criar um grupo de afinidade no Azure].
6. Na lista suspensa **Assinatura**, selecione a assinatura com a qual a conta de armazenamento será usada.
7.  Clique em **Criar Conta de Armazenamento**. O processo de criação da conta de armazenamento pode levar vários minutos para ser concluído.
8.  Para verificar se a conta de armazenamento foi criada com êxito, verifique se a conta aparece nos itens listados para **Armazenamento** com um status de **Online**.

<a id="Step2"> </a>
<h2>Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento</h2>

depois que você habilitar o acesso à CDN para uma conta de armazenamento ou serviço hospedado, todos os objetos disponíveis publicamente estarão qualificados para armazenamento em cache de borda CDN. Se você modificar um objeto que está armazenado em cache na CDN atualmente, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualize seu conteúdo quando o período de vida do conteúdo em cache expirar.

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure], no painel de navegação, clique em **CDN**.

2. Na faixa de opções, clique em **Novo**. Na caixa de diálogo **novo**, selecione **Serviços de Aplicativo**, **CDN** e, em seguida, em **Criação Rápida**.

3. No menu suspenso **Domínio de Origem**, selecione a conta de armazenamento que você criou na seção anterior na lista de suas contas de armazenamento disponíveis. 

4. Clique no botão **Criar** para criar um novo ponto de extremidade.

5. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade da assinatura. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem. 

	O domínio de origem é o local do qual a CDN armazena conteúdo em cache. O domínio de origem pode ser uma conta de armazenamento ou um serviço de nuvem. Uma conta de armazenamento é usada para fins deste exemplo. O conteúdo do armazenamento é armazenado em cache em servidores de borda, de acordo com uma configuração de controle de cache que você especificar ou com a heurística padrão da rede de armazenamento em cache. Consulte [Como gerenciar a expiração do conteúdo Blob](http://msdn.microsoft.com/pt-br/library/gg680306.aspx) para obter mais informações. 


    <div class="dev-callout">
    <strong>Observação</strong>
    <p>A configuração criada para o ponto de extremidade não estará imediatamente disponível. Pode levar até 60 minutos para o registro se propagar pela rede CDN. Os usuários que tentarem usar o nome de domínio CDN imediatamente poderão receber o código de status 400 (Solicitação incorreta) até que o conteúdo esteja disponível por meio da CDN.</p>
    </div>

<a id="Step3"> </a>
<h2>Etapa 3: Acessar conteúdo CDN</h2> 

Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. O endereço de um blob armazenado em cache será semelhante ao seguinte:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Etapa 4: Remover conteúdo da CDN</h2>

Se não desejar mais armazenar em cache um objeto na CDN (Rede de Distribuição de Conteúdo) do Azure, você poderá executar uma das seguintes etapas:

-   Para um blob do Azure, você pode excluir o blob do contêiner público.
-   Você pode tornar o contêiner particular em vez de público. Consulte [Restringir o acesso aos contêineres e Blobs (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/dd179354.aspx) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de Gerenciamento.
-   Você pode modificar seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado na CDN permanecerá em cache até que o período de tempo de vida do objeto expire. quando o período de vida expira, a CDN verifica se o ponto de extremidade CDN ainda é válido, e se o objeto ainda pode ser acessado anonimamente. Se não estiver, o objeto não estará mais armazenado em cache.

Nenhuma ferramenta explícita de "limpeza" está disponível atualmente para a CDN do Azure.

## Recursos adicionais

-   [Como criar um Grupo de Afinidade no Azure]
-   [Como gerenciar contas de armazenamento para uma assinatura do Azure]
-   [Sobre a API de Gerenciamento de Serviços (a página pode estar em inglês)]
-   [Como mapear o conteúdo CDN para um domínio personalizado]

  [Criar conta de armazenamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
  [Locais de nós CDN para Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg680302.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [plano de faturamento]: /pt-br/pricing/calculator/?scenario=full
  [Usando os Serviços de Armazenamento do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee924681.aspx
  [Como registrar um nome de subdomínio personalizado para acessar Blobs no Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee795179.aspx
  [Como criar um grupo de afinidade no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh531560.aspx
  [Visão geral da CDN do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff919703.aspx
  [Como gerenciar contas de armazenamento para uma assinatura do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh531567.aspx
  [Sobre a API de Gerenciamento de Serviços (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee460807.aspx
  [Como mapear o conteúdo CDN para um domínio personalizado]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png


