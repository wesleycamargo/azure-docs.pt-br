<properties 
	pageTitle="Como dimensionar a codificação de unidades reservadas"
	description="Saiba como dimensionar os Serviços de Mídia especificando o número de Unidades Reservadas para Streaming por Demanda e Unidades Reservadas para Codificação com as quais você deseja provisionar sua conta."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="juliako"/>


#Como dimensionar a codificação

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)

##Visão geral

Uma conta de serviços de mídia está associada a um tipo de unidade reservada que determina a velocidade com que seus trabalhos de codificação são processados. Você pode escolher entre os seguintes tipos de unidade reservada: **Basic**, **Standard** ou **Premium**. Por exemplo, o mesmo trabalho de codificação é executado mais rápido quando se usa o tipo de unidade reservada **Standard** em comparação com aquela do tipo **Basic**. Para saber mais, consulte [Tipos de unidade reservada para codificação](http://azure.microsoft.com/blog/author/milanga).

Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com unidades reservadas para codificação. O número de unidades reservadas para codificação provisionada determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas para serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente assim que uma tarefa em execução seja concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

Para alterar o tipo de unidade reservada e o número de unidades reservadas para codificação, faça o seguinte:

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.

2. Selecione a página **CODIFICAÇÃO**.

	Para alterar o **TIPO DE UNIDADE RESERVADA**, pressione BASIC, STANDARD ou PREMIUM.

	Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionado, use controle deslizante **CODIFICAÇÃO**.
	
	
	![Página Processadores](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

	  
	>[AZURE.NOTE]Os datacenters a seguir não oferecem o tipo de unidade reservada Premium: Singapura, Hong Kong, Osaka, Pequim, Xangai.

3. Pressione o botão SALVAR para salvar as alterações.

	As novas unidades reservadas para codificação são alocadas assim que você pressiona SALVAR.

	>[AZURE.NOTE]O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo.

##Cotas e limitações

Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).




 

<!---HONumber=August15_HO9-->