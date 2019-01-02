---
title: Conectar o Serviço de Kubernetes do Azure (AKS) ao Banco de Dados do Azure para PostgreSQL
description: Saiba mais sobre a conexão do Serviço de Kubernetes do Azure com o Banco de Dados do Azure para PostgreSQL
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 770a583ad09ec1aaa8191f5dafb3db61b2386e68
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166227"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Conectar o Serviço de Kubernetes do Azure e o Banco de Dados do Azure para PostgreSQL

O Serviço de Kubernetes do Azure (AKS) fornece cluster de Kubernetes gerenciado que você pode usar no Azure. Abaixo estão algumas opções a considerar ao usar o AKS e o Banco de Dados do Azure para PostgreSQL juntos para criar um aplicativo.


## <a name="accelerated-networking"></a>Redes aceleradas
Use VMs subjacentes habilitadas para rede acelerada em seu cluster AKS. Quando a rede acelerada está habilitada em uma VM, há menor latência, menor oscilação e menor utilização da CPU na VM. Saiba mais sobre como funciona a rede acelerada, as versões de sistema operacional compatíveis e as instâncias de VM compatíveis para [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de novembro de 2018, o AKS oferece suporte à rede acelerada nessas instâncias de VM compatíveis. A rede acelerada é ativada por padrão nos novos clusters do AKS que usam essas VMs.

Você pode confirmar se o seu cluster AKS acelerou a rede:
1. Vá para o portal do Azure e selecione seu cluster AKS.
2. Selecione a guia Propriedades.
3. Copie o nome do **Grupo de Recursos de Infraestrutura**.
4. Use a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione uma VM nesse grupo de recursos.
6. Vá para a guia VM **Rede**.
7. Confirme se a **Rede acelerada** está "Ativada".


## <a name="open-service-broker-for-azure"></a>Instalar o Open Service Broker para o Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) permite provisionar serviços do Azure diretamente do Kubernetes ou do Cloud Foundry. É uma implementação da API do [Open Service Broker](https://www.openservicebrokerapi.org/) para o Azure.

Com o OSBA, você pode criar um Banco de Dados do Azure para PostgreSQL e associá-lo ao seu cluster AKS usando a linguagem nativa do Kubernetes. Saiba mais sobre como usar o OSBA e o Banco de Dados do Azure para PostgreSQL juntos na [página do GitHub do OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Pool de conexão
O pooler de conexão minimiza o custo e o tempo associado criando e fechando novas conexões com o banco de dados. O pool é uma coleção de conexões que pode ser reutilizada. 

Há vários poolers de conexão, que você pode usar com PostgreSQL. Um deles é [PgBouncer](https://pgbouncer.github.io/). O Registro de Contêiner do Azure, nós fornecemos um PgBouncer em contêiner leve que pode ser usado em um sidecar para conexões de pool de AKS para o Banco de Dados do Azure para PostgreSQL. Visite a [página de hub do docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) para saber como acessar e usar essa imagem. 


## <a name="next-steps"></a>Próximas etapas
-  [Criar um cluster do Serviço de Kubernetes do Azure](../aks/kubernetes-walkthrough.md)
