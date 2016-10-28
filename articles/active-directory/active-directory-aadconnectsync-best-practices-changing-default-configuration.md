<properties
	pageTitle="Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão | Microsoft Azure"
	description="Fornece práticas recomendadas para alterar a configuração padrão da sincronização do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="markvi;andkjell"/>


# Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão
O objetivo deste tópico é descrever as alterações com e sem suporte para a sincronização do Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "da forma como é" para a maioria dos ambientes que sincronizam o Active Directory local com o AD do Azure. No entanto, em alguns casos, é necessário aplicar alterações a uma configuração para atender a uma necessidade ou requisito específico.

## Alterações na conta de serviço
A sincronização do Azure AD Connect é executada em uma conta de serviço criada pelo assistente de instalação. Essa conta de serviço mantém as chaves de criptografia no banco de dados usado pela sincronização. Ela é criada com uma senha de 127 caracteres que é definida para não expirar.

- **Não há suporte** para alteração ou redefinição da senha da conta de serviço. Isso destruirá as chaves de criptografia e o serviço não poderá acessar o banco de dados e iniciar.

## Alterações no agendador
A partir da versão 1.1 (fevereiro de 2016), você pode configurar o [agendador](active-directory-aadconnectsync-feature-scheduler.md) para ter um ciclo de sincronização diferente do padrão de 30 minutos.

## Alterações nas regras de sincronização
O assistente de instalação fornece uma configuração que deve funcionar nos cenários mais comuns. No caso de precisar fazer alterações na configuração, você deve seguir estas regras para continuar com uma configuração com suporte.

- Você poderá [alterar fluxos de atributos](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributos diretos padrão não forem adequados para sua organização.
- Se você optar por [não fluir um atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e remover valores do atributo existentes no Azure AD, você precisará criar uma regra para este cenário.
- [Desabilite uma Regra de sincronização indesejada](#disable-an-unwanted-sync-rule) em vez de excluí-la. Uma regra excluída é recriada durante uma atualização.
- Para [alterar uma regra integrada](#change-an-out-of-box-rule), você deverá fazer uma cópia da regra original e desabilitar a regra integrada. O Editor de Regra de Sincronização solicita e ajuda você.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. O editor fornece um script do PowerShell que você pode usar para recriá-los facilmente em um cenário de recuperação de desastres.

>[AZURE.WARNING] As regras de sincronização integrada têm uma impressão digital. Se você fizer uma alteração a essas regras, a impressão digital não corresponderá mais. Você pode ter problemas no futuro, quando tentar aplicar uma nova versão do Azure AD Connect. Faça alterações somente como é descrito neste artigo.

### Desabilitar uma Regra de Sincronização indesejada
Não exclua uma regra de sincronização integrada. Ela será recriada durante a próxima atualização.

Em alguns casos, o assistente de instalação produz uma configuração que não funciona para sua topologia. Por exemplo, se houver uma topologia de floresta de recurso de conta, mas você estendeu o esquema na floresta de conta com o esquema do Exchange, as regras do Exchange serão criadas para a floresta de contas e para a floresta de recursos. Nesse caso, você deverá desabilitar a Regra de Sincronização do Exchange.

![Regra de sincronização desabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na figura acima, o assistente de instalação encontrou um esquema antigo do Exchange 2003 na floresta de contas. Esta extensão de esquema foi adicionada antes da floresta de recursos ter sido introduzida no ambiente da Fabrikam. Para garantir que nenhum atributo da implementação do Exchange antigo seja sincronizado, a regra de sincronização deve ser desabilitada conforme mostrado.

### Alterar uma regra integrada
Se você precisar fazer alterações em uma regra integrada, deverá fazer uma cópia da regra integrada e desabilitar a regra original. Em seguida, faça as alterações desejadas à regra clonada. O Editor de Regra de Sincronização ajuda você com essas etapas. Quando você abre uma regra integrada, vê essa caixa de diálogo: ![Aviso de regra integrada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. Então, a regra clonada é aberta. ![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Na regra clonada, faça as alterações necessárias no escopo, na associação e nas transformações.

## Próximas etapas

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->