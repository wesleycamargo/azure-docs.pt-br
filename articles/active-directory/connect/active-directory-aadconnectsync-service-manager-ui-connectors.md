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
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6d893efd775ff6b55524ba3a621d8248adbdd432
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017

---
# Usando conectores com o Azure AD Connect Sync Service Manager
<a id="using-connectors-with-the-azure-ad-connect-sync-service-manager" class="xliff"></a>

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

A guia Conectores é usada para gerenciar todos os sistemas aos quais o mecanismo de sincronização está conectado.

## Ações do Conector
<a id="connector-actions" class="xliff"></a>
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

### Excluir
<a id="delete" class="xliff"></a>
A ação de exclusão é usada com duas finalidades diferentes.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A opção **Excluir apenas o espaço do conector** remove todos os dados, mas mantém a configuração.

A opção **Excluir Conector e Espaço** remove os dados e a configuração. Essa opção é usada quando você não deseja mais se conectar a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos do metaverso. Essa ação é uma operação demorada.

### Configurar perfis de execução
<a id="configure-run-profiles" class="xliff"></a>
Esta opção permite que você veja os perfis de execução configurados para um Conector.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### Pesquisar Espaço do Conector
<a id="search-connector-space" class="xliff"></a>
A ação de espaço do conector de pesquisa é útil para encontrar objetos e solucionar problemas de dados.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comece selecionando um **escopo**. É possível pesquisar com base nos dados (RDN, DN, Âncora, Subárvore) ou no estado do objeto (todas as outras opções).  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Por exemplo, se fizer uma pesquisa em Subárvore, você obterá todos os objetos em uma UO.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Nessa grade, é possível selecionar um objeto, selecionar **propriedades** e [segui-lo](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) do espaço conector de origem, passando pelo metaverso, até o espaço conector de destino.

### Alterando a senha da conta do AD DS
<a id="changing-the-ad-ds-account-password" class="xliff"></a>
Se você alterar a senha da conta, o Serviço de Sincronização não poderá mais importar/exportar alterações para o AD local.   Você poderá ver o seguinte:

- A etapa de importação/exportação do conector do AD falha com o erro “no-start-credentials”.
- No Visualizador de Eventos do Windows, o log de eventos do aplicativo contém um erro com a ID do Evento 6000 e a mensagem “O agente de gerenciamento "contoso.com" não foi executado porque as credenciais eram inválidas”.

Para resolver o problema, atualize a conta de usuário do AD DS usando o seguinte:


1. Inicie o Synchronization Service Manager (INICIAR → Serviço de Sincronização).
</br>![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Vá para a guia **Conectores**.
3. Selecione o Conector do AD que está configurado para usar a conta do AD DS.
4. Em Ações, selecione **Propriedades**.
5. Na caixa de diálogo pop-up, selecione Conectar-se à Floresta do Active Directory:
6. O Nome da floresta indica o AD local correspondente.
7. O Nome de usuário indica a conta do AD DS usada para sincronização.
8. Insira a nova senha da conta do AD DS na caixa de texto ![Utilitário de Chave de Criptografia de Sincronização do Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png) da Senha
9. Clique em OK para salvar a nova senha e reinicie o Serviço de Sincronização para remover a senha antiga do cache de memória.



## Próximas etapas
<a id="next-steps" class="xliff"></a>
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

