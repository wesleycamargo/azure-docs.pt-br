<properties
	pageTitle="Níveis de desempenho no Banco de Dados de Documentos | Microsoft Azure"
	description="Saiba como os níveis de desempenho no Banco de Dados de Documentos permitem reservar a produtividade com base na coleção."
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
	ms.date="08/26/2016"
	ms.author="mimig"/>

# Níveis de desempenho no Banco de Dados de Documentos

Este artigo fornece uma visão geral dos níveis de desempenho no [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Após ler este artigo, você poderá responder as perguntas a seguir:

-	O que é um nível de desempenho?
-	Como a produtividade é reservada para uma conta do banco de dados?
-	Como eu trabalho com níveis de desempenho?
-	Como eu seu cobrado pelos níveis de desempenho?

## Introdução aos níveis de desempenho

Cada coleção do Banco de Dados de Documentos criada em uma conta padrão é provisionada com um nível de desempenho. Cada coleção em um banco de dados pode ter um nível de desempenho diferente, permitindo designar uma produtividade maior para coleções acessadas com frequência e menos produtividade para coleções acessadas com pouca frequência. O Banco de Dados de Documentos dá suporte aos níveis de desempenho definidos pelo usuário e aos níveis de desempenho predefinidos.

Cada nível de desempenho tem um limite de [RU (unidade de solicitação)](documentdb-request-units.md) associado. Trata-se do limite que será reservado para uma coleção com base em seu nível de desempenho, e fica disponível para ser usado apenas por essa coleção.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
        	<td valign="top"><p></p></td>
            <td valign="top"><p>Detalhes</p></td>
            <td valign="top"><p>Limites de taxa de transferência</p></td>
            <td valign="top"><p>Limites de armazenamento</p></td>
            <td valign="top"><p>Versão</p></td>
 			<td valign="top"><p>APIs</p></td>            
        </tr>
        <tr>
        	<td valign="top"><p>Desempenho definido pelo usuário</p></td>
            <td valign="top"><p>Armazenamento medido com base no uso em GB.</p><p>Taxa de transferência em unidades de 100 RU/s</p></td>
            <td valign="top"><p>Ilimitado. 400-250.000 unidades de solicitação/s por padrão (mais alto mediante solicitação)</p></td>
            <td valign="top"><p>Ilimitado. 250 GB por padrão (mais alto mediante solicitação) </p></td>
            <td valign="top"><p>V2</p></td>
 			<td valign="top"><p>API 2015-12-16 e mais recentes</p></td>  
        </tr>
        <tr>
        	<td valign="top"><p>Desempenho predefinido</p></td>
            <td valign="top"><p>Armazenamento reservado de 10 GB.</p><p>S1 = 250 RU/s, S2 = 1.000 RU/s, S3 = 2.500 RU/s</p></td>
            <td valign="top"><p>2.500 RU/s</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
 			<td valign="top"><p>Qualquer</p></td>  
        </tr>        
    </tbody>
</table>                


O Banco de Dados de Documentos possibilita um amplo conjunto de operações de banco de dados, incluindo consultas, consultas com UDFs (funções definidas pelo usuário), gatilhos e procedimentos armazenados. O custo de processamento associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para realizar a operação. Em vez de gerenciar e pensar em recursos de hardware, você pode pensar em uma unidade de solicitação como uma unidade que mede os recursos necessários para realizar várias operações de bancos de dados e para atender a uma solicitação do aplicativo.

As coleções podem ser criadas por meio do [portal do Microsoft Azure](https://portal.azure.com), pela [API REST](https://msdn.microsoft.com/library/azure/mt489078.aspx) ou por qualquer [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). As APIs do Banco de Dados de Documentos permitem especificar o nível de desempenho de uma coleção.

> [AZURE.NOTE] O nível de desempenho de uma coleção pode ser ajustado por meio das APIs ou do [portal do Microsoft Azure](https://portal.azure.com/). As alterações no nível de desempenho devem ser concluídas em 3 minutos.

## Definindo níveis de desempenho para coleções
Quando uma coleção é criada, a alocação total de RUs baseada no nível de desempenho designado é reservada para a coleção.

Observe que tanto com o nível de desempenho predefinido quanto com o nível definido pelo usuário, o Banco de Dados de Documentos opera com base na reserva de taxa de transferência. Ao criar uma coleção, um aplicativo reservou e é cobrado por uma taxa de transferência reservada, independentemente de quanto dessa taxa de transferência é ativamente usada. Com níveis de desempenho definidos pelo usuário, o armazenamento é medido com base no consumo, mas, com níveis de desempenho predefinidos, 10 GB de armazenamento são reservados no momento da criação da coleção.

Após as coleções serem criadas, você pode modificar o nível de desempenho por meio dos SDKs do Banco de Dados de Documentos ou do Portal Clássico do Azure.

> [AZURE.IMPORTANT] Coleções padrão do Banco de Dados de Documentos são cobradas com base em uma taxa por hora e cada coleção que você criar será cobrada por, no mínimo, uma hora de uso.

Se ajustar o nível de desempenho de uma coleção dentro de uma hora, você será cobrado pelo nível de desempenho mais alto definido durante aquela hora. Por exemplo, se aumentar o nível de desempenho de uma coleção às 8h53, você será cobrado pelo novo nível a partir das 8h. Da mesma forma, se você diminuir o nível de desempenho às 8h53, a nova taxa será aplicada às 9h.

As unidades de solicitação são reservadas para cada coleção com base no nível de desempenho definido. O consumo de unidades de solicitação é avaliado em uma taxa por segundo. Aplicativos que ultrapassam a taxa de unidades solicitação (ou o nível de desempenho) provisionada para uma coleção são limitados até que a taxa caia para baixo do nível reservado. Se o seu aplicativo demandar um nível de produtividade mais alto, você pode aumentar o nível de desempenho para cada coleção.

> [AZURE.NOTE] Quando seu aplicativo ultrapassa os níveis de desempenho para uma ou várias coleções, as solicitações são limitadas por coleção. Isso significa que algumas solicitações do aplicativo podem ser bem-sucedidas enquanto outras são limitadas. Recomendamos a adição de um pequeno número de repetições durante uma limitação, a fim de lidar com picos de tráfego de solicitação.

## Trabalhando com os níveis de desempenho
Coleções do Banco de Dados de Documentos permitem agrupar seus dados com base nos padrões de consulta e nas necessidades de desempenho do seu aplicativo. Com o suporte a consultas e a indexação automática do Banco de Dados de Documentos, é bastante comum colocar documentos heterogêneos na mesma coleção. As principais considerações para decidir se coleções diferentes devem ser usadas incluem:

- Consultas – Uma coleção é o escopo para a realização de consultas. Se você precisar consultar um conjunto de documentos, os padrões de leitura mais eficientes vêm da colocação de documentos em uma única coleção.
- Transações – todas as transações têm como escopo uma única coleção. Se você tiver documentos que devem ser atualizados dentro de um único procedimento armazenado ou gatilho, eles deverão ser armazenados na mesma coleção. Mais especificamente, uma chave de partição dentro de uma coleção é o limite de transação. Consulte [Particionamento no Banco de Dados de Documentos](documentdb-partition-data.md) para obter mais detalhes.
- Isolamento de desempenho – uma coleção tem um nível de desempenho associado a ela. Isso garante que cada coleção tenha um desempenho previsível por meio das RUs reservadas. Os dados podem ser alocados a coleções diferentes, com níveis de desempenho diferentes, com base na frequência de acesso.

> [AZURE.IMPORTANT] É importante entender que você será cobrado segundo as taxas padrão cheias, com base no número de coleções criadas por seu aplicativo.

É recomendável que seu aplicativo use um pequeno número de coleções, a menos que você tenha grandes requisitos de armazenamento ou taxa de transferência. Certifique-se de que você tenha padrões de aplicativo bem compreendidos para a criação de novas coleções. Você pode optar por fazer da criação de coleções uma ação de gerenciamento feita fora do aplicativo. De forma semelhante, ajustar o nível de desempenho para uma coleção altera a taxa por hora segundo a qual a coleção é cobrada. Você deve monitorar os níveis de desempenho da coleção se o aplicativo ajustar esses níveis dinamicamente.

## <a id="changing-performance-levels-using-the-azure-portal"></a>Alterar de S1, S2, S3 para desempenho definido pelo usuário

Siga estas etapas para alterar o uso dos níveis de taxa de transferência predefinidos para níveis de taxa de transferência definidos pelo usuário no Portal do Azure. Ao usar níveis de taxa de transferência definidos pelo usuário, você pode ajustar a taxa de transferência de acordo com suas necessidades. E, se ainda estiver usando uma conta S1, você poderá aumentar a taxa de transferência padrão de 250 RU/s para 400 RU/s com apenas alguns cliques.

Para obter mais informações sobre as alterações de preços relacionadas à produtividade predefinida e definida pelo usuário, confira a postagem no blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (Banco de Dados de Documentos: tudo o que você precisa saber sobre como usar as novas opções de preços).

> [AZURE.VIDEO changedocumentdbcollectionperformance]

1. No navegador, acesse o [**Portal do Azure**](https://portal.azure.com).
2. Clique em **Procurar** -> **Contas de Banco de Dados de Documentos** e selecione a conta do Banco de Dados de Documentos a ser modificada.
3. Na lente **Bancos de Dados**, escolha o banco de dados a ser modificado e, na folha **Banco de Dados**, escolha a coleção a ser modificada. As contas que usam a taxa de transferência predefinida têm um tipo de preço de S1, S2 ou S3.

      ![Captura de tela da folha de banco de dados com uma coleção S1](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)

4. Na folha **Coleções**, clique em **Mais** e em **Configurações** na barra superior.
5. Na folha **Configurações**, clique em **Tipo de Preço** e observe que a estimativa de custo mensal para cada plano é exibida na folha **Escolha a camada de preço**. Para alterar a produtividade definida pelo usuário, clique em **Standard** e em **Selecionar** para salvar as alterações.

      ![Faça uma captura de tela das Configurações de Banco de Dados de Documentos e escolha as folhas do tipo de preço](./media/documentdb-performance-levels/documentdb-change-performance.png)

6. De volta na folha **Configurações**, o **Tipo de Preço** foi alterado para **Standard** e a caixa **Produtividade (RU/s)** é exibida com um valor padrão de 400. Defina a produtividade entre 400 e 10.000 [Unidades de Solicitação](documentdb-request-units.md)/segundo (RUS/s). O **Resumo de Preços** na parte inferior da página é atualizado automaticamente para fornecer uma estimativa do custo mensal. Clique em **OK** para salvar as alterações.

	![Captura de tela da folha Configurações, mostrando em que lugar alterar o valor da taxa de transferência](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

7. De volta na folha **Banco de Dados**, você pode verificar a nova produtividade da coleção.

	![Captura de tela da folha Banco de dados com a coleção modificada](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

Se determinar que precisa de uma taxa de transferência maior (mais de 10.000 RU/s) ou mais armazenamento (mais de 10 GB), você poderá criar uma coleção particionada. Para criar uma coleção particionada, confira [Criar uma coleção](documentdb-create-collection.md).

>[AZURE.NOTE] Alterar os níveis de desempenho de uma coleção pode levar até 2 minutos.

## Alterando os níveis de desempenho usando o SDK .NET

Outra opção para alterar os níveis de desempenho de suas coleções é por meio de nossos SDKs. Esta seção aborda apenas a alteração do nível de desempenho da coleção usando nosso [SDK .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), mas o processo é semelhante para os outros [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). Se você for novo em nosso SDK .NET, visite nosso [tutorial de introdução](documentdb-get-started.md).

Aqui está um trecho de código para alterar a taxa de transferência de oferta para 50.000 unidades de solicitação por segundo:

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
		              .Where(r => r.ResourceLink == collection.SelfLink)    
		              .AsEnumerable()
		              .SingleOrDefault();

	// Set the throughput to 5000 request units per second
	offer = new OfferV2(offer, 5000);

	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);

	// Set the throughput to S2
	offer = new Offer(offer);
	offer.OfferType = "S2";

	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);



> [AZURE.NOTE] Coleções provisionadas com menos de 10.000 unidades de solicitação por segundo podem ser migradas entre ofertas com taxa de transferência definida pelo usuário e a produtividade predefinida (S1, S2, S3) a qualquer momento. Coleções provisionadas com mais de 10.000 unidades de solicitação por segundo não podem ser convertidas para níveis predefinidos de taxa de transferência.

Visite o [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para exibir exemplos adicionais e saber mais sobre os métodos oferecidos:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a id="change-throughput"></a>Alterando a produtividade de uma coleção

Se já estiver usando desempenho definido pelo usuário, você poderá alterar a produtividade da sua coleção seguindo o procedimento abaixo. Se precisar alterar o nível de desempenho de S1, S2 ou S3 (desempenho predefinido) para desempenho definido pelo usuário, confira [Alterar de S1, S2, S3 para desempenho definido pelo usuário](#changing-performance-levels-using-the-azure-portal).

1. Pelo navegador, acesse o [**portal do Azure**](https://portal.azure.com).
2. Clique em **Procurar** -> **Contas do DocumentDB** e escolha a conta do DocumentDB a ser modificada.
3. Na folha **Conta do DocumentDB**, na lente **Bancos de Dados**, escolha o banco de dados a ser modificado e, na folha **Banco de Dados**, escolha a coleção a ser modificada.
4. Na folha **Coleções**, clique em **Configurações** na barra superior.
5. Na folha **Configurações**, aumente o valor na caixa **Produtividade (RU/s)** e clique em **OK** para salvar a alteração. O **Resumo de Preços**, na parte inferior da folha, é atualizado para mostrar o novo custo mensal estimado da coleção em uma única região.

    ![Captura de tela da folha Configurações, destacando a caixa Produtividade e o Resumo de Preços](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

Se não tiver certeza de quanto aumentar sua produtividade, confira [Estimativa das necessidades de produtividade](documentdb-request-units.md#estimating-throughput-needs) e a [Calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner).

## Próximas etapas

Para saber mais sobre a definição de preços e o gerenciamento de dados no Banco de Dados de Documentos do Azure, explore esses recursos:

- [Definição de preços no Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gerenciando a capacidade no Banco de Dados de Documentos](documentdb-manage.md)
- [Modelando dados no Banco de Dados de Documentos](documentdb-modeling-data.md)
- [Particionando dados no Banco de Dados de Documentos](documentdb-partition-data.md)
- [Unidades de solicitação](http://go.microsoft.com/fwlink/?LinkId=735027)

Para saber mais sobre o Banco de Dados de Documentos, veja a [documentação](https://azure.microsoft.com/documentation/services/documentdb/) do Banco de Dados de Documentos do Azure.

Para começar com os testes de desempenho e escala com o DocumentDB, confira [Teste de desempenho e escalabilidade com o Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0831_2016-->