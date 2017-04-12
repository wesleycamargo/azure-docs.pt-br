---
title: "Azure RemoteApp – testando o uso da largura de banda de sua rede com alguns cenários comuns | Microsoft Docs"
description: "Saiba mais sobre cenários de uso comuns que podem ajudá-lo a entender suas necessidades de largura de banda de rede para o Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 06417c75-0c63-4ecf-b9d1-66a7af6b7b91
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a321f9c03f3ec8f898ddf9bdd588c1d79bfd163a
ms.lasthandoff: 03/31/2017


---
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - testando o uso da largura de banda de sua rede com alguns cenários comuns
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Conforme discutimos em [Estimate Azure RemoteApp network bandwidth usage (Estimar o uso de largura de banda de rede do RemoteApp do Azure)](remoteapp-bandwidth.md), a melhor maneira de entender qual é o impacto do RemoteApp do Azure sobre sua rede é executar alguns testes de uso. Execute estes testes por um período de tempo definido e meça a largura de banda necessária para cada cenário. Se tiver como fazer isso, você também poderá comparar a perda de pacote de rede e a tremulação de rede para entender os padrões de rede que serão criados em seu ambiente específico.

Ao avaliar o uso de largura de banda, lembre-se de que o uso varia entre diferentes usuários em sua empresa. Por exemplo, usuários que gravam e leem textos geralmente consomem menos largura de banda do que usuários que trabalham com vídeos. Para obter melhores resultados, estude as necessidades dos seus usuários e crie uma combinação dos cenários a seguir que melhor represente os usuários de sua empresa. Lembre-se de [examinar os fatores que afetam o uso de largura de banda e a experiência do usuário](remoteapp-bandwidthexperience.md) , pois isso o ajudará a identificar os testes ideais.

Primeiro, leia sobre os testes, selecione sua combinação e, por fim, execute-os. Você pode usar a tabela a seguir para ajudar a acompanhar o desempenho.

> [!NOTE]
> Se não puder ou não tiver tempo para fazer seus próprios testes de rede, veja nossas [estimativas/recomendações básicas de largura de banda de rede](remoteapp-bandwidthguidelines.md). No entanto, seu nível de uso pode variar. Sendo assim, se *puder* executar seus próprios testes, faça isso.
> 
> 

## <a name="the-usage-tests"></a>Os testes de uso
Cada um destes testes é executado por períodos de tempo diferentes e testa funções/recursos diferentes que consomem largura de banda de rede. Lembre-se de escolher a combinação de testes que melhor atenda os usuários individuais de sua empresa.

### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint executivo/completo - executado por 900 a 1000 segundos
Um usuário apresenta entre 45 e 50 slides de alta fidelidade usando o Microsoft Office PowerPoint no modo de tela inteira. Os slides devem conter imagens, transições (com animações) e planos de fundo com gradiente de cores que são comuns para sua empresa. O usuário deve passar pelo menos 20 segundos em cada slide.

Este cenário cria tráfego intermitente quando um slide faz a transição para o slide seguinte na apresentação.

### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint simples - execute por cerca de 620 segundos
Um usuário apresenta um arquivo de PowerPoint simples com aproximadamente 30 slides usando o Microsoft Office PowerPoint no modo de tela inteira. Os slides consomem mais texto que o cenário de PowerPoint executivo/complexo e têm planos de fundo e imagens mais simples (diagramas pretos). 

### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - execute por cerca de 250 segundos
Um usuário navega na Web usando o Internet Explorer. O usuário navega e percorre uma combinação de textos, imagens naturais e alguns diagramas esquemáticos. As páginas da Web são armazenadas na unidade de disco local do servidor do Host da Sessão da Área de Trabalho Remota (Host da Sessão RD), como um arquivo .MHT. O usuário rola usando as teclas Page Up, Page Down, para baixo e para cima, com intervalos diferentes para cada tecla/tipo de rolagem:

    - Seta para baixo – 250 pressionamentos de tecla a cada 500 ms
    - Page Up – 36 pressionamentos de tecla a cada 1.000 ms
    - Seta para baixo – 75 pressionamentos de tecla a cada 100 ms
    - Page Down – 20 pressionamentos de tecla a cada 500 ms
    - Seta para cima – 120 pressionamentos de tecla a cada 300 ms

### <a name="pdf-document---simple---run-for-610-seconds"></a>Documento em PDF - simples - execute por cerca de 610 segundos
Um usuário lê e pesquisa em um documento em PDF de várias maneiras usando o Adobe Acrobat Reader. O documento deve consistir em tabelas, gráficos simples e várias fontes de texto. O documento tem de 35 a 40 páginas. O usuário rola pelo documento em duas velocidades diferentes, do início ao fim e vice-versa, em quatro níveis de zoom diferentes (ajustar à página, ajustar à largura, 100% e outro de sua preferência). O zoom garante que o texto (fonte) seja renderizado em tamanhos diferentes. A rolagem é feita usando as teclas Page Up, Page Down, para baixo e para cima, com intervalos diferentes para cada rolagem.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Documento em PDF - misto - execute por cerca de 320 segundos
Um usuário lê e pesquisa em um documento em PDF de várias maneiras usando o Adobe Acrobat Reader. O documento consiste em imagens de alta qualidade (incluindo fotografias), tabelas, gráficos simples e várias fontes de texto. O usuário rola pelo documento em duas velocidades diferentes, do início ao fim e vice-versa, em quatro níveis de zoom diferentes (ajustar à página, ajustar à largura, 100% e outro de sua preferência). O zoom garante que o texto (fonte) seja renderizado em tamanhos diferentes. A rolagem é feita usando as teclas Page Up, Page Down, para baixo e para cima, com intervalos diferentes para cada rolagem.

