---
title: Bancos de dados SQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode implantar bancos de dados SQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Remover o provedor de recursos do SQL

Para remover o provedor de recursos do SQL, é essencial primeiro remova quaisquer dependências:

1. Certifique-se de que você tenha o pacote de implantação original que foi baixado para esta versão do adaptador de provedor de recursos do SQL.

2. Todos os bancos de dados de usuário devem ser excluídos do provedor de recursos. (Excluir os bancos de dados do usuário não exclui a dados). Essa tarefa deve ser executada pelos usuários.

3. O administrador deve excluir os servidores de hospedagem do adaptador de provedor de recursos de SQL.

4. O administrador deve excluir todos os planos que referenciam o adaptador de provedor de recursos do SQL.

5. O administrador deve excluir qualquer SKUs e cotas que estão associadas com o adaptador de provedor de recursos do SQL.

6. Executar novamente o script de implantação com os seguintes elementos:
    - -Desinstalar o parâmetro
    - Os pontos de extremidade do Gerenciador de recursos do Azure
    - O DirectoryTenantID
    - As credenciais da conta de administrador de serviço

