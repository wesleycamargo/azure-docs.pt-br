<properties 
	pageTitle="Como adicionar unidades de codificação" 
	description="Saiba como adicionar unidades de codificação com o .NET"  
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>




# Como adicionar unidades de codificação usando o SDK do .NET

Este artigo faz parte da série do [vídeo de serviços de mídia no fluxo de trabalho de demanda](../media-services-video-on-demand-workflow).
  
## Visão geral

Uma conta de serviços de mídia está associada a um tipo de unidade reservada que determina a velocidade com que seus trabalhos de codificação são processados. Você pode escolher entre os seguintes tipos de unidade reservada: Basic, Standard ou Premium. Por exemplo, o mesmo trabalho de codificação é executado mais rápido quando se usa o tipo de unidade reservada Padrão em comparação ao tipo Básico. Para obter mais informações, consulte o blog "Encoding Reserved Unit Types", escrito por [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com unidades reservadas para codificação. O número de unidades reservadas para codificação provisionada determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas para serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente assim que uma tarefa em execução seja concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

Para alterar o tipo de unidade reservada e o número de unidades reservadas para codificação usando o SDK do .NET, faça o seguinte:

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

## Abrindo um tíquete de suporte

Por padrão, todas as contas dos Serviços de Mídia podem ser dimensionadas para até 25 Unidades Reservadas para Codificação e cinco para Streaming por Demanda. Você pode solicitar um limite mais alto abrindo um tíquete de suporte.

Para abrir um tíquete de suporte, faça o seguinte: 

1. Faça logon em sua conta do Azure no [Portal de Gerenciamento](http://manage.windowsazure.com).
2. Vá para [Suporte](http://azure.microsoft.com/support/contact/).
3. Clique em "Obter Suporte".
4. Selecione sua assinatura.
5. Em tipo de suporte, selecione "Técnico".
6. Clique em "Criar Tíquete".
7. Selecione "Serviços de Mídia do Azure" na lista de produtos apresentados na próxima página.
8. Selecione "Processamento de mídia" como "Tipo de problema" e selecione "Unidades de reserva" em categoria.
9. Clique em Continuar.
10. Siga as instruções da próxima página e, em seguida, insira os detalhes sobre quantas unidades reservadas para Codificação ou Streaming por Demanda você precisa.
11. Clique em Enviar para abrir o tíquete.


<!--HONumber=45--> 
