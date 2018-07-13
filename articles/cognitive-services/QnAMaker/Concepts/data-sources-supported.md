---
title: Fontes de dados com suporte - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Fontes de dados com suporte
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364258"
---
# <a name="data-sources"></a>Fontes de dados 
O QnA Maker pode extrair automaticamente pares de pergunta-resposta de formatos de conteúdo semiestruturados comuns como perguntas frequentes e manuais de produtos. O conteúdo também pode ser adicionado à base de dados de conhecimento desde arquivos estruturados.

## <a name="plain-faq-pages"></a>Páginas de perguntas frequentes simples
Este é o tipo mais comum de página de perguntas frequentes em que as respostas a seguem imediatamente às perguntas na mesma página. 

![Página de perguntas frequentes simples](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Páginas de perguntas frequentes com Links de Seção 
Nesse tipo de página de perguntas frequentes, as perguntas são agregadas e vinculadas a respostas em seções diferentes na mesma página.

 ![Página de perguntas frequentes sobre o link de seção](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Páginas de perguntas frequentes com links para páginas diferentes 
Esse tipo de página de perguntas frequentes é semelhante a uma página de perguntas frequentes vinculada à seção, exceto que os links redirecionam para uma página diferente. O QnA Maker rastreia todas as páginas vinculadas para extrair as respostas correspondentes.

 ![Página de perguntas Frequentes de link profundo](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Manuais de produtos

Normalmente, um manual é o material de diretrizes que acompanha um produto. Ele ajuda o usuário a configurar, usar, manter e solucionar problemas do produto. Quando o QnA Maker processa um manual, ele extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> A extração funciona melhor em manuais com uma tabela de conteúdo e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicos.


## <a name="structured-data-format-through-file-upload"></a>Formato de dados estruturados por meio de upload de arquivo

Os arquivos estruturados, como .tsv, .xlsx com colunas formatadas, também podem ser carregados para QnA Maker durante a criação da base de dados de conhecimento. Você também pode carregar arquivos da guia **Configurações** de uma base de dados de conhecimento

| Pergunta  | Resposta  | Metadados                |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | `Key1:Value1\|Key2:Value2` |
| Pergunta2 | Resposta2 |      `Key:Value`           |
As colunas adicionais no arquivo de origem são ignoradas.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação
Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um arquivo .tsv estruturado que contenha informações de fonte de dados. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados                |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1|`Key1:Value1\|Key2:Value2` |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="editorial"></a>Editorial
Se não tiver conteúdo pré-existente para preencher a base de dados de conhecimento, você também poderá adicioná-lo editorialmente na base de dados de conhecimento do Maker QnA. Saiba como atualizar sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Consulte também 

[Visão geral do QnA Maker](../Overview/overview.md)