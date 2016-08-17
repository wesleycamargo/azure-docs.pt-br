<properties 
	pageTitle="Visão geral de mapas e do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba como usar mapas com o Enterprise Integration Pack e Aplicativos lógicos" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Saiba mais mapas e o Enterprise Integration Pack

## Visão geral
O Enterprise Integration usa mapas para transformar dados XML de um formato para outro formato.

## O que é um mapa?
Um mapa é um documento XML que define os dados em um documento que devem ser transformados em outro formato.

## Por que usar mapas?
Vamos supor que você recebe regularmente pedidos ou faturas B2B de clientes que usam o formato AAAAMMDD para datas. No entanto, em sua organização, você armazena datas no formato MMDDAAAA. Você pode usar um mapa para *transformar* o formato de data AAAAMMDD em MMDDAAAA antes de armazenar os detalhes do pedido ou da fatura em seu banco de dados de atividade do cliente.

## Como carregar um mapa?
No Portal do Azure:
1. Selecione **Procurar**![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selecione a **conta integração** à qual você adicionará o mapa ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Escolha o bloco **Mapas** ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. Selecione o botão **Adicionar** na folha Mapas que se abre. ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. Insira um **Nome** para o mapa e, para carregar o arquivo de mapa, clique no ícone de pasta à direita da caixa de texto **Mapa**. Após a conclusão do processo de carregamento, selecione o botão **OK**. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. Agora, o mapa está sendo adicionado à sua conta de integração. Você receberá uma notificação na tela indicando o sucesso ou fracasso da adição do arquivo de mapa. Depois de receber a notificação, selecione o bloco **Mapas**, você verá seu mapa recém-adicionado na folha Mapas: ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## Como editar um mapa?
Para editar um mapa, você deve carregar um novo arquivo de mapa com as alterações desejadas. Primeiro, você pode baixar o mapa e editá-lo.

Execute estas etapas para carregar um novo mapa que substitui um mapa existente:
1. Escolha o bloco **Mapas**
2. Selecione o mapa que você deseja editar quando a folha Mapas for aberta
3. Na folha **Mapas**, selecione o link **Atualizar** ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. Selecione o arquivo de mapa que você deseja carregar usando a caixa de diálogo do seletor de arquivos e, em seguida, escolha **Abrir** no seletor de arquivo ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. Após o carregamento do mapa, você receberá um pop-up de notificação.

## Como excluir um mapa?
1. Escolha o bloco **Mapas**
2. Selecione o mapa que você deseja excluir quando a folha Mapas for aberta
3. Selecione o link **Excluir** ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. Confirme que você realmente deseja excluir o mapa. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## Próximas etapas
- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")
- [Saiba mais sobre contratos](./app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração corporativa")
- [Saiba mais sobre transformações](./app-service-logic-enterprise-integration-transform.md "Saiba mais sobre as transformações de integração corporativa")

<!---HONumber=AcomDC_0803_2016-->