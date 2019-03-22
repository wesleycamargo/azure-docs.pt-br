---
title: O que é a API da Pesquisa Visual Computacional?
titlesuffix: Azure Cognitive Services
description: A API da Pesquisa Visual Computacional fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 812f957ffe2a3f24ead4ef0fe66a25c3cfd17995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866905"
---
# <a name="what-is-computer-vision-api-version-10"></a>O que é a API da Pesquisa Visual Computacional versão 1.0?

> [!IMPORTANT]
> Uma nova versão da API da Pesquisa Visual Computacional agora está disponível, confira:
>- [Visão geral](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [API da Pesquisa Visual Computacional versão 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

A API da Pesquisa Visual Computacional baseada em nuvem fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações. Ao fazer upload de uma imagem ou especificar uma URL de imagem, algoritmos da Pesquisa Visual Computacional da Microsoft podem analisar o conteúdo visual de maneiras diferentes com base em entradas e nas opções do usuário. Com a API da Pesquisa Visual Computacional, os usuários podem analisar imagens para:
* Marcar imagens com base no conteúdo.
* Categorizar imagens.
* Identificar o tipo e a qualidade das imagens.
* [Detectar faces humanas e retornar suas coordenadas.](#Faces)
* Reconhecer um conteúdo específico a um domínio.
* Gerar descrições do conteúdo.
* Usar o reconhecimento óptico de caracteres para identificar o texto impresso encontrado nas imagens.
* Reconhecer texto manuscrito.
* Distinguir esquemas de cores.
* Sinalizar conteúdo para adulto.
* Cortar as fotos a serem usadas como miniaturas.

## <a name="requirements"></a>Requisitos
* Suporte para métodos de entrada: binário da imagem bruta na forma de um aplicativo/fluxo de octeto ou uma URL da imagem.
* Formatos de imagem com suporte: JPEG, PNG, GIF, BMP.
* Tamanho do arquivo de imagem: Menos de 4 MB.
* Dimensões da imagem: Maior que 50 x 50 pixels.

## <a name="tagging-images"></a>Marcando imagens
A API da Pesquisa Visual Computacional retorna marcas com base em milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as marcações são ambíguas ou pertencem a um conhecimento não comum, a resposta da API fornece 'dicas' para esclarecer o significado da marcação no contexto de um cenário conhecido. As marcas não são organizadas como uma taxonomia e não existe nenhuma hierarquia de herança. Uma coleção de marcas de conteúdo constitui a base para uma 'description' de imagem exibida como uma linguagem legível por humanos formatada em frases completas. Observe que, no momento, para a descrição da imagem, o inglês é o único idioma compatível.

Após o upload de uma imagem ou a especificação de uma URL de imagem, os algoritmos da API da Pesquisa Visual Computacional geram marcas com base nos objetos, em seres vivos e nas ações identificadas na imagem. A marcação não está limitada ao assunto principal, como uma pessoa em primeiro plano, mas também inclui o cenário (interno ou externo), móveis, ferramentas, plantas, animais, acessórios, gadgets, etc.

### <a name="example"></a>Exemplo
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Categorizando imagens
Além da marcação e das descrições, a API da Pesquisa Visual Computacional retorna as categorias baseadas em taxonomia definidas nas versões anteriores. Essas categorias são organizadas como uma taxonomia com hierarquias hereditárias de pai/filho. Todas as categorias estão disponíveis em inglês. Elas podem ser usadas isoladamente ou com nossos novos modelos.

### <a name="the-86-category-concept"></a>O conceito de 86 categorias
Com base em uma lista dos 86 conceitos vistos no diagrama a seguir, os recursos visuais encontrados em uma imagem podem ser categorizados de amplo a específico. Para obter a taxonomia completa em formato de texto, confira [Taxonomia de categoria](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analisar as categorias](./Images/analyze_categories.png)

Imagem                                                  | Response
------------------------------------------------------ | ----------------
![Mulher no teto](./Images/woman_roof.png)                 | people
![Foto de família](./Images/family_photo.png)             | people_crowd
![Cachorro bonito](./Images/cute_dog.png)                     | animal_dog
![Montanha ao ar livre](./Images/mountain_vista.png)       | outdoor_mountain
![Alimentos pão com análise da pesquisa visual](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identificando tipos de imagem
Há várias maneiras para categorizar imagens. A API da Pesquisa Visual Computacional pode definir um sinalizador booliano para indicar se uma imagem é preto e branco ou colorida. Ela também pode definir um sinalizador para indicar se uma imagem é um desenho de linha ou não. Além disso, ela pode indicar se uma imagem é clip-art ou não e indicar sua qualidade, como em uma escala de 0 a 3.

### <a name="clip-art-type"></a>Tipo de clip-art
Detecta se uma imagem é clip-art ou não.  

Valor | Significado
----- | --------------
0     | Non-clip-art
1     | ambiguous
2     | normal-clip-art
3     | good-clip-art

Imagem|Response
----|----
![Clip-art de queijo com análise da pesquisa visual](./Images/cheese_clipart.png)|3 good-clip-art
![Quintal da casa com análise da pesquisa visual](./Images/house_yard.png)|0 Non-clip-art

### <a name="line-drawing-type"></a>Tipo de desenho de linha
Detecta se uma imagem é um desenho de linha ou não.

Imagem|Response
----|----
![Desenho de leão com análise da pesquisa visual](./Images/lion_drawing.png)|True
![Flor com análise da pesquisa visual](./Images/flower.png)|Falso

### <a name="faces"></a>Faces
Detecta as faces humanas em uma imagem e gera as coordenadas da face, o retângulo da face, o gênero e a idade. Esses recursos visuais são um subconjunto de metadados gerados para a face. Para obter metadados mais abrangentes gerados para faces (identificação facial, detecção de pose e muito mais), use a API de Detecção Facial.  

Imagem|Response
----|----
![Face de mulher no teto com análise da pesquisa visual](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Faces de mãe e filha com análise da pesquisa visual](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Face de foto de família com análise da pesquisa visual](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Conteúdo específico a um domínio

Além da marcação e da categorização de nível superior, a API da Pesquisa Visual Computacional também dá suporte a informações especializadas (ou específicas a um domínio). Informações especializadas podem ser implementadas como um método autônomo ou com a categorização de nível superior. Elas funcionam como um meio de refinar ainda mais a taxonomia de 86 categorias por meio da adição de modelos específicos a um domínio.

Atualmente, as únicas informações especializadas compatíveis são o reconhecimento de celebridades e de pontos de referência. Elas são refinamentos específicos a um domínio de pessoas e categorias do grupo de pessoas, bem como de pontos de referência no mundo todo.

Há duas opções para usar os modelos específicos a um domínio:

### <a name="option-one---scoped-analysis"></a>Opção 1 – análise com escopo
Analise somente um modelo escolhido, invocando uma chamada HTTP POST. Para essa opção, se você souber qual modelo deseja usar, especifique o nome do modelo e obtenha somente informações relevantes a esse modelo. Por exemplo, você pode usar essa opção para procurar somente o reconhecimento de celebridades. A resposta contém uma lista de possíveis correspondências de celebridades, acompanhadas por suas pontuações de confiança.

### <a name="option-two---enhanced-analysis"></a>Opção 2 – análise avançada
Análise para fornecer detalhes adicionais relacionados às categorias da taxonomia de 86 categorias. Essa opção está disponível para uso em aplicativos em que os usuários desejam obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos a um domínio. Quando esse método é invocado, o classificador da taxonomia de 86 categorias é chamado primeiro. Se uma das categorias corresponder àquelas dos modelos conhecidos/correspondentes, uma segunda passagem de invocações do classificador ocorrerá. Por exemplo, se 'details=all' ou "details" incluir 'celebrities' o método chamará o classificador de celebridades após a chamada ao classificador de 86 categorias. O resultado inclui marcas que começam com 'people_'.

## <a name="generating-descriptions"></a>Gerando descrições 
Os algoritmos da API da Pesquisa Visual Computacional analisam o conteúdo de uma imagem. Essa análise constitui a base para a 'description' exibida como um idioma legível por humanos em sentenças completas. A descrição resume o que é encontrado na imagem. Os algoritmos da API da Pesquisa Visual Computacional geram várias descrições baseadas nos objetos identificados na imagem. Cada uma das descrições é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa. Um exemplo de um bot que usa essa tecnologia para gerar as legendas de imagem pode ser encontrado [aqui](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Exemplo de geração de descrição
![Edifícios P&B](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Percebendo esquemas de cores
O algoritmo de Pesquisa Visual Computacional extrai cores de uma imagem. As cores são analisadas em três contextos diferentes: primeiro plano, tela de fundo e completo. Elas são agrupadas em doze (12) cores de destaque dominantes. Essas cores de destaque são preto, azul, marrom, cinza, verde, laranja, rosa, roxo, vermelho, azul-petróleo, branco e amarelo. Dependendo das cores de uma imagem, as cores preto e branco simples ou cores de destaque podem ser retornadas em códigos de cores hexadecimais.

Imagem                                                       | Primeiro plano |Segundo plano| Cores
----------------------------------------------------------- | --------- | ------- | ------
![Montanha ao ar livre](./Images/mountain_vista.png)            | Preto     | Preto   | Branco
![Flor com análise da pesquisa visual](./Images/flower.png)               | Preto     | Branco   | Branco, preto, verde
![Estação ferroviária com análise da pesquisa visual](./Images/train_station.png) | Preto     | Preto   | Preto

### <a name="accent-color"></a>Cor de destaque
Cor extraída de uma imagem designada para representar a cor de maior destaque para os usuários por meio de uma combinação de cores dominantes e saturação.

Imagem                                                       | Response
----------------------------------------------------------- | ----
![Montanha ao ar livre](./Images/mountain_vista.png)            | #BC6F0F
![Flor com análise da pesquisa visual](./Images/flower.png)               | #CAA501
![Estação ferroviária com análise da pesquisa visual](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Preto e branco
Um sinalizador booliano que indica se uma imagem é preto e branco ou não.

Imagem                                                      | Response
---------------------------------------------------------- | ----
![Edifício com análise da pesquisa visual](./Images/bw_buildings.png)      | True
![Quintal da casa com análise da pesquisa visual](./Images/house_yard.png)      | Falso

## <a name="flagging-adult-content"></a>Sinalizando conteúdo para adulto
Entre as várias categorias visuais está o grupo de conteúdo para adulto, que permite a detecção de materiais para adulto e restringe a exibição de imagens que contêm conteúdo sexual. O filtro para detecção de conteúdo para adulto pode ser definido em uma escala deslizante para acomodar a preferência do usuário.

## <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)
A tecnologia OCR detecta o conteúdo de texto em uma imagem e extrai o texto identificado em um fluxo de caracteres legível por computador. Você pode usar o resultado para pesquisa e várias outras finalidades, como registros médicos, segurança e serviços bancários. Ele detecta o idioma automaticamente. O OCR economiza tempo e fornece conveniência aos usuários, permitindo que eles tirem fotos do texto em vez de transcrevê-lo.

O OCR dá suporte a 25 idiomas. Esses idiomas são: árabe, chinês simplificado, chinês tradicional, tcheco, dinamarquês, holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polonês, português, romeno, russo, sérvio (cirílico e latino) eslovaco, espanhol, sueco e turco.

Se necessário, o OCR corrige a rotação do texto reconhecido, em graus, ao redor do eixo horizontal da imagem. O OCR fornece as coordenadas de quadro de cada palavra, conforme visto na ilustração abaixo.

![Visão geral do OCR](./Images/vision-overview-ocr.png) Requisitos do OCR:
- O tamanho da imagem de entrada precisa estar entre 40 x 40 e 3200 x 3200 pixels.
- A imagem não pode ser superior a 10 megapixels.

A imagem de entrada pode ser girada por qualquer múltiplo de 90 graus, além de um ângulo pequeno de até 40 graus.

A precisão do reconhecimento de texto depende da qualidade da imagem. Uma leitura imprecisa pode ser causada pelas seguintes situações:
- Imagens desfocadas.
- Texto manuscrito ou cursivo.
- Estilos de fonte artísticos.
- Tamanho de texto pequeno.
- Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
- Letras maiúsculas muito grandes ou ausentes no início das palavras
- Texto subscrito, sobrescrito ou tachado.

 Limitações: Em fotos em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em algumas fotos, especialmente, em fotos sem nenhum texto, a precisão pode variar muito dependendo do tipo de imagem.

## <a name="recognize-handwritten-text"></a>Reconhecer texto manuscrito
Essa tecnologia permite que você detecte e extraia um texto manuscrito de anotações, cartas, dissertações, quadros brancos, formulários, etc. Ela funciona em diferentes superfícies e telas de fundo, como papel em branco, notas autoadesivas amarelas e quadros brancos.

O reconhecimento de texto manuscrito economiza tempo e esforço e pode torná-lo mais produtivo, permitindo que você extraia imagens de textos, em vez de precisar transcrevê-los. Ele possibilita a digitalização de anotações. Essa digitalização permite que você implemente uma pesquisa rápida e fácil. Também reduz a desorganização de papéis.

Requisitos de entrada:
- Formatos de imagem com suporte: JPEG, PNG e BMP.
- O tamanho do arquivo de imagem precisa ter menos de 4 MB.
- As dimensões da imagem precisam ser, no mínimo, 40 x 40 e, no máximo, 3200 x 3200.

Observação: atualmente, essa tecnologia está em versão prévia e disponível apenas para textos em inglês.

## <a name="generating-thumbnails"></a>Gerando miniaturas
Uma miniatura é uma representação pequena de uma imagem em tamanho original. Dispositivos variados, como telefones, tablets e computadores, criam uma necessidade de diferentes layouts de experiência do usuário e tamanhos de miniatura. Usando o corte inteligente, esse recurso da API da Pesquisa Visual Computacional ajuda a resolver o problema.

Após o upload de uma imagem, uma miniatura de alta qualidade é gerada e o algoritmo da API da Pesquisa Visual Computacional analisa os objetos na imagem. Ele então corta a imagem para atender aos requisitos da "área de interesse". A saída é exibida em uma estrutura especial, conforme visto na ilustração abaixo. A miniatura gerada pode ser apresentada usando uma taxa de proporção diferente da taxa de proporção da imagem original para atender às necessidades do usuário.

O algoritmo de miniatura funciona da seguinte maneira:

1. Remove da imagem os elementos que causam distração e reconhece o objeto principal, a "área de interesse".
2. Corta a imagem com base na área de interesse identificada.
3. Altera a taxa de proporção para se ajustar às dimensões da miniatura de destino.

![Miniaturas](./Images/thumbnail-demo.png)
