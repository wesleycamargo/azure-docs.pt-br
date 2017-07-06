---
title: "Redes conhecidas no portal clássico do Azure | Microsoft Docs"
description: "Configurando redes conhecidas, você pode evitar ter endereços IP que pertencem à sua organização incluídos nos relatórios Entradas de várias regiões geográficas e Entradas de endereços IP com atividade suspeita."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4
ms.contentlocale: pt-br
ms.lasthandoff: 01/19/2017


---
# <a name="known-networks"></a>Redes conhecidas

> [!div class="op_single_selector"]
> * [Portal clássico do Azure](active-directory-known-networks.md)
> * [Portal do Azure](active-directory-known-networks-azure-portal.md)
> 
> 


Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

É possível que os relatórios "*Entradas de várias regiões geográficas*" e "*Entradas de endereços IP com atividade suspeita*" sinalizem incorretamente os endereços IP que realmente pertencem à sua organização. 

Por exemplo, isso pode ocorrer quando: 

* Um usuário em um escritório em Boston ao conectar-se remotamente a um data center em San Francisco aciona o relatório "Entradas de várias regiões geográficas" 
* Um usuário de sua organização que tenta entrar diversas vezes com uma senha incorreta aciona o relatório "Entradas de endereços IP com atividade suspeita” 

Para impedir que esses casos gerem relatórios de segurança enganadores, você deverá adicionar intervalos de endereços IP conhecidos à lista de endereços IP públicos de sua organização.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para adicionar os intervalos de endereços IP públicos da sua organização, execute as seguintes etapas:

1. Entre no [portal de gerenciamento do Azure](https://manage.windowsazure.com).

2. No painel esquerdo, clique em **Active Directory**. 

    ![Redes conhecidas](./media/active-directory-known-networks/known-netwoks-01.png)

3. Na guia **Diretório** , selecione o diretório.

4. No menu na parte superior, clique em **Configurar**. 

    ![Redes conhecidas](./media/active-directory-known-networks/known-netwoks-02.png)

5. Na guia Configurar, vá para **os intervalos de endereços IP públicos da sua organização** 

    ![Redes conhecidas](./media/active-directory-known-networks/known-netwoks-03.png)

6. Clique em **Adicionar Intervalos de Endereços IP conhecidos**.

7. Adicione seus intervalos de endereços na caixa de diálogo que aparece e clique no botão de verificação quando você terminar. 

    ![Redes conhecidas](./media/active-directory-known-networks/known-netwoks-04.png)

**Recursos adicionais:**

* [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)
* [Entradas de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Entradas de várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)


