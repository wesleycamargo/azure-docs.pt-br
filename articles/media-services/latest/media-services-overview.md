---
title: Visão geral dos Serviços de Mídia v3 do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível dos Serviços de Mídia e fornece links para artigos com mais detalhes.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 0f4fd963ce3649c901a76f6677be059ba5be25af
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337554"
---
# <a name="what-is-azure-media-services-v3"></a>O que são os Serviços de Mídia v3 do Azure?

Os Serviços de Mídia do Azure são uma plataforma baseada em nuvem que permite a criação de soluções que apresentem uma transmissão de vídeo com qualidade de difusão, aumentem a acessibilidade e a distribuição, analisem o conteúdo e muito mais. Se você for um desenvolvedor de aplicativos, call center, órgão governamental, empresa de entretenimento, os Serviços de Mídia podem ajudar a criar aplicativos que oferecem experiências de mídia de qualidade superior para um público amplo nos dispositivos móveis e navegadores mais populares da atualidade. 

## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Mídia?

Os Serviços de Mídia permitem criar uma série de fluxos de trabalho de mídia na nuvem. Veja a seguir alguns exemplos do que pode ser feito com os Serviços de Mídia.  

* Fornecer vídeos em vários formatos, para que eles possam ser reproduzidos em uma série de navegadores e dispositivos. Para a entrega de vídeos sob demanda e transmissões ao vivo a vários clientes (dispositivos móveis, TV, PC, etc.) o conteúdo de vídeo e áudio precisa ser codificado e entregue de forma apropriada. Para ver como entregar e transmitir esse conteúdo, confira [Início Rápido: Codificar e transmitir arquivos](stream-files-dotnet-quickstart.md).
* Transmita eventos esportivos ao vivo para um público amplo online, como partidas de futebol, basebol, esportes universitários e escolares, e muito mais. 
* Transmita reuniões públicas e eventos como fóruns, reuniões da câmara de vereadores e órgãos legislativos.
* Analise o conteúdo do áudio ou vídeo gravado. Por exemplo, para alcançar maior satisfação dos clientes, as organizações podem extrair a conversão de fala em texto e criar índices de pesquisa e painéis. Em seguida, elas podem extrair dados inteligentes sobre reclamações comuns, fontes de reclamações e outros dados relevantes.
* Crie um serviço de assinatura de vídeo e transmita conteúdo protegido por DRM quando um cliente (por exemplo, um estúdio de cinema) precisar restringir o acesso e uso de um trabalho protegido por direitos autorais.
* Forneça conteúdo offline para reprodução em aviões, trens e automóveis. Talvez um cliente precise baixar o conteúdo no telefone ou tablet para reprodução quando ele estiver desconectado da rede.
* Implemente uma plataforma de vídeo educacional de aprendizado eletrônico com os Serviços de Mídia do Azure e a [API de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para fazer a conversão de fala em texto de legenda, traduzir para vários idiomas, etc. 
* Use os Serviços de Mídia do Azure junto com a [API de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para adicionar subtítulos e legendas a vídeos para abranger um público mais amplo (por exemplo, pessoas com deficiência auditiva ou que desejam acompanhar a leitura em um idioma diferente).
* Habilite a CDN do Azure para conseguir um dimensionamento grande para lidar melhor com altas cargas instantâneas (por exemplo, o início de um evento de lançamento de produto). 

## <a name="v3-capabilities"></a>Funcionalidades da v3

A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. 

Esta versão fornece os seguintes recursos:  

* **Transformações** que ajudam a definir fluxos de trabalho simples de processamento de mídia ou tarefas de análise. A Transformação é uma receita para processar os seus arquivos de áudio e vídeo. Em seguida, você pode aplicá-la repetidamente para processar todos os arquivos em sua biblioteca de conteúdos, enviando trabalhos para a Transformação.
* **Trabalhos** para processar (codificar ou analisar) seus vídeos. Um conteúdo de entrada pode ser especificado em um trabalho, usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.
* **Notificações** que monitoram o andamento ou estado do trabalho ou eventos iniciar/parar e de erro do Canal ao Vivo. As notificações são integradas com o sistema de notificação de Grade de Eventos do Azure. Você pode se inscrever com facilidade para eventos em vários recursos nos Serviços de Mídia do Microsoft Azure. 
* Os modelos de **Gerenciamento de Recursos do Azure** podem ser usados para criar e implantar as Transformações, Pontos de Extremidade de Transmissão, Canais e muito mais.
* O **Controle de acesso baseado em função** pode ser definido a nível de recurso, permitindo bloquear o acesso a recursos específicos, como Transformações, Canais e muito mais.
* **SDKs de Clientes** em várias linguagens: .NET, .NET core, Python, Go, Java e Node.js.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a atribuição de nome do Azure Resource Manager, confira: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Princípios de design da API v3

Um dos princípios de design de chave da API v3 é tornar a API mais segura. As APIs v3 não retornam segredos ou as credenciais em uma operação **Get** ou **List**. As chaves são sempre nulas, vazias ou corrigidas na resposta. Você precisa chamar um método de ação separada para obter as credenciais ou segredos. As ações separadas permitem que você defina permissões de segurança RBAC diferentes no caso de algumas APIs recuperar/exibir segredos enquanto outras APIs, não. Para saber mais sobre como gerenciar o acesso usando o RBAC, veja [Usar RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Os exemplos disso incluem 

* não retornando valores de ContentKey em Get de StreamingLocator, 
* não retornando as chaves de restrição no get da ContentKeyPolicy, 
* não retornando a parte da cadeia de caracteres de consulta da URL (para remover a assinatura) de URLs de entrada de HTTP de trabalhos.

Confira o exemplo [Obter a política de chave de conteúdo – .NET](get-content-key-policy-dotnet-howto.md).

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a v3?

Para obter informações sobre como iniciar o desenvolvimento com a API dos Serviços de Mídia v3 usando uma variedade de ferramentas e SDKs, confira [Começar o desenvolvimento](developers-guide.md).

## <a name="v3-content-map"></a>Mapa de conteúdo da v3

O conteúdo dos Serviços de Mídia v3 é organizado de acordo com a seguinte estrutura (também refletida no sumário):

|Seções| DESCRIÇÃO|
|---|---|
| Visão geral | Explica as funcionalidades dos Serviços de Mídia e o que é possível fazer com o serviço.|
| Guia de início rápido | Mostra instruções iniciais básicas para que os novos clientes experimentem os Serviços de Mídia rapidamente.|
| Tutoriais | Mostra procedimentos baseados em cenário para algumas das principais tarefas dos Serviços de Mídia.|
| Exemplos | Links para exemplos de código. |
| Conceitos | Contém explicações detalhadas sobre a funcionalidade e diagramas dos Serviços de Mídia v3. Os conceitos fundamentais descritos nestes tópicos devem ser examinados antes do início do desenvolvimento.<br/><br/>* Upload e armazenamento na nuvem<br/>* Codificação<br/>* Análise de mídia<br/>* Empacotamento, entrega, proteção<br/>* Transmissão ao vivo<br/>* Monitoramento contínuo<br/>* Clientes de player<br/><br/>e mais. |
| Guias de instruções | Demonstra como concluir uma tarefa.|

## <a name="next-steps"></a>Próximas etapas

Para ver como é fácil começar a codificar e transmitir arquivos de vídeo, confira [Arquivos de transmissão](stream-files-dotnet-quickstart.md). 

