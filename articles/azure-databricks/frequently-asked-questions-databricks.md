---
title: 'Azure Databricks: ajuda e perguntas comuns | Microsoft Docs'
description: "Obtenha respostas a perguntas comuns e informações de solução de problemas sobre o Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Versão prévia do Azure Databricks: ajuda e perguntas comuns

Este artigo lista as principais dúvidas que você pode ter relacionadas ao Azure Databricks. Também lista alguns problemas comuns que você pode enfrentar durante o uso do Azure Databricks. Para obter mais informações sobre o Azure Databricks, consulte [O que é o Azure Databricks?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Perguntas comuns

Esta seção lista as perguntas comuns relacionadas ao Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Posso usar minhas próprias chaves para a criptografia local? 
Na versão atual, não há suporte para o uso de chaves próprias do Azure Key Vault. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Posso usar VNETs do Azure com o Azure Databricks?
Uma nova VNET é criada como parte do provisionamento do Azure Databricks. Como parte dessa liberação, não é possível usar sua própria VNET do Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Como fazer para acessar o Azure Data Lake Store em um bloco de anotações? 

1. No Azure Active Directory, provisione uma Entidade de Serviço e registre sua chave.
2. Atribua as permissões necessárias à Entidade de Serviço no Azure Data Lake Store.
3. Para acessar um arquivo no Azure Data Lake Store, use as credenciais da Entidade de Serviço no bloco de anotações.

Para obter mais informações, consulte [Usar o Data Lake Store com o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Solucionar problemas

Esta seção descreve como solucionar problemas comuns com o Azure Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: A assinatura não está registrada para usar o namespace ‘Microsoft.Databricks’

**Mensagem de erro**

Essa assinatura não está registrada para usar o namespace ‘Microsoft.Databricks’. Consulte https://aka.ms/rps-not-found para saber como registrar assinaturas. (Código: MissingSubscriptionRegistration)

**Solução**

1. Vá para o [portal do Azure](https://portal.azure.com).
2. Clique em **Assinaturas**, na assinatura que você está usando e, em seguida, clique em **Provedores de recursos**. 
3. Na lista de provedores de recursos, em relação a **Microsoft.Databricks**, clique em **Registrar**. Você deve ter a função de Colaborador ou Proprietário na assinatura para registrar o provedor de recursos.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: sua conta {email} não tem a função Proprietário ou Colaborador no recurso do espaço de trabalho do Databricks no portal do Azure.

**Mensagem de erro**

Sua conta {email} não tem a função Proprietário ou Colaborador no recurso do espaço de trabalho do Databricks no portal do Azure. Esse erro também poderá ocorrer se você for um usuário convidado no locatário. Solicite ao administrador para conceder acesso a você ou adicioná-lo como um usuário A diretamente no espaço de trabalho do Databricks. 

**Solução**

A seguir, estão algumas soluções para esse problema:

* Para inicializar o locatário, você deve estar conectado como um usuário normal do locatário, não um usuário convidado. Você também deve ter a função Colaborador no recurso do espaço de trabalho do Databricks. Conceda o acesso a um usuário por meio da guia **Controle de acesso (IAM)** no espaço de trabalho do Azure Databricks do portal do Azure.

* Esse erro também poderá ocorrer se o nome de domínio de email for atribuído a vários Active Directories. Para contornar esse problema, crie um novo usuário no Active Directory que contém a assinatura com o seu espaço de trabalho do Databricks.

    a. No portal do Azure, vá para o Azure Active Directory, clique em **Usuários e grupos** e clique em **Adicionar um usuário**.

    b. Adicione um usuário com um email de `@<tenant_name>.onmicrosoft.com` em vez do email @< seu_domínio >. Você pode encontrar o <nome_de_locatário>.onmicrosoft.com associado ao Active Directory nos **Domínios personalizados** no Azure Active Directory no portal do Azure.
    
    c. Conceda essa nova função de usuário de **Colaborador** no recurso do espaço de trabalho do Databricks.
    
    d. Faça logon no portal do Azure com o novo usuário e localize o espaço de trabalho do Databricks.
    
    e. Inicie o espaço de trabalho do Databricks como esse usuário.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: sua conta {email} não foi registrada no Databricks 

**Solução**

Se você não criou o espaço de trabalho e foi adicionado como um usuário do espaço de trabalho, contate a pessoa que criou o espaço de trabalho para adicioná-lo usando o Console de Administração do Azure Databricks. Para obter instruções, consulte [Adicionando e gerenciando usuários](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Se você criou o espaço de trabalho e ainda recebe esse erro, tente clicar em “Inicializar Espaço de Trabalho” novamente no portal do Azure.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: falha ao iniciar o provedor de nuvem (PublicIPCountLimitReached): foi encontrado um erro do provedor de nuvem durante a configuração do cluster

**Mensagem de erro**

Falha ao iniciar o Provedor de Nuvem: foi encontrado um erro do provedor de nuvem durante a configuração do cluster. Consulte o guia do Databricks para obter mais informações. Código de erro do Azure: PublicIPCountLimitReached. Mensagem de erro do Azure: não é possível criar mais de 60 endereços IP públicos para esta assinatura nesta região.

**Solução**

Os clusters do Azure Databricks usam um endereço IP público por nó. Caso sua assinatura já tenha usado todos os seus IPs públicos, [solicite um aumento da cota](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Escolha **Cota** como o **Tipo de problema**, **Rede: ARM** como o **Tipo de cota** e solicitar um aumento de cota do endereço IP público em **Detalhes**. Por exemplo, se o limite atualmente for 60 e você deseja criar um cluster de 100 nós, solicite um aumento de limite para 160.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: falha ao iniciar o provedor de nuvem (MissingSubscriptionRegistration): foi encontrado um erro do provedor de nuvem durante a configuração do cluster

**Mensagem de erro**

Falha ao iniciar o Provedor de Nuvem: foi encontrado um erro do provedor de nuvem durante a configuração do cluster. Consulte o guia do Databricks para obter mais informações.
Código de erro do Azure: mensagem de erro do Azure MissingSubscriptionRegistration: a assinatura não está registrada para usar o namespace 'Microsoft.Compute'. Consulte https://aka.ms/rps-not-found para saber como registrar assinaturas

**Solução**

1. Vá para o [portal do Azure](https://portal.azure.com).
2. Clique em **Assinaturas**, na assinatura que você está usando e, em seguida, clique em **Provedores de recursos**. 
3. Na lista de provedores de recursos, em relação a **Microsoft.Compute**, clique em **Registrar**. Você deve ter a função de Colaborador ou Proprietário na assinatura para registrar o provedor de recursos.

Consulte [Provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md) para obter instruções mais detalhadas.

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory da versão 2, consulte os tutoriais a seguir:

- [Guia de início rápido: Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [O que é o Azure Databricks?](what-is-azure-databricks.md)

