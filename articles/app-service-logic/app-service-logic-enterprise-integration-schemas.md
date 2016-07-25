<properties 
	pageTitle="Visão geral de esquemas e do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba como usar esquemas com o Enterprise Integration Pack e Aplicativos lógicos" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Saiba mais esquemas e o Enterprise Integration Pack  

## Por que usar um esquema
Você pode usar esquemas para confirmar que os documentos XML recebidos são válidos, ou seja, que os documentos contêm os dados esperados em um formato predefinido.

## Como adicionar um esquema
No Portal do Azure:
1. Selecione **Procurar**![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selecione a **conta integração** à qual você adicionará o esquema ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Escolha o bloco **Esquemas** ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. Selecione o botão **Adicionar** na folha Esquemas que se abre. ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. Insira um **Nome** para o esquema e, para carregar o arquivo de esquema, clique no ícone de pasta à direita da caixa de texto **Esquema**. Após a conclusão do processo de carregamento, selecione o botão **OK**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. Escolha o ícone de notificação de *sino* para ver o progresso do processo de carregamento de esquema. ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. Escolha o bloco **Esquemas**. Isso atualiza o bloco e você poderá ver o número de esquemas aumentar, refletindo que o novo esquema foi adicionado com êxito. Depois de selecionar o bloco **Esquemas**, você também verá o parceiro recém-adicionado exibido na folha Esquemas, à direita. ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## Como usar esquemas
- Esquemas são usados para validar mensagens trocadas em um cenário B2B.

## Como editar esquemas
1. Escolha o bloco **Esquemas**
2. Selecione o esquema que você deseja editar na folha Esquemas que abrir
3. Selecione o link **Carregar** na folha Esquemas ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. Selecione o arquivo de esquema que você deseja carregar usando a caixa de diálogo do seletor de arquivos.
5. Selecione **Abrir** no seletor de arquivos ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. Você receberá uma notificação informando que o carregamento foi bem-sucedido ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## Como excluir esquemas
1. Escolha o bloco **Esquemas**
2. Selecione o esquema que você deseja excluir na folha Esquemas que se abre
3. Selecione o link **Excluir** na barra de menus na folha Esquemas ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. Se você realmente deseja excluir o esquema selecionado, escolha **Sim** na caixa de diálogo Excluir esquema para confirmar sua escolha ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. Finalmente, observe que a lista de esquemas na folha Esquemas é atualizada e o esquema excluído não aparece mais na lista ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## Próximas etapas

- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

      

<!---HONumber=AcomDC_0713_2016-->