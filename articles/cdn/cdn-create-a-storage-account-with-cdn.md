<properties
	pageTitle="Como usar a CDN | Microsoft Azure"
	description="Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure para fornecer conteúdo com alta largura de banda armazenando em cache blobs e conteúdo estático."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# Integrar uma conta de armazenamento com CDN

CDN pode ser habilitada em cache o conteúdo do armazenamento do Azure. Ela oferece aos desenvolvedores uma solução global para entrega de conteúdo de largura de banda armazenando em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul.


## Etapa 1: Criar uma conta de armazenamento

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura do Azure. A conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o mais alto nível do namespace para acessar cada um dos componentes do serviço de armazenamento do Azure: serviços Blob, serviços Fila e serviços Tabela. Para obter mais informações, veja [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).

Para criar uma conta de armazenamento, você deve ser o administrador de serviços ou um coadministrador da assinatura associada.

> [AZURE.NOTE] Há vários métodos que você pode usar para criar uma conta de armazenamento, incluindo o Portal do Azure e o Powershell. Para este tutorial, usaremos o Portal do Azure.

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Entre no [Portal do Azure](https://portal.azure.com).
2.  No canto superior esquerdo, selecione **Novo**. No Diálogo **Novo**, selecione **Dados + Armazenamento** e clique em **Conta de armazenamento**. Mantenha **Clássico** selecionado como o modelo de implantação e clique em **Criar**.

    A folha **Conta de armazenamento** é exibida.

    ![Criar Conta de Armazenamento][create-new-storage-account]

4. No campo **Armazenamento**, digite um nome de subdomínio. Essa entrada pode conter de 3 a 24 letras minúsculas e números.

    Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão. Para atender a um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, em que *<StorageAccountLabel>* refere-se ao valor digitado em **Inserir uma URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** o rótulo da URL forma o subdomínio do URI da conta de armazenamento e deve ser exclusivo entre todos os serviços hospedados no Azure.

	Esse valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5.  Selecione um **Tipo de preço** ou use o padrão. Para obter mais informações sobre tipos de preço, veja [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

6.  Selecione ou crie um **Grupo de Recursos**. Para obter mais informações sobre Grupos de Recursos, confira [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md#resource-groups).

7. Selecione a **assinatura** que será usada com a conta de armazenamento.

8.  Clique em **Criar**. O processo de criação da conta de armazenamento pode levar vários minutos para ser concluído.

9.  Para verificar se a conta de armazenamento foi criada com êxito, verifique se a conta aparece nos itens listados para **Armazenamento** com um status de **Online**.


## Etapa 2: criar um novo perfil CDN

Um perfil CDN é uma coleção de pontos de extremidade CDN. Cada perfil contém um ou mais pontos de extremidade CDN. Você pode desejar usar vários perfis para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério.

> [AZURE.TIP] Se você já tiver um perfil CDN que você deseja usar para este tutorial, vá para [Etapa 3](#step-3-create-a-new-cdn-endpoint).

**Para criar um perfil CDN**

1. No [Portal de Gerenciamento do Azure](https://portal.azure.com), no canto superior esquerdo, clique em **Novo**. Na folha **Novo**, selecione **Mídia + CDN** e, em seguida, **CDN**.

    A folha do novo perfil CDN é exibida.

    ![Novo perfil CDN][new-cdn-profile]

2. Insira um nome para o perfil CDN.

3. Selecione um **Tipo de preço** ou use o padrão.

4. Selecione ou crie um **Grupo de Recursos**. Não é necessário que esse seja o mesmo Grupo de Recursos que a sua conta de armazenamento.

5. Selecione a **Assinatura** para este perfil CDN. Isso precisará ser a mesma assinatura que a da conta de armazenamento para os fins deste tutorial.

6. Selecione um **Local**. Esse é o local do Azure onde suas informações de perfil CDN serão armazenadas. Ele não tem impacto sobre os locais de ponto de extremidade CDN. Não precisa ser o mesmo local da conta de armazenamento.

7. Clique no botão **Criar** para criar um novo perfil.

## Etapa 3: criar um novo ponto de extremidade CDN

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure](https://portal.azure.com), navegue até o seu perfil CDN. Você pode ter fixado ao painel na etapa anterior. Se não, você poderá encontrá-lo clicando em **Procurar**, em **perfis CDN** e clicando no perfil ao qual você pretende adicionar o ponto de extremidade.

    A folha do perfil CDN é exibida.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar Ponto de Extremidade**.

    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]

    A folha **Adicionar um ponto de extremidade** é exibida.

    ![Adicionar folha de ponto de extremidade][cdn-add-endpoint]

3. Insira um **Nome** para esse ponto de extremidade CDN. Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net`.

4. No menu suspenso **Tipo de origem**, selecione *Armazenamento*.

5. No menu suspenso **Nome do host de origem**, selecione a conta de armazenamento.

6. Mantenha os padrões para **Caminho de origem**, **Cabeçalho de host de origem** e **Porta de protocolo/origem**. Você deve especificar pelo menos um protocolo (HTTP ou HTTPS).

    > [AZURE.NOTE] Essa configuração permite o caching na CDN de todos os contêineres publicamente visíveis na conta de armazenamento. Se você deseja limitar o escopo a um único contêiner, use o **Caminho de origem**. Observe que o contêiner deve ter sua visibilidade definida como pública.

7. Clique no botão **Adicionar** para criar um novo ponto de extremidade.

8. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade do perfil. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem.

    ![Ponto de extremidade CDN][cdn-endpoint-success]

    > [AZURE.NOTE] O ponto de extremidade não estará imediatamente disponível para uso. Pode levar até 90 minutos para que o registro seja propagado pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 404 até que o conteúdo esteja disponível pela CDN.


## Etapa 4: acessar conteúdo da CDN

Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. O endereço de um blob armazenado em cache será semelhante ao seguinte:

http://<*NomePontoExtremidade*>.azureedge.net/<*meuContêinerPúblico*>/<*NomeBlob*>

> [AZURE.NOTE] Depois que você habilitar o acesso à CDN para uma conta de armazenamento ou serviço hospedado, todos os objetos disponíveis publicamente estarão qualificados para armazenamento em cache de borda CDN. Se você modificar um objeto que está armazenado em cache na CDN atualmente, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualize seu conteúdo quando o período de vida do conteúdo em cache expirar.

## Etapa 5: remover conteúdo da CDN

Se não desejar mais armazenar em cache um objeto na CDN (Rede de Distribuição de Conteúdo) do Azure, você poderá executar uma das seguintes etapas:

-   Você pode tornar o contêiner particular em vez de público. Veja [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../storage/storage-manage-access-to-resources.md) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de Gerenciamento.
-   Você pode modificar seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado em cache na CDN permanecerá em cache até que o período de vida útil do objeto expire ou até que o ponto de extremidade seja limpo. Quando o período de vida expira, a CDN verifica se o ponto de extremidade CDN ainda é válido, e se o objeto ainda pode ser acessado anonimamente. Se não for, o objeto não estará mais armazenado em cache.


## Recursos adicionais

-   [Como mapear o conteúdo da CDN para um domínio personalizado](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0504_2016-->