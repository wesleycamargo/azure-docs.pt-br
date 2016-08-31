
<properties 
    pageTitle="Uso de largura de banda de rede estimado do Azure RemoteApp | Microsoft Azure"
	description="Saiba mais sobre os requisitos de largura de banda de rede para as coleções e aplicativos do Azure RemoteApp."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# Estimar o uso de largura de banda de rede do Azure RemoteApp 

> [AZURE.IMPORTANT]
O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

O Azure RemoteApp usa o RDP (protocolo RDP) para a comunicação entre aplicativos em execução na nuvem do Azure e seus usuários. Este artigo fornece algumas diretrizes básicas que você pode usar para calcular esse uso da rede e potencialmente avaliar o uso de largura de banda de rede por usuário do Azure RemoteApp.

Estimar o uso de largura de banda por usuário é muito complexo e exige executar vários aplicativos simultaneamente em cenários multitarefa, nos quais os aplicativos podem afetar o desempenho uns dos outros com base em sua demanda de largura de banda de rede. Até mesmo o tipo de cliente de Área de Trabalho Remota (como o cliente Mac versus cliente HTML5) pode levar a resultados diferentes de largura de banda. Para ajudá-lo a superar essas complicações, dividiremos os cenários de uso em várias das categorias comuns para imitar cenários reais. (Em que o cenário real é, obviamente, uma mistura de categorias e difere por usuário.)

Antes de nos aprofundarmos mais - Observe que podemos presumir que o RDP fornece uma experiência boa a excelente para a maioria dos cenários de uso em redes com latência abaixo de 120 ms e largura de banda superior a 5 MB - isso é baseado na capacidade do RDP de se ajustar dinamicamente usando a largura de banda de rede disponível e a largura de banda estimada que o aplicativo precisa. Este artigo vai além daqueles "cenários de mais uso" para observar a borda, em que os cenários começam a desenrolar e experiência do usuário começa a degradar-se.

Agora, confira os artigos a seguir para obter detalhes, incluindo fatores a considerar, recomendações de linha de base e o que não incluímos em nossas estimativas.

- [Como a largura de banda de rede e a qualidade da experiência funcionam juntas?](remoteapp-bandwidthexperience.md)
- [Testando o uso da largura de banda de sua rede com alguns cenários comuns](remoteapp-bandwidthtests.md)
- [Diretrizes rápidas se você não tiver o tempo nem capacidade para teste](remoteapp-bandwidthguidelines.md)


## O que não estamos incluindo?

Ao examinar os testes propostos e nossas recomendações gerais (e reconhecidamente genéricas), lembre-se de que há vários fatores que nós não consideramos. Por exemplo, as complicações de experiência de usuário fornecidas pela natureza assimétrica da largura de banda de upload vs. download. A natureza assimétrica da maioria das redes Wi-Fi afetará, adicionalmente, o desempenho e a percepção da experiência do usuário. Para cenários interativos, o tráfego downstream pode ter uma prioridade menor do que o upstream, que pode aumentar o número de quadros de vídeos ou áudio perdidos e, portanto, afetar a percepção da experiência de streaming pelo usuário. Você pode executar seus próprios testes para ver o que é bom para sua rede e caso de uso específico.

Embora, discutamos o redirecionamento de dispositivo, podemos não levar em consideração o impacto de largura de banda do tráfego de rede causado por dispositivos conectados, como armazenamento, impressoras, scanners, câmeras Web e outros dispositivos USB. O efeito desses dispositivos geralmente aumenta as necessidades de largura de banda em picos temporariamente e desaparece quando a tarefa for concluída. Mas se feito com frequência, essa demanda de largura de banda poderá ser bastante perceptível.

Também não discutimos como um usuário pode afetar outros usuários na mesma rede. Por exemplo, um usuário consumindo 4K vídeo em uma rede de 100 MB/s pode afetar significativamente outros usuários na mesma rede tentando fazer a mesma tarefa. Infelizmente, torna-se progressivamente mais difícil determinar o impacto de uso simultâneo para fornecer uma recomendação comum ou totalmente abrangente sobre o desempenho do sistema durante a agregação. Tudo o que podemos dizer é que a tecnologia de protocolo subjacente fará o melhor uso da largura de banda de rede disponível, mas ela tem suas limitações.

<!---HONumber=AcomDC_0817_2016-->