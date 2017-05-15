---
title: "Criar um aplicativo de funções no Portal do Azure | Microsoft Docs"
description: "Crie um novo aplicativo de funções no Serviço de Aplicativo do Azure por meio do portal."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fb8eaa4bb122cd6bb7e3f72bb5f7000103bffb29
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar um aplicativo de funções no portal do Azure

Os Aplicativos do Azure Functions usam a infraestrutura do Serviço de Aplicativo do Azure. Este tópico mostra como criar um aplicativo de funções no portal do Azure. Um aplicativo de funções é o contêiner que hospeda a execução de funções individuais. Quando você cria um aplicativo de funções no plano de hospedagem do Serviço de Aplicativo, o aplicativo de funções pode usar todos os recursos do Serviço de Aplicativo.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Ao criar um aplicativo de funções, forneça um **Nome de aplicativo** válido, que pode conter apenas letras, números e hifens. Sublinhado (**_**) não é um caractere permitido. 

Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. O nome da sua conta de armazenamento deve ser exclusivo no Azure. 

Depois de criar o aplicativo de funções, é possível criar funções individuais em uma ou mais linguagens diferentes. Crie funções [usando o portal](functions-create-first-azure-function.md#create-function), [a implantação contínua](functions-continuous-deployment.md) ou [carregando com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).  

## <a name="service-plans"></a>Planos de serviço

O Azure Functions tem dois planos de serviço diferentes: o plano de Consumo e o plano do Serviço de Aplicativo. O plano de Consumo automaticamente aloca potência de computação quando seu código está em execução, escala horizontalmente conforme a necessidade para tratar da carga e reduz horizontalmente quando o código não está em execução. O plano do Serviço de Aplicativo fornece ao aplicativo de funções o acesso a todos os recursos do Serviço de Aplicativo. É necessário escolher o plano de serviço quando o aplicativo de funções é criado, e ele não pode ser alterado. Para obter mais informações, consulte [Escolher um plano de hospedagem do Azure Functions](functions-scale.md).

Se você estiver planejando executar funções do JavaScript em um plano do Serviço de Aplicativo, deverá escolher um plano com menos núcleos. Para obter mais informações, consulte a [Referência do JavaScript para funções](functions-reference-node.md#choose-single-core-app-service-plans). 

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar um aplicativo de funções no Serviço de Aplicativo, é necessário criar ou se vincular a uma conta do Armazenamento do Azure de uso geral que dá suporte ao armazenamento de Blobs, Filas e Tabelas. Internamente, o Functions usa o Armazenamento para operações como gerenciamento de gatilhos e log de execuções de função. Algumas contas de armazenamento não dão suporte a filas e tabelas, como contas de armazenamento somente blob, Armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas são filtradas na folha Conta de Armazenamento durante a criação de um aplicativo de funções.

>[!NOTE]
>Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

Para saber mais sobre tipos de conta de armazenamento, consulte [Introduzindo os Serviços de Armazenamento do Azure] (../storage/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]




