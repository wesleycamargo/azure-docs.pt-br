---
title: "Visão geral de esquemas e do Enterprise Integration Pack | Microsoft Docs"
description: "Saiba como usar esquemas com o Enterprise Integration Pack e aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2e0419c46e9380eb7e1848524a99501b4961d35c


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Saiba mais esquemas e o Enterprise Integration Pack
## <a name="why-use-a-schema"></a>Por que usar um esquema?
Use esquemas para confirmar que os documentos XML recebidos são válidos, com os dados esperados em um formato predefinido. Esquemas são usados para validar mensagens trocadas em um cenário B2B.

## <a name="add-a-schema"></a>Adicionar um esquema
No Portal do Azure:  

1. Escolha **Mais serviços**.  
   ![Captura de tela do portal do Azure, com "Mais serviços" realçado](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados.     
   ![Captura de tela da caixa de pesquisa do filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Escolha a **conta de integração** à qual você adicionará o esquema.    
   ![Captura de tela da lista de contas de integração](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Escolha o bloco **Esquemas**.  
   ![Captura de tela da conta de integração de IEP com "Esquemas" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Adicionar arquivo de esquema com menos de 2 MB
1. Na folha **Esquemas** que abrir (das etapas anteriores), selecione **Adicionar**.  
   ![Captura de tela da folha Esquemas, com o botão "Adicionar" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. Digite um nome para o seu esquema. Para carregar o arquivo de esquema, selecione o ícone da pasta ao lado da caixa de texto **Esquema**. Após a conclusão do processo de upload, selecione **OK**.    
   ![Captura de tela de "Adicionar esquema" com "Arquivo pequeno" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Adicione um arquivo de esquema com mais de 2 MB (até um máximo de 8 MB)
O processo para isso depende do nível de acesso do contêiner de blob: **Público** ou **Sem acesso anônimo**. Para determinar o nível de acesso, no **Gerenciador de Armazenamento do Azure**, em **Contêineres de Blob**, selecione o contêiner de blob desejado. Selecione **Segurança** e selecione a guia **Nível de Acesso**.

1. Se o nível de acesso de segurança do blob é **Público**, siga estas etapas.  
   ![Captura de tela do Gerenciador de Armazenamento do Azure, com "Contêineres de Blob", "Segurança" e "Público" realçados](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
    a. Carregue o esquema no armazenamento e copie o URI.  
    ![Captura de tela da conta de armazenamento com o URI realçado](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  
   
    b. Selecione o **arquivo grande** em **Adicionar Esquema** e forneça o URI na caixa de texto **URI de Conteúdo**.  
    ![Captura de tela de esquemas, com o botão "Adicionar" e "Arquivo grande" realçados](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
2. Se o nível de acesso da segurança do blob é **Sem acesso anônimo**, siga estas etapas.  
   ![Captura de tela do Gerenciador de Armazenamento do Azure, com "Contêineres de Blob", "Segurança" e "Sem acesso anônimo" realçados](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
    a. Carregue o esquema no armazenamento.  
    ![Captura de tela da conta de armazenamento](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   
    b. Gere uma Assinatura de Acesso Compartilhado para o esquema.  
    ![Captura de tela da conta de armazenamento com a guia Assinaturas de acesso compartilhado realçada](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   
    c. Escolha **Arquivo grande** em **Adicionar Esquema** e forneça o URI da Assinatura de Acesso Compartilhado na caixa de texto **URI de Conteúdo**.  
    ![Captura de tela de esquemas, com o botão "Adicionar" e "Arquivo grande" realçados](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
3. Na folha **Esquemas** da conta de integração de EIP, agora você deve ver o esquema recém-adicionado.  
   ![Captura de tela da conta de integração de EIP com "Esquemas" e o novo esquema realçados](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Editar esquemas
1. Escolha o bloco **Esquemas**.  
2. Selecione o esquema que você deseja editar na folha **Esquemas** que abrir.
3. Na folha **Esquemas**, selecione **Editar**.  
   ![Captura de tela da folha Esquemas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Selecione o arquivo de esquema que você deseja editar usando a caixa de diálogo do seletor de arquivos que abrir.
5. Escolha **Abrir** no seletor de arquivos.  
   ![Captura de tela do seletor de arquivo](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Você receberá uma notificação informando que o carregamento foi bem-sucedido.  

## <a name="delete-schemas"></a>Excluir esquemas
1. Escolha o bloco **Esquemas**.  
2. Selecione o esquema que você deseja excluir na folha **Esquemas** que abrir.  
3. Na folha **Esquemas**, selecione **Excluir**.
   ![Captura de tela da folha Esquemas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. Para confirmar sua escolha, selecione **Sim**.  
   ![Captura de tela da mensagem de confirmação "Excluir esquema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finalmente, observe que a lista de esquemas na folha **Esquemas** é atualizada e o esquema excluído não aparece mais na lista.  
   ![Captura de tela da conta de integração de EIP com "Esquemas" realçado](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  




<!--HONumber=Nov16_HO3-->


