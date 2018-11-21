---
title: Fontes de dados com suporte – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker extrai automaticamente pares de pergunta-resposta do conteúdo semiestruturado, como perguntas frequentes, manuais de produto, diretrizes, documentos de suporte e políticas armazenadas como arquivos de documentos do Microsoft Word, arquivos PDF ou páginas da Web. O conteúdo também pode ser adicionado à base de conhecimento dos arquivos de conteúdo QnA estruturados.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: e6c654b00ee6be0ed87feb0fb2a5ccba38e5cbe4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624870"
---
# <a name="data-sources-for-qna-maker-content"></a>Fontes de dados para conteúdo do QnA Maker

O QnA Maker extrai automaticamente pares de pergunta-resposta do conteúdo semiestruturado, como perguntas frequentes, manuais de produto, diretrizes, documentos de suporte e políticas armazenadas como arquivos de documentos do Microsoft Word, arquivos PDF ou páginas da Web. O conteúdo também pode ser adicionado à base de conhecimento dos arquivos de conteúdo QnA estruturados. 

A tabela a seguir resume os tipos de conteúdo e formatos de arquivo com suporte no QnA Maker.

|Tipo de Fonte|Tipo de conteúdo| Exemplos|
|--|--|--|
|URL|Perguntas frequentes (simples, com seções ou com uma página inicial de tópicos)|[Perguntas frequentes simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), [Perguntas frequentes com links](https://www.microsoft.com/software-download/faq), [Perguntas frequentes com página inicial de tópicos](https://support.microsoft.com/products/windows?os=windows-10)|
|PDF/DOC|Perguntas frequentes, Manual do Produto, Brochuras, Artigo, Panfleto de Política, Guia de suporte, QnA estruturada etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf), [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Arquivo de QnA estruturado (incluindo suporte para RTF, HTML)|[Exemplo de QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Arquivo QnA estruturado|[Exemplo de chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="faq-urls"></a>URLs de perguntas frequentes

O QnA Maker pode dar suporte a páginas da Web de perguntas frequentes de três maneiras diferentes: páginas de Perguntas Frequentes Simples, páginas de Perguntas Frequentes com links, páginas de Perguntas Frequentes com uma Página Inicial de Tópicos.

### <a name="plain-faq-pages"></a>Páginas de perguntas frequentes simples

Este é o tipo mais comum de página de perguntas frequentes em que as respostas a seguem imediatamente às perguntas na mesma página. 

Abaixo está um exemplo de uma página de perguntas frequentes simples:

![Página de perguntas frequentes simples](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Páginas de perguntas frequentes com links 

Nesse tipo de página de perguntas frequentes, as perguntas são agregadas e vinculadas a respostas em seções diferentes da mesma página ou em páginas diferentes.

Abaixo está um exemplo de uma página de perguntas frequentes com links nas seções que estão na mesma página:

 ![Página de perguntas frequentes sobre o link de seção](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Páginas de perguntas frequentes com uma página inicial de Tópicos

Esse tipo de perguntas frequentes tem uma página inicial com Tópicos, em que cada tópico é um link para as QnAs relevantes em uma página diferente. Aqui, o QnA Maker rastreia todas as páginas vinculadas para extrair as perguntas e respostas correspondentes.

Abaixo está um exemplo de uma página de perguntas frequentes em que uma página inicial de tópicos tem links para seções de perguntas frequentes em diferentes páginas. 

 ![Página de perguntas Frequentes de link profundo](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>Arquivos PDF/DOC

O QnA Maker pode processar o conteúdo semiestruturado em um arquivo PDF ou documento e convertê-la em QnAs. Um bom arquivo que pode ser extraído também é um arquivo em que o conteúdo está organizado em alguma forma estruturada e é representado em seções bem definidas. As seções adicionais podem ser divididas em subseções ou subtópicos. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

O QnA Maker identifica seções e subseções e relacionamentos no arquivo com base em indícios visuais, como tamanho da fonte, estilo da fonte, numeração, cores etc. Arquivos PDF ou DOC semiestruturados poderiam ser Manuais, Perguntas Frequentes, Diretrizes, Políticas, Brochuras, Folhetos e muitos outros tipos de arquivos. Abaixo estão alguns tipos de exemplo desses arquivos.

### <a name="product-manuals"></a>Manuais de produtos

Normalmente, um manual é o material de diretrizes que acompanha um produto. Ele ajuda o usuário a configurar, usar, manter e solucionar problemas do produto. Quando o QnA Maker processa um manual, ele extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Abaixo está um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de Manual do produto](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> A extração funciona melhor em manuais com uma tabela de conteúdo e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicos.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros arquivos

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, contanto que tenham uma estrutura e um layout claros. Eles incluem: brochuras, diretrizes, relatórios, white papers, artigos científicos, políticas, livros etc. Veja um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Abaixo está um exemplo de um documento semiestruturado sem um índice:

 ![Documento semiestruturado de armazenamento de Blobs do Azure](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Documento de QnA Estruturado

O formato para Pergunta-Respostas estruturas em arquivos DOC é na forma de Perguntas e Respostas alternadas por linha, uma pergunta por linha seguida pela respectiva resposta na linha seguinte, conforme mostrado abaixo: 

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento do word de QnA estruturado:

 ![Documento de QnA estruturado](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Arquivos *TXT*, *TSV* e *XLS* Estruturados

QnAs na forma de arquivos *.txt*, *.tsv* ou *.xls* estruturados também podem ser carregadas para o QnA Maker para criar ou ampliar uma base de conhecimento.  Podem ser texto sem formatação ou ter conteúdo em RTF ou HTML. 

| Pergunta  | Resposta  | Metadados                |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

As colunas adicionais no arquivo de origem são ignoradas.

Abaixo está um exemplo de arquivo *.xls* de QnA estruturada, com conteúdo HTML:

 ![Excel do QnA estruturado](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um arquivo .tsv estruturado que contenha informações de fonte de dados. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados                |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de modo editorial à base de dados de conhecimento

Se não tiver conteúdo pré-existente para preencher a base de dados de conhecimento, você poderá adicionar QnAs editorialmente na Base de Dados de Conhecimento do QnA Maker. Saiba como atualizar sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um arquivo ou URL, ele é convertido em Markdown e armazenado nesse formato. Se o processo de conversão não estiver convertendo corretamente links em seus arquivos e URLs, edite as perguntas e respostas na página **Editar**. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Consulte também 

[Visão geral do QnA Maker](../Overview/overview.md)
