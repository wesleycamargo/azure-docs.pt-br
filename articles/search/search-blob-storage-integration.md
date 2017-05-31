---
title: Adicionar o Azure Search ao Armazenamento de Blobs | Microsoft Docs
description: "Crie um índice no código usando a API REST HTTP da Pesquisa do Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Pesquisando no Armazenamento de Blobs com o Azure Search

Pesquisar toda a variedade de tipos de conteúdo armazenados no Armazenamento de Blobs do Azure pode ser um problema difícil de solucionar. No entanto, você pode indexar e pesquisar o conteúdo de seus Blobs em apenas alguns cliques usando o Azure Search. Pesquisar no Armazenamento de Blobs requer o provisionamento de um serviço Azure Search. Os diversos limites de serviço e tipos de preço do Azure Search podem ser encontrados na [página de preços](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>O que é o Azure Search?
O [Azure Search](https://aka.ms/whatisazsearch) é uma solução de pesquisa que torna mais fácil para os desenvolvedores adicionar experiências de pesquisa de texto completo robustas para aplicativos Web e móveis. Como serviço, o Azure Search remove a necessidade de gerenciar qualquer infraestrutura de pesquisa, oferecendo simultaneamente um [SLA com tempo de atividade de 99,9%](https://aka.ms/azuresearchsla).

Com suporte avançado a 56 idiomas, o Azure Search pode analisar seu conteúdo e lidar de modo inteligente com as construções específicas a um idioma. O Azure Search também fornece as ferramentas para criar uma experiência de usuário de pesquisa avançada. É simples adicionar recursos como navegação mista, sugestões de pesquisa de digitação antecipada e realce de ocorrências para interfaces do usuário usando o Azure Search. Para saber mais sobre os recursos do Azure Search, você pode ler a [documentação](https://aka.ms/azsearchdocs) do serviço.

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>Abrir e pesquisar o conteúdo de formatos de documento da empresa
Com suporte para [extração de documentos](https://aka.ms/azsblobindexer) no Armazenamento de Blobs do Azure, o Azure Search pode indexar o conteúdo de uma variedade de tipos de arquivo armazenados em blobs:
- PDF
- Microsoft Office: DOCX/DOC, XLSX/XLS, PPT/PPTX, MSG (emails do Outlook)
- HTML
- Arquivos de texto sem formatação

Extraindo o texto e os metadados desses tipos de arquivo, é fácil pesquisar em vários formatos de arquivo com uma única consulta para encontrar os documentos mais relevantes, independentemente do tipo. Ao indexar o conteúdo e os metadados de documentos do Microsoft Office, PDFs e emails, é possível criar uma solução de gerenciamento de conteúdo corporativo usando o Armazenamento de Blobs e o Azure Search.

## <a name="search-through-your-blob-metadata"></a>Pesquisar os metadados de blob
Um cenário comum que facilita a classificação de blobs de qualquer tipo de conteúdo é a indexação dos metadados de blob personalizados definidos pelo usuário, bem como as propriedades do sistema para cada um dos seus blobs. Dessa forma, as informações para cada blob são indexadas independentemente do tipo de documento no blob, de modo que você pode facilmente classificar e navegar pelas facetas de todo o conteúdo do Armazenamento de Blobs.

[Saiba que mais sobre a indexação de metadados de blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Pesquisa de imagem
As funcionalidades de pesquisa de texto completo, navegação facetada e classificação do Azure Search agora podem ser aplicadas aos metadados de imagem armazenados em blobs.

Se essas imagens são pré-processadas usando a [API da Pesquisa Visual Computacional](https://www.microsoft.com/cognitive-services/computer-vision-api) dos Serviços Cognitivos da Microsoft, é possível indexar o conteúdo visual encontrado em cada imagem, incluindo OCR e reconhecimento de manuscrito. Estamos trabalhando na adição de OCR e outras funcionalidades de processamento de imagem diretamente para o Azure Search; se você estiver interessado nessas funcionalidades, envie uma solicitação em nosso [UserVoice](https://aka.ms/azsuv) ou [envie-nos um email](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexar e pesquisar em blobs do JSON
O Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado nos blobs que contêm JSON. O Azure Search pode ler blobs do JSON e analisar o conteúdo estruturado nos campos apropriados de um documento do Azure Search. O Azure Search também pode levar os blobs que contêm uma matriz de objetos JSON e mapeiam cada elemento para um documento do Azure Search separado.

Observe que atualmente a análise de JSON não é configurável por meio do Portal. [Saiba mais sobre análise de JSON no Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Início rápido
O Azure Search pode ser adicionado para blobs diretamente da folha do portal de Armazenamento de Blobs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clicar na opção "Adicionar Azure Search" inicia um fluxo em que você pode selecionar um serviço do Azure Search existente ou criar um novo. Se você criar um novo serviço, você será direcionado para fora da experiência de portal da conta de armazenamento. Você precisará navegar novamente até a folha Portal de armazenamento e selecionar novamente a opção "Adicionar Azure Search", em que você pode selecionar o serviço existente.

### <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Indexador de Blobs do Azure Search na [documentação](https://aka.ms/azsblobindexer) completa.

