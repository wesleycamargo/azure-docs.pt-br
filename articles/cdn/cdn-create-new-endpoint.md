<properties 
	 pageTitle="Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure" 
	 description="Este tópico mostra como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>



#Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure  

A CDN pode ser habilitada para a origem por meio do Portal de Gerenciamento. Há suporte para vários tipos de origens integradas do Azure, incluindo Aplicativos Web, Armazenamento de Blobs e Serviços de Nuvem. Você também pode habilitar a CDN para o ponto de extremidade de Streaming dos Serviços de Mídia do Azure. Se a origem não for um desses serviços do Azure, ou for hospedada em outro lugar fora do Azure, você também poderá criar uma origem personalizada. Depois de habilitar um ponto de extremidade da CDN para sua origem, todos os objetos publicamente disponíveis ficam qualificados para o cache de borda da CDN.

## Criar um novo perfil CDN

Um perfil CDN é um conjunto de pontos de extremidade CDN. Cada perfil contém um ou mais pontos de extremidade CDN. Você pode usar vários perfis para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério.

> [AZURE.NOTE]Uma única assinatura do Azure está limitada a quatro perfis CDN. Cada perfil CDN está limitado a quatro pontos de extremidade CDN.
>
> Os preços da CDN são aplicados no nível de perfil CDN. Se você quiser usar uma combinação de recursos CDN Standard e Premium, precisará de vários perfis CDN.


**Para criar um perfil CDN**

1. No [Portal de Gerenciamento do Azure](https://portal.azure.com), no canto superior esquerdo, clique em **Novo**. Na folha **Novo**, selecione **Mídia + CDN** e, em seguida, **CDN**.

    A folha do novo perfil CDN é exibida.
    
    ![Novo perfil CDN][new-cdn-profile]

2. Insira um nome para o perfil CDN.

3. Selecione um **Tipo de preço** ou use o padrão.

4. Selecione ou crie um **Grupo de Recursos**. Para obter mais informações sobre Grupos de Recursos, confira [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview/#resource-groups).

5. Selecione a **Assinatura** para este perfil CDN.

6. Selecione um **Local**. Esse é o local do Azure onde suas informações de perfil CDN serão armazenadas. Ele não tem impacto sobre os locais de ponto de extremidade CDN. Não precisa ser o mesmo local da conta de armazenamento.

7. Clique no botão **Criar** para criar um novo perfil.

## Criar um novo ponto de extremidade CDN

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure](https://portal.azure.com), navegue até o seu perfil CDN. Você pode ter fixado ao painel na etapa anterior. Se não, poderá encontrá-lo clicando em **Procurar**, em **perfis CDN** e clicando no perfil ao qual pretende adicionar o ponto de extremidade.

    A folha do perfil CDN é exibida.
    
    ![Perfil CDN][cdn-profile-settings]
    
2. Clique no botão **Adicionar Ponto de Extremidade**.

    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]

    A folha **Adicionar um ponto de extremidade** é exibida.
    
    ![Adicionar folha de ponto de extremidade][cdn-add-endpoint]

3. Insira um **Nome** para esse ponto de extremidade CDN. Esse nome será usado para acessar os recursos armazenados em cache no domínio `<EndpointName>.azureedge.net`.

4. No menu suspenso **Tipo de origem**, selecione o tipo de origem.
	
	![Tipo de origem CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. No menu suspenso **Nome do host de origem**, selecione ou digite o domínio de origem. O menu suspenso listará todas as origens disponíveis do tipo especificado na etapa 4. Se você selecionou *Origem personalizada* como o **Tipo de origem**, você digitará no domínio de sua origem personalizada.

6. Na caixa de texto **Caminho de origem**, digite o caminho para os recursos que você deseja armazenar em cache, ou deixe em branco para permitir o cache de qualquer recurso no domínio especificado na etapa 5.

7. No **Cabeçalho de host de origem**, digite o cabeçalho de host que você deseja que o CDN envie com cada solicitação ou deixe o padrão.

8. Para **Protocolo** e **Porta de origem**, especifique os protocolos e as portas usadas para acessar os recursos na origem. Seus clientes continuarão usando esses mesmos protocolos e portas ao acessar recursos na CDN. É necessário selecionar pelo menos um protocolo (HTTP ou HTTPS).

9. Clique no botão **Adicionar** para criar um novo ponto de extremidade.

10. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade do perfil. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem.

    ![Ponto de extremidade CDN][cdn-endpoint-success]

    > [AZURE.NOTE]O ponto de extremidade não estará imediatamente disponível para uso. Pode levar até 90 minutos para que o registro seja propagado pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 404 até que o conteúdo esteja disponível pela CDN.

##Consulte também
- [Como mapear o conteúdo da CDN (rede de distribuição de conteúdo) para um domínio personalizado](cdn-map-content-to-custom-domain.md)
- [Limpar um ponto de extremidade CDN do Azure](cdn-purge-endpoint.md)

[new-cdn-profile]: ./media/cdn-create-new-endpoint/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
 

<!---HONumber=AcomDC_1210_2015-->