<properties 
	pageTitle="Níveis de desempenho no Banco de Dados de Documentos | Azure" 
	description="Saiba como os níveis de desempenho no Banco de Dados de Documentos permitem reservar a produtividade com base na coleção." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="johnmac"/>

# Níveis de desempenho no Banco de Dados de Documentos #

Este artigo fornece uma visão geral dos níveis de desempenho no [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/). 

> [AZURE.IMPORTANT] Os níveis de desempenho serão colocados em disponibilidade geral com o Banco de Dados de Documentos do Azure. O lançamento está programado para abril de 2015.

Após ler este artigo, você poderá responder as perguntas a seguir:  

-	O que é um nível de desempenho?
-	Como a produtividade é reservada para uma conta do banco de dados?
-	Como eu trabalho com níveis de desempenho?
-	Como eu seu cobrado pelos níveis de desempenho?

##<a id="Sub1"></a>Introdução aos níveis de desempenho##

Cada coleção do Banco de Dados de Documentos criada em uma conta padrão é provisionada com um nível de desempenho. Os níveis de desempenho são designados como S1, S2 ou S3, indo do mais baixo ao mais alto em termos de desempenho. O nível de desempenho da coleção determina a quantidade de recursos de serviço reservados ao seu aplicativo. Cada coleção em um banco de dados pode ter um nível de desempenho diferente, permitindo designar uma produtividade maior para coleções acessadas com frequência e menos produtividade para coleções acessadas com pouca frequência. 

