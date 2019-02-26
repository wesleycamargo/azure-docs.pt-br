---
title: O que é a API de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: Saiba como usar o serviço de Detecção Facial para detectar e analisar rostos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 2f5f57f0978adbdf33ed4ce25ba9b32247ea0484
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455969"
---
# <a name="what-is-the-azure-face-api"></a>O que é a API de Detecção Facial do Azure?

A API de Detecção Facial do Azure é um serviço cognitivo que fornece algoritmos para detectar, reconhecer e analisar rostos humanos em imagens. A capacidade de processar informações de rostos humanos é importante em muitos cenários de software diferentes, como segurança, interface natural do usuário, análise e gerenciamento de conteúdo de imagem, aplicativos móveis e robótica.

A API de Detecção Facial fornece várias funções diferentes, cada uma descrita nas seções a seguir. Continue lendo para saber mais sobre cada uma delas.

## <a name="face-detection"></a>Detecção facial

A API de Detecção Facial pode detectar rostos humanos em uma imagem e retornar as coordenadas do retângulo de seus locais. Opcionalmente, a detecção facial pode extrair uma série de atributos relacionados ao rosto, como pose, pose da cabeça, gênero, idade, emoção, pelos do rosto e óculos.

![Uma imagem de uma mulher e um homem, com retângulos desenhados em torno de seus rostos e informações de idade e sexo exibidas](./Images/Face.detection.jpg)

> [!NOTE] 
> O recurso de detecção facial também está disponível na [API de Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), mas se você quiser realizar mais operações com os dados faciais, deverá usar a API de Detecção Facial (este serviço). 

Para obter mais informações sobre a detecção facial, confira a [API de Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Verificação facial

A API de Verificação executa uma autenticação com relação a dois rostos detectados ou de um rosto detectado com relação a um objeto pessoa. Na prática, ela avalia se dois rostos pertencem à mesma pessoa. Isso pode ser útil em cenários de segurança. Para obter mais informações, confira a [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API de Localização de Semelhantes usa um rosto selecionado e um conjunto de rostos elegíveis e o restringe a um conjunto menor de rostos que são mais semelhantes ao rosto selecionado. Dois modos de trabalhar, **matchPerson** e **matchFace**, têm suporte. O modo **matchPerson** retorna rostos semelhantes após a filtragem pela mesma pessoa (usando a [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). O modo **matchFace** ignora o filtro da mesma pessoa e retorna uma lista de rostos semelhantes que podem ou não ser a mesma pessoa.

No exemplo a seguir, este é o rosto selecionado:

![Uma mulher sorrindo](./Images/FaceFindSimilar.QueryFace.jpg)

E estes são os rostos elegíveis:

![Cinco imagens de pessoas sorrindo. As imagens a) e b) são da mesma pessoa](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro rostos semelhantes, o modo **matchPerson** retornaria (a) e (b), que representam a mesma pessoa que o rosto buscado. O modo **matchFace** retorna (a), (b) (c) e (d)&mdash;, exatamente quatro possibilidades, mesmo se algumas não são a pessoa selecionada ou têm similaridade baixa. Para obter mais informações, confira [API de Localização de Semelhantes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Agrupamento facial

A API de Grupo divide um conjunto de rostos desconhecidos em vários grupos com base na similaridade. Cada grupo é um devido subconjunto separado do conjunto original de rostos. Todos os rostos em um grupo têm probabilidade de pertencer à mesma pessoa, mas pode haver vários grupos diferentes para uma mesma pessoa (diferenciados por outro fator, como expressão, por exemplo). Para obter mais informações, confira [API de Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificação pessoal

A API de Identificação pode ser usada para identificar um rosto detectado comparado com um banco de dados de pessoas. Isso pode ser útil para marcação de imagem automática em software de gerenciamento de fotos. Você cria o banco de dados com antecedência, e ele pode ser editado ao longo do tempo.

A imagem a seguir é um exemplo de um banco de dados chamado “myfriends”. Cada grupo pode conter até um milhão de objetos pessoa diferentes, e cada objeto pessoa pode ter até 248 rostos registrados.

![Uma grade com três colunas para pessoas diferentes, cada uma com três linhas de imagens de rosto](./Images/person.group.clare.jpg)

Após um banco de dados ter sido criado e treinado, você pode fazer a identificação com relação ao grupo e a um novo rosto detectado. Se a face for identificada como uma pessoa no grupo, ela é retornada.

Para mais informações sobre identificação de pessoas, confira [API de Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Usar contêineres

[Use o contêiner de Detecção Facial](face-how-to-install-containers.md) para detectar, reconhecer e identificar rostos, instalando um contêiner do Docker padronizado mais próximos aos seus dados.

## <a name="sample-apps"></a>Aplicativos de exemplo

Os aplicativos de exemplo a seguir demonstram algumas maneiras de uso da API de Detecção Facial.

- [API de Detecção Facial da Microsoft: Biblioteca de Clientes do Windows e amostra](https://github.com/Microsoft/Cognitive-Face-Windows) – um aplicativo WPF que demonstra vários cenários de detecção facial, análise e identificação.
- [Aplicativo UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes): um aplicativo UWP (Plataforma Universal do Windows) que usa identificação facial com fala, Cortana, tinta e câmera em um cenário de compartilhamento de anotações em família.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Detecção Facial devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga um início rápido para implementar um cenário de detecção facial simples em código.
- [Início Rápido: Detectar rostos em uma imagem usando o SDK do .NET com C#](quickstarts/csharp.md) (outras linguagens disponíveis)
