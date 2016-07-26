<properties
   pageTitle="Como criar um tíquete de suporte para o SQL Data Warehouse | Microsoft Azure"
   description="Como criar um tíquete de suporte no Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="sonyam;barbkess;sonyama"/>

# Como criar um tíquete de suporte para o SQL Data Warehouse
 
Caso você tenha problemas com o SQL Data Warehouse, crie um tíquete de suporte para que nossa equipe de engenharia possa ajudá-lo.

## Criar um tíquete de suporte

1. Abra o [Portal do Azure][].

2. Na tela Inicial, clique no bloco **Ajuda + suporte**.

    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. Na folha Ajuda + Suporte, clique em **Criar solicitação de suporte**.

    ![Nova solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a>

4. Selecione o **Tipo de Solicitação**.

    ![Tipo de solicitação](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, você precisa saber que cada 100 [DWU][] do SQL Data Warehouse consome 750 DTU. Portanto, a cota padrão de 45.000 permitirá que você crie até um DW6000 (45.000/750) ou vários bancos de dados menores com menos DWU. Se, por exemplo, você quiser hospedar dois DW6000s em um SQL Server, deverá solicitar uma cota DTU de 90.000. Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e retomados contam como a cota de DTU.

5. Selecione a **Assinatura** que hospeda o banco de dados com o problema que você está relatando.

    ![Assinatura](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Selecione **SQL Data Warehouse** como o Recurso.

    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Selecione seu [Plano de suporte do Azure][].

    - O suporte do **gerenciamento de assinaturas, cobrança e cotas ** está disponível em todos os níveis de suporte.
    - O suporte do **conserto** é fornecido por meio do suporte do [Desenvolvedor][], [Standard][], [Professional Direct][] ou [Premier][]. As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
    - O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct][] e [Premier][].
    
    Se tiver um plano de suporte Premier, você também poderá relatar problemas relacionados ao SQL Data Warehouse no [Portal online Microsoft Premier][]. Consulte os [planos de suporte do Azure][Azure support plan] para saber mais sobre os vários planos de suporte, incluindo o escopo, tempos de resposta, preços etc. Para ver as perguntas frequentes sobre o suporte do Azure, consulte [Perguntas frequentes do suporte do Azure][].

    ![Plano de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Selecione o **Tipo de Problema** e a **Categoria**.

    ![Categoria de tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Descreva o problema e escolha o nível de impacto nos negócios.

    ![Descrição do problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Suas **informações de contato** para esse tíquete de suporte serão preenchidas previamente. Atualize-as se necessário.

    ![Informações de contato](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Clique em **Criar** para enviar a solicitação de suporte.


## Monitorar um tíquete de suporte

Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Gerenciar solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## Outros recursos

Além disso, você pode conectar a comunidade do SQL Data Warehouse no [Stack Overflow][] ou no [Fórum MSDN do Azure SQL Data Warehouse][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portal do Azure]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[Plano de suporte do Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[Desenvolvedor]: https://azure.microsoft.com/support/plans/developer/
[Standard]: https://azure.microsoft.com/support/plans/standard/
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/
[Premier]: https://azure.microsoft.com/support/plans/premier/
[Perguntas frequentes do suporte do Azure]: https://azure.microsoft.com/support/faq/
[Portal online Microsoft Premier]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Fórum MSDN do Azure SQL Data Warehouse]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

<!---HONumber=AcomDC_0720_2016-->