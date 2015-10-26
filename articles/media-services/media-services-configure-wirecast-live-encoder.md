<properties 
	pageTitle="Configurar o codificador Telestream Wirecast para enviar uma transmissão ao vivo de taxa de bits única" 
	description="Este tópico mostra como configurar o codificador ativo Wirecast para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa." 
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
	ms.date="09/29/2015"    
	ms.author="juliako"/>

#Usar o codificador Wirecast para enviar uma transmissão ao vivo de taxa de bits única

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md) 

Este tópico mostra como configurar o codificador ativo [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) para enviar uma transmissão de taxa de bits única para os canais do AMS que estão habilitados para a codificação ativa. Para obter mais informações, consulte [Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar o AMS (Serviços de Mídia do Azure) com a ferramenta AMSE (Gerenciador de Serviços de Mídia da Azure). Essa ferramenta é executada apenas em PCs com Windows. Se você estiver usando um Mac ou Linux, use o Portal de Gerenciamento do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program).


##Pré-requisitos

- [Criar uma conta de Serviços de Mídia do Azure](media-services-create-account.md)
- Verifique se há um Ponto de Extremidade de Transmissão em execução com pelo menos uma unidade de transmissão alocada. Para obter mais informações, veja [Gerenciar Pontos de Extremidade de Transmissão em uma conta de Serviços de Mídia](media-services-manage-origins.md) 
- Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer). 
- Inicie a ferramenta e conecte-se à sua conta do AMS.

##Dicas

- Sempre que possível, use uma conexão de Internet com fio. 
- Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.  
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.


## Criar um canal

1.  Na ferramenta AMSE, navegue até a guia **Ao Vivo** e clique com o botão direito do mouse na área de canais. Selecione **Criar canal...** no menu.  

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal; o campo de descrição é opcional. Em Configurações de Canal, selecione **Standard** para a opção de Codificação Ativa, com o Protocolo de Entrada definido como **RTMP**. Você pode deixar todas as outras configurações como estão.


	 Verifique se a opção **Iniciar o novo canal agora** está marcada.
 
3. Clique em **Criar Canal**.![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE]O canal pode levar até 20 minutos para ser iniciado.

Enquanto o canal é iniciado, você pode [configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT]Lembre-se de que a cobrança começa assim que o Canal entra em um estado pronto. Para obter mais informações, veja [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurar o codificador Telestream Wirecast

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

1. Abra o aplicativo do Telestream Wirecast no computador que está sendo usado e configure a transmissão RTMP.
2. Configure a saída navegando até a guia **Saída** e selecionando **Configurações de saída...**.
	
	Verifique se o **Destino de Saída** está definido como **Servidor RTMP**.
3. Clique em **OK**.
4. Na página de configurações, defina o campo **Destino** como **Serviços de Mídia do Azure**.
 
	O perfil de Codificação é pré-selecionado para **Azure H.264 720p 16:9 (1280x720)**. Para personalizar essas configurações, selecione o ícone de engrenagem à direita da lista suspensa e escolha **Nova Predefinição**.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configure as predefinições do codificador.

	Nomeie a predefinição e verifique as seguintes configurações recomendadas:

	**Vídeo**
	
	- Codificador: MainConcept H.264
	- Quadros por segundo: 30
	- Taxa de bits média: 5.000 kbits/s (Pode ser ajustada com base nas limitações de rede)
	- Perfil: Principal
	- Quadro chave: a cada 60 quadros

	**Áudio**

	- Taxa de bits de destino: 192 kbits/s
	- Taxa de amostragem: 44,100 kHz
	 
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Pressione **Salvar**.

	O campo Codificação agora tem o perfil recém-criado disponível para seleção.

	Certifique-se de selecionar o novo perfil.

7. Obtenha a URL de entrada do canal para atribuí-la ao **Ponto de extremidade RTMP** do Wirecast.
	
	Navegue de volta para a ferramenta AMSE e verifique o status de conclusão do canal. Depois que o Estado for alterado de **Inicial** para **Em execução**, é possível obter a URL de entrada.
	  
	Quando o canal estiver em execução, clique com o botão direito do mouse no nome do canal, navegue até e focalize sobre **Copiar a URL de Entrada na Área de Transferência** e selecione **URL de entrada primária**.
	
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Na janela **Configurações de Saída** do Wirecast, cole essas informações no campo **Endereço** da seção de saída e atribua um nome de transmissão.


	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Selecione **OK**.

10. Na tela principal do **Wirecast**, confirme se as fontes de entrada de áudio e vídeo estão prontas e pressione **Transmissão** no canto superior esquerdo.

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Antes de clicar em **Transmissão**, é **necessário** verificar se o Canal está pronto. Além disso, lembre-se de não deixar o Canal em um estado pronto sem um feed de contribuição de entrada por mais de 15 minutos.

##Reprodução de teste
  
1. Navegue até a ferramenta AMSE e clique com botão direito do mouse no canal a ser testado. No menu, passe o mouse sobre **Reproduzir a Visualização** e selecione **com o Azure Media Player**.  

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se a transmissão for exibida no player, isso significa que o codificador foi corretamente configurado para se conectar ao AMS.

Se um erro for recebido, será necessário redefinir o canal e ajustar as configurações do codificador. Veja o tópico [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.

##Criar um programa

1. Depois que a reprodução do canal for confirmada, crie um programa. Na guia **Ao Vivo** na ferramenta AMSE, clique com o botão direito do mouse na área de programas e selecione **Criar Novo Programa**.  

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO3-->