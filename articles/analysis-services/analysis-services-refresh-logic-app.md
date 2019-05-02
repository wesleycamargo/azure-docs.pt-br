---
title: Atualização com aplicativos lógicos de modelos do Azure Analysis Services | Microsoft Docs
description: Aprenda a codificar atualização assíncrona usando aplicativos lógicos do Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6e1ac5dfd1972e406a1bd8dcd26e6aef2c4ea6d1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919877"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com aplicativos lógicos

Usando aplicativos lógicos e chamadas REST, você pode executar operações de atualização de dados automatizado em seus modelos de tabela de análise do Azure, incluindo a sincronização de réplicas somente leitura para expansão de consulta.

Para saber mais sobre como usar as APIs REST com o Azure Analysis Services, consulte [assíncrona de atualização com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  Os exemplos neste artigo usará um serviço Principal (SPN) para se autenticar no Azure Analysis Services. Para obter mais informações, consulte [criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Projetar o aplicativo lógico

> [!IMPORTANT]
> Os exemplos a seguir pressupõem que o firewall do Azure Analysis Services está desabilitado.  Se o firewall estiver habilitado, o endereço IP público do iniciador de solicitação deve ser na lista de permissões no firewall do Azure Analysis Services. Para saber mais sobre intervalos de IP do aplicativo lógico por região, consulte [informações de configuração para aplicativos lógicos do Azure e limites](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar uma entidade de serviço (SPN)

Para saber mais sobre como criar uma entidade de serviço, consulte [criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar permissões no Azure Analysis Services
 
A entidade de serviço que você cria deve ter permissões de administrador do servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurar o aplicativo lógico

Neste exemplo, o aplicativo lógico foi projetado para disparar quando uma solicitação HTTP é recebida. Isso permitirá o uso de uma ferramenta de orquestração, como o Azure Data Factory, para disparar a atualização do modelo do Azure Analysis Services.

Depois de criar um aplicativo lógico:

1. No designer de aplicativo lógico, escolha a primeira ação como **quando uma solicitação HTTP é recebida**.

   ![Adicionar atividade HTTP recebida](./media/analysis-services-async-refresh-logic-app/1.png)

Esta etapa será populado com a URL de HTTP POST depois que o aplicativo lógico é salvo.

2. Adicionar uma nova etapa e pesquise **HTTP**.  

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **HTTP** para adicionar esta ação.

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade HTTP da seguinte maneira:

|Propriedade  |Value  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https://*sua região do servidor*/servers/*nome do servidor aas*/models/*seu nome de banco de dados*/ <br /> <br /> Por exemplo: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Cabeçalhos**     |   Content-Type, application/json <br /> <br />  ![Cabeçalhos](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre formando o corpo da solicitação, consulte [assíncrona de atualização com a API REST - POST /refreshes.](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Active Directory OAuth         |
|**Tenant**     |Preencha o TenantId do Azure Active Directory         |
|**Público-alvo**     |https://*.asazure.windows.net         |
|**ID do cliente**     |Insira o ClientID do nome da entidade de serviço         |
|**Tipo de credencial**     |Segredo         |
|**Segredo**     |Insira o segredo de nome de entidade de serviço         |

Exemplo:

![Atividade HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora teste o aplicativo lógico.  No designer de aplicativo lógico, clique em **executar**.

![Testar o aplicativo lógico](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir o aplicativo lógico com o Azure Data Factory

Depois que o aplicativo lógico é salvo, examine os **quando uma solicitação HTTP é recebida** atividade e copie o **URL de HTTP POST** que agora é gerado.  Esta é a URL que pode ser usada pelo Azure Data Factory para fazer a chamada assíncrona para disparar o aplicativo lógico.

Aqui está um exemplo de atividade do Azure Data Factory da Web que faz essa ação.

![Atividade da Web do Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Usar um aplicativo lógico independente

Se você não planeja usar uma ferramenta de orquestração, como o Data Factory para disparar a atualização do modelo, você pode definir o aplicativo lógico para disparar a atualização com base em um agendamento.

Usando o exemplo acima, exclua a primeira atividade e substituí-lo com um **agendamento** atividade.

![Atividade de agendamento](./media/analysis-services-async-refresh-logic-app/12.png)

![Atividade de agendamento](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo usará **recorrência**.

Depois que a atividade foi adicionada, configurar o intervalo e a frequência, em seguida, adicione um novo parâmetro e escolha **a estas horas**.

![Atividade de agendamento](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas desejadas.

![Atividade de agendamento](./media/analysis-services-async-refresh-logic-app/15.png)

Salve o aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

[Exemplos](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
