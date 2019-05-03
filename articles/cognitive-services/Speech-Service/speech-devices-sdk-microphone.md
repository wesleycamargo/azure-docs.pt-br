---
title: Recomendações de matriz de microfone do SDK de dispositivos fala - serviços de fala
titleSuffix: Azure Cognitive Services
description: Recomendações de matriz de microfone de SDK de dispositivos de fala. As geometrias de matriz a seguir são recomendadas para uso com a pilha de áudio da Microsoft. Local das fontes de som e rejeição de acordo com o som são aprimorados com número maior de microfones com dependências em aplicativos específicos, cenários de usuário e o fator forma de dispositivo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: c8bc11b8f81fe034ceaa93c7bd8a49771d9407f9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025774"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendações de matriz do microfone do SDK de dispositivos de fala

Neste artigo, você aprenderá como criar uma matriz de microfone para o SDK de dispositivos de fala.

O SDK de dispositivos de fala funciona melhor com uma matriz de microfone que tenha sido projetada de acordo com as diretrizes a seguir, incluindo a seleção de geometria e o componente do microfone. Diretrizes também recebe em considerações elétricas e integração.

## <a name="microphone-geometry"></a>Geometria do microfone

As geometrias de matriz a seguir são recomendadas para uso com a pilha de áudio da Microsoft. Local das fontes de som e rejeição de acordo com o som são aprimorados com número maior de microfones com dependências em aplicativos específicos, cenários de usuário e o fator forma de dispositivo.

|          | Matriz circular    |       |  Matriz linear              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |![7 matriz circular do mic](media/speech-devices-sdk/7-mic-c.png)|![4 matriz circular do mic](media/speech-devices-sdk/4-mic-c.png)|![4 matriz linear do mic](media/speech-devices-sdk/4-mic-l.png)|![2 matriz linear do mic](media/speech-devices-sdk/2-mic-l.png)|
| \# Microfones  | 7                 | 4                 | 4              | 2              |
| Geometria | 6 1 externa, Center, o raio = 42.5 mm, com espaçamento uniforme| 3 1 externa, Center, o raio = 42.5 mm, com espaçamento uniforme | Comprimento = 120 mm, espaçamento = 40 mm | Espaçamento = 40 mm |

## <a name="component-selection"></a>Seleção de componente

Componentes de microfone devem ser selecionados para reproduzir com precisão um sinal sem ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro                         | Recomendadas                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz sinal 94 dBSPL, ponderados de um ruído)   |
| Amplitude de correspondência                | ± 1 dB @ 1 kHz                     |
| Fase de correspondência                    | ± 2° @ 1 kHz                       |
| Ponto de sobrecarga acústicos (AOP)     | \> 120 dBSPL (THD = 10%)          |
| Taxa de bits                          | Mínimo de 24 bits                    |
| Taxa de amostragem                     | Mínimo 16 kHz\*                   |
| Directivity                       | Unidirecional                   |
| Resposta de frequência                | ± 3 dB, 200 8000 Hz flutuante máscara\*|
| Confiabilidade                       | Intervalo de temperatura de armazenamento-40 ° C a 70 ° C<br />Intervalo de temperatura operacional-20 ° C a 55 ° C  |

*\*Taxas de amostragem mais altas ou intervalos de frequência "maior" podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

Seleção de componente boa deve ser emparelhada com boa integração electroacoustic para evitar prejudicando o desempenho dos componentes usados. Casos de uso exclusivo também podem necessitar de requisitos adicionais (por exemplo: operando intervalos de temperatura).

## <a name="microphone-array-integration"></a>Integração de matriz de microfone

O desempenho das matrizes quando integrado em um dispositivo e depois de qualquer ganho fixo ou EQ deve atender as recomendações a seguir:

|  Parâmetro        |    Recomendadas |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz sinal 94 dBSPL, ponderados de um ruído) |
|  Sensibilidade de saída  | -26 dBFS/Pa @ 1 kHz (recomendado) |
|  Amplitude de correspondência  | ± 2 dB, 200-8000 Hz |
|  Fase de correspondência      | ± 5°, 200-8000 Hz |
| THD%                 | ≤ 1%, 200 8000 Hz, 94 dBSPL, 5 de ordem |
|  Resposta de frequência  | ± 6 dB, 200 8000 Hz flutuante máscara\* |

