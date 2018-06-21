---
title: Exportar e excluir os dados do Azure Machine Learning Studio - Azure | Microsoft Docs
description: Os dados armazenados no produto pelo Azure Machine Learning Studio estão disponíveis para exportação e exclusão por meio do portal do Azure e também por meio de APIs REST autenticadas. Os dados de telemetria podem ser acessados através do Portal de Privacidade do Azure. Este artigo mostra como fazer isso.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 6317d4baba5775c1e5a4fda66de80dd8299d8fed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658853"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Exportar e excluir dados do usuário no produto do Machine Learning Studio

É possível excluir ou exportar dado armazenados no produto pelo Azure Machine Learning Studio usando o portal do Azure, a interface do Studio, o PowerShell e APIs REST autenticadas. Este artigo informa como fazer isso. 

Os dados de telemetria podem ser acessados através do portal de Privacidade do Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Que tipos de dados do usuário o Studio coleta?

Para este serviço, os dados do usuário consistem em informações sobre usuários autorizados a acessar espaços de trabalho e registros de telemetria das interações do usuário com o serviço.

Há dois tipos de dados do usuário no Machine Learning Studio:
- **Dados da conta pessoal:** IDs de conta e endereços de email associados a uma conta.
- **Dados do cliente:** dados carregados para análise.

## <a name="studio-account-types-and-how-data-is-stored"></a>Tipos de conta do Studio e como os dados são armazenados

Há três tipos de contas no Machine Learning Studio. O tipo de conta que você tem determina como os dados são armazenados e como é possível excluí-los ou exportá-los.

- Um **espaço de trabalho de convidado** é uma conta anônima gratuita. Você inscreve-se sem fornecer credenciais como um endereço de email ou senha.
    -  Os dados serão limpos após a expiração do espaço de trabalho de convidado.
    - Os usuários convidados podem exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
- Um **espaço de trabalho gratuito** é uma conta gratuita com a qual você entra com as credenciais da conta da Microsoft - um endereço de email e senha.
    - É possível exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR (direitos de entidades de dados).
    - É possível exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
    - Para espaços de trabalho gratuitos que não usam contas do Azure AD, os dados telemétricos podem ser exportados usando o Portal de Privacidade.
    - Ao excluir o espaço de trabalho, todos os dados pessoais do cliente serão excluídos.
- Um **espaço de trabalho padrão** é uma conta paga que você acessa com credenciais de entrada.
    - É possível exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR.
    - É possível acessar dados através do portal de privacidade do Azure
    - É possível exportar dados pessoais e de clientes por meio da interface do usuário, APIs REST ou pacote do PowerShell
    - É possível excluir os dados no portal do Azure.

## <a name="delete-workspace-data-in-studio"></a>Excluir dados do espaço de trabalho no Studio 

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Os usuários podem excluir ativos em um espaço de trabalho, selecionando-os e, em seguida, clicando no botão Excluir.

![Excluir ativos no Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Excluir um espaço de trabalho inteiro

Os usuários também podem excluir o espaço de trabalho inteiro:
- Espaço de trabalho pago: exclua através do portal do Azure.
- Espaço de trabalho gratuito: use o botão Excluir no painel **Configurações**.

![Excluir um espaço de trabalho gratuito no Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exportar dados do Studio com o PowerShell
Use o PowerShell para exportar todas as informações para um formato portátil do Azure Machine Learning Studio usando comandos. Para obter mais informações, consulte o artigo [Módulo do PowerShell para Azure Machine Learning](powershell-module.md).

## <a name="next-steps"></a>Próximas etapas

Para documentação abrangendo serviços web e cobrança do plano de compromisso, consulte [Referência da API REST do Azure Machine Learning](https://docs.microsoft.com/en-us/rest/api/machinelearning/). 
