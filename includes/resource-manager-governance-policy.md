---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740522"
---
As [Políticas do Azure](/azure/azure-policy/) ajudam a garantir que todos os recursos da assinatura atendam aos padrões corporativos. Utilize as políticas para reduzir os custos, restringindo as opções de implantação apenas a esses tipos de recursos e SKUs aprovados. Você define as regras e ações para os recursos e essas regras serão aplicadas automaticamente durante a implantação. Por exemplo, você poderá controlar os tipos de recursos que serão implantados. Ou, poderá restringir os locais aprovados para recursos. Algumas políticas negam uma ação e, algumas políticas configuram auditoria de uma ação.

A política é complementar para RBAC (controle de acesso baseado em função). O RBAC concentra-se no acesso de usuário e é um sistema de permissão explícita e negação padrão. A política concentra-se nas propriedades de recursos durante e após a implantação. É um sistema de negação explícita e permissão padrão.

Há dois conceitos para entender com políticas - *definições de política* e *atribuições de política*. Uma definição de política descreve as condições de gerenciamento que você deseja aplicar. Uma atribuição de política coloca uma definição de política em ação para um escopo específico.

![Atribuir políticas](./media/resource-manager-governance-policy/policy-concepts.png)

O Azure fornece várias definições de política interna que podem ser utilizadas sem qualquer modificação. Você passa os valores dos parâmetros para especificar os valores permitidos no escopo. Se a definição de política interna não atender aos requisitos, você poderá [criar definições de políticas personalizadas](../articles/azure-policy/create-manage-policy.md).
