---
title: Redes nomeadas no Azure Active Directory | Microsoft Docs
description: "Ao configurar redes nomeadas, você pode evitar ter endereços IP que pertençam à sua organização gerando a criação de falsos positivos para as entradas de várias regiões geográficas e as entradas de endereços IP com atividade suspeita."
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
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Redes nomeadas no Azure Active Directory

> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-known-networks-azure-portal.md)
> * [Portal clássico do Azure](active-directory-known-networks.md)
>
>


Azure Active Directory cria um registro para cada detectado [evento de risco](active-directory-identity-protection-risk-events.md). Com as informações de evento de risco disponíveis por meio dos relatórios de segurança do Azure Active Directory, você pode obter informações sobre a probabilidade de contas de usuário comprometidas em seu ambiente.   

É possível que o Azure Active Directory detecta falsos positivos do *viagem impossível a localizações atípicas* e *entradas de endereços IP com atividade suspeita* [tipos de evento de risco](active-directory-reporting-risk-events.md#risk-event-types) para endereços IP que realmente pertencem a sua organização. 

Por exemplo, isso pode ocorrer quando:

- Um usuário em um escritório em Boston ao conectar-se remotamente a um data center em San Francisco gera um evento de risco *entradas de várias regiões geográficas*

- Um usuário de sua organização que tenta entrar diversas vezes com uma senha incorreta gera um evento de risco *entradas de endereços IP com atividade suspeita*

Para impedir que esses casos gerem eventos de risco enganadores, você deverá adicionar intervalos de endereços IP nomeados à lista de endereços IP públicos de sua organização.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para adicionar os intervalos de endereços IP públicos da sua organização, execute as seguintes etapas:

1. Entre no portal de gerenciamento do Azure.

2. No painel esquerdo, clique em **Active Directory**.

    ![Redes conhecidas](./media/active-directory-known-networks-azure-portal/01.png)

3. Na folha do diretório, no **gerenciar** seção, clique em **redes nomeadas**.

    ![Redes conhecidas](./media/active-directory-known-networks-azure-portal/02.png)


4. Clique em **adicionar local**

    ![Redes conhecidas](./media/active-directory-known-networks-azure-portal/03.png)

5. Sobre o **adicionar** folha, fazer:

    ![Redes conhecidas](./media/active-directory-known-networks-azure-portal/04.png)

    a. Na caixa de texto **Nome**, digite um nome.

    b. No **intervalo IP** caixa de texto, digite um intervalo de IP. O intervalo de IP precisa estar no formato CIDR. Atualização em massa, você pode carregar um arquivo CSV com os intervalos de IP. Um carregamento adiciona os intervalos de IP no arquivo à lista em vez de substituir a lista.

    c. Clique em **Criar**.


**Recursos adicionais:**

* [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)
* [Entradas de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Entradas de várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)

