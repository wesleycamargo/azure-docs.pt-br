---
title: O que é o Serviço da API de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: O glossário explica termos que você pode encontrar conforme trabalha com o Serviço de API de Detecção Facial.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123463"
---
# <a name="what-is-the-face-api-service"></a>O que é o Serviço da API de Detecção Facial?

Bem-vindo ao Serviço da API de Detecção Facial, um serviço baseado em nuvem que fornece os mais avançados algoritmos de detecção facial. A API de Detecção Facial possui duas funções principais: detecção facial com atributos e reconhecimento facial.

## <a name="face-detection"></a>Detecção Facial

A API de Detecção Facial detecta até 64 faces humanas com localização de faces de alta precisão em uma imagem. E a imagem pode ser especificada por arquivo em bytes ou um URL válido.

![Visão Geral - Detecção Facial](./Images/Face.detection.jpg)

Retângulo em torno da face (esquerda, no topo, largura e altura) indicando que a localização facial na imagem é retornada com cada face detectada. Opcionalmente, a detecção facial extrai uma série de atributos relacionados à face, como pose, gênero, idade, pose da cabeça, pelos faciais e óculos. Para obter mais informações, consulte [Face - Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Reconhecimento Facial

O reconhecimento facial é amplamente usado em vários cenários, incluindo segurança, interface do usuário natural, análise e gerenciamento de conteúdo de imagens, aplicativos móveis e robótica. Quatro funções de reconhecimento facial são fornecidos: verificação de face, localização de faces semelhantes, agrupamento de faces e identificação pessoal.

### <a name="face-verification"></a>Verificação Facial

A verificação da API de Detecção Facial executa uma autenticação com relação a duas faces detectadas ou de uma face detectada com relação a uma pessoa objeto. Para informações mais detalhadas, consulte [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Encontrar Face Semelhante

Com uma determinada face de destino detectada e um conjunto de faces candidatas com as quais pesquisar, o serviço localiza um pequeno grupo de faces que parecem mais semelhantes à face de destino. Há suporte para dois modos em funcionamento, `matchFace` e `matchPerson`. O modo `matchPerson` retorna faces semelhantes após aplicar um limite das mesmas pessoas derivado de [Face - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo `matchFace` ignora o limite das mesmas pessoas e retorna as faces candidatas mais semelhantes. Pegue o exemplo a seguir: faces candidatas são listadas.
![Visão Geral - Localização de Face Semelhante](./Images/FaceFindSimilar.Candidates.jpg) e a face de consulta é ![Visão geral - Localização de Face Semelhante](./Images/FaceFindSimilar.QueryFace.jpg)

Para localizar quatro faces semelhantes, o modo `matchPerson` retorna (a) e (b), que pertencem à mesma pessoa com face de consulta. O modo `matchFace` retorna (a), (b), (c) e (d), exatamente quatro candidatos, mesmo com baixa similaridade. Para obter mais informações, consulte [Face - Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Agrupamento Facial

Com um conjunto de faces desconhecidas, a API de agrupamento de faces as divide automaticamente em vários grupos com base em similaridade. Cada grupo é um subconjunto adequando desconectado do grupo de faces desconhecidas original e contém faces semelhantes. E pode-se considerar que todas as faces no mesmo grupo pertencem à mesma pessoa objeto. Para obter mais informações, consulte [Face - Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identificação Facial

A API de Detecção Facial pode identificar pessoas com base em uma face detectada e um banco de dados de pessoas (definido como um GrupoGrandeDePessoas/GrupoDePessoas). Crie esse banco de dados com antecedência, que pode ser editado ao longo do tempo.

A figura a seguir é um exemplo de um GrupoGrandeDePessoas/GrupoDePessoas chamado "meusamigos". Cada grupo pode conter até 1.000.000/10.000 pessoas objeto. Enquanto isso, cada pessoa objeto pode ter até 248 faces registradas.

![Visão Geral - GrupoGrandeDePessoas/GrupoDePessoas](./Images/person.group.clare.jpg)

Após um GrupoGrandeDePessoas/GrupoDePessoas ter sido criado e treinado, a identificação pode ser realizada com relação ao grupo e a uma nova face detectada. Se a face for identificada como uma pessoa objeto no grupo, ela é retornada.

Para mais informações sobre identificação de pessoas, consulte os guias a seguir da API:

[Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[GrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[Pessoa de GrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[GrupoDePessoas - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[GrandeGrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[Pessoa de GrandeGrupoDePessoas - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[GrandeGrupoDePessoas - Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Armazenamento Facial

O Armazenamento Facial permite que uma assinatura Standard armazene faces persistentes ao usar Pessoas Objeto de GrupoGrandeDePessoas/GrupoDePessoas([Pessoa de GrupoDePessoas - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Pessoa de GrupoGrandeDePessoas - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) ou GrandesListasDeFaces/ListasDeFaces ([ListasDeFaces - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) para identificação ou similaridade que corresponde à API de Detecção Facial. As imagens armazenadas são cobradas a uma taxa de $0,5 por 1000 rostos. Essa taxa é dividida proporcionalmente por dia. Assinaturas de camada gratuita são grátis, mas limitadas a 1.000 pessoas no total.

O preço para Armazenamento Facial é dividido proporcionalmente por dia. Por exemplo, se sua conta usou 10.000 faces persistentes todos os dias na primeira metade do mês e nenhuma para a segunda metade, você será cobrado somente pelas 10.000 faces dos dias armazenados. Como alternativa, se a cada dia durante o mês você persistir 1.000 faces por algumas horas, excluindo-as a cada noite, você ainda será cobrado por 1.000 faces persistentes por dia.

## <a name="getting-started-tutorials"></a>Tutoriais de Introdução

Os tutoriais a seguir demonstram as funcionalidades e processos de assinatura básicos da API de Detecção Facial:

- [Tutorial Guia de Introdução com a API de Detecção Facial no CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial Guia de Introdução com a API de Detecção Facial no Java para Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Tutorial Guia de Introdução com a API de Detecção Facial no tutorial de Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Aplicativos de Exemplo

Dê uma olhada nesses aplicativos de exemplo que fazem uso da API de Detecção Facial.

- [API de Detecção Facial da Microsoft: Windows Client Library & Amostra](https://github.com/Microsoft/Cognitive-Face-Windows)
  - O aplicativo de exemplo WPF que demonstra vários cenários de detecção, análise e identificação facial.
- [Aplicativo FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes)
  - O aplicativo de exemplo Universal Windows Platform (UWP) que mostra o uso de fala, Cortana, tinta e câmera por meio de um cenário de compartilhamento de observações da família.
- [Exemplo de Análise de Quadros de Vídeo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - O aplicativo de exemplo Universal Windows Platform (UWP) que mostra transmissões de vídeo ao vivo quase em tempo real usando as APIs de Detecção Facial, Visual Computacional e Detecção de Emoções.

## <a name="related-topics"></a>Tópicos Relacionados

- [Notas de Versão da API de Detecção Facial Versão 1.0](ReleaseNotes.md)
- [Como detectar faces em imagem](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Como identificar faces em imagem](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
