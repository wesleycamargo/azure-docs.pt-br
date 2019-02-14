---
title: Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a sincronização de atributos usando a tarefa de solução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180818"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Solucionar problemas de um atributo que não está sincronizando no Azure AD Connect

## <a name="recommended-steps"></a>**Etapas Recomendadas**

Antes de investigar problemas de sincronização de atributo, vamos entender o processo de sincronização do **Azure AD Connect**:

  ![Processo de Sincronização do Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço do Conector, uma tabela no banco de dados.
* **MV:** Metaverso, uma tabela no banco de dados.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Etapas de Sincronização**

* Importar do AD: Objetos do Active Directory são trazidos para o AD CS.

* Importar do AAD: Objetos do Azure Active Directory são trazidos para o AAD CS.

* Sincronização: **Regras de Sincronização de Entrada** e Regras de Sincronização de Saída são executadas na ordem de número de precedência da menor para maior. Para exibir as Regras de Sincronização, você pode ir para **Editor de Regras de Sincronização** dos aplicativos da área de trabalho. As **Regras de Sincronização de Entrada** trazem dados de CS para MV. As **Regras de Sincronização de Saída** movem dados de MV para CS.

* Exportar para o AD: depois de executar a sincronização, os objetos são exportados do AD CS para o **Active Directory**.

* Exportar para o AAD: depois de executar a sincronização, os objetos são exportados do AAD CS para o **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Investigação passo a passo**

* Vamos começar nossa pesquisa do **Metaverso** e examinar o mapeamento de atributo de origem para destino.

* Inicie **Synchronization Service Manager** em aplicativos da área de trabalho, como mostrado abaixo:

  ![Inicializar o Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* No **Synchronization Service Manager**, selecione **Pesquisa de Metaverso**, selecione **Escopo por Tipo de Objeto**, selecione o objeto usando um atributo e clique no botão **Pesquisa**.

  ![Pesquisa de Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Clique duas vezes no objeto encontrado na pesquisar **Metaverso** para exibir todos os seus atributos. Você pode clicar na guia **Conectores** para examinar o objeto correspondente em todos os **Espaços do Conector**.

  ![Conectores de Objeto do Metaverso](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Clique duas vezes no **Active Directory Connector** para exibir os atributos do **Espaço do Conector**. Clique no botão **Visualização**, na caixa de diálogo seguinte, clique no botão a **Gerar Visualização**.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Agora, clique no **Fluxo de Atributo de Importação**, isso mostra o fluxo de atributos do **Espaço do Active Directory Connector** para o **Metaverso**. A coluna **Regra de Sincronização** mostra quais **regras de sincronização** contribuem para esse atributo. A coluna **Fonte de Dados** mostra os atributos do **Espaço do Conector**. A coluna **Atributo de Metaverso** mostra os atributos no **Metaverso**. Você pode procurar o atributo que não está sincronizando aqui. Se você não encontrar o atributo aqui, ele não será mapeado e você precisará criar uma nova **Regra de Sincronização** personalizada para mapear o atributo.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Clique em **Exportar Fluxo de Atributos** no painel esquerdo para exibir o fluxo de atributos do **Metaverso** de volta no **Espaço do Active Directory Connector** usando **Regras de Sincronização de Saída**.

  ![Atributos de Espaço do Conector](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Da mesma forma, você pode exibir o objeto **Espaço de Conector do Azure Active Directory** e gerar a **Visualização** para exibir o fluxo de atributos de **Metaverso** para o **Espaço de Conector** e vice-versa, dessa forma, você poderá investigar por que um atributo não está sincronizando.

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Sincronização do Azure AD Connect: conceitos técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Sincronização do Azure AD Connect: Noções básicas sobre a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Sincronização do Azure AD Connect: Noções básicas sobre o provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: noções básicas sobre a configuração padrão](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Sincronização do Azure AD Connect: Noções básicas sobre usuários, grupos e contatos](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Sincronização do Azure AD Connect: Atributos sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Próximas etapas

- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é identidade híbrida?](whatis-hybrid-identity.md).
