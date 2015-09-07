<properties 
	pageTitle="Alterar a configuração padrão do AD do Azure Connect"
	description="Saiba como alterar a configuração padrão para o Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Alterando a configuração padrão do Azure AD Connect 


A configuração padrão do Azure AD Connect na maioria dos casos é suficiente para estender facilmente seus diretórios locais para a nuvem. No entanto, há determinadas instâncias em que talvez seja necessário modificar o padrão de acordo com a lógica de negócios das organizações. Nesses casos, você pode modificar a configuração padrão; no entanto, há algumas coisas que você precisa saber antes de fazer isso.

Quando você precisar alterar a configuração padrão, faça o seguinte:

- Quando você precisa modificar um fluxo de atributos de uma regra de sincronização integrada, não altere. Em vez disso, crie uma nova regra de sincronização com uma precedência mais alta (valor numérico menor) que contém o fluxo de atributo necessário.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.
- Se você precisar alterar o escopo ou a configuração de junção em uma regra de sincronização integrada, documente isso e reaplique a alteração após atualizar para uma versão mais recente do Azure AD Connect. 

<!---HONumber=August15_HO9-->