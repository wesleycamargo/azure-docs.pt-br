---
title: Conectar uma conta do Google Cloud Platform ao Gerenciamento de Custos do Azure | Microsoft Docs
description: "Conectar uma conta do Google Cloud Platform para exibição dos dados de uso e custo em relatórios do Gerenciamento de Custos."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 81a38f471ee1f2f8064a956eca121fd0e6feb235
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Conectar uma conta do Google Cloud Platform

É possível conectar a sua conta existente do Google Cloud Platform ao Gerenciamento de Custos do Azure. Após conectar a sua conta ao Gerenciamento de Custos, os dados de uso e custo estarão disponíveis nos relatórios de Gerenciamento de Custos. Este artigo ajuda a configurar e conectar sua conta do Google com o Gerenciamento de Custos.

## <a name="collect-project-information"></a>Coletar informações do projeto

Inicie coletando informações sobre seu projeto.

1. Entre no console do Google Cloud Platform em [https://console.cloud.google.com](https://console.cloud.google.com).
2. Revise as informações do projeto que deseja para o Gerenciamento de Custos e anote o **Nome do Projeto** e a **Project ID**. Mantenha as informações acessíveis para as etapas posteriores.  
    ![Console do Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se a cobrança não estiver habilitada e vinculada ao seu projeto, crie uma conta de cobrança. Para obter mais informações, consulte [Criar uma nova conta de cobrança](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Habilitar a exportação de cobrança de bucket

O Gerenciamento de Custos recupera seus dados de cobrança do Google de um bucket de armazenamento. Mantenha as informações de **Nome do Bucket** e **Prefixo do Relatório** acessíveis para uso posterior durante o registro do Gerenciamento de Custos.

Usar o Google Cloud Storage para armazenar relatórios de uso incorre em taxas mínimas. Para obter mais informações, consulte [Preços do Armazenamento em Nuvem](https://cloud.google.com/storage/pricing).

1. Se você não habilitou a exportação de cobrança para um arquivo, siga as instruções em [Como habilitar a exportação de cobrança para um arquivo](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). É possível usar o formato de exportação de cobrança JSON ou CSV.
2. Caso contrário, no console do Google Cloud Platform, navegue para **Cobrança** > **Exportação de cobrança**. Anote o **Nome do Bucket** e o **Prefixo do Relatório** de cobrança.  
    ![Exportação de cobrança](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Habilitar APIs do Google Cloud Platform

Para coletar informações de ativo e uso, o Gerenciamento de Custos precisa das seguintes APIs do Google Cloud Platform habilitadas:

- API do BigQuery
- SQL do Google Cloud
- API do Google Cloud Datastore
- Google Cloud Storage
- API JSON do Google Cloud Storage
- API do Google Compute Engine

### <a name="enable-or-verify-apis"></a>Habilitar ou verificar APIs

1. No console do Google Cloud Platform, selecione o projeto que deseja registrar no Gerenciamento de Custos.
2. Navegue até **APIs e Serviços** > **Biblioteca**.
3. Use a pesquisa para localizar cada API listada anteriormente.
4. Para cada API, verifique se está indicada como **API habilitada**. Caso contrário, clique em **HABILITAR**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Adicionar uma conta do Google Cloud ao Gerenciamento de Custos

1. Abra o portal do Cloudyn a partir do Portal do Azure ou navegue até [https://azure.cloudyn.com](https://azure.cloudyn.com/) e faça logon.
2. Clique em **Configurações** (símbolo de engrenagem) e, em seguida, selecione **Contas do Cloud**.
3. Em **Gerenciamento de Contas**, selecione a guia **Contas do Google** e, em seguida, clique em **Adicionar novo +**.
4. Em **Nome da Conta do Google**, insira o endereço de email da conta de cobrança e clique em **Próximo**.
5. Na caixa de diálogo de autenticação do Google, selecione ou insira uma Conta do Google e, em seguida, **PERMITIR** cloudyn.com acessar sua conta.
6. Adicione as informações do projeto da solicitação anotada anteriormente. As informações incluem a **ID do Projeto**, o nome do **Projeto**, nome do bucket de **cobrança** e prefixo do relatório do **arquivo de cobrança** e, em seguida, clique em **Salvar**.  
    ![Adicionar projeto do Google](./media/connect-google-account/add-project.png)

Sua conta do Google aparecerá na lista de contas e deverá indicar **Autenticada**. Na conta, o nome e a ID do projeto do Google deverão aparecer e terem um símbolo de marca de seleção verde. O Status da Conta deverá indicar **Concluído**.

Dentro de algumas horas, os relatórios de Gerenciamento de Custos mostrarão as informações de uso e custo do Google.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Gerenciamento de Custos do Azure pelo Cloudyn, continue com o tutorial [Analisar o uso e os custos](./tutorial-review-usage.md) para Gerenciamento de Custos.
