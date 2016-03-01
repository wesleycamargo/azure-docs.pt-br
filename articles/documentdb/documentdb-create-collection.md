<properties 
	pageTitle="Criar uma coleção de banco de dados do Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba como criar coleções de documento JSON usando o portal de serviço online para Banco de Dados de Documentos do Azure, um banco de dados de documento NoSQL com base na nuvem. Obtenha hoje mesmo uma avaliação gratuita." 
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
	ms.topic="get-started-article" 
	ms.date="02/22/2016" 
	ms.author="mimig"/>

# Como criar uma coleção do Banco de Dados de Documentos usando o portal do Azure

Para usar o Banco de Dados de Documentos do Microsoft Azure, você deve ter uma conta do [Banco de Dados de Documentos](documentdb-create-account.md), um [banco de dados](documentdb-create-database.md), uma coleção e documentos. Este tópico descreve como criar uma coleção do Banco de Dados de Documentos no Portal do Azure.

Não sabe direito o que é uma coleção? Confira [O que é uma coleção do Banco de Dados de Documentos?](#what-is-a-documentdb-collection)

1.  No [Portal do Azure](https://portal.azure.com/), no Jumpbar, clique em **Contas do Banco de Dados de Documentos**. Se **Contas do Banco de Dados de Documentos** não estiver visível, clique em **Procurar** e então clique em **Contas do Banco de Dados de Documentos**.

    ![Captura de tela realçando as contas do Banco de Dados de Documentos no Jumpbar, a conta na folha do Banco de Dados de Documentos e o banco de dados na folha da conta do Banco de Dados de Documentos, nas lentes dos Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

2.  Na folha **Contas do Banco de Dados de Documentos**, escolha a conta na qual adicionar uma coleção. Se você não tiver contas listadas, você precisará [criar uma conta do Banco de Dados de Documentos](documentdb-create-account.md).

3. Na folha **Conta de Banco de Dados de Documentos** da conta selecionada, role para baixo até as lentes dos **Bancos de dados** e escolha o banco de dados ao qual adicionar uma coleção.

    ![Captura de tela realçando as contas do Banco de Dados de Documentos no Jumpbar, a conta na folha do Banco de Dados de Documentos e o banco de dados na folha da conta do Banco de Dados de Documentos, nas lentes dos Bancos de Dados](./media/documentdb-create-collection/docdb-database-creation-3.png)

4. Na folha **Banco de dados**, clique em **Adicionar coleções**.

	![Captura de tela realçando o botão Adicionar Coleção na folha do Banco de Dados, as configurações na folha Adicionar Coleção e o botão OK — portal do Azure para Banco de Dados de Documentos — criador do banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-4.png)

5. Na folha **Adicionar Coleção**, na caixa **ID**, insira a ID da nova coleção. Os nomes de coleção devem ter entre 1 e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita. Quando o nome for validado, uma marca de seleção verde será exibida na caixa de identificação.

	![Captura de tela realçando o botão Adicionar Coleção na folha do Banco de Dados, as configurações na folha Adicionar Coleção e o botão OK — portal do Azure para Banco de Dados de Documentos — criador do banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

6. Selecione uma faixa de preço para a nova coleção. Cada coleção que você cria é uma entidade faturável. Para obter mais informações sobre os níveis de desempenho disponíveis, consulte [Níveis de desempenho no Banco de Dados de Documentos](documentdb-performance-levels.md).

7. Escolha uma das seguintes **Políticas de Indexação**.

	- **Padrão**. Essa política usa a indexação de hash para cadeias de caracteres e indexação de intervalo para números. Ela é mais adequada para consultas de igualdade em relação a cadeias de caracteres, ORDER BY, e consultas de intervalo e igualdade em números. Essa política tem uma sobrecarga de armazenamento de índice menor e inclui indexação geoespacial.
	- **Hash**. Essa política é mais útil quando você está usando consultas ORDER BY, de intervalo e de igualdade em números e cadeias de caracteres. Essa política tem uma sobrecarga de armazenamento de índice maior que a **Padrão** e inclui indexação geoespacial.

	Para obter mais informações sobre políticas de indexação, confira [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md).

8. Clique em **OK** na parte inferior da tela para criar uma nova coleção.


9. A nova coleção agora aparece na lente **Coleções** na folha **Banco de dados**.
 
	![Captura de tela da nova coleção na folha Banco de Dados — Portal do Azure para Banco de Dados de Documentos — criador do banco de dados baseado na nuvem para bancos de dados JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## O que é uma coleção do Banco de Dados de Documentos? 

Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Uma coleção é uma entidade faturável, em que o [custo](documentdb-performance-levels.md) é determinado pelo nível de desempenho associado à coleção.

As coleções são o limite de transação para procedimentos e gatilhos armazenados, bem como o ponto de entrada para consultas e operações CRUD. Cada coleção tem uma quantidade reservada de produtividade específica, que não é compartilhada com outras coleções na mesma conta. Portanto, você pode escalar horizontalmente o aplicativo em termos de armazenamento e produtividade, acrescentando mais coleções e distribuindo seus documentos entre elas.

Coleções não são o mesmo que tabelas em bancos de dados relacionais. As coleções não impõem esquemas. Na verdade, o Banco de Dados de Documentos não impõe nenhum esquema. Trata-se de um banco de dados livre de esquema. Sendo assim, você pode armazenar tipos diferentes de documentos com vários esquemas na mesma coleção. É possível optar por usar coleções para armazenar objetos de um único tipo, assim como acontece com as tabelas. O melhor modelo depende apenas de como os dados aparecem juntos em consultas e transações.

## Outra formas de criar uma coleção do Banco de Dados de Documentos

As coleções não precisam ser criadas usando o portal, também é possível criá-las usando os [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). Para obter exemplos de código C# que mostrem como trabalhar com coleções que usam o SDK do .NET do Banco de Dados de Documentos, veja os [exemplos de coleções do C#](documentdb-dotnet-samples.md#collection-examples). Para obter exemplos de código Node.js que mostrem como trabalhar com coleções que usam o SDK do .NET do Banco de Dados de Documentos, veja os [exemplos de coleções do Node.js.](documentdb-nodejs-samples.md#collection-examples)

## Solucionar problemas

Se a opção **Adicionar Coleção** estiver desabilitada no Portal do Azure, isso significa que sua conta está desabilitada no momento, o que normalmente ocorre quando todos os créditos de benefícios do mês são usados.

## Próximas etapas

Agora que você tem uma coleção, a próxima etapa é adicionar documentos ou importar documentos na coleção. Quando se trata de adicionar documentos a uma coleção, você tem algumas opções:

- Você pode [adicionar documentos](documentdb-view-json-document-explorer.md) usando o Gerenciador de Documentos no portal.
- Você pode [importar documentos e dados](documentdb-import-data.md) usando a ferramenta de migração de dados do Banco de Dados de Documentos, que permite importar arquivos JSON e CSV, bem como dados do SQL Server, MongoDB, armazenamento de tabela do Azure e outras coleções do Banco de Dados de Documentos. 
- Ou você pode adicionar documentos usando um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. Para obter exemplos de código C# que mostrem como trabalhar com documentos que usam o SDK do .NET do Banco de Dados de Documentos, veja os [exemplos de documentos do C#](documentdb-dotnet-samples.md#document-examples). Para obter exemplos de código Node.js que mostrem como trabalhar com documentos que usam o SDK do .NET do Banco de Dados de Documentos, veja os [exemplos de documentos do Node.js](documentdb-nodejs-samples.md#document-examples).

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_0224_2016-->