### <a name="flash-video-playback---run-for-180-seconds"></a>Reprodução de vídeo Flash - execute por cerca de 180 segundos
Um usuário exibe um vídeo codificado com Adobe Flash inserido em uma página da Web. A página da Web é armazenada no disco rígido local do servidor Host da Sessão da Área de Trabalho Remota. O vídeo é reproduzido no Internet Explorer por um plug-in de player inserido.

Este cenário emula usuários exibindo páginas da Web com conteúdo avançado e contendo multimídia. A maioria dos dados deve ser por meio de VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Digitação remota do Word - execute por cerca de 1.800 segundos
Um usuário digita um documento em uma sessão de RDP. Os pressionamentos de tecla são enviados do lado do cliente por meio da sessão de RDP para um documento do Microsoft Word em execução na sessão remota. A taxa de digitação é de um caractere a cada 250 ms (total de 7.050 caracteres). 

Este é um dos cenários mais comuns para um trabalhador do conhecimento. Este cenário testa a capacidade de resposta de um usuário digitando em um processador de trabalho moderno. O cenário é sensível até mesmo a pequenas alterações no uso de largura de banda.

## <a name="tracking-the-test-results"></a>Acompanhando os resultados de teste
Você pode usar a tabela a seguir para avaliar os cenários em seu ambiente. Os dados fornecidos abaixo são apenas para fins de ilustração e podem ser muito diferentes daqueles que você observará. 

Para simplificar, estamos supondo que todos os cenários são testados usando a mesma resolução de tela de 1920 x 1080 pixels e o transporte por TCP ocorra em uma rede com latência (atraso) abaixo de 200 ms e tremulação de rede na marca de 120 ms ou mais de cerca de 1%.

Sobre a tabela:

* **Experiência média** contém a largura de banda de rede em que a produtividade do usuário não é afetada significativamente, mas não elimina falhas ocasionais de áudio ou vídeo. O sistema é capaz de se recuperar rapidamente aproveitando a lógica dinâmica. As estimativas de largura de banda de rede tentam garantir a qualidade da experiência do usuário.
  * **Problemas perceptíveis (ponto de interrupção)** contém a largura de banda de rede em que os usuários poderão perceber problemas significativos em sua experiência e sua produtividade será afetada por períodos de tempo consideráveis. Nesse ponto, os algoritmos de RDP encontram dificuldades e não podem garantir a qualidade da experiência do usuário devido à largura de banda de rede insuficiente.
  * **Recomendado** contém a largura de banda de rede recomendada para uma boa ou excelente experiência. Normalmente, está um pouco acima do valor na coluna **Experiência média** correspondente.
  * **Notas** incluem observações e comentários.

| Teste | Experiência média | Problemas perceptíveis (ponto de interrupção) | Largura de banda de rede recomendada | Notas |
| --- | --- | --- | --- | --- |
| PPT executivo/complexo |10 MB/s |1 MB/s |> 10 MB/s, preferencialmente 100 MB/s |A 1 MB/s, muitas animações são perdidas |
| PPT simples |5 MB/s |256 KB/s |10 MB/s |A 256 KB/s, os slides são carregados com atraso perceptível |
| Internet Explorer |10 MB/s |1 MB/s |> 10 MB/s, preferencialmente 100 MB/s |A 1 MB/s, vídeos da Web ficam confusos e instáveis, a rolagem rápida apresenta problemas |
| PDF simples |1 MB/s |256 KB/s |5 MB/s |A 256 KB/s, é necessário algum tempo para carregar a página |
| PDF misto |1 MB/s |256 KB/s |5 MB/s |A 256 KB/s, a página leva uma quantidade considerável de tempo para ser carregada |
| Reprodução de vídeo Flash |10 MB/s |1 MB/s |> 10 MB/s, preferencialmente 100 MB/s |A 1 MB/s, o vídeo fica granulado e alguns quadros são descartados |
| Digitação remota do Word |256 KB/s |128 KB/s |1 MB/s |A 256 KB/s, o usuário pode observar o tempo decorrido entre os pressionamentos de tecla |

Para avaliar a largura de banda de rede por usuário, crie uma combinação dos cenários acima e da proporção correspondente de largura de banda necessária. Selecione o número mais alto necessário para seus cenários. Como os usuários quase nunca usam o sistema sozinhos, leve em consideração alguma largura de banda de reserva para usuários que trabalham simultaneamente na mesma rede.

## <a name="learn-more"></a>Saiba mais
* [Estimar o uso de largura de banda de rede do Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp - como a largura de banda de rede e a qualidade da experiência funcionam juntas?](remoteapp-bandwidthexperience.md)
* [Largura de banda de rede do Azure RemoteApp - diretrizes gerais (se não puder testar a sua)](remoteapp-bandwidthguidelines.md)


