<properties 
	pageTitle="Como usar a CDN | Microsoft Azure" 
	description="Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure para fornecer conteúdo com alta largura de banda armazenando em cache blobs e conteúdo estático." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="mazha"/>


# Integrar uma conta de armazenamento com CDN

CDN pode ser habilitada em cache o conteúdo do armazenamento do Azure. Ela oferece aos desenvolvedores uma solução global para entrega de conteúdo de largura de banda armazenando em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul.


## Etapa 1: Criar uma conta de armazenamento

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura do Azure. A conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o mais alto nível do namespace para acessar cada um dos componentes do serviço de armazenamento do Azure: serviços Blob, serviços Fila e serviços Tabela. Para obter mais informações sobre os serviços de armazenamento do Azure, consulte [Usando os Serviços de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para criar uma conta de armazenamento, você deve ser o administrador de serviços ou um coadministrador da assinatura associada.

> [AZURE.NOTE]Para obter informações sobre como executar esta operação usando a API de Gerenciamento de Serviços do Azure, consulte o tópico de referência [Criar conta de armazenamento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Faça logon no [Portal de Gerenciamento do Azure].
2.  No canto inferior esquerdo, clique em **Novo**. Na caixa de diálogo **Novo**, selecione **Serviços de Dados** e, em seguida, clique em **Armazenamento** e em **Criação Rápida**.

    A caixa de diálogo **Criar Conta de Armazenamento** é exibida.

    ![Criar Conta de Armazenamento][create-new-storage-account]

4. No campo **URL**, digite um nome de subdomínio. Essa entrada pode conter de 3 a 24 letras minúsculas e números.

    Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão. Para atender a um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, em que *<StorageAccountLabel>* refere-se ao valor digitado em **Inserir uma URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** o rótulo da URL forma o subdomínio do URI da conta de armazenamento e deve ser exclusivo entre todos os serviços hospedados no Azure.

	Esse valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5.  Na lista suspensa **Região/Grupo de Afinidade**, selecione uma localização ou um grupo de afinidade para a conta de armazenamento. Selecione um grupo de afinidade em vez de uma região se desejar ter os serviços de armazenamento no mesmo data center com outros serviços do Microsoft Azure que você está usando. Isso pode melhorar o desempenho e nenhum encargo será incorrido para a saída.

    **Observação:** para criar um grupo de afinidade, abra a área **Configurações** do Portal de Gerenciamento, clique em **Grupos de Afinidade** e, em seguida, clique em **Adicionar um Grupo de Afinidade** ou então em **Adicionar**. Você também pode criar e gerenciar os grupos de afinidade usando a API de Gerenciamento de Serviços do Microsoft Azure. Para saber mais, consulte [Operações em grupos de afinidade].

6. Na lista suspensa **Assinatura**, selecione a assinatura com a qual a conta de armazenamento será usada.
7.  Clique em **Criar Conta de Armazenamento**. O processo de criação da conta de armazenamento pode levar vários minutos para ser concluído.
8.  Para verificar se a conta de armazenamento foi criada com êxito, verifique se a conta aparece nos itens listados para **Armazenamento** com um status de **Online**.


## Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento

Depois que você habilitar o acesso à CDN para uma conta de armazenamento ou serviço hospedado, todos os objetos disponíveis publicamente estarão qualificados para armazenamento em cache de borda CDN. Se você modificar um objeto que está armazenado em cache na CDN atualmente, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualize seu conteúdo quando o período de vida do conteúdo em cache expirar.

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure], no painel de navegação, clique em **CDN**.

2. Na faixa de opções, clique em **Novo**. Na caixa de diálogo **novo**, selecione **Serviços de Aplicativo**, **CDN** e, em seguida, em **Criação Rápida**.

3. No menu suspenso **Domínio de Origem**, selecione a conta de armazenamento que você criou na seção anterior na lista de suas contas de armazenamento disponíveis.

4. Clique no botão **Criar** para criar um novo ponto de extremidade.

5. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade da assinatura. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem.

	O domínio de origem é o local de onde a CDN armazena em cache o conteúdo. O domínio de origem pode ser uma conta de armazenamento ou um serviço de nuvem. Uma conta de armazenamento é usada para fins deste exemplo. O conteúdo do armazenamento é armazenado em cache em servidores de borda, de acordo com uma configuração de controle de cache que você especificar ou com a heurística padrão da rede de armazenamento em cache. Consulte [Como gerenciar a expiração do conteúdo Blob](http://msdn.microsoft.com/library/gg680306.aspx) para obter mais informações.


    > [AZURE.NOTE]A configuração criada para o ponto de extremidade não estará imediatamente disponível. Pode levar até 60 minutos para o registro se propagar pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 400 (Solicitação Incorreta) até que o conteúdo esteja disponível por meio da CDN.


## Etapa 3: Acessar conteúdo CDN

Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. O endereço de um blob armazenado em cache será semelhante ao seguinte:

http://<*NamespaceDoCDN*>.vo.msecnd.net/<*meuContêinerPúblico*>/<*NomeDoBlob*>


## Etapa 4: Remover conteúdo da CDN

Se não desejar mais armazenar em cache um objeto na CDN (Rede de Distribuição de Conteúdo) do Azure, você poderá executar uma das seguintes etapas:

-   Você pode tornar o contêiner particular em vez de público. Consulte [Restringir o acesso aos contêineres e Blobs (a página pode estar em inglês)](http://msdn.microsoft.com/library/dd179354.aspx) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de Gerenciamento.
-   Você pode modificar seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado na CDN permanecerá em cache até que o período de tempo de vida do objeto expire. Quando o período de vida expira, a CDN verifica se o ponto de extremidade CDN ainda é válido, e se o objeto ainda pode ser acessado anonimamente. Se não for, o objeto não estará mais armazenado em cache.

A capacidade de limpar imediatamente o conteúdo não tem suporte atualmente no Portal de Gerenciamento do Azure. Se for necessário limpar o conteúdo imediatamente, entre em contato com o [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Recursos adicionais

-   [Como Criar um Grupo de Afinidade no Azure]
-   [Como gerenciar contas de armazenamento para uma assinatura do Azure]
-   [Como mapear o conteúdo da CDN para um domínio personalizado]

[Create Storage Account]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure CDN Node Locations]: cdn-pop-locations.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[billing plan]: /pricing/calculator/?scenario=full
[How to Register a Custom Subdomain Name for Accessing Blobs in Azure]: ../storage-custom-domain-name.md
[Como Criar um Grupo de Afinidade no Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: cdn-overview.md
[Como gerenciar contas de armazenamento para uma assinatura do Azure]: https://msdn.microsoft.com/pt-br/library/azure/hh531793.aspx
[Como mapear o conteúdo da CDN para um domínio personalizado]: cdn-map-content-to-custom-domain.md


[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

 

<!---HONumber=August15_HO7-->