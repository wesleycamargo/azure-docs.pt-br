---
title: "Controlando o comportamento de cache da CDN Premium do Azure da Verizon para as solicitações com cadeias de consulta | Microsoft Docs"
description: "O cache da cadeia de caracteres de consulta da CDN do Azure controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 20efef4e60da6a0ab07eab22ff18e7ae9ab12a30


---
# <a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Controlando o comportamento do cache de solicitações CDN com cadeias de caracteres de consulta - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Premium do Azure da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Visão geral
O cache da cadeia de caracteres de consulta controla como os arquivos devem ser armazenados em cache quando contêm cadeias de caracteres de consulta.

> [!IMPORTANT]
> Os produtos CDN Standard e Premium fornecem a mesma funcionalidade de cache de cadeia de consulta, mas a interface do usuário varia.  Este documento descreve a interface da **CDN Premium do Azure da Verizon**.  Para o cache da cadeia de consulta com a **CDN Standard do Azure da Akamai** e a **CDN Standard do Azure da Verizon**, consulte [Controlar o comportamento de cache das solicitações CDN com cadeias de consulta](cdn-query-string.md).
> 
> 

Existem três modos disponíveis:

* **standard-cache**: este é o modo padrão.  O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e deixará o ativo em cache.  Todas as solicitações subsequentes para esse ativo que são atendidas a partir do nó de borda ignorarão a cadeia de caracteres de consulta até que o ativo em cache expire.
* **no-cache**: nesse modo, as solicitações com cadeias de consulta não estão em cache no nó de borda da CDN.  O nó de borda recupera o ativo diretamente da origem e passa-o para o solicitante com cada solicitação.
* **unique-cache**: este modo trata cada solicitação com uma cadeia de consulta como um ativo exclusivo com seu próprio cache.  Por exemplo, a resposta da origem de uma solicitação de *foo.ashx?q=bar* seria armazenada em cache no nó de borda e retornada para os caches subsequentes com a mesma cadeia de caracteres de consulta.  Uma solicitação de *foo.ashx?q=somethingelse* será armazenada em cache como um ativo separado com seu próprio tempo de vida.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterando as configurações de cache da cadeia de consulta para perfis de CDN Premium
1. Na folha do perfil CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**.  Clique em **Cache de Cadeia de Caracteres de Consulta**.
   
    As opções de cache de cadeia de caracteres de consulta são exibidas.
   
    ![Opções de cache de cadeia de caracteres de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Após fazer a seleção, clique no botão **Atualizar**.

> [!IMPORTANT]
> As mudanças de configuração podem não estar visíveis imediatamente, pois o registro demora um pouco para se propagar pela CDN.  Para perfis da <b>CDN do Azure da Verizon</b>, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.
> 
> 




<!--HONumber=Nov16_HO3-->


