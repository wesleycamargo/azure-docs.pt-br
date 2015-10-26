<properties
	pageTitle="Azure AD Connect Sync: noções básicas sobre expressões de provisionamento declarativo | Microsoft Azure"
	description="Explica as expressões de provisionamento declarativo."
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
	ms.author="markusvi"/>


# Azure AD Connect Sync: noções básicas sobre expressões de provisionamento declarativo

O serviço Azure Active Directory Connect Synchronization (Azure AD Connect Sync) baseia-se no provisionamento declarativo introduzido pela primeira vez no Forefront Identity Manager 2010, para permitir que você implemente sua lógica de negócios de integração de identidade completa sem necessidade de escrever código.

Uma parte essencial do provisionamento declarativo é a linguagem de expressão usada nos fluxos de atributo. A linguagem usada é um subconjunto de VBA (Visual Basic® for Applications) da Microsoft. Essa linguagem é usada no Microsoft Office e os usuários com experiência em VBScript também a reconhecerão. A Linguagem de expressão de provisionamento declarativo está apenas usando funções e não é uma linguagem estruturada; não existem métodos nem instruções. Em vez disso, as funções serão aninhadas no fluxo do programa expresso.

Para obter mais detalhes, consulte [Bem-vindo ao Visual Basic para referência de linguagem de aplicativos para Office 2013](https://msdn.microsoft.com/library/gg264383(v=office.15).aspx).

Os atributos são fortemente tipados. Uma função que espera que um atributo de cadeia de caracteres de valor único não aceite vários valores ou atributos de um tipo diferente. Ela também diferencia maiúsculas de minúsculas. Tanto nomes de funções quanto nomes de atributo devem ter a capitalização apropriada, ou um erro será gerado





## Identificadores e definições de idioma

- Funções têm um nome seguido por argumentos entre colchetes angulares: NomeDaFunção(<<argument 1>>,<<argument N>>).
- Os atributos são identificados por colchetes, [NomeDoAtributo]
- Parâmetros são identificados por sinais de porcentagem: %NomeDoParâmetro%
- Constantes de cadeia de caracteres ficam entre aspas: por exemplo, “Contoso”
- Valores numéricos são expressos sem aspas e espera-se que sejam de tipo decimal. Valores hexadecimais são prefixados com &H. Por exemplo 98052, &HFF
- Valores boolianos são expressos com constantes: True, False.
- Constantes internas são expressas com apenas seu nome: NULL, CRLF, IgnoreThisFlow


## Operadores

Os operadores a seguir podem ser usados:

- **Comparação**: <, <=, <>, =, >, >=
- **Matemática**: +, -, *, -
- **Cadeia de caracteres**: & (concatenado)
- **Lógica**: && (e), || (ou)
- **Ordem de avaliação**: ( )



Os operadores são avaliados da esquerda para a direita. 2*(5+3) não é o mesmo que 2*5+3.<br> Os parênteses ( ) são usados para alterar a ordem de avaliação.





## Parâmetros

Um parâmetro é definido por um Connector ou por um administrador usando o PowerShell. Geralmente, os parâmetros contêm valores que serão diferentes de sistema para sistema; por exemplo, o nome do domínio no qual o usuário está localizado. Eles podem ser usados em fluxos de atributo.

O Active Directory Connector forneceu os seguintes parâmetros para Regras de Sincronização de entrada:


| Domain.Netbios | Domain.FQDN | Domain.LDAP | | Forest.Netbios | Forest.FQDN | Forest.LDAP |


O sistema fornece o seguinte parâmetro:

Connector.ID

Um exemplo que preencherá o domínio de atributo metaverso com o nome netbios do domínio em que o usuário está localizado.

domain <- %Domain.Netbios%

## Cenários comuns

### Comprimento de atributos

Atributos de cadeia de caracteres são definidos por padrão para serem indexáveis e seu comprimento máximo é 448 caracteres. Se você está trabalhando com atributos de cadeia de caracteres que podem conter mais, certifique-se de incluir o seguinte no fluxo de atributo:

`attributeName <- Left([attributeName],448)`

### Alterando o userPrincipalSuffix

O atributo userPrincipalName no Active Directory não é sempre conhecido pelos usuários e pode não ser adequado como a ID de logon. O guia de instalação do AAD Sync permite escolher um atributo diferente, por exemplo, mail. Mas, em alguns casos, o atributo deve ser calculado. Por exemplo, a empresa Contoso tem dois diretórios AAD, um para produção e outro para testes. Eles querem que os usuários em seu locatário de teste apenas alterem o sufixo no logon ID.userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"

Nesta expressão, tomamos tudo à esquerda do primeiro símbolo “@” (Word) e concatenamos com uma cadeia de caracteres fixa.





### Converter um número de vários valores em um único valor

Alguns atributos no Active Directory são compostos de vários valores no esquema, mesmo que pareçam de valor único nos Usuários e computadores do Active Directory. Um exemplo é o atributo de descrição.

Nesta expressão, caso o atributo tenha um valor, podemos levar o primeiro item (Item) no atributo, remover espaços à direita e à esquerda (Trim, cortar) e, em seguida, manter os primeiros 448 caracteres (Left, à esquerda) na cadeia de caracteres.



## Conceito avançado

### NULL versus IgnoreThisFlow

Para Regras de Sincronização de entrada, a constante **NULL** sempre deve ser usada. Isso indica que o fluxo não tem nenhum valor com o qual contribuir e que outra regra pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, o atributo metaverso é removido.

Há duas constantes diferentes a usar para Regras de Sincronização de saída: NULL e IgnoreThisFlow. Ambas indicam que o fluxo de atributo não tem nada com o que contribuir, mas a diferença é o que acontece quando não há outra regra que tenha, tampouco, algo com o que contribuir. Se houver um valor existente no diretório conectado, uma constante NULL preparará uma exclusão do atributo e o removerá, enquanto IgnoreThisFlow manterá o valor existente.



#### ImportedValue

A função ImportedValues é diferente de todas as outras funções, já que o nome de atributo deve ser colocado entre aspas, em vez de colchetes: ImportedValue("proxyAddresses").

Geralmente, durante a sincronização, um atributo usará o valor esperado, mesmo que ele ainda não tenha sido exportado ou que um erro tenha sido recebido durante a exportação ("topo da torre"). Uma sincronização de entrada presumirá que um atributo que ainda não atingiu um diretório conectado eventualmente o atingirá. Em alguns casos, é importante sincronizar apenas um valor que tenha sido confirmado pelo diretório conectado e, nesse caso, a função ImportedValue será usada ("holograma e torre de importação delta").

Um exemplo disso pode ser encontrado na Regra de Sincronização de Entrada integrada no AD – Usuário Comum do Exchange no qual, no Exchange Híbrido, o valor adicionado pelo Exchange online só deverá ser sincronizado caso tenha sido confirmado que o valor foi exportado com êxito:


`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues(“proxyAddresses”)))`

Para obter uma lista completa de funções, consulte [Azure AD Connect Sync: referência de funções](active-directory-aadconnectsync-functions-reference.md)


## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=Oct15_HO3-->