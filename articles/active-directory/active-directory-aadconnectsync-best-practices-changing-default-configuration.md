<properties
	pageTitle="Práticas recomendadas para alterar a configuração padrão"
	description="Fornece práticas recomendadas para alterar a configuração padrão do Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: práticas recomendadas para alterar a configuração padrão

A configuração criada pelo Azure AD Connect funciona "como está" para a maioria dos ambientes de sincronização do Active Directory local com o AD do Azure.<br> No entanto, em alguns casos, é necessário aplicar alterações a uma configuração para atender a uma necessidade ou requisito específico.

Mesmo havendo suporte para aplicar as alterações à sua configuração do AD do Azure, você deve aplicá-las com cuidado, porque o AD do Azure deve estar o mais próximo possível de um dispositivo.

A seguir, uma lista de comportamentos esperados:

- Depois de atualizar o Azure AD Connect para uma versão mais recente, a maioria das configurações será redefinida de volta ao padrão.
- Alterações nas regras de sincronização integradas são perdidas após uma atualização.
- Regras de sincronização integrada excluídas são recriadas durante uma atualização para uma versão mais recente.
- Regras de sincronização personalizadas criadas por você permanecem inalteradas quando uma atualização para uma versão mais recente é aplicada.



Quando você precisar alterar a configuração padrão, faça o seguinte:

- Quando você precisa modificar um fluxo de atributos de uma regra de sincronização integrada, não altere. Em vez disso, crie uma nova regra de sincronização com uma precedência mais alta (valor numérico menor) que contém o fluxo de atributo necessário.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.
- Se você precisar alterar o escopo ou a configuração de junção em uma regra de sincronização integrada, documente isso e reaplique a alteração após atualizar para uma versão mais recente do Azure AD Sync.



**Outras observações importantes:**

- Se você tem sincronização de senha e filtragem baseadas em atributos configuradas, certifique-se de que somente os objetos sincronizados com o AD do Azure estão no escopo da sincronização de senha. 





## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->