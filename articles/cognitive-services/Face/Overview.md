---
title: O que é o Serviço da API de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: Este tópico explica o serviço de API de Detecção Facial e os termos relacionados.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310346"
---
# <a name="what-is-the-face-api-service"></a>O que é o Serviço da API de Detecção Facial?

O serviço de API de Detecção Facial é um serviço baseado em nuvem que fornece algoritmos para analisar rostos humanos em imagens e vídeo. A API de Detecção Facial possui duas funções principais: detecção facial com atributos e reconhecimento facial.

## <a name="face-detection"></a>Detecção facial

A API de Detecção Facial pode detectar até 64 faces humanas com localização de faces de alta precisão em uma imagem. A imagem pode ser especificada por arquivo (um fluxo de bytes) ou com uma URL válida.

![Visão Geral - Detecção Facial](./Images/Face.detection.jpg)

O retângulo em torno da face (esquerda, no topo, largura e altura) indicando que a localização facial na imagem é retornada com cada face detectada. Opcionalmente, a detecção facial extrai uma série de atributos relacionados à face, como pose, gênero, idade, pose da cabeça, pelos faciais e óculos. Para obter mais informações, consulte [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Reconhecimento facial

A capacidade de identificar rostos humanos é importante em vários cenários, incluindo segurança, interface do usuário natural, análise e gerenciamento de conteúdo de imagens, aplicativos móveis e robótica. O serviço de API de Detecção Facial oferece quatro funções de reconhecimento facial: verificação de face, localização de faces semelhantes, agrupamento de faces e identificação pessoal.

### <a name="face-verification"></a>Verificação facial

A verificação facial executa uma autenticação com relação a duas faces detectadas ou de uma face detectada com relação a uma pessoa objeto. Para informações mais detalhadas, consulte [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Encontrar faces semelhantes

Com uma determinada face de destino detectada e um conjunto de faces candidatas com as quais pesquisar, o serviço localiza um pequeno grupo de faces que parecem mais semelhantes à face de destino. Dois modos de trabalhar, **matchFace** e **matchPerson** têm suporte. O modo **matchPerson** retorna faces semelhantes após aplicar um limite das mesmas pessoas derivado de [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo **matchFace** ignora o limite das mesmas pessoas e retorna as faces candidatas mais semelhantes. No exemplo a seguir as faces candidatas são listadas.
![Visão geral - Encontrar faces semelhantes](./Images/FaceFindSimilar.Candidates.jpg) A face de consulta é esta.
![Visão geral - Encontrar faces semelhantes](./Images/FaceFindSimilar.QueryFace.jpg)

Para encontrar quatro faces semelhantes, o modo **matchPerson** retornaria (a) e (b), que representam a mesma pessoa que a face da consulta. O modo **matchFace** retorna (a), (b), (c) e (d), exatamente quatro candidatos, mesmo com baixa similaridade. Para obter mais informações, consulte [Face - Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Agrupamento facial

Com um conjunto de faces desconhecidas, a API de agrupamento de faces as divide automaticamente em vários grupos com base em similaridade. Cada grupo é um subconjunto adequado desconectado do grupo de faces desconhecidas original e contém faces semelhantes. Pode-se considerar que todas as faces no mesmo grupo pertencem à mesma pessoa. Para obter mais informações, consulte [Face - Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Identificação pessoal

A API de Detecção Facial pode identificar pessoas com base em uma face detectada e um banco de dados de pessoas. Você cria esse banco de dados com antecedência, e ele pode ser editado ao longo do tempo.

A figura a seguir é um exemplo de um banco de dados chamado “meusamigos”. Cada grupo pode conter até 1.000.000/10.000 pessoas objeto diferentes. Cada pessoa objeto pode ter até 248 faces registradas.

![Visão Geral - GrupoGrandeDePessoas/GrupoDePessoas](./Images/person.group.clare.jpg)

Após um banco de dados ter sido criado e treinado, a identificação pode ser realizada com relação ao grupo e a uma nova face detectada. Se a face for identificada como uma pessoa no grupo, ela é retornada.

Para mais informações sobre identificação de pessoas, consulte os guias a seguir da API:

[Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[GrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[Pessoa GrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[GrupoDePessoas - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[GrandeGrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[Pessoa GrandeGrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[GrandeGrupoDePessoas - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Armazenamento facial e preços

O Armazenamento Facial permite que uma assinatura Standard armazene faces persistentes ao usar Pessoas Objeto de GrupoGrandeDePessoas/GrupoDePessoas([Pessoa de GrupoDePessoas - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Pessoa de GrupoGrandeDePessoas - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) ou GrandesListasDeFaces/ListasDeFaces ([ListasDeFaces - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) para identificação ou similaridade que corresponde à API de Detecção Facial. As imagens armazenadas são cobradas a US$ 0,50 por mil faces, e essa taxa é dividida proporcionalmente por dia. Assinaturas de camada gratuita são limitadas a 1.000 pessoas no total.

Por exemplo, se sua conta usou 10.000 faces persistentes todos os dias na primeira metade do mês e nenhuma para a segunda metade, você será cobrado somente pelas 10.000 faces dos dias armazenados. Como alternativa, se a cada dia durante o mês você persistir 1.000 faces por algumas horas, excluindo-as a cada noite, você ainda será cobrado por 1.000 faces persistentes por dia.

## <a name="sample-apps"></a>Aplicativos de exemplo

Dê uma olhada nesses aplicativos de exemplo que fazem uso da API de Detecção Facial.

- [API de Detecção Facial da Microsoft: Windows Client Library & Amostra](https://github.com/Microsoft/Cognitive-Face-Windows)
  - O aplicativo de exemplo WPF que demonstra vários cenários de detecção, análise e identificação facial.
- [Aplicativo FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes)
  - O aplicativo de exemplo Universal Windows Platform (UWP) que mostra o uso de fala, Cortana, tinta e câmera por meio de um cenário de compartilhamento de observações da família.
- [Exemplo de Análise de Quadros de Vídeo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Aplicativo de exemplo Win32 que mostra transmissões de vídeo ao vivo quase em tempo real usando as APIs de Detecção Facial, Pesquisa Visual Computacional e Detecção de Emoções.

## <a name="tutorials"></a>Tutoriais
Os tutoriais a seguir demonstram as funcionalidades e processos de assinatura básicos da API de Detecção Facial:
- [Tutorial de Introdução à API de Detecção Facial em CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial Guia de Introdução com a API de Detecção Facial no Java para Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Tutorial de introdução à API de Detecção Facial em Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>Próximas etapas

Experimente um início rápido para implementar um cenário simples de API de Detecção Facial.
- [Início rápido: detectar faces em uma imagem usando C#](quickstarts/csharp.md) (outras linguagens disponíveis)
