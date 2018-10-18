---
title: Glossário do-serviço de API de detecção facial
titleSuffix: Azure Cognitive Services
description: O glossário explica termos que você pode encontrar conforme trabalha com o Serviço de API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 215b780bc403ab2df40567c3eb5a7ae86c9fe130
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127356"
---
# <a name="glossary"></a>Glossário

## <a name="a"></a>O 

#### <a name="attributes"></a>Atributos

Atributos são opcionais nos resultados de [detecção](#Detection-Face-Detection), como [idade](#Age-Attribute), [gênero](#Gender-Attribute), [pose da cabeça](#Head-Pose-Attribute), [forma facial](#Facial-Hair-Attribute), [sorriso](#Smile-Attribute).
Pode ser obtidos a partir da API de [detecção](#Detection-Face-Detection) especificando os parâmetros de consulta: returnFaceAttributes. Os atributos fornecem informações adicionais sobre [faces](#Face) selecionadas; além de [ID de face](#Face-ID) e [retângulo](#Face-Rectangle).

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Idade (atributo)

A idade é um do [atributos](#Attributes) que descreve a idade de uma face específica. O atributo idade é opcional nos resultados de [detecção](#Detection-Face-Detection) e pode ser controlado com uma solicitação de [detecção](#Detection-Face-Detection) especificando o parâmetro returnFaceAttributes.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>b

## <a name="c"></a>C

#### <a name="candidate"></a>Candidato

Candidatos são essencialmente os resultados de [identificação](#Identification) (por exemplo, pessoas identificadas e nível de confiança nas detecções). Um candidato é representado pelo [PersonID](#Person-ID) e [confiança](#Confidence), indicando que a pessoa que é identificada com um alto nível de confiança.

Para obter mais detalhes, consulte o guia [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Confiança

Confiança é uma medida revelando a similaridade entre as [faces](#Face) ou [Pessoa](#Person) em valores numéricos – que é usado na [identificação](#Identification), e [ verificação](#Verification) para indicar as semelhanças dos resultados pesquisados, identificados e verificados.

Para obter mais detalhes, consulte as seguintes guias: [Face - Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Detecção facial/detecção

A detecção facial é a ação de encontrar as faces em imagens. Os usuários podem carregar uma imagem ou especificar uma URL de imagem na solicitação. As faces detectadas são retornadas com [IDs de Face](#Face-ID)  indicando uma identidade exclusiva na API de Detecção Facial. Os retângulos indicam as localizações de face da imagem em pixels, bem como [atributos](#Attributes) opcionais para cada face como [idade](#Age-Attribute), [gênero](#Gender-Attribute), [pose da cabeça ](#Head-Pose-Attribute), [forma facial](#Facial-Hair-Attribute) e [sorriso](#Smile-Attribute).

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Face

Face é um termo unificado para os resultados derivados da API de Detecção Facial relacionados às faces detectadas. Por fim, a face é representada por uma identidade unificada ([ID de Face](#Face-ID)), uma região específica em imagens ([Retângulo de Face](#Face-Rectangle)) e [atributos](#Face-Attributes-Facial-Attributes) de face extra, como [idade](#Age-Attribute), [gênero](#Gender-Attribute), [pontos de referência](#Face-Landmarks-Facial-Landmarks) e [pose da cabeça](#Head-Pose-Attribute). Além disso, as faces podem ser retornadas da [detecção](#Detection-Face-Detection).

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API de Detecção Facial

A API de Detecção Facial é uma API baseada em nuvem que fornece os algoritmos mais avançados para reconhecimento e detecção de face. A funcionalidade principal da API de Detecção Facial pode ser dividida em duas categorias: detecção [facial](#Detection-Face-Detection) com [atributos](#Face-Attributes-Facial-Attributes)e [reconhecimento facial](#Recognition).

Para obter mais detalhes, consulte as seguintes guias: [Visão Geral da API de Detecção Facial](./Overview.md), [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atributos de rosto/atributos faciais

Consulte [Atributos](#Attributes).

#### <a name="face-id"></a>Face ID

A ID facial é derivada dos resultados de [detecção](#Detection-Face-Detection), no qual uma cadeia de caracteres representa um [face](#Face) na [API de Detecção Facial](#Face-API).

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Pontos de referência faciais marcos/Facial

Os pontos de referência são opcionais nos resultados de [detecção](#Detection-Face-Detection); que são pontos faciais semânticos, como olhos, nariz e boca (ilustrados na figura a seguir). Os pontos de referência podem ser controlados com uma solicitação de [detecção](#Detection-Face-Detection) pelo número booliano de returnFaceLandmarks. Se o returnFaceLandmarks for definido como verdadeiro, as faces retornadas terão os atributos de ponto de referência.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Retângulo facial

O retângulo de face é derivado dos resultados de [detecção](#Detection-Face-Detection), que é um retângulo vertical (esquerda, superior, largura, altura) nas imagens em pixels. O canto superior esquerdo de uma [face](#Face) (esquerda e superior), além da largura e altura, indica os tamanhos nos eixos x e y, respectivamente.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Pelos faciais (atributo)

O pelo facial é um dos [atributos](#Attributes) usados para descrever o comprimento do pelo facial das faces disponíveis. O atributo de pelo facial é opcional nos resultados de [detecção](#Detection-Face-Detection) e pode ser controlado com uma solicitação de [detecção](#Detection-Face-Detection) pelo returnFaceAttributes. Se returnFaceAttributes contiver “facialHair”, as faces retornadas terá os atributos de pelo facial.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

A FaceList é uma coleção de [PersistedFace](#PersistedFace) e é a unidade de [Encontrar Semelhante](#Find-Similar). Uma FaceList vem com um [ID de FaceList](#FaceList-ID), bem como outros atributos, como [Nome](#Name) e [Dados de Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as seguintes guias: [FaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>ID de FaceList

A ID de FaceList é uma cadeia de caracteres fornecida pelo usuário usada como um identificador de uma [FaceList](#FaceList). A ID de FaceList deve ser exclusiva dentro da assinatura.

Para obter mais detalhes, consulte as seguintes guias: [FaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Localizar semelhante

Essa API é usada para pesquisar/consultar faces semelhantes com base em uma coleção de faces. As faces de consulta e coleções de face são representadas como [IDs de face](#Face-ID) ou [ID FceLis](#FaceList-ID)/[ID LargeFaceList](#LargeFaceList-ID) na solicitação. Os resultados retornados são faces semelhantes pesquisadas, representadas por [IDs de face](#Face-ID) ou [IDs de PersistedFace](#PersistedFace-ID).

Para obter mais detalhes, consulte as seguintes guias: [Face - Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Gênero (atributo)

O gênero é um dos [atributos](#Attributes) usados para descrever os gêneros das faces disponíveis. O atributo de gênero é opcional nos resultados de [detecção](#Detection-Face-Detection) e pode ser controlado com uma solicitação de [detecção](#Detection-Face-Detection) pelo returnFaceAttributes. Se returnFaceAttributes contiver “gender”, as faces retornadas terá os atributos de gênero.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Agrupamento

O agrupamento de face é o agrupamento de uma coleção de faces de acordo com as semelhanças faciais. As coleções de face são indicadas pelas coleções de ID de face na solicitação. Como resultado de agrupamento, faces semelhantes são agrupadas juntas como [grupos](#Groups), e as faces que não são semelhantes a qualquer outra face são mescladas em conjunto como um grupo confuso. No máximo, há um [grupo confuso](#Messy-Group) no resultado do agrupamento.

Para obter mais detalhes, consulte o guia [Face - Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupos

Os grupos são provenientes de resultados de [agrupamento](#Grouping). Cada grupo contém uma coleção de faces semelhantes, em que as faces são indicadas por [IDs de face](#Face-ID).

Para obter mais detalhes, consulte o guia [Face - Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Head representar (atributo)

A pose da cabeça é um dos [atributos](#Attributes) que representa a orientação de face no espaço em 3D de acordo com os ângulos de sobreposição, de passo e de guinada, conforme exibido na figura a seguir. Os intervalos de valores de passo e guinada são [-180, 180] e [-90, 90] em graus. Na versão atual, o valor de passo retornado de detecção é sempre 0. O atributo de pose da cabeça é opcional nos resultados de [detecção](#Detection-Face-Detection) e pode ser controlado com uma solicitação de [detecção](#Detection-Face-Detection) pelo parâmetro returnFaceAttributes. Se o parâmetro returnFaceAttributes contiver “headPose”, as faces retornadas terão os atributos de pose de cabeça.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identificação

A identificação é para identificar uma ou mais faces de um LargePersonGroup/PersonGroup.
Um [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) é uma coleção de [Pessoas](#Person).
Faces e o LargePersonGroup/PersonGroup são representados por [IDs de face](#Face-ID) e [IDs LargePersonGroup](#LargePersonGroup-ID)/[IDs PersonGroup](#PersonGroup-ID) respectivamente na solicitação.
Os resultados identificados são [candidatos](#Candidate) representados por [Pessoas](#Person) com confiança.
As várias faces na entrada são consideradas separadamente, e cada face terá seu próprio resultado identificado.

> [!NOTE]
> O LargePersonGroup/PersonGroup deve ser treinado com êxito antes da identificação. Se o LargePersonGroup/PersonGroup não for treinado, ou de [status](#Status-Train) de treinamento não for exibido como "com êxito" (ou seja, “em execução”, “com falha” ou “tempo limite”), a resposta da solicitação é 400.
> 

Para obter mais detalhes, consulte as seguintes guias: [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical é um campo booliano de resultados de [verificação](#Verification) resultados que indica se duas faces pertencem à mesma pessoa.

Para obter mais detalhes, consulte a guia [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Pontos de referência

Consulte [pontos de referência de face](#Face-Landmarks-Facial-Landmarks).

#### <a name="largefacelist"></a>LargeFaceList

A LargeFaceList é uma coleção de [PersistedFace](#PersistedFace) e é a unidade de [Encontrar Semelhante](#Find-Similar). Uma LargeFaceList vem com um [ID de LargeFaceList](#LargeFaceList-ID), bem como outros atributos, como [Nome](#Name) e [Dados de Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as guias a seguir: [LargeFaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - Listar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

A ID de FaceList é uma cadeia de caracteres fornecida pelo usuário usada como um identificador de uma [LargeFaceList](#LargeFaceList). A ID de LargeFaceList deve ser exclusiva dentro da assinatura.

Para obter mais detalhes, consulte as guias a seguir: [LargeFaceList - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup é uma coleção de [Pessoas](#Person) e é a unidade de [Identificação](#Identification). Um LargePersonGroup vem com uma [ID de LargePersonGroup](#LargePersonGroup-ID), bem como outros atributos, como [Nome](#Name) e [Dados de Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [Pessoa LargePersonGroup - Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup ID

A ID LargePersonGroup ID é uma cadeia de caracteres fornecida pelo usuário com identificadora de um [LargePersonGroup](#LargePersonGroup). A ID de LargePersonGroup deve ser exclusiva dentro da assinatura.

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Messy group

O grupo confuso é derivado dos resultados de [agrupamento](#Grouping) que contém as faces não semelhantes a qualquer outra face. Cada face em um grupo confuso é indicada pela [ID de face](#Face-ID).

Para obter mais detalhes, consulte o guia [Face - Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nome (pessoa)

Nome é uma cadeia de caracteres descritiva amigável para [Pessoa](#Person). Ao contrário da [ID de pessoa](#Person-ID), o nome de pessoas pode ser duplicado em um grupo.

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Nome (LargePersonGroup/PersonGroup)

Nome também é uma cadeia de caracteres descritiva amigável para [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Ao contrário da [ID LargePersonGroup](#LargePersonGroup-ID)/[ID PersonGroup](#PersonGroup-ID), o nome do LargePersonGroups/PersonGroups pode ser duplicado em uma assinatura.

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace é uma estrutura de dados na API detecção Facial. PersistedFace vem com uma [ID PersistedFace](#PersistedFace-ID), bem como outros atributos como [Nome](#Name), e [Dados do Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as seguintes guias: [LargeFaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [Pessoa LargePersonGroup - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Pessoa PersonGroup - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>ID da pessoa

A ID da pessoa que é gerada quando uma [PersistedFace](#PersistedFace) é criada com êxito. Uma cadeia de caracteres é criada para representar esta Face na [API de Detecção Facial](#Face-API).

Para obter mais detalhes, consulte as seguintes guias: [LargeFaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [Pessoa LargePersonGroup - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ Pessoa PersonGroup - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Pessoa

Pessoa é uma estrutura de dados gerenciada na API de Detecção Facial. Pessoa vem com uma [ID de Pessoa](#Person-ID), bem como outros atributos como [Nome](#Name), uma coleção de [PersistedFace](#PersistedFace), e [Dados do Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID da pessoa

A ID da Pessoa é gerada quando uma [Pessoa](#Person) é criada com êxito. Uma cadeia de caracteres é criada para representar esta pessoa na [API de Detecção Facial](#Face-API).

Para obter mais detalhes, consulte as seguintes guias: [LargePersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup é uma coleção de [Pessoas](#Person) e é a unidade de [Identificação](#Identification). Um PersonGroup vem com uma [ID de PersonGroup](#PersonGroup-ID), bem como outros atributos, como [Nome](#Name) e [Dados de Usuário](#UserData-User-Data).

Para obter mais detalhes, consulte as seguintes guias: [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [Pessoa PersonGroup - Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>ID de PersonGroup

A ID PersonGroup ID é uma cadeia de caracteres fornecida pelo usuário com identificadora de um [PersonGroup](#PersonGroup). A ID de grupo deve ser exclusiva dentro da assinatura.

Para obter mais detalhes, consulte as seguintes guias: [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Pose (atributo)

Consulte [Pose da Cabeça](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="recognition"></a>Reconhecimento

Reconhecimento é uma área de aplicativos populares de tecnologias de face como [Encontrar Semelhante](#Find-Similar), [Agrupamento](#Grouping), [Identificar](#Identification),[verificar duas faces iguais ou não ](#Verification).

Para obter mais detalhes, consulte as seguintes guias: [Face -Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Retângulo (Face)

Consulte o [retângulo de face](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="smile-attribute"></a>Smiley (atributo)

O sorriso é um dos [atributos](#Attributes) usados para descrever a expressão de sorriso das faces disponíveis. O atributo de sorriso é opcional nos resultados de [detecção](#Detection-Face-Detection) e pode ser controlado com uma solicitação de [detecção](#Detection-Face-Detection) pelo returnFaceAttributes. Se returnFaceAttributes contiver “sorriso”, as faces retornadas terá os atributos de sorriso.

Para obter mais detalhes, consulte o guia [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Pesquisa de Face Semelhante

Consulte [Encontrar Semelhante](#Find-Similar).

#### <a name="status-train"></a>Status (treinar)

Status é uma cadeia de caracteres usada para descrever o procedimento para [Treinamento LargePersonGroups/LargeFaceList/PersonGroups](#Train), incluindo 'notstarted', 'running', 'succeeded', 'failed'.

Para obter mais detalhes, consulte a guia [LargeFaceList - Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Chave de assinatura

A chave de assinatura é uma cadeia de caracteres que é necessário especificar como um parâmetro de cadeia de caracteres de consulta para chamar qualquer API de Detecção Facial. A chave de assinatura pode ser encontrada na página Minhas Assinaturas depois de entrar no portal de serviços Cognitivos da Microsoft. Haverá duas chaves associadas com cada assinatura: uma chave primária e uma secundária. Ambas podem ser usadas para chamar a API de forma idêntica. É necessário proteger as chaves de assinatura e você pode regenerar chaves de assinatura a qualquer momento na página Minhas Assinaturas também.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Treinar (LargePersonGroup/LargeFaceList/PersonGroup)

Essa API é usada para pré-processar a [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) para garantir o desempenho de [Localizar Semelhante](#Find-Similar)/[Identificação](#Identification). Se não for operado o treinamento, ou o [Status do Treinamento](#Status-Train) não for exibido como bem-sucedido, a identificação para este PersonGroup resultará em falha.

Para obter mais detalhes, consulte a guia [LargeFaceList - Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Dados de usuário/UserData

Os dados de usuário são informações adicionais associadas a [Pessoa](#Person) e [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Os dados de usuário são definidos pelos usuários para tornar mais fáceis de usar, entender e lembrar dos dados.

Para obter mais detalhes, consulte as guias a seguir: [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Verificação

Essa API é usada para verificar se as duas faces são iguais ou não. Ambas as faces são representadas como IDs de face na solicitação. Os resultados verificados contêm um campo booliano ([isIdentical](#Is-Identical)) indicando se o mesmo é verdadeiro e campo de número ([confiança](#Confidence)) que indica o nível de confiança.

Para obter mais detalhes, consulte a guia [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>S

## <a name="z"></a>Z
