<properties
	pageTitle="Práticas recomendadas para alterar a configuração padrão | Microsoft Azure | Microsoft Azure"
	description="Fornece práticas recomendadas para alterar a configuração padrão da sincronização do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="markusvi;andkjell"/>


# Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão

O objetivo deste tópico é descrever as alterações com e sem suporte para a sincronização do Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "da forma como é" para a maioria dos ambientes que sincronizam o Active Directory local com o AD do Azure. No entanto, em alguns casos, é necessário aplicar alterações a uma configuração para atender a uma necessidade ou requisito específico.

## Alterações na conta de serviço
A sincronização do Azure AD Connect é executada em uma conta de serviço criada pelo assistente de instalação. Essa conta de serviço mantém as chaves de criptografia no banco de dados usado pela sincronização. Ela é criada com uma senha de 127 caracteres que é definida para não expirar.

- **Não há suporte** para alteração ou redefinição da senha da conta de serviço. Se isso ocorrer, as chaves de criptografia serão destruídas e o serviço não poderá acessar o banco de dados e iniciar.

## Alterações no agendador
A sincronização do Azure AD Connect está definida para sincronizar dados de identidade a cada três horas. Durante a instalação, uma tarefa agendada é criada em execução em uma conta de serviço com permissões para operar o servidor de sincronização.

- **Não há suporte** para alterações na tarefa agendada. A senha da conta de serviço não é conhecida. Consulte [Alterações na conta de serviço](#changes-to-the-service-account)
- **Não há suporte** para frequência de sincronização menor do que o padrão de 3 horas.

## Alterações nas regras de sincronização

Mesmo com suporte para aplicar alterações à sua configuração de sincronização do Azure AD Connect, você deve aplicá-las com cuidado, porque a sincronização do Azure AD Connect deve estar o mais próximo possível de um dispositivo.

A seguir, uma lista de comportamentos esperados:

- Depois de atualizar o Azure AD Connect para uma versão mais recente, a maioria das configurações será redefinida de volta ao padrão.
- Alterações nas regras de sincronização integradas são perdidas após uma atualização.
- Regras de sincronização integrada excluídas são recriadas durante uma atualização para uma versão mais recente.
- Regras de sincronização personalizadas criadas por você permanecem inalteradas quando uma atualização para uma versão mais recente é aplicada.



Quando você precisar alterar a configuração padrão, faça o seguinte:

- Quando você precisa modificar um fluxo de atributos de uma regra de sincronização integrada, não altere. Em vez disso, crie uma nova regra de sincronização com uma precedência mais alta (valor numérico menor) que contém o fluxo de atributo necessário.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.
- Se você precisar alterar o escopo ou a configuração de junção em uma regra de sincronização integrada, documente isso e reaplique a alteração após atualizar para uma versão mais recente do Azure AD Sync.



## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!--Image references-->

<!---HONumber=Oct15_HO3-->