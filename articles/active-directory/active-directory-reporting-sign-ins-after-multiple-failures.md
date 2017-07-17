---
title: "Entradas após várias falhas"
description: "Um relatório que indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161
ms.contentlocale: pt-br
ms.lasthandoff: 11/17/2016

---
# Entradas após várias falhas
<a id="sign-ins-after-multiple-failures" class="xliff"></a>
Este relatório indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas. Entre as possíveis causas estão:

* O usuário esqueceu a senha</li><li>O usuário for vítima de uma ataque de força bruta de detecção de senha bem-sucedido

Os resultados desse relatório mostrarão o número de tentativas consecutivas de entrada com falha feitas antes da entrada bem-sucedida e um carimbo de data/hora associado à primeira entrada bem-sucedida.

**Configurações do Relatório**: é possível configurar o número mínimo de tentativas consecutivas de entrada com falha que devem ocorrer antes que ele possa ser exibido no relatório. Quando você fizer alterações nessa configuração, é importante observar que essas alterações não serão aplicadas a qualquer entrada com falha existente exibido no momento no relatório existente. Todavia, elas serão aplicadas a todas as entradas futuras. Alterações a esse relatório só podem ser feitas por administradores licenciados.

![Entradas após várias falhas](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)


