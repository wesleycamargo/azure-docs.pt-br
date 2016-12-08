---
title: "Azure RemoteApp - como a largura de banda de rede e a qualidade da experiência funcionam juntas? | Microsoft Docs"
description: "Saiba como a largura de banda de rede no Azure RemoteApp pode afetar a qualidade da experiência do usuário."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 74ebc1fb-5187-4056-b08c-0e03b5ecaca6
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fc634941e311e6a1492313c434e0da24056e2ceb


---
# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - como a largura de banda de rede e a qualidade da experiência funcionam juntas?
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Quando você está examinando a [largura de banda de rede geral](remoteapp-bandwidth.md) necessária para o Azure RemoteApp, tenha em mente os fatores a seguir - eles fazem parte de um sistema dinâmico que afeta a experiência geral do usuário. 

* **Largura de banda disponível e as condições de rede atuais** – um conjunto de parâmetros (perda, latência, distorção) na mesma rede em um determinado momento pode afetar a experiência de streaming do aplicativo, significando uma experiência geral do usuário reduzida. A largura de banda disponível em sua rede é uma função de congestionamento, perda aleatória e latência, porque todos esses parâmetros afetam o mecanismo de controle de congestionamento, o que, por sua vez, controla a velocidade de transmissão para evitar colisões.  Por exemplo, uma rede com muitas perdas ou com alta latência tornará a experiência do usuário ruim até mesmo em uma rede com largura de banda de 1.000 MB. A perda e latência variam com base no número de usuários que estão na mesma rede e no que esses usuários estão fazendo (por exemplo, assistindo a vídeos, baixando ou carregando arquivos grandes, imprimindo).
* **Cenário de uso** - a experiência depende do que os usuários estão fazendo individualmente e como um grupo na mesma rede. Por exemplo, ler um slide requer apenas a atualização de um único quadro; se o usuário visualiza e rola pelo conteúdo de um documento de texto, é necessário que um número maior de quadros sejam atualizados por segundo. A comunicação de ida e volta com o servidor nesse cenário consumirá, eventualmente, mais largura de banda de rede. Além disso, considere um exemplo extremo: vários usuários estão assistindo vídeos de alta definição (como resolução de 4K), mantendo teleconferências HD, jogando video games em 3D ou trabalhando em sistemas CAD. Todos esses itens podem tornar até mesmo uma rede de largura de banda realmente alta praticamente inutilizável.
* **Número de telas e resolução de tela** – é necessária mais largura de banda de rede para a atualização completa de telas maiores do que de telas menores. A tecnologia subjacente faz um trabalho muito bom de codificar e transmitir apenas as regiões das telas que foram atualizadas, mas de vez em quando, a tela inteira precisa ser atualizada. Quando o usuário tem uma tela com resolução superior (por exemplo, resolução de 4K), essa atualização exige mais largura de banda de rede que uma tela com resolução mais baixa (como 1.024 x 768 px). Essa mesma lógica se aplicará se você usar mais de uma tela para redirecionamento. Largura de banda deve aumentar junto com o número de telas.
* **Redirecionamento de dispositivo e área de transferência** – esse é um problema não muito óbvio, mas em muitos casos, se um usuário armazena um grande bloco de dados na área de transferência, é preciso um pouco de tempo para que essas informações sejam transferidas do cliente de Área de Trabalho Remota para o servidor. A experiência de downstream pode ser afetada pela experiência de enviar o conteúdo da área de transferência sentido upstream. O mesmo se aplica ao redirecionamento de dispositivo - se um scanner ou Webcam produz muitos dados que precisam ser enviados upstream para o servidor, ou uma impressora precisa receber um documento grande, ou armazenamento local precisa estar disponível para um aplicativo em execução na nuvem para copiar um arquivo grande, os usuários podem notar quadros eliminados ou vídeo temporariamente "congelado" porque os dados necessários para o redirecionamento de dispositivo estão aumentando as necessidades de largura de banda de rede. 

Quando você avalia suas necessidades de largura de banda de rede, certifique-se de considerar todos esses fatores funcionando como um sistema.

Agora, volte para o [artigo de largura de banda de rede principal](remoteapp-bandwidth.md), ou prossiga para o teste de sua [largura de banda de rede](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Saiba mais
* [Estimar o uso de largura de banda de rede do Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp - testando o uso da largura de banda de sua rede com alguns cenários comuns](remoteapp-bandwidthtests.md)
* [Largura de banda de rede do Azure RemoteApp - diretrizes gerais (se não puder testar a sua)](remoteapp-bandwidthguidelines.md)




<!--HONumber=Nov16_HO3-->


