---
title: Solução de Cofre de Chaves do Azure no Log Analytics | Microsoft Docs
description: Você pode usar a solução de Cofre de Chaves do Azure no Log Analytics para examinar logs do Cofre de Chaves do Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: richrund

---
# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Solução de Cofre de Chaves do Azure (Visualização) no Log Analytics
> [!NOTE]
> Esta é uma [solução de visualização](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

Você pode usar a solução de Cofre de Chaves do Azure no Log Analytics para examinar logs AuditEvent do Cofre de Chaves do Azure.

Você pode habilitar o log de eventos de auditoria para o Cofre de Chaves do Azure. Esses logs são gravados no Armazenamento de Blobs, no qual eles podem então ser indexados pelo Log Analytics para pesquisa e análise.

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as instruções a seguir para instalar e configurar a solução de Cofre de Chaves do Azure:

1. Habilite o [log de diagnóstico para os recursos do Cofre de Chaves](../key-vault/key-vault-logging.md) que você deseja monitorar
2. Configure o Log Analytics para ler os logs do Armazenamento de Blobs usando o processo descrito em [Arquivos JSON no armazenamento de blobs](log-analytics-azure-storage-json.md).
3. Habilite a solução de Cofre de Chaves do Azure usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Veja os detalhes da coleta de dados do Cofre de Chaves do Azure
A solução de Cofre de Chaves do Azure coleta logs de diagnóstico do Armazenamento de Blobs do Azure para o Cofre de Chaves do Azure.
Nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Cofre de Chaves do Azure.

| Plataforma | Agente direto | Agente do SCOM (System Center Operations Manager) | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| As tabelas |![Não](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Não](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Sim](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![Não](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Não](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 minutos |

## <a name="use-azure-key-vault"></a>Usar o Cofre de Chaves do Azure
Depois de instalar a solução, você pode exibir o resumo dos diferentes status da solicitação ao longo do tempo para seus Cofres de Chaves monitorados usando o bloco **Cofre de Chaves do Azure** na página **Visão Geral** do Log Analytics.

![imagem do bloco Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Depois de clicar no bloco **Visão Geral**, você pode exibir resumos dos seus logs e então aprofundar-se nos detalhes das seguintes categorias:

* Volume de todas as operações do Cofre de Chaves ao longo do tempo
* Volumes de operação com falha ao longo do tempo
* Latência operacional média por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1.000 ms e uma lista das operações que levam mais de 1.000 ms

![imagem do painel Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagem do painel Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para exibir detalhes de qualquer operação
1. Na página **Visão Geral**, clique no bloco **Cofre de Chaves do Azure**.
2. Na painel **Cofre de Chaves do Azure**, examine as informações resumidas em uma das folhas e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página pesquisa de log.
   
    Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="log-analytics-records"></a>Registros do Log Analytics
A solução de Cofre de Chaves do Azure analisa os registros que têm um tipo de **KeyVaults** que são coletados de [logs de AuditEvent](../key-vault/key-vault-logging.md) no Diagnóstico do Azure.  As propriedades desses registros são descritas na tabela a seguir.  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |Endereço IP do cliente que fez a solicitação |
| Categoria |Para logs do Cofre da Chave, AuditEvent é o único valor disponível. |
| CorrelationId |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| DurationMs |Tempo necessário para atender à solicitação da API REST, em milissegundos. Isso não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| HttpStatusCode_d |Código de status HTTP retornado pela solicitação |
| Id_s |ID exclusiva da solicitação |
| Identity_o |Identidade do token que foi apresentado ao fazer a solicitação da API REST. Isso geralmente é um "usuário", uma "entidade de serviço" ou uma combinação "usuário+appId" como no caso de uma solicitação resultante de um cmdlet do Azure PowerShell. |
| OperationName |Nome da operação conforme documentado no [Registro em Log do Cofre de Chaves do Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Versão da API REST solicitada pelo cliente |
| RemoteIPLatitude |Latitude do cliente que fez a solicitação |
| RemoteIPLongitude |Longitude do cliente que fez a solicitação |
| RemoteIPCountry |País do cliente que fez a solicitação |
| RequestUri_s |O URI da solicitação |
| Recurso |Nome do cofre de chaves |
| ResourceGroup |Grupo de recursos do cofre de chaves |
| ResourceId |ID do Recurso do Gerenciador de Recursos do Azure. Para os logs do Cofre da Chave, isso sempre será a ID do recurso do Cofre da Chave. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |Status HTTP |
| ResultType |Resultado da solicitação da API REST |
| SubscriptionId |A ID da assinatura do Azure que contém o Cofre de Chaves |

## <a name="next-steps"></a>Próximas etapas
* Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Cofre de Chaves do Azure.

<!--HONumber=Oct16_HO2-->


