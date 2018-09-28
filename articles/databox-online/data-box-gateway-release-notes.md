---
title: Notas de versão da versão prévia do Gateway do Azure Data Box | Microsoft Docs
description: Descreve problemas críticos em aberto e resoluções para a versão prévia do Gateway do Azure Data Box.
services: databox-edge-gateway
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 7619056ace5d9b3cf083a40a6cfa06a0cac0561e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949682"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Notas de versão da versão prévia do Gateway do Azure Data Box

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas críticos em aberto e os problemas resolvidos para a versão prévia do Microsoft Gateway do Azure Data Box.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o Gateway do Data Box, examine cuidadosamente as informações contidas nas notas de versão.

A versão prévia corresponde à versão de software do **Gateway do Data Box versão prévia 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problemas corrigidos na versão prévia

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Problema |
| --- | --- |
| 1 | Nesta versão, quando um arquivo carregado por outra ferramenta (AzCopy) é atualizado e, depois, atualizado de um jeito que aumentam/estende o tamanho do arquivo, o seguinte erro é observado: *Erro 400: InvalidBlobOrBlock (o blob especificado ou conteúdo do bloco é inválido.)*|


## <a name="known-issues-in-preview-release"></a>Problemas conhecidos na versão prévia

A tabela a seguir fornece um resumo dos problemas conhecidos para o Gateway do Data Box executando a versão prévia.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos do Gateway do Data Box criados em versões prévias anteriores não podem ser atualizados para esta versão. |Faça o download das imagens de disco virtual da nova versão e configure e implante os novos dispositivos. Para saber mais, acesse [Preparar para implantar o Gateway do Azure Data Box](data-box-gateway-deploy-prep.md). |
| **2.** |Disco de dados provisionado |Após o provisionamento de um disco de dados de um determinado tamanho e a criação do Gateway do Data Box correspondente, você não deve reduzir o disco de dados. A tentativa de reduzir o disco resulta na perda de todos os dados locais no dispositivo. | |
| **3.** |Atualizar |Nesta versão, você pode atualizar apenas um compartilhamento por vez. | |
| **4.** |Renomear |Não há suporte para renomeação de objetos. |Entre em contato com o Suporte da Microsoft se esse recurso for crucial para seu fluxo de trabalho. |
| **5.** |Copiar| Se um arquivo somente leitura for copiado no dispositivo, a propriedade somente leitura não será preservada. | |
| **6.** |Logs| Devido a um bug nesta versão, você poderá ver as instâncias do código de erro 110 em *error.xml* com nomes de item irreconhecíveis. | |
| **7.** |Carregar | Devido a um bug nesta versão, você pode ver instâncias do código de erro 2003 durante o upload de arquivos específicos. | |
| **8.** |Exclusão | Devido a um bug nesta versão, se um compartilhamento NFS for excluído, o compartilhamento não poderá ser excluído. O status do compartilhamento mostrará *Excluindo*.  |Isso ocorre apenas quando o compartilhamento estiver usando um nome de arquivo sem suporte. |
| **9.** |Ajuda online |Talvez os links para a Ajuda no portal do Azure não levem à documentação.|Os links para a Ajuda funcionarão na versão de Disponibilidade geral. |



## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Gateway do Azure Data Box](data-box-gateway-deploy-prep.md).