*\*"Maior" intervalos de frequência podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

## <a name="speaker-integration-recommendations"></a>Recomendações de integração de alto-falante

Conforme necessário, para dispositivos de reconhecimento de fala que contêm os alto-falantes cancelamento de eco, recomendações adicionais são fornecidas para integração e a seleção de alto-falante.

| Parâmetro                         | Recomendadas                       |
|-----------------------------------|-----------------------------------|
| Considerações de linearidade          | Nenhum processamento não-linear após a referência de alto-falante, caso contrário, um fluxo de referência de loopback com base em hardware é necessário  |
| Loopback de alto-falante                  | Fornecido por meio de WASAPI, APIs privadas, personalizado ALSA plug-in (Linux) ou fornecidos por meio do canal de firmware      |
| THD%                              | 3ª octave bandas mínimo 5 de ordem, 70 dBA reprodução @ m 0,8 ≤ 6.3%, 500 315 Hz ≤ 5%, 5000 630 Hz                 |
| Acoplamento de eco a microfones      | \> dB-10 TCLw usando o método ITU-T G.122 Anexo B.4, normalizado para o nível de mic<br />TCLw = TCLwmeasured \+ (medido em nível - sensibilidade de saída de destino)<br />TCLw = TCLwmeasured \+ (medido em nível - (-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de integração de design

As seguintes diretrizes para arquitetura são necessárias ao integrar microfones em um dispositivo:

| Parâmetro                         | Recomendações                    |
|-----------------------------------|-----------------------------------|
| Similaridade de porta do MIC               | Todas as portas de microfone têm o mesmo comprimento na matriz    |
| Dimensões de porta do MIC               | Porta mm de 1.0 Ø0.8 de tamanho. Comprimento da porta / porta diâmetro \< 2              |
| Lacre MIC                       | Lacrar gaskets implementadas uniformemente na pilha para cima. Recomendamos \> 70% de taxa de compactação para gaskets espuma     |
| Confiabilidade do MIC                   | Malha deve ser usada para impedir a poeira e a entrada (entre PCB para inferior portado microfones e vedação rosto gaxeta/superior)  |
| Isolamento do MIC                     | Gaskets borracha e vibração desacoplamento por meio da estrutura, particularmente para isolar os caminhos de vibração devido ao alto-falantes integrados      |
| Relógio de amostragem                    | Áudio de dispositivo deve estar livre de tremulação e quedas com descompasso baixa    |
| Recurso de registro                 | O dispositivo deve ser capaz de gravar fluxos bruto de canal individual simultaneamente |
| USB                               | Todos os dispositivos entrados de áudio de USB devem definir os descritores de acordo com o [especificação de Rev3 de dispositivos de áudio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria do microfone               | Drivers devem implementar [descritores de geometria de matriz de microfone](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) corretamente  |
| Capacidade de descoberta                   | Dispositivos não devem ter qualquer undiscoverable ou incontrolável de hardware, firmware ou 3ª algoritmos de processamento de áudio não-linear com base em software de terceiros de/para o dispositivo|
| Formato da captura                    | Formatos de captura devem usar uma taxa de amostragem mínimo de 16 kHz e profundidade de 24 bits recomendada      |

## <a name="electrical-architecture-considerations"></a>Considerações sobre a arquitetura elétrica

Onde aplicável, as matrizes podem estar conectadas a um host USB (por exemplo, um SoC que executa a pilha de áudio da Microsoft) e interfaces para outros aplicativos ou serviços de fala.

Componentes de hardware, como a conversão de PDM-TDM devem garantir que o intervalo dinâmico e SNR dos microfones é preservado dentro de classificadores de reinicialização.

Alta velocidade 2.0 de classe de áudio USB deve ser compatível dentro de qualquer MCUs áudio para fornecer a largura de banda necessária para até sete canais em taxas mais altas de exemplo e intensidades de bit.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Saiba mais sobre o [SDK de dispositivos de fala](speech-devices-sdk.md)