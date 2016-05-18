<properties
	pageTitle="Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo | Microsoft Azure"
	description="Explica as expressões de provisionamento declarativo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="markusvi;andkjell"/>


# Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo
A sincronização do Azure AD Connect baseia-se no provisionamento declarativo introduzido pela primeira vez no Forefront Identity Manager 2010, para permitir que você implemente sua lógica de negócios de integração de identidade completa sem necessidade de escrever código compilado.

Uma parte essencial do provisionamento declarativo é a linguagem de expressão usada nos fluxos de atributo. A linguagem usada é um subconjunto de VBA (Visual Basic® for Applications) da Microsoft. Essa linguagem é usada no Microsoft Office e os usuários com experiência em VBScript também a reconhecerão. A Linguagem de expressão de provisionamento declarativo está apenas usando funções e não é uma linguagem estruturada; não existem métodos nem instruções. Em vez disso, as funções serão aninhadas no fluxo do programa expresso.

Para obter mais detalhes, consulte [Bem-vindo ao Visual Basic para referência de linguagem de aplicativos para Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos são fortemente tipados. Uma função aceita somente atributos do tipo correto. Ela também diferencia maiúsculas de minúsculas. Tanto nomes de funções quanto nomes de atributo devem ter a capitalização apropriada, ou um erro será gerado

## Identificadores e definições de idioma

- As funções têm um nome seguido por argumentos entre colchetes: NomeDaFunção (argumento 1, argumento N).
- Os atributos são identificados por colchetes, [NomeDoAtributo]
- Parâmetros são identificados por sinais de porcentagem: %NomeDoParâmetro%
- Constantes de cadeia de caracteres ficam entre aspas: por exemplo, "Contoso" (observação: deve usar aspas normais "" e não as aspas "")
- Valores numéricos são expressos sem aspas e espera-se que sejam de tipo decimal. Valores hexadecimais são prefixados com &H. Por exemplo 98052, &HFF
- Valores boolianos são expressos com constantes: True, False.
- Constantes internas são expressas com apenas seu nome: NULL, CRLF, IgnoreThisFlow

### Funções
Provisionamento declarativo para usar muitas funções para habilitar a possibilidade de transformar os valores de atributo. Podem ser aninhados para que o resultado de uma função seja passado para outra função.

As funções também podem operar sobre um atributo com vários valores. Nesse caso, a função será operar em cada valor individual e aplicar a mesma função para cada valor. Por exemplo `Trim([proxyAddresses])` faria um corte de todos os valores no atributo proxyAddress.

A lista completa de funções pode ser encontrada em [referência de função](active-directory-aadconnectsync-functions-reference.md).

### Parâmetros

Um parâmetro é definido por um Connector ou por um administrador usando o PowerShell para defini-los. Geralmente, os parâmetros contêm valores que serão diferentes de sistema para sistema; por exemplo, o nome do domínio no qual o usuário está localizado. Eles podem ser usados em fluxos de atributo.

O Active Directory Connector forneceu os seguintes parâmetros para Regras de Sincronização de entrada:

| Nome do Parâmetro | Comentário |
| --- | --- |
| Domain.Netbios | O formato NetBIOS do domínio que está sendo importado no momento, por exemplo, FABRIKAMSALES |
| Domain.FQDN | O formato FQDN do domínio que está sendo importado no momento, por exemplo, sales.fabrikam.com |
| Domain.LDAP | O formato LDAP do domínio que está sendo importado no momento, por exemplo, DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | O formato Netbios do domínio que está sendo importado no momento, por exemplo, FABRIKAMCORP |
| Forest.FQDN | O formato FQDN do domínio que está sendo importado no momento, por exemplo, fabrikam.com |
| Forest.LDAP | O formato LDAP do domínio que está sendo importado no momento, por exemplo, DC=fabrikam,DC=com |

O sistema fornece o seguinte parâmetro usado para obter o identificador do conector em execução no momento:

`Connector.ID`

Um exemplo que preencherá o domínio de atributo metaverso com o nome netbios do domínio em que o usuário está localizado:

`domain` <- `%Domain.Netbios%`

### Operadores

Os operadores a seguir podem ser usados:

- **Comparação**: <, <=, <>, =, >, >=
- **Matemática**: +, -, *, -
- **Cadeia de caracteres**: & (concatenado)
- **Lógica**: && (e), || (ou)
- **Ordem de avaliação**: ( )

Os operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Por exemplo, o * (multiplicador) não é avaliado antes - (subtração). 2*(5+3) não é o mesmo que 2*5+3. Os parênteses ( ) são usados para alterar a ordem de avaliação quando a ordem de avaliação da direita à esquerda não é apropriada.

## Cenários comuns

### Comprimento de atributos

Atributos de cadeia de caracteres são definidos por padrão para serem indexáveis e seu comprimento máximo é 448 caracteres. Se você está trabalhando com atributos de cadeia de caracteres que podem conter mais, certifique-se de incluir o seguinte no fluxo de atributo:

`attributeName` <- `Left([attributeName],448)`

### Alterando o userPrincipalSuffix

O atributo userPrincipalName no Active Directory não é sempre conhecido pelos usuários e pode não ser adequado como a ID de logon. O guia de instalação da sincronização do Azure AD Connect permite escolher um atributo diferente, por exemplo, email. Mas, em alguns casos, o atributo deve ser calculado. Por exemplo, a empresa Contoso tem dois diretórios do AD do Azure, um para produção e outro para testes. Eles querem que os usuários em seu locatário de teste apenas alteram o sufixo na ID de logon.

`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Nesta expressão, tomamos tudo à esquerda do primeiro símbolo “@” (Word) e concatenamos com uma cadeia de caracteres fixa.

### Converter um número de vários valores em um único valor

Alguns atributos no Active Directory são compostos de vários valores no esquema, mesmo que pareçam de valor único nos Usuários e computadores do Active Directory. Um exemplo é o atributo de descrição.

`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Nesta expressão, caso o atributo tenha um valor, podemos levar o primeiro item (Item) no atributo, remover espaços à direita e à esquerda (Trim, cortar) e, em seguida, manter os primeiros 448 caracteres (Left, à esquerda) na cadeia de caracteres.

## Conceito avançado

### NULL versus IgnoreThisFlow

Para Regras de Sincronização de entrada, a constante **NULL** sempre deve ser usada. Isso indica que o fluxo não tem nenhum valor com o qual contribuir e que outra regra pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, o atributo metaverso é removido.

Há duas constantes diferentes a usar para Regras de Sincronização de saída: NULL e IgnoreThisFlow. Ambas indicam que o fluxo de atributo não tem nada com o que contribuir, mas a diferença é o que acontece quando não há outra regra que tenha, tampouco, algo com o que contribuir. Se houver um valor existente no diretório conectado, uma constante NULL preparará uma exclusão do atributo e o removerá, enquanto IgnoreThisFlow manterá o valor existente.

### ImportedValue

A função ImportedValue é diferente de todas as outras funções, pois o nome de atributo deve ser colocado entre aspas, em vez de colchetes: ImportedValue("proxyAddresses").

Geralmente, durante a sincronização, um atributo usará o valor esperado, mesmo que ele ainda não tenha sido exportado ou que um erro tenha sido recebido durante a exportação ("topo da torre"). Uma sincronização de entrada presumirá que um atributo que ainda não atingiu um diretório conectado eventualmente o atingirá. Em alguns casos, é importante sincronizar apenas um valor que tenha sido confirmado pelo diretório conectado e, nesse caso, a função ImportedValue será usada ("holograma e torre de importação delta").

Um exemplo disso pode ser encontrado na Regra de Sincronização de Entrada integrada no AD – Usuário Comum do Exchange no qual, no Exchange Híbrido, o valor adicionado pelo Exchange online só deverá ser sincronizado caso tenha sido confirmado que o valor foi exportado com êxito:

`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

Para obter uma lista completa de funções, consulte [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)


## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=AcomDC_0420_2016-->