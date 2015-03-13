<properties 
	pageTitle="Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia" 
	description="Este tópico mostra como gerenciar pontos de extremidade de Streaming usando o Portal de Gerenciamento do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
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


# <a id="managemediaservicesorigins"></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](../media-services-video-on-demand-workflow) e [fluxo de trabalho da transmissão ao vivo dos serviços de mídia](../media-services-live-streaming-workflow).  


Nos serviços de mídia, um ponto de extremidade de Streaming representa um serviço de streaming que pode entregar conteúdo diretamente a um aplicativo de player do cliente ou para rede de entrega um conteúdo (CDN) para a distribuição posterior. Atualmente, os serviços de mídia do Microsoft Azure não oferecem uma integração CDN perfeita, mas você pode utilizar um dos provedores de CDN no mercado (CDN do Azure ou Akamai). O fluxo de saída do serviço de ponto de extremidade do streaming pode ser um fluxo ao vivo ou um vídeo de ativo sob demanda na sua conta de serviços de mídia. 

Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de Streaming para lidar com crescentes necessidades de largura de banda ajustando as unidades de dimensionamento (também conhecido como unidades de streaming). É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de dimsneionamento fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui recursos de empacotamento dinâmico. 

Este tópico mostra como gerenciar pontos de extremidade de Streaming usando o Portal de Gerenciamento do Azure.


## Adicionando e excluindo pontos de extremidade de streaming 

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de **PONTOS DE EXTREMIDADE DE STREAMING**. 
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído. 
4. Clique no botão INICIAR para iniciar o ponto de extremidade de streaming. 
5. Clique no nome do ponto de extremidade de streaming para configurá-lo.   

	![Origin page][origin-page]

## <a id="scale_streaming_endpoints"></a>Dimensionar o ponto de extremidade de Streaming

As unidades para Streaming fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui [recursos de empacotamento dinâmico](http://go.microsoft.com/fwlink/?LinkId=276874). Por padrão, o streaming é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso, etc.) são compartilhados com todos os outros usuários. Para melhorar uma taxa de transferência de streaming, é recomendável adquirir unidades de Streaming. 

Para alterar o número de unidades de streaming, faça o seguinte:

1. Para especificar o número de unidades de streaming, selecione a guia ESCALA e mova o controle deslizante **capacidade reservada**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Pressione o botão SALVAR para salvar as alterações.

	A alocação de quaisquer novas unidades de streaming leva cerca de 20 minutos para ser concluída. 

	 
	>[AZURE.NOTE] No momento, mudar de qualquer valor positivo de unidades de streaming de volta para nenhuma pode desabilitar o streaming por demanda por até uma hora.

	>[AZURE.NOTE] O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Configurando o ponto de extremidade de streaming

A guia CONFIGURAR permite que você realize configurações conforme mostrado nesta imagem. A seguir, a descrição dos campos.

>[AZURE.NOTE] A configuração nesta página se aplica apenas aos pontos de extremidade de streaming que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda.

![Configure origin][configure-origin]
  

1. Defina o período de cache máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache definido explicitamente no conteúdo do blob.

2. Especifique endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especifique a configuração para autenticação do cabeçalho de assinatura do Akamai.

4. Você pode especificar uma política de acesso entre domínios para clientes do Adobe Flash (para obter mais informações, consulte [Especificação de arquivo de política entre domínios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Além de política de acesso de cliente para clientes do Microsoft Silverlight (para obter mais informações, consulte [Tornando um serviço disponível entre os limites de domínio](https://msdn.microsoft.com/pt-br/library/cc197955(v=vs.95).aspx).  

5. Você também pode configurar nomes de host personalizados clicando no botão **configurar**. Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint](https://msdn.microsoft.com/pt-br/library/dn783468.aspx).  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
