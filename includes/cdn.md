# Usando a CDN para Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure oferece aos desenvolvedores uma solução global para entrega de conteúdo de largura de banda armazenando em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul. Para obter uma lista atual de locais de nó CDN, consulte [Locais de nós CDN Azure].

Esta tarefa inclui as seguintes etapas:

* [Etapa 1: Criar uma conta de armazenamento](#Step1)
* [Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento](#Step2)
* [Etapa 3: Acesse seu conteúdo CDN](#Step3)
* [Etapa 4: Remova seu conteúdo CDN](#Step4)

Os benefícios do uso da CDN para armazenar dados do Azure em cache incluem:

-   Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos onde muitas 'viagens de Internet' são necessárias para carregar conteúdo
-   Grande escala distribuída para lidar melhor com alta carga instantânea, digamos, no início de um evento, como o lançamento de um produto

Clientes CDN existentes agora podem usar a CDN do Azure no [Portal de Gerenciamento do Azure]. A CDN é um recurso suplementar à sua assinatura e possui um [plano de faturamento] separado.

<a id="Step1"> </a>
<h2>Etapa 1: Criar uma conta de armazenamento</h2>

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura do Azure. A conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o nível mais alto do namespace para acessar cada componente de serviços de armazenamento do Azure: Serviços Blob, serviços Fila e serviços Tabela. Para obter mais informações sobre os serviços de armazenamento do Azure, consulte [Usando os Serviços de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/azure/gg433040.aspx).

Para criar uma conta de armazenamento, você deve ser o administrador de serviços ou um coadministrador da assinatura associada.

<div class="dev-callout">
<strong>Observação</strong>
<p>Para obter informações sobre executar esta operação usando a API de Gerenciamento de Serviço do Azure, consulte o tópico de referência <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx">Criar conta de armazenamento</a> .</p>
</div>

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Faça logon no [Portal de Gerenciamento do Azure].
2.  No canto inferior esquerdo, clique em **Novo**. No diálogo **Novo**, selecione **Serviços de dados**, em seguida, clique em **Armazenamento**, depois em **Criação rápida**.

    O diálogo **Criar conta de armazenamento** é exibido.

    ![Create Storage Account][create-new-storage-account]

4. No campo **URL**, digite um nome de subdomínio.Essa entrada pode conter de 3 a 24 letras minúsculas e números.

    Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão. Para abordar um recurso de contêiner no serviço Blob, você usaria um URI no formato a seguir, em que *<StorageAccountLabel>* refere-se ao valor que digitou em **Inserir uma URL**:

    http://*<StorageAcountLabel>*.blob.core.windows.net/*<mycontainer>*

    **Importante:**O rótulo da URL forma o subdomínio do URI da conta de armazenamento e deve ser exclusivo entre todos os serviços hospedados no Azure.

	Esse valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5.  Na lista suspensa **Região/Grupo de afinidade**, selecione uma região ou grupo de afinidade para a conta de armazenamento. Selecione um grupo de afinidade em vez de uma região se desejar ter os serviços de armazenamento no mesmo data center com outros serviços do Microsoft Azure que você está usando.  Isso pode melhorar o desempenho e nenhum encargo será incorrido para a saída.  

    **Observação:**  Para criar um grupo de afinidade, abra a área **Configurações** do Portal de Gerenciamento, clique em **Grupos de Afinidade** e em **Adicionar grupo de afinidade** ou em **Adicionar**.Você também pode criar e gerenciar os grupos de afinidade usando a API de Gerenciamento de Serviços do Microsoft Azure.Para obter mais informações, consulte [Operações em grupos de afinidade].

6. Na lista suspensa **Assinatura**, selecione a assinatura com que a conta de armazenamento será usada.
7.  Clique em **Criar Conta de Armazenamento**.O processo de criação da conta de armazenamento pode levar vários minutos para ser concluído.
8.  Para verificar que a conta de armazenamento foi criada com sucesso, verifique se a conta aparece nos itens listados para **Armazenamento** com um status de **Online**.

<a id="Step2"> </a>
<h2>Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento</h2>

Depois que você habilitar o acesso à CDN para uma conta de armazenamento ou serviço hospedado, todos os objetos disponíveis publicamente estarão qualificados para armazenamento em cache de borda CDN. Se você modificar um objeto que está armazenado em cache na CDN atualmente, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualize seu conteúdo quando o período de vida do conteúdo em cache expirar.

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure], no painel de navegação, clique em **CDN**.

2. Na faixa de opções, clique em **Novo**. No diálogo **Novo**, selecione **Serviços de Aplicativos**, em seguida,**CDN**, depois**Criação rápida**.

3. No menu suspenso **Domínio de origem**, selecione a conta de armazenamento que criou na seção anterior da lista de suas contas de armazenamento disponíveis. 

4. Clique no botão **Criar** para criar o novo ponto de extremidade.

5. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade da assinatura. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem. 

	O domínio de origem é o local de onde a CDN armazena em cache o conteúdo. O domínio de origem pode ser uma conta de armazenamento ou um serviço de nuvem. Uma conta de armazenamento é usada para fins deste exemplo. O conteúdo do armazenamento é armazenado em cache em servidores de borda, de acordo com uma configuração de controle de cache que você especificar ou com a heurística padrão da rede de armazenamento em cache. Consulte [Como gerenciar expiração de conteúdo Blob](http://msdn.microsoft.com/pt-br/library/gg680306.aspx) para obter mais informações. 


    <div class="dev-callout">
    <strong>Note</strong>
    <p>The configuration created for the endpoint will not
    immediately be available; it can take up to 60 minutes for the
    registration to propagate through the CDN network. Users who try to
    use the CDN domain name immediately may receive status code 400
    (Bad Request) until the content is available via the CDN.</p>
    </div>

<a id="Step3"> </a>
<h2>Etapa 3: Acesse o conteúdo CDN</h2> 

Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. O endereço de um blob armazenado em cache será semelhante ao seguinte:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Etapa 4: Remover o conteúdo do CDN</h2>

Se você não quiser mais armazenar em cache um objeto na CDN
(Rede de Distribuição de Conteúdo) do Azure, você pode executar uma das duas seguintes etapas:

-   Para um blob do Azure, você pode excluir o blob do contêiner público. -   Você pode tornar o contêiner particular em vez de público. Consulte [Restringir o acesso aos contêineres e blobs](http://msdn.microsoft.com/pt-br/library/dd179354.aspx) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de Gerenciamento.
-   Você pode modificar seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado na CDN permanecerá em cache até que o período de tempo de vida do objeto expire. Quando o período de vida expira, a CDN verifica se o ponto de extremidade CDN ainda é válido, e se o objeto ainda pode ser acessado anonimamente. Se não for, o objeto não estará mais armazenado em cache.

A capacidade de limpar imediatamente o conteúdo não tem suporte atualmente no Portal de Gerenciamento do Azure. Entre em contato com o [Suporte do Azure](http://azure.microsoft.com/pt-br/support/options/)  se você precisar limpar o conteúdo imediatamente. 

## Recursos adicionais

-   [Como criar um grupo de afinidade no Azure]
-   [Como: Gerenciar contas de armazenamento para uma assinatura do Azure]
-   [Sobre a API de Gerenciamento de Serviço]
-   [Como mapear o conteúdo da CDN para um domínio personalizado]

  [Criar conta de armazenamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
  [Locais de nó do CDN do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg680302.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [plano de faturamento]: /pt-br/pricing/calculator/?scenario=full
  [Como registrar um nome de subdomínio personalizado para acessar Blobs no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee795179.aspx
  [Como criar um grupo de afinidade no Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Visão geral da CDN do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff919703.aspx
  [Como: Gerenciar contas de armazenamento para uma assinatura do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh531567.aspx
  [Sobre a API de Gerenciamento de Serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee460807.aspx
  [Como mapear o conteúdo da CDN para um domínio personalizado]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Portal de Gerenciamento anterior]: ../../Shared/Media/previous-portal.png

<!--HONumber=35_1-->
