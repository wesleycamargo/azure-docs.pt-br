---
title: Falha ao se conectar a um cluster no Gerenciador de dados do Azure
description: Este artigo descreve as etapas de solução de problemas para se conectar no Data Explorer do Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965051"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Solucionar problemas: Falha ao se conectar a um cluster no Data Explorer do Azure

Se você não conseguir se conectar a um cluster no Data Explorer do Azure, siga estas etapas.

1. Certifique-se de que sua cadeia de conexão está correta. Deve estar no formato: `https://<ClusterName>.<Region>.kusto.windows.net`, como no seguinte exemplo: `https://docscluster.westus.kusto.windows.net`.

1. Certifique-se de ter as permissões adequadas. Se você não fizer isso, obterá uma resposta de *não autorizado*.

    Para obter mais informações sobre permissões, consulte [Gerenciar permissões de banco de dados](manage-database-permissions.md). Se necessário, trabalhe junto ao administrador ou do cluster para que você possa ser adicionado à função apropriada.

1. Verifique se o cluster ainda não foi excluído: Examine o log de atividades em sua assinatura.

1. Verifique o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/>). Procure o status do Data Explorer do Azure na região em que você está tentando se conectar a um cluster.

    Se o status não for **Bom** (marca de seleção verde), tente criar o cluster após a melhoria do status.

1. Se você ainda precisar de ajuda para resolver seu problema, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com).