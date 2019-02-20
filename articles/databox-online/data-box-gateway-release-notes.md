---
title: Notas sobre a versão prévia do Azure Data Box Gateway | Microsoft Docs
description: Descreve problemas críticos em aberto e resoluções para a versão prévia do Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: 0265de5b224e62d188fe6e3b9322d5c2e3f77fa1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883127"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Notas de versão da versão prévia do Azure Data Box Gateway

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas críticos em aberto e os problemas resolvidos para a versão prévia do Microsoft Azure Data Box Gateway.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o Data Box Gateway, examine cuidadosamente as informações contidas nas notas de versão.

A versão prévia corresponde à versão de software do **Data Box Gateway versão prévia 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problemas corrigidos na versão prévia

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Problema |
| --- | --- |
| **1.** | Nesta versão, quando um arquivo que foi carregado por outra ferramenta (AzCopy) é atualizado e, depois, atualizado de um jeito que aumenta/estende o tamanho do arquivo, o seguinte erro é observado: *Erro 400: InvalidBlobOrBlock (o conteúdo do blob ou bloco especificado é inválido.)*|
| **2.** |Devido a um bug nesta versão, você poderá ver as instâncias do código de erro 110 em *error.xml* com nomes de item irreconhecíveis. | 
| **3.** |Devido a um bug nesta versão, poderá ocorrer casos do código de erro 2003 durante o upload de arquivos específicos. | 
| **4.** |Nesta versão, você pode atualizar apenas um compartilhamento por vez. | 


## <a name="known-issues-in-preview-release"></a>Problemas conhecidos na versão prévia

A tabela a seguir fornece um resumo dos problemas conhecidos para o Data Box Gateway executando a versão prévia.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos do Data Box Gateway criados em versões prévias anteriores não podem ser atualizados para esta versão. |Faça o download das imagens de disco virtual da nova versão e configure e implante os novos dispositivos. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Disco de dados provisionado |Após o provisionamento de um disco de dados de um determinado tamanho e a criação do Data Box Gateway correspondente, você não deve reduzir o disco de dados. A tentativa de reduzir o disco resulta na perda de todos os dados locais no dispositivo. | |
| **3.** |Renomear |Não há suporte para renomeação de objetos. |Entre em contato com o Suporte da Microsoft se esse recurso for crucial para seu fluxo de trabalho. |
| **4.** |Copiar| Se um arquivo somente leitura for copiado no dispositivo, a propriedade somente leitura não será preservada. | |
| **5.** |Tipos de arquivo | Não há suporte para os seguintes tipos de arquivo do Linux: arquivos de caracteres, arquivos de bloco, soquetes, pipes e links simbólicos.  |A cópia desses arquivos resulta na criação de arquivos de comprimento 0 no compartilhamento NFS. Esses arquivos permanecem no estado de erro e também são relatados no *error.xml*. |
| **6.** |Exclusão | Devido a um bug nesta versão, se um compartilhamento NFS for excluído, o compartilhamento não poderá ser excluído. O status do compartilhamento mostrará *Excluindo*.  |Isso ocorre apenas quando o compartilhamento estiver usando um nome de arquivo sem suporte. |
| **7.** |Atualizar | As permissões e ACLs (listas de controle de acesso) não são preservadas em uma operação de atualização.  | |
| **8.** |Copiar | A cópia de dados falha com erro:  Não foi possível concluir a operação solicitada devido a uma limitação do sistema de arquivos.  |Esse erro ocorre quando o ADS (Fluxo de Dados Alternativo) associado ao arquivo excede 128 KB (limite máximo para ReFS).  |
| **9.** |Links simbólicos |Não há suporte para links simbólicos.  |Links simbólicos para diretórios resultam nos diretórios nunca serem marcados como offline. Como resultado, você não poderá ver a cruz cinza nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente carregado no Azure. |
| **10.** |Compartilhamentos |Atualizar um contêiner existente com Blobs de páginas, para um compartilhamento de blob de blocos (ou vice-versa) resulta no carregamento de falhas na modificação do arquivo.  |Esse comportamento é visto quando estas etapas são seguidas: <li> Crie um compartilhamento de blob de blocos no dispositivo. </li><li> Associe o compartilhamento de um contêiner de nuvem existente que tenha os Blobs de Páginas.</li><li>Atualize esse compartilhamento. </li><li>Modifique alguns dos arquivos atualizados que já estão armazenados como Blobs de Páginas na nuvem.</li> São vistas falhas de carregamento. |
| **11.** |Ajuda online |Talvez os links para a Ajuda no portal do Azure não levem à documentação.|Os links para a Ajuda funcionarão na versão de Disponibilidade geral. |

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


