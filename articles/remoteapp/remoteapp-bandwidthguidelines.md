---
title: Largura de banda de rede do Azure RemoteApp - diretrizes gerais | Microsoft Docs
description: "Compreenda algumas diretrizes básicas de largura de banda de rede para as coleções e aplicativos do Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 529bf318-ae37-4c14-a11c-43fa703d68a3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44fcb6cca30acbd5771a0301f72faa7d9105849


---
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Largura de banda de rede do Azure RemoteApp - diretrizes gerais (se não puder testar a sua)
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Caso você não tenha tempo nem capacidade de executar os [testes de largura de banda de rede](remoteapp-bandwidthtests.md) para o Azure RemoteApp, aqui estão algumas diretrizes bastante genéricas que poderão ajudá-lo a estimar a largura de banda de rede por usuário.

Caso você tenha uma combinação desses cenários, nós não recomendamos nada menor que (ou igual a) 10 MB/s, como a largura de banda de rede MÍNIMA para aplicativos modernos conectados à Internet em um ambiente remoto. (Embora, conforme discutido, isso não assegure uma experiência de usuário acima da média.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint complexo, PowerPoint simples
O Azure RemoteApp funciona melhor em LAN de 100 MB. No perfil de rede 10 MB/s, quando a tremulação acima de 120 ms é superior a 5%, o usuário verá uma experiência média. A 1 MB/s, a diferença é evidente - por exemplo, em uma apresentação de slides, o usuário talvez não veja transição animada nenhuma porque os quadros são ignorados.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, PDF misto, PDF, Texto
O perfil de rede de 10 MB/s se aproxima de LAN na maioria dos aspectos. 1 MB/s fornecerá uma experiência OK, embora possa haver alguma tremulação quando o usuário rola enquanto há imagens na tela.

## <a name="flash-video-youtube"></a>Vídeo em Flash (YouTube)
LAN de 100 MB/s fornece a melhor experiência, enquanto 10 MB/s é aceitável (que significa acompanhar a taxa de quadros, mas com aumento da tremulação). A 1 MB/s, a tremulação é muito alta e perceptível.

## <a name="word-typing-word-remote-input"></a>Digitação de palavras (entrada remota do Word)
Esse é um cenário de uso de baixa largura de banda. A 256 KB/s fornecemos uma experiência tão boa quanto aquela obtida com LAN.

## <a name="learn-more"></a>Saiba mais
* [Estimar o uso de largura de banda de rede do Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp - como a largura de banda de rede e a qualidade da experiência funcionam juntas?](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp - testando o uso da largura de banda de sua rede com alguns cenários comuns](remoteapp-bandwidthtests.md)




<!--HONumber=Nov16_HO3-->


