---
title: "Localizações nomeadas no Azure Active Directory | Microsoft Docs"
description: "Ao configurar localizações nomeadas, você pode evitar que endereços IP que pertencem à sua organização gerem falsos positivos para o tipo de evento de risco Viagem impossível a localizações atípicas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Localizações nomeadas no Azure Active Directory

Localizações nomeadas é um recurso do Azure Active Directory que permite rotular os intervalos de endereços IP confiáveis em suas organizações. Em seu ambiente, use localizações nomeadas no contexto da detecção de [eventos de risco](active-directory-reporting-risk-events.md) para reduzir o número de falsos positivos relatados para o tipo de evento de risco *Viagem impossível a localizações atípicas*. 




## <a name="configuration"></a>Configuração

**Para configurar uma localização nomeada:**

1. Entre no [portal do Azure](https://portal.azure.com) como administrador global.

2. No painel esquerdo, clique em **Azure Active Directory**.

    ![Localizações nomeadas](./media/active-directory-named-locations/01.png)

3. Na folha **Azure Active Directory**, na seção **Segurança**, clique em **Acesso Condicional**.

    ![Localizações nomeadas](./media/active-directory-named-locations/05.png)


4. Na folha **Acesso Condicional**, na seção **Gerenciar**, clique em **Localizações nomeadas**.

    ![Localizações nomeadas](./media/active-directory-named-locations/06.png)


5. Na folha **Localizações nomeadas**, no menu na parte superior, clique em **Novas localizações**.

    ![Localizações nomeadas](./media/active-directory-named-locations/07.png)


6. Na folha **Novo**, realize as seguintes etapas:

    ![Localizações nomeadas](./media/active-directory-named-locations/08.png)

    a. Na caixa de texto **Nome**, digite um nome para a localização nomeada.

    b. No **intervalo IP** caixa de texto, digite um intervalo de IP. O intervalo de IP precisa estar no formato *CIDR* (Roteamento Entre Domínios Sem Classe).  

    c. Clique em **Criar**.



## <a name="what-you-should-know"></a>O que você deve saber

**Atualizações em massa** – ao criar ou atualizar localizações nomeadas, para atualizações em massa, você pode carregar ou baixar um arquivo CSV com os intervalos de IP. Um carregamento adiciona os intervalos de IP no arquivo à lista em vez de substituir a lista.

![Localizações nomeadas](./media/active-directory-named-locations/09.png)


**Limitações** – você pode definir um máximo de 60 localizações nomeadas com um intervalo de IP atribuído a cada uma delas. Se você tiver apenas uma localização nomeada configurada, poderá definir até 500 intervalos de IP para ela.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre:

- Eventos de risco, consulte [Eventos de risco do Azure Active Directory](active-directory-reporting-risk-events.md)


