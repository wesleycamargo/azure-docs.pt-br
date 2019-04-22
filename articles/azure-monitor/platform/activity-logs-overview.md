---
title: Visão geral do Log de Atividades do Azure
description: Saiba o que é o Log de Atividades do Azure e como usá-lo para compreender os eventos que ocorrem dentro de sua assinatura do Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: b84238e8a659358f2c065eb1533f0d21a5335d43
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496872"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorar a atividade da assinatura com o Log de Atividades do Azure

O **Log de Atividades do Azure** é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. O Log de Atividades era conhecido como "Logs de Auditoria" ou "Logs Operacionais", já que a categoria Administrativa relata eventos de plano de controle de suas assinaturas. Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo Clássico/"RDFE".

![Logs de Atividade X outros tipos de logs](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

Figura 1: Logs de Atividade X outros tipos de logs

O Log de Atividades difere dos [Logs de Diagnóstico](diagnostic-logs-overview.md). Os Logs de Atividade fornecem dados sobre as operações em um recurso externo (o “plano de controle”). Os Logs de Diagnóstico são emitidos por um recurso e fornecem informações sobre a operação do recurso (o “plano de dados”).

> [!WARNING]
> O Log de Atividades do Azure é usado principalmente para atividades que ocorrem no Azure Resource Manager. Ele não controla os recursos usando o modelo Clássico/RDFE. Alguns tipos de recursos Clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se você interagir com um tipo de recurso Clássico por meio do Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no Log de Atividades. Se você interagir com um tipo de recurso clássico fora dos proxies do Azure Resource Manager, suas ações somente serão registradas no Log de Operação. O Log de Operação pode ser pesquisado em uma seção separada do portal.
>
>

Você pode recuperar os eventos de seu Log de Atividade usando o Portal do Azure, a CLI, cmdlets do PowerShell e a API REST do Azure Monitor.

> [!NOTE]
> [Os alertas mais recentes](../../azure-monitor/platform/alerts-overview.md) oferecem uma experiência aprimorada ao criar e gerenciar regras de alerta do log de atividades.  [Saiba mais](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="categories-in-the-activity-log"></a>Categorias no Log de Atividades
O Log de Atividades contém várias categorias de dados. Para obter todos os detalhes sobre o esquema dessas categorias, [veja este artigo](../../azure-monitor/platform/activity-log-schema.md). Estão incluídos:
* **Administrativos** -essa categoria contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Os exemplos dos tipos de eventos que você vê nessa categoria incluem "criar máquina virtual" e "excluir grupo de segurança". Cada ação tomada por um usuário ou um aplicativo usando o Resource Manager é modelada como uma operação em um tipo de recurso específico. Se o tipo de operação for Gravação, Exclusão ou Ação, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. A categoria administrativa também inclui alterações de controle de acesso baseado em função em uma assinatura.
* **Integridade do Serviço** - essa categoria contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo do tipo de evento que você vê nessa categoria é "SQL Azure no Leste dos EUA está passando por tempo de inatividade". Eventos de integridade do serviço são fornecidos em cinco variedades: Ação Necessária, Recuperação Assistida, Incidente, Manutenção, Informações ou Segurança, e só aparecerão se você tiver um recurso na assinatura que seria afetada pelo evento.
* **Integridade do recurso** – Esta categoria contém o registro de qualquer evento de integridade do recurso ocorrido nos recursos do Azure. Um exemplo do tipo de evento que você veria nessa categoria é "Status de integridade da máquina virtual alterado para não disponível". Eventos de integridade de recursos podem representar um dos quatro status de integridade: Disponível, Não Disponível, Degradado e Desconhecido. Além disso, os eventos de integridade de recursos podem ser categorizados como Iniciados pela plataforma ou Iniciados pelo usuário.
* **Alerta** - esta categoria contém o registro de todas as ativações de alertas do Azure. Um exemplo do tipo de evento que você vê nessa categoria é "% de CPU em myVM foi 80 nos últimos 5 minutos." Uma variedade de sistemas do Azure têm um conceito de alerta – você pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem a essa regra. Cada vez que um tipo de alerta com suporte do Azure é 'ativado', ou as condições são atendidas para gerar uma notificação, um registro de ativação também é enviado para essa categoria de Log de Atividades.
* **Dimensionamento automático** -essa categoria contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo do tipo de evento que você veria nessa categoria é "Falha na ação de escalar horizontalmente do Dimensionamento Automático". Usando o dimensionamento automático, você pode dimensionar o número de instâncias em um tipo de recurso com suporte com base na hora do dia e/ou dados de carga (métricas) usando uma configuração de dimensionamento automático. Quando as condições de redimensionamento forem atendidas, os eventos iniciados e bem-sucedidos ou com falha serão registrados nessa categoria.
* **Recomendação** – Esta categoria contém eventos de recomendação do Assistente do Azure.
* **Segurança** – Esta categoria contém o registro de todos os alertas gerados pela Central de Segurança do Azure. Um exemplo do tipo de evento que você veria nessa categoria é "Arquivo de extensão dupla suspeito executado".
* **Política**: esta categoria contém registros de todas as operações de ação de efeito executadas pelo Azure Policy. Os exemplos dos tipos de eventos que você vê nessa categoria incluem Auditar e Negar. Cada ação tomada pelo Policy é modelada como uma operação em um recurso.

## <a name="event-schema-per-category"></a>Esquema de eventos por categoria

[Veja este artigo para compreender o esquema de evento do Log de Atividades por categoria.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>O que você pode fazer com o Log de Atividades

Veja algumas coisas que você pode fazer com o Log de Atividades:

![Log de Atividades do Azure](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Consultar e exibi-lo no **Portal do Azure**.
* [Criar um alerta em um evento do Log de Atividades.](../../azure-monitor/platform/activity-log-alerts.md)
* [Stream-lo para um **Hub de eventos** ](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análises personalizadas, como o Power BI.
* Analisá-los no Power BI usando o [ **o pacote de conteúdo do Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Salvá-lo em uma **Conta de Armazenamento** para inspeção manual ou arquivamento](../../azure-monitor/platform/archive-activity-log.md). Você pode especificar o tempo de retenção (em dias) usando o **Perfil de Log**.
* Consultar por meio de Cmdlet do PowerShell, da CLI ou da API REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Consultar o Log de Atividades no Portal do Azure

No Portal do Azure, você pode exibir seu Log de Atividades em vários locais:
* O **Log de Atividades**, que pode ser acessado pesquisando o Log de Atividades em **Todos os Serviços** no painel de navegação esquerdo.
* **Monitor**, que é exibido por padrão no painel de navegação esquerdo. O Log de Atividades é uma seção do Azure Monitor.
* A maioria dos **recursos**, por exemplo, o blade de configuração de uma máquina virtual. O Log de atividades é uma seção na maioria dos blades de recursos, e clicar nele filtra automaticamente os eventos para aqueles relacionados a esse recurso específico.

No Portal do Azure, você pode filtrar seu Log de Atividades por estes campos:
* Intervalo de tempo – A hora de início e de término dos eventos.
* Categoria – A categoria do evento conforme descrito acima.
* Assinatura – Um ou mais nomes de assinatura do Azure.
* Grupo de recursos – Um ou mais grupos de recursos nessas assinaturas.
* Recurso (nome) – O nome de um recurso específico.
* Tipo de recurso – O tipo de recurso, por exemplo, Microsoft.Compute/virtualmachines.
* Nome da operação – O nome de uma operação do Azure Resource Manager, por exemplo, Microsoft.SQL/servers/Write.
* Gravidade – O nível de gravidade do evento (Informativo, Aviso, Erro, Crítico).
* Evento iniciado por – O "chamador" ou o usuário que realizou a operação.
* Pesquisa aberta – Isso é uma caixa de pesquisa de texto aberto que procura essa cadeia de caracteres em todos os campos de todos os eventos.

Depois de definir um conjunto de filtros, você pode fixar uma consulta ao seu painel do Azure para sempre ficar de olho em eventos específicos.

Para ainda mais poder, você pode clicar no ícone **Logs**, que exibe os dados do log de atividades na [solução de Coleta e análise dos Logs de Atividades](../../azure-monitor/platform/collect-activity-logs.md). A folha Log de Atividades oferece uma experiência básica de filtro/procura nos logs, mas o recurso de logs do Azure Monitor permite que você dinamize, consulte e visualize os dados de formas mais eficientes.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportar o Log de Atividades com um Perfil de Log

Um **Perfil de Log** controla o modo de exportação de seu Log de Atividades. Com um Perfil de Log, você pode configurar:

* Aonde o Log de Atividades deve ser enviado (Conta de Armazenamento ou Hubs de Eventos)
* Quais categorias de evento (Gravação, Exclusão, Ação) devem ser enviadas. *O significado de "categoria" em Perfis de Log e eventos de Log de Atividades é diferente. No Perfil de Log, "Category" representa o tipo de operação (Gravar, Excluir, Ação). Em um evento do Log de Atividades, a propriedade "category" representa a origem ou o tipo de evento (por exemplo, Administração, Serviço de Integridade, Alerta e muito mais).*
* Quais regiões (locais) devem ser exportados. Inclua "global", pois muitos eventos no Log de Atividades são eventos globais.
* Quanto tempo o Log de Atividades deve ser mantido em uma Conta de Armazenamento.
    - Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.
    - Se as políticas de retenção estiverem definidas, mas o armazenamento de logs em uma Conta de Armazenamento estiver desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou Log Analytics forem selecionadas), as políticas de retenção não terão efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. A exclusão começa à meia-noite UTC, mas observe que pode levar até 24 horas para que os logs sejam excluídos da conta de armazenamento.

Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura para emitir logs. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

Essas configurações podem ser definidas por meio da opção "Exportar" na folha do Log de Atividades no portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI. Uma assinatura pode ter somente um perfil de log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar os perfis de log usando o Portal do Azure

Você pode transmitir o log de atividades para um hub de eventos ou armazená-los em uma conta de armazenamento usando a opção "Exportar para o hub de eventos" no portal do Azure.

1. Navegue até **Log de Atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de Atividades no portal](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Clique no botão **Exportar para o Hub de Eventos** na parte superior da lâmina.

    ![Botão Exportar no portal](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. Na folha que aparece, você pode selecionar:
   * regiões para as quais você deseja exportar eventos
   * a conta de armazenamento na qual você deseja salvar os eventos
   * o número de dias para manter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
   * o namespace do Barramento de Serviço no qual você deseja que um Hub de Eventos seja criado para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar os perfis de log usando Cmdlets do Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente

```powershell
Get-AzLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log

```powershell
Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Propriedade | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| Name |Sim |Nome de seu perfil de log. |
| StorageAccountId |Não  |ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId |Não  |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Location |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Category |Não  |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log

```powershell
Remove-AzLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Configurar perfis de log usando a CLI do Azure

#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

A propriedade `name` deve ser o nome de seu perfil de log.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Para obter a documentação completa para criar um perfil do monitor com a CLI, consulte a[Referência de comando da CLI](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Remover um perfil de log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Log de Atividades (anteriormente conhecido como Logs de Auditoria)](../../azure-resource-manager/resource-group-audit.md)
* [Transmissão do Log de Atividades do Azure para os Hubs de Eventos](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
