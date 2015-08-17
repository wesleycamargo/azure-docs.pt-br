<properties
	pageTitle="Escrevendo expressões para mapeamentos de atributo no Active Directory do Azure"
	description="Aprenda a usar o mapeamentos de expressão para transformar valores de atributo em um formato aceitável durante o provisionamento automatizado de objetos de aplicativo SaaS no Active Directory do Azure."
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


# Escrevendo expressões para mapeamentos de atributo no Active Directory do Azure

Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Nesses casos, você deve escrever uma expressão semelhante a script que permite transformar os dados de usuários em formatos que são mais aceitáveis para o aplicativo SaaS.





## Visão geral da sintaxe

A sintaxe de expressões para mapeamentos de atributos é semelhante à das funções de VBA (Visual Basic for Applications).

- A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido pelos argumentos entre parênteses: <br> *FunctionName(<<argument 1>>,<<argument N>>)*


- Você pode aninhar funções dentro umas das outras. Por exemplo: <br> *FunctionOne(FunctionTwo(<<argument1>>))*


- Você pode passar três tipos diferentes de argumentos em funções:

   1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]

   2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"

   3. Outras funções. Por exemplo: FunctionOne(<<argument1>>, FunctionTwo(<<argument2>>))


- Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\\) ou aspas (") na cadeia de caracteres, ela deve ser escapada com o símbolo de barra invertida (\\). Por exemplo: "Nome da empresa: "Contoso""



## Lista de funções

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [MatchRegex](#matchregex) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [ObsoleteReplace](#obsoletereplace) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceRegex](#replaceregex) &nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)





----------
### Acrescentar

**Função:**<br> Append(source, suffix)

**Descrição:**<br> seleciona um valor da cadeia de caracteres de source e acrescenta o sufixo ao final dela.
 
**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Normalmente o nome do atributo do objeto de source |
| **suffix** | Obrigatório | Cadeia de caracteres | A cadeia de caracteres que você deseja acrescentar ao final do valor de source. |


----------
### Coalesce

**Função:**<br> Coalesce(source1, source2, …)

**Descrição:**<br> retorna o primeiro valor não vazio da lista dos parâmetros source.
 
**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **source1 .. sourceN ** | Obrigatório, número de vezes variável | Cadeia de caracteres |Valores de **source** entre os quais escolher |



----------
### FormatDateTime

**Função:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Descrição:**<br> obtém uma cadeia de caracteres de data de um formato e a converte em um formato diferente.
 
**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Normalmente o nome do atributo do objeto de source. |
| **inputFormat** | Obrigatório | Cadeia de caracteres | Formato esperado do valor de source. Para conhecer os formatos com suporte, consulte [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** | Obrigatório | Cadeia de caracteres | Formato da data de saída. |



----------
### Join

**Função:**<br> Join(separator, source1, source2, …)

**Descrição:**<br> Join () é semelhante a Append(), exceto que ele pode combinar vários valores de cadeia de caracteres de **source** em uma única cadeia de caracteres e cada valor será separado por uma cadeia de caracteres de **separator**.

Se um dos valores de source for um atributo com vários valores, todos os valores nesse atributo serão unidos, separados pelo valor de separator.

 
**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **separator** | Obrigatório | Cadeia de caracteres | Cadeia de caracteres usada para separar os valores de source quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se não for necessário nenhum separador. |
| ****source1 … sourceN ** | Obrigatório, número de vezes variável| Cadeia de caracteres | Valores da cadeia de caracteres a serem unidos. |





----------
### MatchRegex

**Função:**<br> MatchRegex(source, find, group)

**Descrição:**<br> retorna a subcadeia de caracteres dentro do valor de source que corresponde ao padrão de expressão regular especificado no parâmetro find. Se group for especificado, retorna somente o valor desse grupo de RegEx


**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Valor de **source** a pesquisar. |
| **find** | Obrigatório | Cadeia de caracteres | Expressão regular para corresponder dentro do valor de **source**. |
| **group** | Opcional | Cadeia de caracteres | Nome do grupo dentro da correspondência da expressão regular cujo valor desejamos usar. |



----------
### Mid

**Função:**<br> Mid(source, start, length)

**Descrição:**<br> retorna uma subcadeia de caracteres do valor de source. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.


**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Geralmente o nome do atributo. |
| **iniciar** | Obrigatório | inteiro | Índice na cadeia de caracteres de **source** em que a subcadeia de caracteres deve começar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
| **length** | Obrigatório | inteiro | Comprimento da subcadeia de caracteres. Se o comprimento terminar fora da cadeia de caracteres de **source**, a função retornará uma subcadeia de caracteres do índice **start** até o final da cadeia de caracteres de **source**. |




----------
### Not

**Função:**<br> Not(source)

**Descrição:**<br> inverte o valor booliano do **source**. Se o valor de **source** for "*True*", retorna "*False*". Caso contrário, retorna "*True*".


**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres booliana | Os valores de **source** esperados são "True" ou "False". |



----------
### ObsoleteReplace

**Função:**<br> ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrição:**<br>
> [AZURE.NOTE]Essa função será preterida no futuro próximo e substituída por versões mais simples

Substitui valores dentro de uma cadeia de caracteres. Ela funciona de maneira diferente dependendo dos parâmetros fornecidos:

- Quando **oldValue** e **replacementValue** são fornecidos:

   - Substitui todas as ocorrências de oldValue no source por replacementValue

- Quando **oldValue** e **template** são fornecidos:

   - Substitui todas as ocorrências do **oldValue** no **template** pelo valor do **source**

- Quando **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementValue** são fornecidos:

   - Substitui todos os valores que correspondem a oldValueRegexPattern na cadeia de caracteres de source por replacementValue

- Quando **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementPropertyName** são fornecidos:

   - Se **source** tiver um valor, o **source** é retornado

- Se **source** não tiver valor, usa **oldValueRegexPattern** e **oldValueRegexGroupName** para extrair o valor de substituição da propriedade com **replacementPropertyName**. O valor de substituição é retornado como o resultado


**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Normalmente o nome do atributo do objeto de source. |
| **oldValue** | Opcional | Cadeia de caracteres | Valor a ser substituído em **source** ou **template**. |
| **regexPattern** | Opcional | Cadeia de caracteres | Padrão de Regex para o valor a ser substituído em **source**. Ou, quando replacementPropertyName for usado, o padrão para extrair o valor da propriedade de substituição. |
| **regexGroupName** | Opcional | Cadeia de caracteres | Nome do grupo dentro de **regexPattern**. Somente quando replacementPropertyName for usado, extrairemos valor desse grupo como replacementValue da propriedade de substituição. |
| **replacementValue** | Opcional | Cadeia de caracteres | Novo valor com o qual substituir um antigo. |
| **replacementAttributeName** | Opcional | Cadeia de caracteres | Nome do atributo a ser usado para o valor de substituição quando source não tiver nenhum valor. |
| **template** | Opcional | Cadeia de caracteres | Quando o valor de **template** for fornecido, procuraremos **oldValue** dentro de template e o substituiremos pelo valor de source. |



----------
### Substitua

**Função:**<br> Replace(source, find, replace)

**Descrição:**<br> substitui todas as ocorrências do valor de **find** na cadeia de caracteres de **source** pelo valor do parâmetro **replace**.

**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Valor de **source** a pesquisar. |
| **find** | Obrigatório | Cadeia de caracteres | Valor a pesquisar. |
| **substitui** | Obrigatório | Cadeia de caracteres | Valor com o qual substituir. |



----------
### ReplaceRegex

**Função:**<br> ReplaceRegex(source, find, replace, group)

**Descrição:**<br> dentro da cadeia de caracteres de **source**, substitui todas as subcadeias de caracteres que correspondem à expressão regular de **find** pelo valor de **replace**. Se um **group** for especificado, ele substitui somente o valor desse grupo de RegEx.

**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Valor de **source** a pesquisar. |
| **find** | Obrigatório | Cadeia de caracteres | Expressão regular para corresponder dentro do valor de **source**. |
| **substitui** | Obrigatório | Cadeia de caracteres | Valor com o qual substituir. |
| **group** | Opcional | Cadeia de caracteres | Nome do grupo dentro da correspondência da expressão regular cujo valor desejamos usar. |




----------
### StripSpaces

**Função:**<br> StripSpaces(source)

**Descrição:**<br> remove todos os caracteres de espaço (" ") da caracteres da cadeia de source.

**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Valor de **source** a atualizar. |



----------
### Switch

**Função:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrição:**<br> quando o valor de **source** corresponde a um parâmetro **key**, retorna **value** para esse parâmetro **key**. Se o valor de **source** não corresponde a nenhum parâmetro key, retorna o **defaultValue**. Os parâmetros **key** e **value** devem sempre ocorrer em pares. A função sempre espera um número par de parâmetros.

**Parâmetros:**<br>

|Nome| Obrigatório/repetição | Tipo | Observações |
|--- | ---                 | ---  | ---   |
| **fonte** | Obrigatório | Cadeia de caracteres | Valor de **Source** a atualizar. |
| **defaultValue** | Opcional | Cadeia de caracteres | Valor padrão a ser usado quando source não corresponde a nenhum parâmetro. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** | Obrigatório | Cadeia de caracteres | Parâmetro **key** com o qual comparar o valor de **source**. |
| **valor** | Obrigatório | Cadeia de caracteres | Valor de substituição para o **source** que corresponde ao parâmetro key. |



## Exemplos

### Retirar o nome de domínio conhecido

Você precisa retirar um nome de domínio conhecido do email de um usuário para obter um nome de usuário. <br> Por exemplo, se o domínio for "contoso.com", você pode usar a seguinte expressão:


**Expressão:** <br> `Replace([mail], "@contoso.com", "")`

**Entrada/saída de exemplo:** <br>

- **ENTRADA** (email): "john.doe@contoso.com"

- **SAÍDA**: "john.doe"



### Acrescentar sufixo constante ao nome de usuário

Se você estiver usando um Salesforce Sandbox, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.




**Expressão:** <br> `Append([userPrincipalName], ".test"))`

**Entrada/saída de exemplo:** <br>

- **ENTRADA**: (userPrincipalName): "John.Doe@contoso.com"


- **SAÍDA**: "John.Doe@contoso.com.test"





### Gerar o alias de usuário concatenando partes do nome e do sobrenome

Você precisa gerar um alias de usuário selecionando as três primeiras letras do nome do usuário e as cinco primeiras letras do sobrenome do usuário.


**Expressão:** <br> `Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

- **ENTRADA** (givenName): "John"

- **SAÍDA** (sobrenome): "Doe"

- **SAÍDA**: "JohDoe"




### Gerar data como uma cadeia de caracteres em um determinado formato

Você deseja enviar datas para um aplicativo SaaS em um determinado formato. <br> Por exemplo, você deseja formatar datas para o ServiceNow.



**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

- **ENTRADA** (extensionAttribute1): "20150123105347.1Z"

- **SAÍDA**: "2015-01-23"





### Substituir um valor com base em um conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no AD do Azure. <br> Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Australia/Sydney".


**Expressão:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

- **ENTRADA** (estado): "QLD"

- **SAÍDA**: "Australia/Brisbane"


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=06-->