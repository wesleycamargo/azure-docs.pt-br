<properties 
	pageTitle="Configurar o codificador FMLE para enviar uma transmissão ao vivo de taxa de bits única | Microsoft Azure" 
	description="Este tópico mostra como configurar o codificador FMLE (Flash Media Live Encoder) para enviar uma transmissão de taxa de bits única para os canais do AMS que são habilitados para codificação ativa." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako;cenkdin;anilmur"/>

#Usar o codificador FMLE para enviar uma transmissão ao vivo de taxa de bits única

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

Este tópico mostra como configurar o codificador [FMLE](http://www.adobe.com/products/flash-media-encoder.html) (Flash Media Live Encoder) para enviar uma transmissão de taxa de bits única para os canais do AMS que são habilitados para codificação ativa. Para obter mais informações, consulte [Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver usando um Mac ou Linux, use o Portal Clássico do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

Observe que este tutorial descreve como usar o AAC. No entanto, por padrão, o FMLE dá suporte ao AAC. Você precisa comprar um plug-in para codificação do AAC como MainConcept: [AAC plug-in](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

##Pré-requisitos

- [Criar uma conta de Serviços de Mídia do Azure](media-services-create-account.md)
- Verifique se há um Ponto de Extremidade de Transmissão em execução com pelo menos uma unidade de transmissão alocada. Para obter mais informações, veja [Gerenciar Pontos de Extremidade de Transmissão em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md)
- Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Inicie a ferramenta e conecte-se à sua conta do AMS.

##Dicas

- Sempre que possível, use uma conexão de Internet com fio.
- Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.

## Criar um canal

1.  Na ferramenta AMSE, navegue até a guia **Ao Vivo** e clique com o botão direito do mouse na área de canais. Selecione **Criar canal...** no menu.

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Especifique um nome de canal; o campo de descrição é opcional. Em Configurações de Canal, selecione **Standard** para a opção de Codificação Ativa, com o Protocolo de Entrada definido como **RTMP**. Você pode deixar todas as outras configurações como estão.


Verifique se a opção **Iniciar o novo canal agora** está marcada.

3. Clique em **Criar Canal**.![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] O canal pode levar até 20 minutos para ser iniciado.


Enquanto o canal é iniciado, você pode [configurar o codificador](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

>[AZURE.IMPORTANT] Lembre-se de que a cobrança começa assim que o Canal entra em um estado pronto. Para obter mais informações, veja [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_fmle_rtmp></a>Configurar o codificador FMLE

Neste tutorial, são usadas as configurações de saída abaixo. O restante desta seção descreve as etapas de configuração mais detalhadamente.

**Vídeo**:
 
- Codec: H.264
- Perfil: Alto (nível 4.0)
- Taxa de bits: 5.000 kbps
- Quadro-chave: 2 segundos (60 segundos)
- Taxa de quadros: 30
 
**Áudio**:

- Codec: AAC (LC)
- Taxa de bits: 192 kbps
- Taxa de amostragem: 44,1 kHz


###Etapas da configuração

1. Navegue até a interface do Flash Media Live Encoder (FMLE) no computador que está sendo usado.

	A interface é uma página principal de configurações. Por favor, anote as seguintes configurações recomendadas para introdução à transmissão usando FMLE.
	
	- Formato: Taxa de quadros H. 264: 30,00
	- Tamanho de entrada: 1280x720
	- Taxa de bits: 5000 kbits/s (pode ser ajustada com base nas limitações de rede)

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

	Quando usar as fontes entrelaçadas desmarque a opção “Desentrelaçar”

2. Selecione o ícone de chave inglesa ao lado de Formato, essas configurações adicionais devem ser:

	- Perfil: Principal
	- Nível: 4.0
	- Frequência de quadro-chave: 2 segundos
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. Defina a configuração de áudio importante a seguir:
	
	- Formato: AAC
	- Taxa de exemplo: 44100 kHz
	- Taxa de bits: 192 Kbps
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. Obtenha a URL de entrada do canal para atribuí-la ao **Ponto de extremidade FMLE** do FMLE.
	
	Navegue de volta para a ferramenta AMSE e verifique o status de conclusão do canal. Depois que o Estado for alterado de **Inicial** para **Em execução**, é possível obter a URL de entrada.
	  
	Quando o canal estiver em execução, clique com o botão direito do mouse no nome do canal, navegue até e focalize **Copiar a URL de Entrada na Área de Transferência** e selecione **URL de Entrada Primária**.
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. Cole essas informações no campo **URL do FMS** da seção de saída e atribua um nome de transmissão.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

	Para redundância adicional, repita essas etapas com a URL de entrada secundária.
8. Selecione **Conectar**.

>[AZURE.IMPORTANT] Antes de clicar em **Conectar**, é **necessário** verificar se o Canal está pronto. Além disso, lembre-se de não deixar o Canal em um estado pronto sem um feed de contribuição de entrada por mais de 15 minutos.

##Reprodução de teste
  
1. Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre **Reproduzir a Visualização** e selecione **com o Azure Media Player**.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.

##Criar um programa

1. Depois que a reprodução do canal for confirmada, crie um programa. Na guia **Ao Vivo** da ferramenta AMSE, clique com o botão direito do mouse na área de programas e selecione **Criar Novo Programa**.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

2. Nomeie o programa e, se necessário, ajuste a **Duração da Janela de Arquivo** (cujo padrão é de 4 horas). Você também pode especificar um local de armazenamento ou deixar como o padrão.
3. Marque a caixa **Iniciar o Programa agora**.
4. Clique em **Criar Programa**.
  
	Observação: a criação do programa leva menos tempo do que a criação do canal.
 
5. Quando o programa estiver em execução, confirme a reprodução clicando com o botão direito do mouse no programa navegando até **Reproduzir o(s) programa(s)** e selecionando **com o Azure Media Player**.
6. Depois de confirmar, clique com o botão direito do mouse no programa novamente e selecione **Copiar a URL de Saída na Área de Transferência** (ou recupere essas informações na opção **Informações e configurações do programa** do menu).

A transmissão agora está pronta para ser inserida em um player ou distribuída para um público para a exibição ao vivo.


## Solucionar problemas

Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->