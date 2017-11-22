---
title: Criar clipes com o Azure Media Clipper | Microsoft Docs
description: "Visão geral do Azure Media Clipper, uma ferramenta para compilar clipes de mídia com base em ativos"
services: media-services
keywords: "clipe;subclipe;codificação;mídia"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a5bb66db889dc1c52252773e3f129ba2c3d55f1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar clipes com o Azure Media Clipper
O Azure Media Clipper é uma biblioteca do JavaScript gratuita que permite que os desenvolvedores Web forneçam aos usuários uma interface para a criação de clipes de mídia. Essa ferramenta pode ser integrada em qualquer página da Web e fornece APIs para carregar ativos e enviar trabalhos de clipes.

O Azure Media Clipper permite que você:
- Corte a pré- e pós-imagem fixa de arquivos ao vivo 
- Compor destaques dos vídeos de eventos ao vivo, arquivos ao vivo ou arquivos VOD fMP4 do AMS 
- Concatenar vídeos de várias fontes 
- Produzir clipes de resumos dos seus ativos de mídia do AMS 
- Recortar vídeos com precisão de quadro 
- Gerar filtros de manifesto dinâmico sobre ativos ao vivo e VOD existentes com precisão de grupo de imagens (GOP) 
- Produzir trabalhos de codificação em relação aos ativos na sua conta de serviços de mídia

Para solicitar novos recursos, fornecer ideias ou comentários, envie para o [UserVoice dos Serviços de Mídia do Azure](http://aka.ms/amsvoice/). Se tiver problemas ou perguntas específicas ou encontrar bugs, entre em contato com a equipe do Serviços de Mídia em amcinfo@microsoft.com.

## <a name="release-notes"></a>Notas de versão
A lista a seguir descreve diversos problemas conhecidos e o log de alterações da versão mais recente do Clipper:
- [Lista de problemas conhecidos](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Log de alterações](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Suporte ao navegador
O Azure Media Clipper é compilado usando tecnologias modernas de HTML5 e tem suporte aos seguintes navegadores:

- Microsoft Edge 13 ou superior
- Internet Explorer 11 ou superior
- Chrome 54 ou superior
- Safari 10 ou superior
- Firefox 50 ou superior

> [!NOTE]
> Só há suporte para reprodução de stream do HTML5 dos Serviços de Mídia do Azure atualmente.

## <a name="language-support"></a>Suporte ao idioma
O widget do Clipper está disponível nos 18 idiomas a seguir:
- Chinês (Simplificado)
- Chinês (Tradicional)
- Tcheco
- Holandês; Flamengo
- Inglês
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polonês
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol
- Sueco
- Turco

## <a name="next-steps"></a>Próximas etapas
Para começar a usar o Azure Media Clipper, leia o artigo de [introdução](media-services-azure-media-clipper-getting-started.md) para obter detalhes sobre como implantar o widget.