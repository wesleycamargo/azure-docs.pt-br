---
title: Glossário do-serviço de API de detecção facial
titleSuffix: Azure Cognitive Services
description: O glossário explica termos que você pode encontrar conforme trabalha com o Serviço de API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: d627c3c4419affa0d71cdb23df945c96d9fd7585
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652426"
---
# <a name="glossary"></a>Glossário

## <a name="a"></a>O 

#### <a name="attributes"></a>Atributos

Atributos são recursos de face opcionais que podem ser detectados, tais como [idade](#age-attribute), [sexo](#gender-attribute), [pose principal](#head-pose-attribute), [pelos faciais](#facial-hair-attribute)e [Smiley](#smile-attribute). Eles podem ser obtidos a API de detecção, especificando o _returnFaceAttributes_ parâmetro de consulta.

Para obter uma lista completa dos atributos faciais, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Idade (atributo)

A idade é um do [atributos](#attributes) que descreve a idade de uma face específica. O atributo idade é opcional nos resultados de detecção e pode ser controlado com uma solicitação de detecção especificando o parâmetro returnFaceAttributes.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Candidato

Candidatos são essencialmente os resultados de [identificação](#identification) (por exemplo, pessoas identificadas e nível de confiança nas detecções). Um candidato é representado pelo [PersonID](#person-id) e [confiança](#confidence), indicando que a pessoa que é identificada com um alto nível de confiança.

Para obter mais informações, consulte a documentação de referência: [Enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Confiança

Confiança é uma medida revelando a similaridade entre as [faces](#face) ou [Pessoa](#person) em valores numéricos – que é usado na [identificação](#identification), e [ verificação](#verification) para indicar as semelhanças dos resultados pesquisados, identificados e verificados.

Para obter mais informações, consulte a documentação de referência: [Rosto – Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) e [Rosto – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Detecção facial/detecção

A detecção facial é a ação de encontrar as faces em imagens. Os usuários podem carregar uma imagem ou especificar uma URL de imagem na solicitação. As faces detectadas são retornadas com [IDs de Face](#face-id)  indicando uma identidade exclusiva na API de Detecção Facial. Os retângulos indicam as localizações de face da imagem em pixels, bem como [atributos](#attributes) opcionais para cada face como [idade](#age-attribute), [gênero](#gender-attribute), [pose da cabeça ](#head-pose-attribute), [forma facial](#facial-hair-attribute) e [sorriso](#smile-attribute).

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Emoção (atributo)

A emoção é um dos [atributos faciais](#attributes). Quando consultado, ele retorna uma lista de emoções e sua confiança de detecção para a determinada face. Pontuações de confiança são normalizadas: adicionará as pontuações em todas as emoções até um. As emoções retornadas são felicidade, tristeza, neutral, raiva, desdém, aversão, surpresa e medo.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Face

Face é um termo unificado para os resultados derivados da API de Detecção Facial relacionados às faces detectadas. Por fim, face é representado por uma identidade unificados ([Face ID](#face-id)), uma região especificada em imagens ([retângulo facial](#face-rectangle)) e os atributos adicionais relacionadas à face, como [idade](#age-attribute), [gênero](#gender-attribute), pontos de referência e [pose principal](#head-pose-attribute). Além disso, a detecção pode retornar rostos.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API de Detecção Facial

A API de Detecção Facial é uma API baseada em nuvem que fornece os algoritmos mais avançados para reconhecimento e detecção de face. A funcionalidade principal da API de Detecção Facial pode ser dividida em duas categorias: detecção facial com atributos e [reconhecimento](#recognition) facial.

Para obter mais informações, consulte a documentação de referência: [Visão geral da API de Detecção Facial](./Overview.md), [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Rosto – Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Rosto – Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) e [Rosto – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atributos de rosto/atributos faciais

Consulte [Atributos](#attributes).

#### <a name="face-id"></a>Face ID

A ID de Detecção Facial é derivada dos resultados de detecção, no qual uma cadeia de caracteres representa um [rosto](#face) na [API de Detecção Facial](#face-api).

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Pontos de referência faciais marcos/Facial

Os pontos de referência são opcionais nos resultados de detecção, que são pontos faciais semânticos, como olhos, nariz e boca (ilustrados na figura a seguir). Os pontos de referência podem ser controlados com uma solicitação de detecção pelo número booliano de returnFaceLandmarks. Se o returnFaceLandmarks for definido como verdadeiro, as faces retornadas terão os atributos de ponto de referência.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Retângulo facial

O retângulo de rosto é derivado dos resultados de detecção, que fazem um retângulo vertical (esquerda, superior, largura, altura) nas imagens em pixels. O canto superior esquerdo de uma [face](#face) (esquerda e superior), além da largura e altura, indica os tamanhos nos eixos x e y, respectivamente.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Pelos faciais (atributo)

O pelo facial é um dos [atributos](#attributes) usados para descrever o comprimento do pelo facial das faces disponíveis. O atributo de pelo facial é opcional nos resultados de detecção e pode ser controlado com uma solicitação de detecção pelo returnFaceAttributes. Se returnFaceAttributes contiver “facialHair”, as faces retornadas terá os atributos de pelo facial.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

A FaceList é uma coleção de [PersistedFace](#persistedface) e é a unidade de [Encontrar Semelhante](#find-similar). Uma FaceList vem com uma [ID de FaceList](#facelist-id), bem como outros atributos, como Nome e Dados de Usuário.

Para obter mais informações, consulte a documentação de referência: [FaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) e [FaceList – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>ID de FaceList

A ID de FaceList é uma cadeia de caracteres fornecida pelo usuário usada como um identificador de uma [FaceList](#facelist). A ID de FaceList deve ser exclusiva dentro da assinatura.

Para obter mais informações, consulte a documentação de referência: [FaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) e [FaceList – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Localizar semelhante

Essa API é usada para pesquisar/consultar faces semelhantes com base em uma coleção de faces. As faces de consulta e coleções de face são representadas como [IDs de face](#face-id) ou [ID FceLis](#facelist-id)/[ID LargeFaceList](#largefacelist-id) na solicitação. Os resultados retornados são rosto semelhantes pesquisadas, representadas por [IDs de rosto](#face-id) ou IDs de PersistedFace.

Para obter mais informações, consulte a documentação de referência: [Rosto – Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [FaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Gênero (atributo)

O gênero é um dos [atributos](#attributes) usados para descrever os gêneros das faces disponíveis. O atributo de gênero é opcional nos resultados de detecção e pode ser controlado com uma solicitação de detecção pelo returnFaceAttributes. Se returnFaceAttributes contiver “gender”, as faces retornadas terá os atributos de gênero.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Agrupamento

O agrupamento de face é o agrupamento de uma coleção de faces de acordo com as semelhanças faciais. As coleções de face são indicadas pelas coleções de ID de face na solicitação. Como resultado de agrupamento, faces semelhantes são agrupadas juntas como [grupos](#groups), e as faces que não são semelhantes a qualquer outra face são mescladas em conjunto como um grupo confuso. No máximo, há um [grupo confuso](#messy-group) no resultado do agrupamento.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupos

Os grupos são provenientes de resultados de [agrupamento](#grouping). Cada grupo contém uma coleção de faces semelhantes, em que as faces são indicadas por [IDs de face](#face-id).

Para obter mais informações, consulte a documentação de referência: [Enfrentam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Head representar (atributo)

A pose da cabeça é um dos [atributos](#attributes) que representa a orientação de face no espaço em 3D de acordo com os ângulos de sobreposição, de passo e de guinada, conforme exibido na figura a seguir. Os intervalos de valores de passo e guinada são [-180, 180] e [-90, 90] em graus. Na versão atual, o valor de passo retornado de detecção é sempre 0. O atributo de pose da cabeça é opcional nos resultados de detecção e pode ser controlado com uma solicitação de detecção pelo parâmetro returnFaceAttributes. Se o parâmetro returnFaceAttributes contiver “headPose”, as faces retornadas terão os atributos de pose de cabeça.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identificação

A identificação é para identificar uma ou mais faces de um LargePersonGroup/PersonGroup.
Um [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) é uma coleção de [Pessoas](#person).
Faces e o LargePersonGroup/PersonGroup são representados por [IDs de face](#face-id) e [IDs LargePersonGroup](#largepersongroup-id)/[IDs PersonGroup](#persongroup-id) respectivamente na solicitação.
Os resultados identificados são [candidatos](#candidate) representados por [Pessoas](#person) com confiança.
As várias faces na entrada são consideradas separadamente, e cada face terá seu próprio resultado identificado.

> [!NOTE]
> O LargePersonGroup/PersonGroup deve ser treinado com êxito antes da identificação. Se o LargePersonGroup/PersonGroup não for treinado, ou de [status](#status-train) de treinamento não for exibido como "com êxito" (ou seja, “em execução”, “com falha” ou “tempo limite”), a resposta da solicitação é 400.
> 

Para obter mais informações, consulte a documentação de referência: [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical é um campo booliano de resultados de [verificação](#verification) resultados que indica se duas faces pertencem à mesma pessoa.

Para obter mais informações, consulte a documentação de referência: [Face: verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Pontos de referência

Consulte os pontos de referência de rosto.

#### <a name="largefacelist"></a>LargeFaceList

A LargeFaceList é uma coleção de [PersistedFace](#persistedface) e é a unidade de [Encontrar Semelhante](#find-similar). Uma LargeFaceList vem com uma [ID de LargeFaceList](#largefacelist-id), bem como outros atributos, como Nome e Dados de Usuário.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce) e [LargeFaceList – Listar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

A ID de FaceList é uma cadeia de caracteres fornecida pelo usuário usada como um identificador de uma [LargeFaceList](#largefacelist). A ID de LargeFaceList deve ser exclusiva dentro da assinatura.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargeFaceList – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup é uma coleção de [Pessoas](#person) e é a unidade de [Identificação](#identification). Um LargePersonGroup vem com uma [ID de LargePersonGroup](#largepersongroup-id), bem como outros atributos, como Nome e Dados de Usuário.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) e [LargePersonGroup Person – Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup ID

A ID LargePersonGroup ID é uma cadeia de caracteres fornecida pelo usuário com identificadora de um [LargePersonGroup](#largepersongroup). A ID de LargePersonGroup deve ser exclusiva dentro da assinatura.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) e [LargePersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Messy group

O grupo confuso é derivado dos resultados de [agrupamento](#grouping) que contém as faces não semelhantes a qualquer outra face. Cada face em um grupo confuso é indicada pela [ID de face](#face-id).

Para obter mais informações, consulte a documentação de referência: [Enfrentam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nome (pessoa)

Nome é uma cadeia de caracteres descritiva amigável para o [pessoa](#person). Ao contrário da [ID de pessoa](#person-id), o nome de pessoas pode ser duplicado em um grupo.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) e [PersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Nome (LargePersonGroup/PersonGroup)

Nome também é uma cadeia de caracteres descritiva amigável para o [LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup). Ao contrário da [ID LargePersonGroup](#largepersongroup-id)/[ID PersonGroup](#persongroup-id), o nome do LargePersonGroups/PersonGroups pode ser duplicado em uma assinatura.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [PersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace é uma estrutura de dados na API detecção Facial. PersistedFace vem com uma ID PersistedFace, bem como outros atributos, como Nome e Dados do Usuário.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) e [PersonGroup Person – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>ID da pessoa

A ID da pessoa que é gerada quando uma [PersistedFace](#persistedface) é criada com êxito. Uma cadeia de caracteres é criada para representar esta Face na [API de Detecção Facial](#face-api).

Para obter mais informações, consulte a documentação de referência: [LargeFaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) e [PersonGroup Person – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Pessoa

Pessoa é uma estrutura de dados gerenciada na API de Detecção Facial. Pessoa vem com uma [ID de Pessoa](#person-id), bem como outros atributos, como Nome, uma coleção de [PersistedFace](#persistedface) e Dados do Usuário.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) e [PersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID da pessoa

A ID da Pessoa é gerada quando uma [Pessoa](#person) é criada com êxito. Uma cadeia de caracteres é criada para representar esta pessoa na [API de Detecção Facial](#face-api).

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) e [PersonGroup Person – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup é uma coleção de [Pessoas](#person) e é a unidade de [Identificação](#identification). Um PersonGroup vem com uma [ID de PersonGroup](#persongroup-id), bem como outros atributos, como Nome e Dados de Usuário.

Para obter mais informações, consulte a documentação de referência: [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) e [PersonGroup Person – Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>ID de PersonGroup

A ID PersonGroup ID é uma cadeia de caracteres fornecida pelo usuário com identificadora de um [PersonGroup](#persongroup). A ID de grupo deve ser exclusiva dentro da assinatura.

Para obter mais informações, consulte a documentação de referência: [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [PersonGroup – Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Pose (atributo)

Consulte [Pose da Cabeça](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Reconhecimento

Reconhecimento é uma área de aplicativos populares de tecnologias de face como [Encontrar Semelhante](#find-similar), [Agrupamento](#grouping), [Identificar](#identification),[verificar duas faces iguais ou não ](#verification).

Para obter mais informações, consulte a documentação de referência: [Rosto – Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Rosto – Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) e [Rosto – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Retângulo (Face)

Consulte o [retângulo de face](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Pesquisa de Face Semelhante

Consulte [Encontrar Semelhante](#find-similar).

#### <a name="smile-attribute"></a>Smiley (atributo)

O sorriso é um dos [atributos](#attributes) usados para descrever a expressão de sorriso das faces disponíveis. O atributo de sorriso é opcional nos resultados de detecção e pode ser controlado com uma solicitação de detecção pelo returnFaceAttributes. Se returnFaceAttributes contiver “sorriso”, as faces retornadas terá os atributos de sorriso.

Para obter mais informações, consulte a documentação de referência: [Enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Instantâneo

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de Detecção Facial. Ele funciona como um tipo de área de transferência para copiar dados de uma assinatura para outra. Primeiro, o usuário “faz” um instantâneo dos dados da assinatura de origem e, em seguida, “aplica-o” em um novo objeto de dados na assinatura de destino. 

Para obter mais detalhes, confira [Guia de migração de detecção facial](./face-api-how-to-topics/how-to-migrate-face-data.md) e as documentações de referência [Instantâneo – Fazer](/rest/api/cognitiveservices/face/snapshot/take) e [Instantâneo – Aplicar](/rest/api/cognitiveservices/face/snapshot/apply) (REST).

#### <a name="status-train"></a>Status (treinar)

Status é uma cadeia de caracteres usada para descrever o procedimento para Treinamento LargePersonGroups/LargeFaceList/PersonGroups, incluindo 'notstarted', 'running', 'succeeded', 'failed'.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Chave de assinatura

A chave de assinatura é uma cadeia de caracteres que é necessário especificar como um parâmetro de cadeia de caracteres de consulta para chamar qualquer API de Detecção Facial. A chave de assinatura pode ser encontrada na página Minhas Assinaturas depois de entrar no portal de serviços Cognitivos da Microsoft. Haverá duas chaves associadas com cada assinatura: uma chave primária e uma secundária. Ambas podem ser usadas para chamar a API de forma idêntica. É necessário proteger as chaves de assinatura e você pode regenerar chaves de assinatura a qualquer momento na página Minhas Assinaturas também.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Treinar (LargePersonGroup/LargeFaceList/PersonGroup)

Essa API é usada para pré-processar a [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) para garantir o desempenho de [Localizar Semelhante](#find-similar)/[Identificação](#identification). Se não for operado o treinamento, ou o [Status do Treinamento](#status-train) não for exibido como bem-sucedido, a identificação para este PersonGroup resultará em falha.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) e [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Dados de usuário/UserData

Os dados de usuário são informações adicionais associadas a [Pessoa](#person) e [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Os dados de usuário são definidos pelos usuários para tornar mais fáceis de usar, entender e lembrar dos dados.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person – Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup – Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) e [PersonGroup Person – Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Verificação

Essa API é usada para verificar se as duas faces são iguais ou não. Ambas as faces são representadas como IDs de face na solicitação. Os resultados verificados contêm um campo booliano (isIdentical) indicando se o mesmo é verdadeiro e campo de número ([confiança](#confidence)) que indica o nível de confiança.

Para obter mais informações, consulte a documentação de referência: [Face: verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
