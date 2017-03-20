---
title: "Conectores na Sincronização do Azure AD Connect: interface de usuário do Synchronization Service Manager | Microsoft Docs"
description: Entenda como usar a guia Conectores no Synchronization Service Manager para o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bd2240678fed44db748ae062bdf91e457159b4a2
ms.lasthandoff: 03/04/2017

---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Usando conectores com o Azure AD Connect Sync Service Manager

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

A guia Conectores é usada para gerenciar todos os sistemas aos quais o mecanismo de sincronização está conectado.

## <a name="connector-actions"></a>Ações do Conector
| Ação | Comentário |
| --- | --- |
| Criação |Não use. Para se conectar a florestas adicionais do AD, use o assistente de instalação. |
| Propriedades |Usado para filtragem de domínio e de UO. |
| [Excluir](#delete) |Usado para excluir os dados no espaço do conector ou para excluir a conexão a uma floresta. |
| [Configurar perfis de execução](#configure-run-profiles) |Com exceção do domínio de filtragem, não há nada a ser configurado aqui. Você pode usar essa ação para ver os perfis de execução já configurados. |
| Executar |Usado para iniciar uma execução única de um perfil. |
| Parar |Interrompe um Conector que, atualmente, executa um perfil. |
| Exportar Conector |Não use. |
| Importar Conector |Não use. |
| Atualizar Conector |Não use. |
| Atualizar Esquema |Atualiza o esquema em cache. É preferível usar a opção no assistente de instalação, já que ela também atualiza as regras de sincronização. |
| [Pesquisar Espaço do Conector](#search-connector-space) |Usado para encontrar objetos e [Seguir um objeto e seus dados por meio do sistema](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Excluir
A ação de exclusão é usada com duas finalidades diferentes.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A opção **Excluir apenas o espaço do conector** remove todos os dados, mas mantém a configuração.

A opção **Excluir Conector e Espaço** remove os dados e a configuração. Essa opção é usada quando você não deseja mais se conectar a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos do metaverso. Essa ação é uma operação demorada.

### <a name="configure-run-profiles"></a>Configurar perfis de execução
Esta opção permite que você veja os perfis de execução configurados para um Conector.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Pesquisar Espaço do Conector
A ação de espaço do conector de pesquisa é útil para encontrar objetos e solucionar problemas de dados.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comece selecionando um **escopo**. É possível pesquisar com base nos dados (RDN, DN, Âncora, Subárvore) ou no estado do objeto (todas as outras opções).  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Por exemplo, se fizer uma pesquisa em Subárvore, você obterá todos os objetos em uma UO.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Nessa grade, é possível selecionar um objeto, selecionar **propriedades** e [segui-lo](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) do espaço conector de origem, passando pelo metaverso, até o espaço conector de destino.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

