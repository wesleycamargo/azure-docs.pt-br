---
title: Métricas com suporte no Azure Monitor no Azure Stack | Microsoft Docs
description: Saiba mais sobre as métricas com suporte para o Azure Monitor no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 109ac1b0d9f52211f05e5fc1b177aa775ba66aae
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889939"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Métricas compatíveis com o Azure Monitor no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode recuperar as métricas do Azure monitor no Azure Stack no mesmo Azure global. É possível que suas medidas no portal de obtê-los da API REST ou consultá-los com o PowerShell ou CLI.

As tabelas a seguir listam as métricas disponíveis com o pipeline de métrica do Azure Monitor no Azure Stack. Para consultar e acessar essas métricas, você precisará de **2018-01-01** versão da versão da api do perfil de API. Para obter mais informações sobre perfis de API e o Azure Stack, consulte [perfis de versão da API de gerenciar no Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média | A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais | Sem dimensões |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Capacidade utilizada | Bytes | Média | Capacidade utilizada pela conta | Sem dimensões |
| Transações | Transações | Contagem | Total | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média | A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Porcentagem | Média | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Capacidade do Blob | Bytes | Total | A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes. | BlobType |
| BlobCount | Contagem de Blobs | Contagem | Total | O número de Blobs no serviço Blob da conta de armazenamento. | BlobType |
| ContainerCount | Contagem de Contêineres de Blobs | Contagem | Média | O número de contêineres no serviço Blob da conta de armazenamento. | Sem dimensões |
| Transações | Transações | Contagem | Total | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média | A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Porcentagem | Média | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Capacidade da Tabela | Bytes | Média | A quantidade de armazenamento utilizada pelo serviço Tabela da conta de armazenamento em bytes. | Sem dimensões |
| TableCount | Contagem de Tabelas | Contagem | Média | O número de tabelas no serviço Tabela da conta de armazenamento. | Sem dimensões |
| TableEntityCount | Contagem de Entidades de Tabela | Contagem | Média | O número de entidades de tabelas no serviço Tabela da conta de armazenamento. | Sem dimensões |
| Transações | Transações | Contagem | Total | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média | A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Porcentagem | Média | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | DESCRIÇÃO | Dimensões |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Capacidade da Fila | Bytes | Média | A quantidade de armazenamento utilizada pelo serviço Fila da conta de armazenamento em bytes. | Sem dimensões |
| QueueCount | Contagem de Filas | Contagem | Média | O número de filas no serviço Fila da conta de armazenamento. | Sem dimensões |
| QueueMessageCount | Contagem de Mensagens da Fila | Contagem | Média | O número aproximado de mensagens da fila no serviço Fila da conta de armazenamento. | Sem dimensões |
| Transações | Transações | Contagem | Total | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta. | ResponseType, GeoType, ApiName |
| Entrada | Entrada | Bytes | Total | A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. | GeoType, ApiName |
| Saída | Saída | Bytes | Total | A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. | GeoType, ApiName |
| SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média | A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. | GeoType, ApiName |
| Disponibilidade | Disponibilidade | Porcentagem | Média | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. | GeoType, ApiName |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [monitorar o Azure no Azure Stack](azure-stack-metrics-azure-data.md).