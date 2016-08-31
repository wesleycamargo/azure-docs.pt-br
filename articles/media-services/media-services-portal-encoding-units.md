<properties
	pageTitle="Como escalar o processamento de mídia usando o Portal Clássico do Azure"
	description="Saiba como dimensionar os Serviços de Mídia especificando o número de Unidades Reservadas para Streaming por Demanda e Unidades Reservadas para Codificação com as quais você deseja provisionar sua conta."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="juliako"/>


# Como escalar o processamento de mídia usando o Portal Clássico do Azure

## Realize essa tarefa usando outras tecnologias

Esta página fornece uma visão geral de como escalar o processamento de mídia e mostra como fazer isso usando o Portal Clássico do Azure. Você também pode realizar essa tarefa usando outras tecnologias:

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Visão geral

Uma conta dos Serviços de Mídia está associada a um Tipo de Unidade Reservada que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**. Para obter mais informações, veja [Reserved Unit Types](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/) (Tipos de unidade reservada).

Além de especificar o tipo de unidade reservada, você pode especificar o provisionamento da sua conta com unidades reservadas. O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas para serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

## Escolha entre tipos diferentes de unidade reservada

A tabela a seguir o ajudará a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Também apresenta alguns casos de parâmetro de comparação e fornece as URLs de SAS para baixar os vídeos para executar seus próprios testes:

Cenários|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Exemplo de uso indicado| Codificação de taxa de bits única. <br/>Arquivos com resoluções SD ou inferiores, não sensível ao tempo, de baixo custo.|Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD. |Codificação de taxa de bits única e taxa de bits múltipla.<br/>Vídeos com resolução Full HD e 4K. Codificação urgente com retorno mais rápido. 
Parâmetro de comparação|[Arquivo de entrada: 5 minutos de duração, 640x360p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>A codificação para um arquivo MP4 de taxa de bits única com a mesma resolução leva aproximadamente 11 minutos.|[Arquivo de entrada: cinco minutos de duração, 1280x720p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>A codificação com a predefinição "H264 Taxa de Bits Única 720p" levará, aproximadamente, cinco minutos.<br/><br/>A codificação com a predefinição "H264 Taxa de Bits Múltipla 720p" levará cerca de 11,5 minutos.|[Arquivo de entrada: 5 minutos de duração, 1920x1080p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>A codificação com a predefinição "H264 Taxa de Bits Única 1080p" leva aproximadamente 2,7 minutos.<br/><br/>A codificação com a predefinição "H264 Taxas de Bits Múltiplas 1080p" leva aproximadamente 5,7 minutos.

##Considerações

>[AZURE.IMPORTANT] As seguintes considerações se aplicam:

- As Unidades Reservadas trabalham para paralelizar todo o processamento de mídia, incluindo trabalhos de indexação usando o Indexador de Mídia do Azure. No entanto, ao contrário da codificação, a indexação de trabalhos não processará mais rapidamente com unidades reservadas mais rápidas.

- Se estiver usando o pool compartilhado, ou seja, sem nenhuma unidade reservada, suas tarefas de codificação terão o mesmo desempenho do que com RUs S1. No entanto, não há nenhum limite superior para o tempo em suas Tarefas podem gastar no estado na fila e, em qualquer momento específico, no máximo uma tarefa será executada.

- Os seguintes data centers não oferecem o tipo de unidade reservada **S2**: Sul do Brasil, Índia Ocidental, Índia Central e Sul da Índia.

- Os seguintes data centers não oferecem o tipo de unidade reservada **S3**: Sul do Brasil, Índia Ocidental, Índia Central.

- O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo.

## Alterar o tipo de unidade reservada

Para alterar o tipo de unidade reservada e o número de unidades reservadas, faça o seguinte:

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.

2. Selecione a página **CODIFICAÇÃO**.

	Para alterar o **TIPO DE UNIDADE RESERVADA**, pressione S1, S2 ou S3.

	Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionado, use controle deslizante **CODIFICAÇÃO**.


	![Página processadores](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

3. Pressione o botão SALVAR para salvar as alterações.

	As novas unidades reservadas são alocadas quando você pressiona SALVAR.
 

##Cotas e limitações

Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).



##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0817_2016-->