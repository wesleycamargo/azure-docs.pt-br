<properties
	pageTitle="Personalizando mapeamentos de atributo"
	description="Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios."
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


# Personalizando mapeamentos de atributo


O AD do Microsoft Azure dá suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, Google Apps e outros. Se você tiver provisionamento de usuário para um aplicativo SaaS de terceiro habilitado, o Portal de Gerenciamento controlará seus valores de atributo na forma de uma configuração chamada "mapeamento de atributo".

Há um conjunto predefinido de mapeamentos de atributo entre objetos de usuário do AD do Azure e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. <br> Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Isso significa que você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.

No portal do AD do Azure, você pode acessar esse recurso clicando em Atributos na barra de ferramentas de um aplicativo SaaS.

> [AZURE.NOTE]O link **Atributos** só estará disponível se você tiver o provisionamento do usuário habilitado para um aplicativo SaaS.


![Salesforce][1]


Quando você clica em Atributos na barra de ferramentas, a lista de mapeamentos atuais configurados para um aplicativo SaaS.

A captura de tela a seguir mostra um exemplo disso:



![Salesforce][2]


No exemplo acima, você pode ver que o atributo **firstName** de um objeto gerenciado no Salesforce é preenchido com o valor **givenName** vinculado ao objeto AD do Azure.

Se você quiser personalizar mapeamentos de atributo ou reverter configurações personalizadas de volta à configuração padrão, pode fazer isso clicando no botão relacionado na barra de ferramentas, na parte inferior de um aplicativo.


![Salesforce][3]


Há mapeamentos de atributo que são exigidos por um aplicativo SaaS para funcionar corretamente. Na tabela de atributos, os mapeamentos de atributo relacionados têm **Sim** como valor para o atributo **Obrigatório**. Se um mapeamento de atributo for obrigatório, você não poderá excluí-lo. Nesse caso, o recurso **Excluir** não estará disponível.

Para modificar um mapeamento de atributo existente, basta selecionar o mapeamento e clicar em **Editar**. Isso abre uma página de diálogo que permite que você modifique o mapeamento de atributo selecionado.


![Editar mapeamento de atributo][4]



## Noções básicas sobre tipos de mapeamento de atributo


Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro. Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.


- **Constante** – o atributo de destino é preenchido com uma cadeia de caracteres especificada por você.


- **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script. Para obter mais detalhes, consulte [Escrevendo expressões para mapeamentos de atributo no Active Directory do Azure](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se o atributo de destino estiver vazio, ele será preenchido com o valor padrão que você especificar.



Além desses quatro tipos de mapeamentos de atributo básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor **padrão**. A atribuição do valor padrão assegura que um atributo de destino seja preenchido com um valor, se não houver um valor no AD do Azure nem no objeto de destino.

O AD do Microsoft Azure fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização. A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Isso ocorre porque uma atualização a uma configuração de mapeamento de atributo requer que todos os objetos gerenciados sejam reavaliados. Por isso, é uma prática recomendada manter o número de alterações consecutivas aos seus mapeamentos de atributos no mínimo.



[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=August15_HO6-->