Cada nível de desempenho tem um limite de RU (unidade de solicitação) associado. Trata-se do limite que será reservado para uma coleção com base em seu nível de desempenho, e fica disponível para ser usado apenas por essa coleção. As coleções podem ser criadas por meio do [Portal do Azure](http://portal.azure.com) ou por qualquer um dos [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). As APIs do Banco de Dados de Documentos permitem especificar o nível de desempenho de uma coleção. 

<table> 
<tbody>
<tr>
<td valign="top" ><p><b>Nível de desempenho da coleção</b></p></td>
<td valign="top" ><p><b>Produtividade reservada</b></p></td>
</tr>

<tr>
<td valign="top" ><p>S1</p></td>
<td valign="top" ><p>250 RU/s</p></td>
</tr>

<tr>
<td valign="top" ><p>S2</p></td>
<td valign="top" ><p>1000 RU/s</p></td>
</tr>

<tr>
<td valign="top" ><p>S3</p></td>
<td valign="top" ><p>2500 RU/s</p></td>
</tr>

</tbody>
</table>

O Banco de Dados de Documentos possibilita um amplo conjunto de operações de banco de dados, incluindo consultas, consultas com UDFs (funções definidas pelo usuário), gatilhos e procedimentos armazenados. O custo de processamento associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para realizar a operação. Em vez de gerenciar e pensar em recursos de hardware, você pode pensar em uma unidade de solicitação como uma unidade que mede os recursos necessários para realizar várias operações de bancos de dados e para atender a uma solicitação do aplicativo.

> [AZURE.NOTE] Os níveis de desempenho são medidos em unidades de solicitação. Cada nível de desempenho tem uma taxa máxima de unidades de solicitação por segundo. O nível de desempenho de uma coleção pode ser ajustado por meio das APIs ou do [Portal do Azure](https://portal.azure.com/).

##<a id="Sub2"></a>Definindo níveis de desempenho para coleções##
Quando uma coleção é criada, a alocação total de RUs baseada no nível de desempenho designado é reservada para a coleção. Por exemplo, se for definida como S3, uma coleção é capaz de processar 2.500 RUs/s. Cada coleção reserva sua produtividade designada e 10 GB de armazenamento no banco de dados. O preço de uma coleção varia com base no nível de desempenho escolhido (S1, S2, S3). Observe que o Banco de Dados de Documentos opera com base na reserva de capacidade. Ao criar uma coleção, um aplicativo reserva e é cobrado segundo o armazenamento em banco de dados e a produtividade reservada, independentemente de quanto do armazenamento e da produtividade são de fato usados.

Após as coleções serem criadas, você pode modificar o nível de desempenho por meio das SDKs do Banco de Dados de Documentos ou do Portal de Gerenciamento do Azure. 

> [AZURE.IMPORTANT] Coleções padrão do Banco de Dados de Documentos são cobradas com base em uma taxa por hora e cada coleção que você criar será cobrada por, no mínimo, uma hora de uso. 

Se ajustar o nível de desempenho de uma coleção dentro de uma hora, você será cobrado pelo nível de desempenho mais alto definido durante aquela hora. Por exemplo, se aumentar o nível de desempenho de uma coleção às 8h53, você será cobrado pelo novo nível a partir das 8h. Da mesma forma, se você diminuir o nível de desempenho às 8h53, a nova taxa será aplicada às 9h.

As unidades de solicitação são reservadas para cada coleção com base no nível de desempenho definido. O consumo de unidades de solicitação é avaliado em uma taxa por segundo. Aplicativos que ultrapassam a taxa de unidades solicitação (ou o nível de desempenho) provisionada para uma coleção são limitados até que a taxa caia para baixo do nível reservado. Se o seu aplicativo demandar um nível de produtividade mais alto, você pode aumentar o nível de desempenho para cada coleção.

> [AZURE.NOTE] Quando seu aplicativo ultrapassa os níveis de desempenho para uma ou várias coleções, as solicitações são limitadas por coleção. Isso significa que algumas solicitações do aplicativo podem ser bem-sucedidas enquanto outras são limitadas.

##<a id="Sub3"></a>Trabalhando com os níveis de desempenho##
Coleções do Banco de Dados de Documentos permitem particionar seus dados com base nos padrões de consulta e nas necessidades de desempenho do seu aplicativo. Veja a [documentação sobre particionamento de dados](documentdb-partition-data.md) para saber mais sobre o particionamento de dados no Banco de Dados de Documentos. Com o suporte a consultas e a indexação automática do Banco de Dados de Documentos, é bastante comum colocar documentos heterogêneos na mesma coleção. As principais considerações para decidir se coleções diferentes devem ser usadas para os dados incluem:

- Consultas - Uma coleção é o escopo para a realização de consultas. Se você precisar consultar um conjunto de documentos, os padrões de leitura mais eficientes vêm da colocação de documentos em uma única coleção.
- Transações - Uma coleção é o domínio de transação para os gatilhos e procedimentos armazenados. Todas as transações têm como escopo apenas uma coleção. 
- Desempenho - Uma coleção tem um nível de desempenho associado a ela. Isso garante que cada coleção tenha um desempenho previsível por meio das RUs reservadas. Os dados podem ser alocados a coleções diferentes, com níveis de desempenho diferentes, com base na frequência de acesso.

> [AZURE.IMPORTANT] É importante entender que você será cobrado segundo as taxas padrão cheias, com base no número de coleções criadas dentro do seu aplicativo.

Recomendamos que seu aplicativo use um número reduzido de coleções e que os padrões do aplicativo sejam bem compreendidos para a criação de novas coleções. Você pode optar por fazer da criação de coleções uma ação de gerenciamento feita fora do aplicativo. De forma semelhante, ajustar o nível de desempenho para uma coleção altera a taxa por hora segundo a qual a coleção é cobrada. Você deve monitorar os níveis de desempenho da coleção se o aplicativo ajustar esses níveis dinamicamente.

##<a name="NextSteps"></a>Próximas etapas

Para saber mais sobre a definição de preços e o gerenciamento de dados no Banco de Dados de Documentos do Azure, explore esses recursos:
 
- [Definição de preços no Banco de Dados de Documentos](http://azure.microsoft.com/pricing/details/documentdb/)
- [Gerenciando a capacidade no Banco de Dados de Documentos](documentdb-manage.md) 
- [Modelando dados no Banco de Dados de Documentos](documentdb-modeling-data.md)
- [Particionando dados no Banco de Dados de Documentos](documentdb-partition-data.md)

Para saber mais sobre o Banco de Dados de Documentos, veja a [documentação](http://azure.microsoft.com/documentation/services/documentdb/) do Banco de Dados de Documentos do Azure. 



<!--HONumber=49-->