<properties 
	pageTitle="Incremente sua conta S1 de Banco de Dados de Documentos | Microsoft Azure" 
	description="Tire proveito da taxa de transferência maior em sua conta S1 de Banco de Dados de Documentos fazendo algumas alterações simples no Portal do Azure." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/27/2016" 
	ms.author="mimig"/>

# Incremente sua conta de Banco de Dados de Documentos

Siga estas etapas para aproveitar o aumento da produtividade da sua conta S1 no Banco de Dados de Documentos do Azure. Com pouco ou nenhum custo adicional, você pode aumentar a produtividade da sua conta S1 existente de 250 [RU/s](documentdb-request-units.md) para 400 RU/s ou mais!

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## Alterar para desempenho definido pelo usuário no Portal do Azure

1. No seu navegador, navegue até o [**Portal do Azure**](https://portal.azure.com).
2. Clique em **Procurar** -> **Contas de Banco de Dados de Documentos** e, em seguida, selecione a conta de Banco de Dados de Documentos para modificar.
3. Na lente **Bancos de dados**, selecione o banco de dados para modificar e, em seguida, na folha **Banco de dados**, selecione a coleção com o tipo de preço S1.

      ![Captura de tela da folha de banco de dados com uma coleção S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. Na folha **Coleções**, clique em **Configurações** na barra superior.
5. Na folha **Configurações**, clique em **Tipo de preço** e observe que a estimativa de custo mensal para cada plano é exibida. Na folha **Escolha o tipo de preço**, clique em **Standard** e, em seguida, clique em **Selecionar** para salvar as alterações.

      ![Faça uma captura de tela das Configurações de Banco de Dados de Documentos e escolha as folhas do tipo de preço](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. Na folha **Configurações**, o **Tipo de preço** foi alterado para **Standard** e a caixa **Taxa de transferência (RU/s)** é exibida com um valor padrão de 400. Clique em **OK** para salvar as alterações.

    > [AZURE.NOTE] Você pode definir a taxa de transferência entre 400 e 10.000 [unidades de solicitação](../articles/documentdb/documentdb-request-units.md)/segundo (RUS/s). O **Resumo de Preços** na parte inferior da página é atualizado automaticamente para fornecer uma estimativa do custo mensal.
    
	![Captura de tela da folha Configurações, mostrando em que lugar alterar o valor da taxa de transferência](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. Na folha **Banco de dados**, você pode verificar a taxa de transferência incrementada da coleção.

	![Captura de tela da folha Banco de dados com a coleção modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para obter mais informações sobre as alterações relacionadas às taxas de transferência definidas e predefinidas do usuário, confira a postagem no blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (Banco de Dados de Documentos: tudo o que você precisa saber sobre como usar as novas opções de preços).

## Próximas etapas

Se determinar que precisa de uma taxa de transferência maior (mais de 10.000 RU/s) ou mais armazenamento (mais de 10 GB), você poderá criar uma coleção particionada. Para criar uma coleção particionada, confira [Criar uma coleção](documentdb-create-collection.md).

<!---HONumber=AcomDC_0720_2016-->