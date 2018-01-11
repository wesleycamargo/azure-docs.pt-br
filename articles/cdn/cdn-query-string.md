---
title: "Controle o comportamento de cache da Rede de Distribuição de Conteúdo do Azure com cadeias de caracteres de consulta | Microsoft Docs"
description: "O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos são armazenados em cache quando contêm cadeias de caracteres de consulta."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 9ffd05a0eb4d976dc40a1c5d45fd22ebf9bd4db1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Controle o comportamento de cache da Rede de Distribuição de Conteúdo do Microsoft Azure  com cadeias de caracteres de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Premium do Azure da Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Visão geral
Com a Rede de Distribuição de Conteúdo do Azure (CDN), você pode controlar como os arquivos são armazenados em cache para uma solicitação na Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, a ordem não importa. 

> [!IMPORTANT]
> Os produtos da CDN premium e padrão oferecem a mesma funcionalidade de cache de cadeia de caracteres de consulta, mas a interface do usuário é diferente.  Este artigo descreve a interface da **CDN Standard do Azure da Akamai** e da **CDN Standard do Azure da Verizon**. Para saber mais sobre o armazenamento em cache de cadeias de caracteres de consulta com a **CDN Premium do Azure da Verizon**, confira [Controlando o comportamento do cache de solicitações de CDN com cadeias de caracteres de consulta - Premium](cdn-query-string-premium.md).

Estão disponíveis três modos de cadeia de caracteres de consulta:

- **Ignorar cadeias de caracteres de consulta**: modo padrão. Neste modo, o nó de borda da CDN passa as cadeias de caracteres de consulta do solicitante para a origem na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para esse ativo que são atendidas a partir do nó de borda ignoram a cadeia de caracteres de consulta até que o ativo em cache expire.
- **Ignorar o cache para cadeias de consulta**: nesse modo, as solicitações com cadeias de caracteres de consulta não são armazenadas em cache no nó de borda da CDN. O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
- **Armazenar em cada cache URL exclusiva**: nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta da origem para uma solicitação de `example.ashx?q=test1` é armazenada em cache no nó de borda e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação `example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterando configurações de cache de cadeia de consulta para perfis de CDN padrão
1. Abra um perfil de CDN e, em seguida, selecione o ponto de extremidade da CDN que deseja gerenciar.
   
   ![Pontos de extremidade de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo em Configurações, clique em **Regras de cache**.
   
    ![Botão de regras de cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na lista **Comportamento de cache da cadeia de caracteres de consulta**, selecione um modo de cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.
   
   ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Como é necessário um tempo para que o registro se propague através da CDN, as alterações nas configurações da cadeia de caracteres de cache poderão não estar visíveis imediatamente. Para perfis da **CDN do Azure da Akamai** , a propagação normalmente é concluída em um minuto. Para perfis da **Azure CDN da Verizon**, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.


