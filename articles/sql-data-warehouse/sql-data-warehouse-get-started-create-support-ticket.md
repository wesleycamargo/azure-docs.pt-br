---
title: "Como criar um tíquete de suporte para o SQL Data Warehouse | Microsoft Docs"
description: "Como criar um tíquete de suporte no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede330870f451bd1e0ed6805fa937e74438fd644


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um tíquete de suporte para o SQL Data Warehouse
Caso você tenha problemas com o SQL Data Warehouse, crie um tíquete de suporte para que nossa equipe de engenharia possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte
1. Abra o [portal do Azure][portal do Azure].
2. Na tela Inicial, clique no bloco **Ajuda + suporte** .
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. Na folha Ajuda + Suporte, clique em **Criar solicitação de suporte**.
   
    ![Nova solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Selecione o **Tipo de Solicitação**.
   
    ![Tipo de Solicitação](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, multiplique 7.5 pelo total de [DWU][DWU] necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL Server, então, deve solicitar uma cota DTU de 90.000.  Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e retomados contam como a cota de DTU. 
   > 
   > 
5. Selecione a **Assinatura** que hospeda o banco de dados com o problema que você está relatando.
   
    ![Assinatura](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Selecione **SQL Data Warehouse** como o Recurso.
   
    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Selecione seu [Plano de suporte do Azure][Plano de suporte do Azure].
   
   * **gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte de **conserto** é fornecido por meio do suporte [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] ou [Premier][Premier]. As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct][Professional Direct] e [Premier][Premier]. 
     
     Se tiver um plano de suporte Premier, você também poderá relatar problemas relacionados ao SQL Data Warehouse no [Portal online Microsoft Premier][Portal online Microsoft Premier].  Consulte os [planos de suporte do Azure][Plano de suporte do Azure] para saber mais sobre os vários planos de suporte, incluindo o escopo, tempos de resposta, preços etc.  Para ver as perguntas frequentes sobre o suporte do Azure, consulte [Perguntas frequentes do suporte do Azure][Perguntas frequentes do suporte do Azure].  
     
     ![Plano de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Selecione o **Tipo de Problema** e **Categoria**.
   
    ![Categoria de tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Descreva o problema e escolha o nível de impacto nos negócios.
   
    ![Descrição do problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Suas **informações de contato** para esse tíquete de suporte serão preenchidas previamente. Atualize-as se necessário.
    
    ![Informações de contato](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Clique em **Criar** para enviar a solicitação de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte
Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Gerenciar solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Outros recursos
Além disso, você pode conectar a comunidade do SQL Data Warehouse no [Stack Overflow][Stack Overflow] ou no [Fórum MSDN do Azure SQL Data Warehouse][Fórum MSDN do Azure SQL Data Warehouse].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[portal do Azure]: https://portal.azure.com/
[Plano de suporte do Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Perguntas frequentes do suporte do Azure]: https://azure.microsoft.com/support/faq/
[Portal online Microsoft Premier]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Fórum MSDN do Azure SQL Data Warehouse]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/




<!--HONumber=Nov16_HO2-->


