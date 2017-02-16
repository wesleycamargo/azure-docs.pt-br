---
title: Limpar um ponto de extremidade da CDN do Azure | Microsoft Docs
description: "Saiba como limpar todo o conteúdo armazenado em cache de um ponto de extremidade da CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 57d00f2192fed7a2e89ac94e110ebb7e84c83b72
ms.openlocfilehash: e19c08d7a47255eb6a6db631cb88ecaa053ee183


---
# <a name="purge-an-azure-cdn-endpoint"></a>Limpar um ponto de extremidade da CDN do Azure
## <a name="overview"></a>Visão geral
Os nós de borda da CDN do Azure armazenarão ativos em cache até a TTL (vida útil) do ativo expirar.  Depois que a TTL do ativo expira, quando um cliente solicita o ativo do nó de borda, o nó recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e atualizar o armazenamento do cache.

Às vezes, convém limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados.  Isso pode ocorrer devido a atualizações do aplicativo Web ou para atualizar rapidamente ativos que contenham informações incorretas.

> [!TIP]
> Observe que a limpeza só limpa o conteúdo em cache nos servidores de borda da CDN.  Qualquer cache downstream, como servidores proxy e caches do navegador local, ainda podem manter uma cópia em cache do arquivo.  É importante lembrar disso quando você definir a vida útil de um arquivo.  Você pode forçar um cliente downstream a solicitar a versão mais recente do arquivo, dando a ela um nome exclusivo sempre que atualizá-la ou aproveitando o [cache de cadeia de caracteres de consulta](cdn-query-string.md).  
> 
> 

Este tutorial o orienta durante a limpeza de ativos de todos os nós de borda de um ponto de extremidade.

## <a name="walkthrough"></a>Passo a passo
1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil CDN que contém o ponto de extremidade que você deseja limpar.
2. Na folha do perfil da CDN, clique no botão Limpar.
   
    ![Folha Perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A folha Limpeza é aberta.
   
    ![Folha Limpeza da CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na folha Limpeza, selecione o endereço do serviço que deseja limpar na lista suspensa de URLs.
   
    ![Formulário de limpeza](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Você também pode acessar a folha Limpeza clicando no botão **Limpar** na folha de ponto de extremidade da CDN.  Nesse caso, o campo **URL** será pré-populado com o endereço do serviço do ponto de extremidade específico.
   > 
   > 
4. Selecione os ativos que você deseja limpar dos nós de borda.  Para limpar todos os ativos, clique na caixa de seleção **Limpar todos** .  Caso contrário, digite o caminho completo de cada ativo que você deseja limpar (por exemplo, `/pictures/kitten.png`) na caixa de texto **Caminho** .
   
   > [!TIP]
   > Outras caixas de texto **Caminho** serão mostradas depois que você digitar um texto, para permitir que crie uma lista com vários ativos.  Para excluir ativos da lista, clique no botão de reticências (...).
   > 
   > Os caminhos devem ser uma URL relativa que atenda à seguinte [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  Para a **CDN do Azure da Verizon** (Standard e Premium), asterisco (\*) pode ser usado como um caractere curinga (por exemplo, `/music/*`).  Curingas e **Limpar tudo** não são permitidos com a **CDN do Azure do Akamai**.
   > 
   > 
5. Clique no botão **Limpar** .
   
    ![Botão Limpar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Solicitações de limpeza demoram aproximadamente de 2 a 3 minutos para serem processadas com a **CDN do Azure da Verizon** (Standard e Premium) e cerca de 7 minutos com a **CDN do Azure do Akamai**.  A CDN do Azure tem um limite de 50 solicitações de limpeza simultâneas por vez. 
> 
> 

## <a name="see-also"></a>Consulte também
* [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](cdn-preload-endpoint.md)
* [Referência da API REST da CDN do Azure – limpar ou pré-carregar um ponto de extremidade](https://msdn.microsoft.com/library/mt634451.aspx)




<!--HONumber=Jan17_HO4-->


