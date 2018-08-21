---
title: Validar uma nova solução do Azure Stack | Microsoft Docs
description: Saiba como validar uma nova solução do Azure Stack com validação como um serviço.
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
ms.openlocfilehash: 90c18af0f846061c102f1daf1b84a332aaec1dc6
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235237"
---
# <a name="validate-a-new-azure-stack-solution"></a>Validar uma nova solução do Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como pode usar o fluxo de trabalho de validação de solução para certificação novas soluções do Azure Stack.

Uma solução do Azure Stack é uma lista de hardware de materiais (BoM) que tenha sido em conjunto combinada com a Microsoft e foi aprovado, o logotipo do Windows Server de requisitos de certificação. Você também pode usar o fluxo de trabalho de validação de soluções quando houve uma alteração para o BoM que poderia causar a ser classificada como uma solução de hardware *novo*. Se houver dúvidas sobre o que dispararia uma **novos** ou **recertificação** de uma solução em contato pelo [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Para certificar sua solução, execute o fluxo de trabalho duas vezes. Executá-lo uma vez para o *minimamente* suporte para configuração. Executar em uma segunda vez para o *máximo* configuração. Microsoft certifica que a solução se ambas as configurações de passarem todos os testes.

Neste início rápido ajuda você ao processo de adição da sua solução e executar testes em andamento.

## <a name="add-a-new-solution"></a>Adicionar uma nova solução

1. Entrar para o [portal de validação](https://azurestackvalidation.com).
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
4. Selecione **carregar** e, em seguida, adicione o arquivo de configuração de implantação. Esta é uma etapa opcional. Você também pode adicionar seus parâmetros de teste, seguindo as etapas na próxima seção.

    > [!note]  
    > Você pode criar o seu arquivo de configuração adicionando os parâmetros nos parâmetros ambientais um seções de parâmetros comuns do teste na interface. Você pode recuperar o arquivo gerado pelo serviço da implantação do Azure Stack que está sendo validado. Para obter instruções, consulte [parâmetros comuns de fluxo de trabalho para a validação do Azure Stack como um serviço](azure-stack-vaas-parameters.md).

5. Adicione os parâmetros ambientais. Para obter mais informações, consulte [adicionar parâmetros ambientais](#add-environmental-parameters).
6. Adicione parâmetros comuns de teste. Para obter mais informações, consulte [adicionar parâmetros comuns de teste](#add-common-test-parameters).

    Dependendo da definição de teste, o teste pode exigir que você insira um valor independentemente dos parâmetros comuns, ou pode permitir que você substitua o valor do parâmetro comum.

7. Clique em **enviar** para agendar o teste.

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

- [Remarcar ou cancelar um teste](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).