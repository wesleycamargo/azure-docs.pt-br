---
title: Correções para o Azure Marketplace e URL SAS problemas comuns
description: Lista problemas comuns sobre o uso de URIs de assinatura de acesso compartilhado e possíveis soluções.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938371"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas comuns de URL do SAS e correções

A tabela a seguir lista alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs enviados para sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Correção** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Falha ao copiar imagens* |  |  |
| "?" não é encontrado na URL do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS usando as ferramentas recomendadas. |
| Parâmetros “st” e “se” não estão no URL do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL do SAS com as **Data de início** e **Data de término** corretas. | 
| "sp = rl" não na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualize o URL do SAS com permissões definidas como `Read` e `List`. | 
| O URL do SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS para remover espaços em branco. |
| Erro de autorização de URL do SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros "st" e "se" da URL do SAS não têm especificação completa de data e hora | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | URL de SAS **data de início** e **data de término** parâmetros (`st` e `se` subcadeias de caracteres) devem ter o formato de data e hora completa, tal como `11-02-2017T00:00:00Z`. Versões abreviadas não são válidas. (Alguns comandos na CLI do Azure podem gerar valores abreviados por padrão). | 
|  |  |  |

Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) para saber mais.
