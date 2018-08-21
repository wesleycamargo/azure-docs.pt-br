---
title: Usar a validação como um serviço para o portal do Azure Stack para agendar seu primeiro teste | Microsoft Docs
description: Use a validação como um serviço para o portal do Azure Stack para agendar seu primeiro teste.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0f681070df4b4b3384171c05edb3851abec2ab5c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235234"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Guia de início rápido: Usar a validação como um portal de serviço para agendar seu primeiro teste

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como agendar seu primeiro teste com a validação como um portal de serviço (VaaS) para verificar o seu hardware. O agente local deve ser implantado na solução do Azure Stack que estão sendo validada antes da execução de testes de validação.

Neste início rápido você adicione sua solução e executar testes.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este início rápido, você precisará ter:
 - Uma validação como uma conta de serviço. Para obter instruções, consulte [configurar a validação de uma conta de serviço](azure-stack-vaas-set-up-account.md).  
- O agente local instalado no seu sistema. Para obter instruções, consulte [implantar as máquinas de virtuais de teste e o agente locais](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Adicionar uma nova solução

1. Entrar para o [portal de validação](https://azurestackvalidation.com).

    ![Entre no portal do validação](media/vaas_portalsignin.png)  

2. Selecione **nova solução**.
3. Insira um nome para a solução e selecione **salvar**.

## <a name="create-a-solution-validation-workflow"></a>Criar um fluxo de trabalho de validação de soluções

1. Selecione o nome da solução.
2. Selecione **gerenciar** sobre o **validações de solução** lado a lado.

    ![Validações de solução](media/image2.png)

## <a name="create-a-solution-workflow"></a>Criar um fluxo de trabalho da solução

1. Selecione **nova validação de solução**.
2. Digite o nome da validação.
3. Selecione **mínimo** ou **máximo**.  
    - **Mínimo**  
    A solução é configurada com o número mínimo com suporte de nós.  
    - **Máximo**  
    A solução é configurada com o número máximo com suporte de nós.
4. Adicione os parâmetros ambientais. Para obter mais informações, consulte [adicionar parâmetros ambientais](#add-environmental-parameters).
5. Adicione parâmetros comuns de teste. Para obter mais informações, consulte [adicionar parâmetros comuns de teste](#add-common-test-parameters).

    Dependendo da definição de teste, o teste pode exigir que você insira um valor independentemente dos parâmetros comuns, ou pode permitir que você substitua o valor do parâmetro comum.
6. Clique em **enviar** para agendar o teste.

## <a name="add-environmental-parameters"></a>Adicionar parâmetros ambientais

Adicione os seguintes parâmetros ambientais:

| Informações de aprovação de teste | Obrigatório | DESCRIÇÃO |
| --- | --- | --- | --- |
| Compilação de pilha do Azure | Obrigatório | Compilação de pilha do Azure que valor numérico (por exemplo 20170501.1) deve ser um válido do Azure Stack compilar número ou versão, por exemplo, 1.0.170330.9 |
| ID do locatário | Obrigatório | ID do locatário do Active Directory. Isso deve ser um GUID (por exemplo, ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Região | Obrigatório | Região de implantação do Azure Stack |
| Ponto de extremidade do Gerenciador de recursos de locatário | Obrigatório | Ponto de extremidade para operações de locatário do Azure Resource Manager (por exemplo https://management.loc-ext.domain.com) |
| Ponto de extremidade do Gerenciador de recursos do administrador | Não requerido | Ponto de extremidade para operações de locatário do Azure Resource Manager (por exemplo https://management.loc-ext.domain.com) |
| FQDN externo | Não requerido | Externo totalmente qualificado de nome de domínio usada como o sufixo para pontos de extremidade. (por exemplo, local.azurestack.external ou redmond.contoso.com) |
| Número de nós | Obrigatório | O número de nós em sua solução. |

## <a name="add-common-test-parameters"></a>Adicionar parâmetros comuns de teste

Adicione os seguintes parâmetros comuns de teste:

| Informações de aprovação de teste | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| Nome de usuário do locatário | Obrigatório | Nome de usuário do locatário (por exemplo tenant@contoso.onmicrosoft.com) |
| Senha do locatário | Obrigatório | A senha para o locatário. |
| Nome de usuário do administrador de serviços | Não requerido | Nome de usuário do locatário (por exemplo tenant@contoso.onmicrosoft.com) |
| Senha de administrador de serviço | Não requerido | Nome de usuário do administrador de serviços (por exemplo serviceadmin@contoso.onmicrosoft.com) |
| Nome de usuário do administrador de nuvem | Não requerido | Conta de administrador de domínio de pilha do Azure (por exemplo, contoso\cloudadmin) |
| Senha do administrador de nuvem | Não requerido | |
|  Cadeia de conexão de diagnóstico | Não requerido | URI de SAS para uma conta de armazenamento do Azure para diagnóstico de quais logs serão copiados durante a execução de teste. Ver [criar um blob de armazenamento do Azure para armazenar logs](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>O valor de **cadeia de conexão de diagnóstico** parâmetro comum será armazenado pelo serviço e fornecido no momento do agendamento para todos os testes no fluxo de trabalho que use esse parâmetro. Quando a URL de SAS é de 30 dias de expiração, você será solicitado para uma nova URL de SAS na página parâmetros comuns. |
| Marca - nome | Não requerido |  Marcas descritivas podem ser inseridas para rotular o fluxo de trabalho. Esse é o nome da marca. |
| Marca - valor | Não requerido | Marcas descritivas podem ser inseridas para rotular o fluxo de trabalho. Esse é o valor da marca. |

## <a name="next-steps"></a>Próximas etapas

- [Validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
