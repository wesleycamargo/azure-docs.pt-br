---
title: Controlando o comportamento de cache da CDN do Azure com cadeias de consulta - Premium | Microsoft Docs
description: "O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 2021b5b7602605a7c264e9cd575399077691da34
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Controle o comportamento de cache da Rede de Distribuição de Conteúdo do Azure com cadeias de caracteres de consulta - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Premium do Azure da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Visão geral
Com a Rede de Distribuição de Conteúdo do Azure (CDN), você pode controlar como os arquivos são armazenados em cache para uma solicitação na Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, a ordem não importa. 

> [!IMPORTANT]
> Os produtos da CDN premium e padrão oferecem a mesma funcionalidade de cache de cadeia de caracteres de consulta, mas a interface do usuário é diferente.  Este artigo descreve a interface da **CDN Premium do Azure da Verizon**. Para o cache da cadeia de consulta com a **CDN Standard do Azure da Akamai** e a **CDN Standard do Azure da Verizon**, consulte [Controlar o comportamento de cache das solicitações CDN com cadeias de consulta](cdn-query-string.md).
>

Estão disponíveis três modos de cadeia de caracteres de consulta:

- **standard-cache**: Modo padrão. Neste modo, o nó de borda da CDN passa as cadeias de caracteres de consulta do solicitante para a origem na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para esse ativo que forem atendidas a partir do nó de borda ignoram a cadeia de caracteres de consulta até que o ativo em cache expire.
- **sem cache**: nesse modo, solicitações com cadeias de caracteres de consulta não estão em cache no nó de borda CDN. O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
- **unique-cache**: Nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta da origem para uma solicitação de `example.ashx?q=test1` é armazenada em cache no nó de borda e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação `example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterando as configurações de cache da cadeia de consulta para perfis de CDN Premium
1. Abra o perfil CDN, clique em **Gerenciar**.
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**. Clique em **Cache de Cadeia de Caracteres de Consulta**.
   
    As opções de cache de cadeia de caracteres de consulta são exibidas.
   
    ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de caracteres de consulta e clique em **Atualização**.

> [!IMPORTANT]
> Como é necessário um tempo para que o registro se propague através da CDN, as alterações nas configurações da cadeia de caracteres de cache poderão não estar visíveis imediatamente. Para perfis da **Azure CDN Premium da Verizon**, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.
 

