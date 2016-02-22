<properties 
	pageTitle="Guia de solução de problemas de transmissão ao vivo" 
	description="Este tópico fornece sugestões sobre como solucionar problemas de transmissão ao vivo." 
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
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Guia de solução de problemas de transmissão ao vivo

Este tópico fornece sugestões sobre como solucionar alguns problemas de transmissão ao vivo.

## Problemas relacionados aos codificadores locais 

Esta seção fornece sugestões sobre como solucionar problemas relacionados aos codificadores locais que estão configurados para enviar uma transmissão de taxa de bits única para canais do AMS habilitados para a codificação ativa.

###Problema: não há nenhuma opção para gerar uma transmissão progressiva

- **Possível problema**: o codificador que está sendo usado não se desentrelaça automaticamente. 

	**Etapas de solução de problemas**: procure uma opção de desentrelaçamento na interface do codificador. Depois de habilitar o desentrelaçamento, verifique novamente as configurações de saída progressiva.
 
###Problema: várias configurações de saída de codificador foram tentadas, mas ainda não é possível se conectar. 

- **Possível problema**: o canal de codificação do Azure não foi redefinido corretamente. 

	**Etapas de solução de problemas**: verifique se o codificador não está mais enviando para o AMS, pare-o e redefina o canal. Quando estiver em execução novamente, tente conectar seu codificador com as novas configurações. Se isso ainda não corrigir o problema, tente criar um novo canal, pois, às vezes, os canais podem ser corrompidos após várias tentativas com falha.

- **Possível problema**: o tamanho do GOP ou as configurações de quadro-chave não são ideais.

	**Etapas de solução de problemas**: o intervalo recomendado de tamanho do GOP ou de quadro-chave é de 2 segundos. Alguns codificadores calculam essa configuração em número de quadros, enquanto que outras usam segundos. Por exemplo: na saída de 30fps, o tamanho de GOP seria 60 quadros, o que é equivalente a 2 segundos.
	 
- **Possível problema**: as portas fechadas estão bloqueando a transmissão.

	**Etapas de solução de problemas**: ao transmitir por RTMP, verifique as configurações de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. Ao usar a transmissão RTP, confirme que a porta de saída 2010 está aberta.


###Problema: ao configurar o codificador para a transmissão com o protocolo RTP, não há nenhum campo para inserir um nome de host. 

- **Possível problema**: muitos codificadores RTP não permitem nomes de host, e será necessário adquirir um endereço IP.  

	**Etapas de solução de problemas**: para localizar o endereço IP, abra um prompt de comando em qualquer computador. Para fazer isso no Windows, abra o inicializador de Execução (WIN + R) e digite “cmd” para abrir.

	Depois de aberto o prompt de comando, digite “Ping [Nome do Host do AMS]”.

	O nome do host pode ser derivado pela omissão do número da porta da URL de Ingestão do Azure, como destacado no seguinte exemplo:

	rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###Problema: não é possível reproduzir a transmissão publicada.
 
- **Possível problema**: não há nenhum Ponto de Extremidade de Transmissão em execução ou não há nenhuma unidade de transmissão (unidades de escala) alocada. 

	**Etapas de solução de problemas**: navegue até a guia “Ponto de Extremidade de Transmissão” na ferramenta AMSE e confirme se há um Ponto de Extremidade de Transmissão com uma unidade de transmissão.
	
>[AZURE.NOTE] Se, depois de seguir as etapas de solução de problemas, você ainda não conseguir realizar a transmissão, envie um tíquete de suporte usando o Portal Clássico do Azure.

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->