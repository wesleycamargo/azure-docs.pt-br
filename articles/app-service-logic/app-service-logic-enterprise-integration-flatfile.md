---
title: "Aprenda a codificar ou decodificar arquivos simples usando o Enterprise Integration Pack e Aplicativos Lógicos | Microsoft Docs"
description: "Use os recursos do Enterprise Integration Pack e Aplicativos Lógicos para codificar ou decodificar arquivos simples"
services: app-service\logic
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5283c565c29c357fa2f82535dc07891ab42e6704


---
# <a name="enterprise-integration-with-flat-files"></a>Integração corporativa com arquivos simples
## <a name="overview"></a>Visão geral
Talvez você queira codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios em um cenário B2B (entre empresas). Em um aplicativo lógico criado pelo recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure, você pode usar o conector de codificação de arquivo simples para fazer isso. O aplicativo lógico criado por você pode obter seu conteúdo XML de diversas fontes, incluindo de um gatilho de solicitação HTTP ou de outro aplicativo, ou até mesmo de um dos vários [conectores](../connectors/apis-list.md). Para saber mais sobre os aplicativos lógicos, veja o [documentação dos aplicativos lógicos](app-service-logic-what-are-logic-apps.md "Saiba mais sobre os Aplicativos Lógicos").  

## <a name="how-to-create-the-flat-file-encoding-connector"></a>Como criar o conector de codificação de arquivo simples
Execute estas etapas para adicionar um conector de codificação de arquivo simples ao seu aplicativo lógico.

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](app-service-logic-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico"). Esta conta contém o esquema que será usado para codificar os dados XML.  
2. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.  
   ![Captura de tela do gatilho a ser selecionado](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
3. Adicione uma ação de codificação de arquivo simples, desta maneira:
   
    a. Selecione o sinal de **mais** .
   
    b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).
   
    c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Escolha a ação **Codificação de Arquivo Simples** na lista.   
   ![Captura de tela da opção Codificação de Arquivo Simples](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
4. Na caixa de texto **Codificação de Arquivo Simples**, selecione a caixa de texto **Conteúdo**.  
   ![Captura de tela da caixa de texto Conteúdo](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)  
5. Selecione a marca body como o conteúdo que será codificado. A marca body preencherá o campo de conteúdo.     
   ![Captura de tela da marca body](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)  
6. Escolha a caixa de listagem **Nome do Esquema** e escolha o esquema que você deseja usar para codificar o conteúdo da entrada.    
   ![Captura de tela da caixa de listagem Nome do Esquema](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)  
7. Salve seu trabalho.   
   ![Captura de tela do ícone Salvar](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, você já configurou seu conector de codificação de arquivo simples. Em um aplicativo real, convém armazenar os dados codificados em um aplicativo de linha de negócios, como o Salesforce. Ou você pode enviar os dados codificados para um parceiro comercial. Você pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou seu parceiro comercial usando qualquer um dos outros conectores fornecido.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML no corpo da solicitação.  

## <a name="how-to-create-the-flat-file-decoding-connector"></a>Como criar o conector de decodificação de arquivo simples
> [!NOTE]
> Para concluir estas etapas, você precisa ter um arquivo de esquema já carregado na conta de integração.
> 
> 

1. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.  
   ![Captura de tela do gatilho a ser selecionado](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
2. Adicione a ação de decodificação de arquivo simples, desta maneira:
   
    a. Selecione o sinal de **mais** .
   
    b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).
   
    c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Escolha a ação **Decodificação de Arquivo Simples** na lista.   
   ![Captura de tela da opção Decodificação de Arquivo Simples](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
3. Selecione o controle **Conteúdo** . Isso produz uma lista do conteúdo das etapas anteriores que você pode usar como o conteúdo para decodificação. Observe que o *Corpo* da solicitação HTTP de entrada estará disponível para uso como o conteúdo para decodificação. Você também pode inserir o conteúdo para decodificação diretamente no controle **Conteúdo** .     
4. Selecione a marca *Body* . Observe que agora a marca body está no controle **Conteúdo** .
5. Selecione o nome do esquema que você deseja usar para decodificar o conteúdo. A captura de tela a seguir mostra que *OrderFile* é o nome do esquema selecionado. Esse nome de esquema foi carregado anteriormente na conta de integração.
   
   ![Captura de tela da caixa de diálogo Decodificação de Arquivo Simples](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Salve seu trabalho.  
   ![Captura de tela do ícone Salvar](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)    

Neste ponto, você já configurou seu conector de decodificação de arquivo simples. Em um aplicativo real, convém armazenar os dados decodificados em um aplicativo de linha de negócios, como o Salesforce. Você pode adicionar facilmente uma ação para enviar a saída da ação de decodificação para o Salesforce.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML que você deseja decodificar no corpo da solicitação.  

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  




<!--HONumber=Nov16_HO3-->


