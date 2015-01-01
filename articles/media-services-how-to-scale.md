<properties urlDisplayName="How to scale" pageTitle="Como dimensionar um serviço de mídia | Documentação do Azure" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





#Como dimensionar um Serviço de Mídia  

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]


Você pode dimensionar os Serviços de Mídia especificando o número de **Unidades Reservadas para Streaming por Demanda** e **Unidades Reservadas para Codificação** com as quais você deseja provisionar sua conta. 


<h2>Unidades reservadas para streaming por demanda</h2>

As unidades reservadas para Streaming por Demanda fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui [recursos de empacotamento dinâmico].(http://go.microsoft.com/fwlink/?LinkId=276874). Por padrão, o streaming por demanda é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso etc.) são compartilhados com todos os outros usuários. Para melhorar a taxa de transferência de um streaming por demanda, é recomendável adquirir unidades reservadas para Streaming por Demanda. 

Para alterar o número de unidades reservadas para streaming por demanda, faça o seguinte:

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**.Em seguida, clique no nome do serviço de mídia.

2. Selecione a página de PONTOS DE EXTREMIDADE DE STREAMING. Então, clique no ponto de extremidade que deseja modificar.


3. Para especificar o número de unidades de streaming, selecione a guia ESCALA e mova o controle deslizante **capacidade reservada**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Pressione o botão SALVAR para salvar as alterações.

	A alocação de quaisquer novas unidades de streaming por demanda leva cerca de 20 minutos para ser concluída. 

	 
	>[Azure.Note] No momento, mudar de qualquer valor positivo de unidades de streaming por demanda para nenhum pode desabilitar o streaming por demanda por até uma hora.

	>[Azure.Note] O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte[Detalhes de preços dos Serviços de Mídia.](http://go.microsoft.com/fwlink/?LinkId=275107).

<h2>Codificando unidades reservadas</h2>

O número de unidades reservadas para codificação provisionadas é igual ao número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas para serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente assim que uma tarefa em execução seja concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

Para alterar o número de unidades reservadas para codificação, faça o seguinte:

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**.Em seguida, clique no nome do serviço de mídia.

2. Selecione a página CODIFICAÇÃO. 
	
	A página Codificação permite selecionar entre três tipos diferentes de unidades reservadas de codificação:  Básica, Padrão e Premium (como mostrado a seguir).

	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	É possível alterar o número de unidades reservadas para o TIPO DE UNIDADE RESERVADA usando o seletor CODIFICAÇÃO.  
	
	A principal diferença entre os tipos de unidade do reservador é a velocidade. Por exemplo, o mesmo trabalho de codificação é executado mais rápido quando se usa o tipo de unidade reservada Padrão em comparação ao tipo Básico. Para obter mais informações, consulte o blog "Encoding Reserved Unit Types", escrito por [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

	>[Azure.Note] Os seguintes data centers não oferecem o tipo de unidade reservada Premium: Singapura, Hong Kong, Osaka, Beijing, Xangai.

3. Pressione o botão SALVAR para salvar as alterações.

	As novas unidades reservadas para codificação são alocadas assim que você pressiona SALVAR.

	>[Azure.Note] O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo.

<h2>Abrindo um tíquete de suporte</h2>


Por padrão, todas as contas dos Serviços de Mídia podem ser dimensionadas para até 25 Unidades Reservadas para Codificação e cinco para Streaming por Demanda. Você pode solicitar um limite mais alto abrindo um tíquete de suporte.

Para abrir um tíquete de suporte, faça o seguinte: 

1. Faça logon em sua conta do Azure no [Portal de Gerenciamento](http://manage.windowsazure.com).
2. Vá para [Suporte](http://www.windowsazure.com/pt-br/support/contact/).
3. Clique em "Obter Suporte".
4. Selecione sua assinatura.
5. Em tipo de suporte, selecione "Técnico".
6. Clique em "Criar Tíquete".
7. Selecione "Serviços de Mídia do Azure" na lista de produtos apresentados na próxima página.
8. Selecione "Processamento de mídia" como "Tipo de problema" e selecione "Unidades de reserva" em categoria.
9. Clique em Continuar.
10. Siga as instruções da próxima página e, em seguida, insira os detalhes sobre quantas unidades reservadas para Codificação ou Streaming por Demanda você precisa.
11. Clique em Enviar para abrir o tíquete.





 





<!--HONumber=35_1-->
