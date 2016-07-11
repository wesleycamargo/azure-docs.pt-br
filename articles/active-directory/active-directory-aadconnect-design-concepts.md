<properties
   pageTitle="Azure AD Connect: conceitos de design | Microsoft Azure"
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
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Azure AD Connect: conceitos de design
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
    - Caracteres diferentes de a-z, A-Z ou 0-9 serão codificados e contados como 3 caracteres
- Não conter nenhum caractere especial: &#92; ! # $ % & * + / = ? ^ &#96; { }| ~ < > ( ) ' ; : , [ ] " @ _
- Ser globalmente exclusivo
- Ser uma cadeia de caracteres, um inteiro ou um binário
- Não ser baseado no nome do usuário, já que eles mudam
- Não diferenciar maiúsculas de minúsculas e evitar valores que podem variar maiúsculas e minúsculas
- Ser atribuído quando o objeto é criado.

Se o sourceAnchor selecionado não for do tipo de cadeia de caracteres, o Azure AD Connect usará Base64Encode no valor do atributo para assegurar que nenhum caractere especial será exibido. Se você usar outro servidor de federação do ADFS, verifique se o servidor também tem a capacidade de usar Base64Encode no atributo.

O atributo sourceAnchor diferencia letras maiúsculas de minúsculas. Um valor "DaviBarros" não é igual a "davibarros".

Se você tiver uma única floresta local, o atributo que você deve usar é **objectGUID**. Esse também é o atributo usado quando você usar configurações expressas no Azure AD Connect, além de ser o atributo usado pelo DirSync.

Se você tiver várias florestas e não mover os usuários entre florestas e entre domínios na mesma floresta, o **objectGUID** será um atributo bom para usar até mesmo nesse caso.

Se você mover os usuários entre domínios e florestas, deve encontrar um atributo que não será alterado ou que não pode ser movido com os usuários durante a movimentação. Uma abordagem recomendada é apresentar um atributo sintético. Por exemplo, um atributo que poderia conter algo parecido com um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbado no usuário. Uma regra personalizada pode ser criada no servidor de mecanismo de sincronização para criar esse valor baseado no **objectGUID** e atualizar o atributo selecionado no ADDS. Ao mover o objeto, não se esqueça também de copiar o conteúdo do valor.

Outra solução é escolher um atributo existente que você sabe que não será alterado. Os atributos usados normalmente incluem **employeeID**. Se você considerar um atributo que conterá letras, verifique se não há nenhuma possibilidade da letra (letras maiúsculas ou letras minúsculas) alterar o valor do atributo. Entre os atributos ruins que não devem ser usados estão os com o nome do usuário. Em um casamento ou divórcio, o nome deve ser alterado, o que não é permitido para esse atributo. Isso também é um dos motivos pelos quais os atributos como **userPrincipalName**, **mail** e **targetAddress** não são nem mesmo possíveis de se selecionar no assistente de instalação do Azure AD Connect. Esses atributos também conterão o caractere @, que não é permitido no sourceAnchor.

### Alterando o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após o objeto ser criado no AD do Azure e a identidade ser sincronizada.

Por esse motivo, as seguintes restrições se aplicam ao Azure AD Connect:

- O atributo sourceAnchor somente pode ser definido durante a instalação inicial. Se você executar o assistente de instalação novamente, essa opção será somente leitura. Se você precisar alterar isso, desinstale e reinstale.
- Se você instalar outro servidor do Azure AD Connect, você deverá selecionar o mesmo atributo sourceAnchor usado anteriormente. Se você usava o DirSync anteriormente e mudou para o Azure AD Connect, será preciso usar **objectGUID**, já que ele é o atributo usado pelo DirSync.
- Se o valor de sourceAnchor for alterado após o objeto ser exportado para o AD do Azure, a sincronização do Azure AD Connect gerará um erro e não permitirá nenhuma outra alteração no objeto antes de o problema ser corrigido e o sourceAnchor ser alterado de volta no diretório de origem.

## Entrar no Azure AD

Ao integrar seu diretório local ao Azure AD, é importante compreender como as configurações de sincronização podem afetar a maneira de autenticar o usuário. O Azure AD usa userPrincipalName ou UPN para autenticar o usuário. No entanto, ao sincronizar os usuários, você deve escolher o atributo a ser usado para o valor de userPrincipalName cuidadosamente.

### Escolher o atributo para userPrincipalName

Ao selecionar o atributo para fornecer o valor de UPN a ser usado no Azure, garanta que

* Os valores de atributo estão de acordo com a sintaxe UPN (RFC 822), ou seja, eles devem estar no formato username@domain.
* O sufixo nos valores corresponde a um dos domínios personalizados verificados no Azure AD

Em configurações expressas, a opção suposta para o atributo é userPrincipalName. No entanto, se você acredita que o atributo userprincipalname não contém o valor que deseja que os usuários usem para fazer logon no Azure, escolha **Instalação Personalizada** e forneça o atributo apropriado.

### Estado de domínio personalizado e UPN
É importante garantir que haja um domínio verificado para o sufixo UPN.

John é um usuário em contoso.com. Você deseja que Júlio use o UPN local john@contoso.com para fazer logon no Azure depois de ter sincronizado os usuários para o diretório azurecontoso.onmicrosoft.com do Azure AD. Para fazer isso, você precisará adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de iniciar a sincronização dos usuários. Se o sufixo UPN de Júlio, por exemplo, contoso.com, não corresponder a um domínio verificado no Azure AD, este substituirá o sufixo UPN com azurecontoso.onmicrosoft.com e Júlio terá que usar john@azurecontoso.onmicrosoft.com para entrar no Azure.

### Domínios locais não roteáveis e UPN para Azure AD
Algumas organizações têm domínios não roteáveis, como contoso.local ou domínios de rótulo único simples, como contoso. Não é possível verificar um domínio no Azure AD que não pode ser roteado. O Azure AD Connect pode sincronizar apenas um domínio verificado no Azure AD. Quando você cria um diretório do Azure AD, ele cria um domínio roteável que torna-se o domínio padrão do Azure AD, por exemplo, contoso.onmicrosoft.com. Portanto, é necessário verificar se outros domínios roteáveis nesse cenário, caso você não deseje sincronizar com o domínio padrão .onmicrosoft.com.

Leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md) para obter mais informações sobre como verificar domínios.

O Azure AD Connect detecta se você está executando em um ambiente de domínio não roteável e avisa corretamente para não prosseguir com configurações expressas. Se você está operando em um domínio não roteável, é provável que o UPN dos usuários também tenham sufixos não roteáveis. Por exemplo, se você estiver executando em contoso.local, o Azure AD Connect vai sugerir o uso de configurações personalizadas em vez de usar as configurações expressas. Usando as configurações personalizadas, você poderá especificar o atributo que deve ser usado como o UPN para fazer logon no Azure depois que os usuários são sincronizados com o Azure AD. Consulte **Selecionar o atributo para o nome UPN no Azure AD** abaixo para ver mais informações.

## Próximas etapas
Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->