---
title: Visão geral de esquemas e do Enterprise Integration Pack | Microsoft Docs
description: Saiba como usar esquemas com o Enterprise Integration Pack e Aplicativos lógicos
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe

---
# Saiba mais esquemas e o Enterprise Integration Pack
## Por que usar um esquema
Você pode usar esquemas para confirmar que os documentos XML recebidos são válidos, ou seja, que os documentos contêm os dados esperados em um formato predefinido.

## Como adicionar um esquema
No Portal do Azure:

1. Escolha **Mais Serviços**  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Escolha a **conta de integração** à qual você adicionará o esquema    
   ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Escolha o bloco **Esquemas**  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

#### Para adicionar arquivo de esquema com menos de 2 MB
1. Escolha o botão **Adicionar** na folha Esquemas que é aberta  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. Insira um **Nome** para o esquema e, para carregar o arquivo de esquema, clique no ícone de pasta à direita da caixa de texto **Esquema**. Após a conclusão do processo de upload, selecione o botão **OK**.    
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

#### Para adicionar um arquivo de esquema com mais de 2 MB (máx. de 8 MB)
1. Se o nível de acesso de segurança do blob é **público**  
   ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
   * Carregue o esquema no armazenamento e copie o URI  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  
   * Selecione o arquivo grande em Adicionar Esquema e forneça o URI no URI de Conteúdo  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)
2. Se o nível de acesso de segurança do blob é **Sem acesso anônimo**  
   ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
   * Carregue o esquema de armazenamento  
     ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   * Gerar um URI de Assinatura de Acesso Compartilhado para o esquema  
     ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   * Escolha Arquivo grande em Adicionar Esquema e forneça o URI da Assinatura de Acesso Compartilhado no URI de Conteúdo  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
3. Você deverá ver o esquema recém-adicionado:  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png) 

## Como usar esquemas
* Esquemas são usados para validar mensagens trocadas em um cenário B2B.

## Como editar esquemas
1. Escolha o bloco **Esquemas**  
2. Selecione o esquema que você deseja editar na folha Esquemas que abrir
3. Escolha o link **Carregar** na folha Esquemas  
   ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Selecione o arquivo de esquema que você deseja carregar usando a caixa de diálogo do seletor de arquivos.
5. Escolha **Abrir** no seletor de arquivos  
   ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Você receberá uma notificação informando que o carregamento foi bem-sucedido  

## Como excluir esquemas
1. Escolha o bloco **Esquemas**  
2. Selecione o esquema que você deseja excluir na folha Esquemas que se abre  
3. Escolha o link **Excluir** na barra de menus na folha Esquemas
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. Se você realmente deseja excluir o esquema selecionado, escolha **Sim** na caixa de diálogo Excluir esquema para confirmar sua escolha  
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finalmente, observe que a lista de esquemas na folha Esquemas é atualizada e o esquema excluído não aparece mais na lista  
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!----HONumber=AcomDC_0921_2016-->