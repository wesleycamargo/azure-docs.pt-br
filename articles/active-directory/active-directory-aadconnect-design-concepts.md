<properties
   pageTitle="Conceitos de design do Azure AD Connect | Microsoft Azure"
	description="Este tópico detalha determinadas áreas de design da implementação"
	services="active-directory"
	documentationCenter=""
	authors="AndKjell"
	manager="stevenpo"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="Identity"
	ms.date="08/27/2015"
	ms.author="andkjell"/>

# Conceitos de design do Azure AD Connect
O objetivo deste tópico é descrever as áreas que devem ser consideradas durante o design de implementação do Azure AD Connect. Trata-se de um aprofundamento em determinadas áreas e esses conceitos também são descritos brevemente em outros tópicos.

## sourceAnchor
O atributo sourceAnchor é definido como *um atributo imutável durante o tempo de vida de um objeto*. Ele identifica de maneira exclusiva um objeto como sendo o mesmo objeto local e no AD do Azure. O atributo também é chamado de **immutableId** e os dois nomes são usados como sinônimos.

A palavra imutável, ou seja, que não pode ser alterado, é importante neste tópico. Como o valor desse atributo não pode ser alterado depois de ser definido, é importante escolher um design que dará suporte ao seu cenário.

O atributo é usado para os seguintes cenários:

- Quando um novo servidor de mecanismo de sincronização é compilado ou recompilado após um cenário de recuperação de desastre, esse atributo vincula objetos existentes no AD do Azure a objetos locais.
- Se você for de uma identidade somente em nuvem para um modelo de identidade sincronizado, esse atributo permitirá que os objetos efetuem o "hard match" dos objetos existentes no AD do Azure em relação aos objetos locais.
- Se você usar federação, esse atributo, junto com **userPrincipalName**, é usado na declaração para identificar exclusivamente um usuário.

Este tópico falará somente sobre o sourceAnchor, uma vez que ele está relacionado a usuários. As mesmas regras se aplicam a todos os tipos de objeto, mas somente para usuários que ele normalmente é uma preocupação.

### Selecionando um bom atributo sourceAnchor
O valor do atributo deve seguir as regras a seguir:

- Ter menos de 60 caracteres
- Não conter nenhum caractere especial: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @
- Ser globalmente exclusivo
- Ser uma cadeia de caracteres, um inteiro ou um binário

Se o sourceAnchor selecionado não for do tipo de cadeia de caracteres, o Azure AD Connect usará Base64Encode no valor do atributo para assegurar que nenhum caractere especial será exibido. Se você usar outro servidor de federação do ADFS, verifique se o servidor também tem a capacidade de usar Base64Encode no atributo.

O atributo sourceAnchor diferencia letras maiúsculas de minúsculas. Um valor "DaviBarros" não é igual a "davibarros".

Se você tiver uma única floresta local, o atributo que você deve usar é **objectGUID**. Esse também é o atributo usado quando você usar configurações expressas no Azure AD Connect, além de ser o atributo usado pelo DirSync.

Se você tiver várias florestas e não mover os usuários entre florestas e entre domínios na mesma floresta, o **objectGUID** será um atributo bom para usar até mesmo nesse caso.

Se você mover os usuários entre domínios e florestas, será preciso encontrar um atributo que não será alterado. Os atributos comuns usados incluem **employeeID**. Se você considerar um atributo que conterá letras, como **sAMAccountName**, verifique se não há nenhuma possibilidade da letra (letras maiúsculas vs letras minúsculas) alterar o valor do atributo. Entre os atributos ruins que não devem ser usados estão os com o nome do usuário. Em um casamento ou divórcio, o nome deve ser alterado, o que não é permitido para esse atributo. Isso também é um dos motivos pelos quais os atributos como **userPrincipalName**, **mail**, e **targetAddress** não são nem mesmo possíveis de selecionar no assistente de instalação do Azure AD Connect. Esses atributos também conterão o caractere @, que não é permitido no sourceAnchor.

Em casos nos quais não há absolutamente nenhum atributo adequado a ser usado, um valor sintético precisará ser introduzido. Por exemplo, um atributo que poderia conter algo parecido com um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbado no usuário. Ao mover o objeto, não se esqueça também de copiar o conteúdo do valor.

### Alterando o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após o objeto ser criado no AD do Azure e a identidade ser sincronizada.

Por esse motivo, as seguintes restrições se aplicam ao Azure AD Connect:

- O atributo sourceAnchor somente pode ser definido durante a instalação inicial. Se você executar o assistente de instalação novamente, essa opção será somente leitura. Se você precisar alterar isso, desinstale e reinstale.
- Se você instalar outro servidor do Azure AD Connect, você deverá selecionar o mesmo atributo sourceAnchor usado anteriormente. Se você usava o DirSync anteriormente e mudou para o Azure AD Connect, será preciso usar **objectGUID**, já que ele é o atributo usado pelo DirSync.
- Se o valor de sourceAnchor for alterado após o objeto ser exportado para o AD do Azure, a sincronização do Azure AD Connect gerará um erro e não permitirá nenhuma outra alteração no objeto antes de o problema ser corrigido e o sourceAnchor ser alterado de volta no diretório de origem.

<!---HONumber=August15_HO9-->