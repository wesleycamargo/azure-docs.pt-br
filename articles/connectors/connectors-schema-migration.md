---
title: Migrar aplicativos para o esquema mais recente – Aplicativo Lógico do Azure | Microsoft Docs
description: Como migrar os aplicativos lógicos para a última versão do esquema
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 8a6925d79b225a34d980472d4fb3241ab9eb1017
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127404"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrar os aplicativos lógicos para a última versão do esquema

Para mover os aplicativos lógicos existentes para o esquema mais recente, siga estas etapas: 

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

2. No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Atualizar esquema**.

   > [!NOTE]
   > Quando você seleciona **Atualizar esquema**, o Aplicativo Lógico do Azure executa automaticamente as etapas de migração e informa a você a saída do código. Você pode usar essa saída para atualizar sua definição de aplicativo lógico. No entanto, certifique-se de seguir as práticas recomendadas, conforme descrito na seção **Práticas recomendadas** a seguir.

   ![Atualizar esquema](./media/connectors-schema-migration/update-schema.png)

   A página Atualizar esquema aparece e mostra um link para um documento que descreve os aperfeiçoamentos no novo esquema.

## <a name="best-practices"></a>Práticas recomendadas

Aqui estão algumas práticas recomendadas para migrar os aplicativos lógicos para a última versão do esquema:

* Copie o script migrado para um novo aplicativo lógico. Não substitua a versão antiga até você concluir o teste e confirmar que seu aplicativo migrado funciona conforme o esperado.

* Teste o aplicativo lógico **antes** de colocá-lo em produção.

* Após concluir a migração, comece a atualizar os aplicativos lógicos para usar as [APIs gerenciadas](../connectors/apis-list.md) sempre que possível. Por exemplo, comece a usar o Dropbox v2 em todos os lugares em que você usa o DropBox v1.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [migrar manualmente os aplicativos lógicos](../logic-apps/logic-apps-schema-2015-08-01.md)
