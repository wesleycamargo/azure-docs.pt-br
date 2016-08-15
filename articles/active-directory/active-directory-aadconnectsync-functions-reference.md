<properties
	pageTitle="Azure AD Connect Sync: referência de funções | Microsoft Azure"
	description="Referência de expressões de provisionamento declarativo na sincronização do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="andkjell;markvi"/>


# Azure AD Connect Sync: referência de funções

No Azure Active Directory Sync, as funções são usadas para manipular um valor de atributo durante a sincronização. A sintaxe das funções é expressa usando o seguinte formato: `<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita diversas sintaxes, todas as sintaxes válidas são listadas. As funções são fortemente tipadas e verificam que o tipo passado corresponde ao tipo documentado. Se o tipo não corresponde, um erro é gerado.

Os tipos são expressos com a seguinte sintaxe:

- **bin** - binário
- **bool** - booliano
- **dt** - data/hora UTC
- **enum** - enumeração das constantes conhecidas
- **exp** - expressão, que espera-se que seja avaliada como um valor Booliano
- **mvbin** - binário de vários valores
- **mvstr** - Cadeia de Caracteres com Vários Valores
- **mvstr** - Referência de Vários Valores
- **num** - numérico
- **ref** - referência de valor único
- **str** - cadeia de caracteres de valor único
- **var** - uma variante de (quase) qualquer outro tipo
- **void** - não retorna um valor

## Referência de funções

Lista de funções | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversão** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Data / hora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Now](#now)
[NumFromDate](#numfromdate) |  
**Diretório** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Avaliação** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matemática** |  
[BitAnd](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**De valores múltiplos** |  
[Contém:](#contains) | [Contagem](#count) | [Item](#item) | [ItemOrNull](#itemornull)
[Join](#join) | [RemoveDuplicates](#removeduplicates) | [Divisão](#split) |
**Fluxo do Programa** |  
[Erro](#error) | [IIF](#iif) | [Switch](#switch)
**Texto** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[Left](#left) | [Len](#len) | [LTrim](#ltrim) | [Mid](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Substitua](#replace)
[ReplaceChars](#replacechars) | [Right](#right) | [RTrim](#rtrim) | [Trim](#trim)
[UCase](#ucase) | [Word](#word)

----------
### BitAnd

**Descrição:** a função BitAnd define os bits especificados em um valor.

**Sintaxe:** `num BitAnd(num value1, num value2)`

- value1, value2: valores numéricos que devem ser agrupados com AND

**Comentários:** esta função converte ambos os parâmetros para a representação binária e define um bit como:

- 0 - se um ou ambos os bits correspondentes na *máscara* e no *sinalizador* são 0
- 1 - se ambos os bits correspondentes são 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:** `BitAnd(&HF, &HF7)` Retorna 7, já que os hexadecimais "F" AND "F7" são avaliados como esse valor.

----------
### BitOr

**Descrição:** a função BitOr define os bits especificados em um valor.

**Sintaxe:** `num BitOr(num value1, num value2)`

- value1, value2: valores numéricos que devem ser agrupados com OR

**Comentários:** esta função converte ambos os parâmetros para a representação binária e define um bit como 1 se um ou ambos os bits correspondentes na máscara e sinalizador forem 1; ou então define o bit como 0, se ambos os bits correspondentes forem 0. Em outras palavras, ele retorna 1 em todos os casos, exceto naqueles em que os bits correspondentes de ambos os parâmetros são 0.

----------
### CBool

**Descrição:** a função CBool retorna um valor booliano com base na expressão avaliada

**Sintaxe:** `bool CBool(exp Expression)`

**Comentários:** se a expressão é avaliada como um valor diferente de zero, CBool retorna True; caso contrário, retorna False.

**Exemplo:** `CBool([attrib1] = [attrib2])`

Retorna True se ambos os atributos têm o mesmo valor.

----------
### CDate

**Descrição:** a função CDate retorna, de uma cadeia de caracteres, um atributo DateTime UTC. DateTime não é um tipo de atributo nativo no Sync, mas é usado por algumas funções.

**Sintaxe:** `dt CDate(str value)`

- Value: uma cadeia de caracteres com uma data, hora e opcionalmente um fuso horário

**Comentários:** a cadeia de caracteres retornada é sempre em UTC.

**Exemplo:** `CDate([employeeStartTime])` retorna um DateTime com base na hora de início do funcionário

`CDate("2013-01-10 4:00 PM -8")` Retorna um DateTime que representa "2013-01-11 12:00 AM"

----------
### CGuid

**Descrição:** a função CGuid converte a representação de cadeia de caracteres de um GUID em sua representação binária.

**Sintaxe:** `bin CGuid(str GUID)`

- Uma cadeia de caracteres formatada nesse padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### Contém:

**Descrição:** a função Contains localiza uma cadeia de caracteres dentro de um atributo com vários valores

**Sintaxe:** `num Contains (mvstring attribute, str search)` - diferencia maiúsculas de minúsculas `num Contains (mvstring attribute, str search, enum Casetype)``num Contains (mvref attribute, str search)` - diferencia maiúsculas de minúsculas

- attribute: o atributo com vários valores pelo qual pesquisar.<br>
- search: cadeia de caracteres a localizar no atributo.<br>
- Casetype: CaseInsensitive ou CaseSensitive.<br>

Retorna o índice no atributo com vários valores em que a cadeia de caracteres foi encontrada. Se a cadeia de caracteres não for encontrada, 0 será retornado.

**Comentários:** para atributos com vários valores de cadeia de caracteres, a pesquisa encontrará subcadeias de caracteres nos valores. Para atributos de referência, a cadeia de caracteres pesquisada deve corresponder exatamente ao valor para que sejam considerados como uma correspondência.

**Exemplo:** `IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))` se o atributo proxyAddresses tem um endereço de email principal (indicado por letras maiúsculas "SMTP:"), o atributo proxyAddress é retornado; caso contrário, um erro é retornado.

----------
### ConvertFromBase64

**Descrição:** a função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.

**Sintaxe:** `str ConvertFromBase64(str source)`<br> - adota Unicode para a codificação de `str ConvertFromBase64(str source, enum Encoding)`

- source: cadeia de caracteres codificada em Base64
- Codificação: Unicode, ASCII, UTF8

**Exemplo:** `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")``ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos retornam "*Hello world!*"

----------
### ConvertFromUTF8Hex

**Descrição:** a função ConvertFromUTF8Hex converte o valor codificado em UTF8 hexadecimal especificado em uma cadeia de caracteres.

**Sintaxe:** `str ConvertFromUTF8Hex(str source)`

- source: cadeia de caracteres codificada em UTF8 de 2 bytes

**Comentários:** a diferença entre essa função e ConvertFromBase64(,UTF8) é que o resultado é amigável para o atributo DN. Esse formato é usado pelo Active Directory do Azure como DN.

**Exemplo:** `ConvertFromUTF8Hex("48656C6C6F20776F726C6421")` retorna "*Hello world!*"

----------
### ConvertToBase64

**Descrição:** a função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Unicode em base64. Converte o valor de uma matriz de inteiros em sua representação equivalente de cadeia de caracteres, que é codificada com dígitos em base 64.

**Sintaxe:** `str ConvertToBase64(str source)`

**Exemplo:** `ConvertToBase64("Hello world!")` retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### ConvertToUTF8Hex

**Descrição:** a função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado em UTF8 hexadecimal.

**Sintaxe:** `str ConvertToUTF8Hex(str source)`

**Comentários:** o formato de saída dessa função é usado pelo Azure Active Directory como formato do atributo DN.

**Exemplo:** `ConvertToUTF8Hex("Hello world!")` retorna 48656C6C6F20776F726C6421

----------
### Contagem

**Descrição:** a função Count retorna o número de elementos em um atributo com valores múltiplos

**Sintaxe:** `num Count(mvstr attribute)`

----------
### CNum

**Descrição:** a função CNum usa uma cadeia de caracteres e retorna um tipo de dados numérico.

**Sintaxe:** `num CNum(str value)`

----------
### CRef

**Descrição:** converte uma cadeia de caracteres em um atributo de referência

**Sintaxe:** `ref CRef(str value)`

**Exemplo:** `CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### CStr

**Descrição:** a função CStr o converte em um tipo de dados String.

**Sintaxe:** `str CStr(num value)``str CStr(ref value)``str CStr(bool value)`

- valor: pode ser um valor numérico, o atributo de referência ou booliano.

**Exemplo:** `CStr([dn])` poderia retornar “cn=Pedro,dc=contoso,dc=com”

----------
### DateAdd

**Descrição:** retorna um parâmetro Date, contendo uma data à qual um intervalo de tempo especificado foi adicionado.

**Sintaxe:** `dt DateAdd(str interval, num value, dt date)`

- interval: expressão de cadeia de caracteres que é o intervalo de tempo que você deseja adicionar. A cadeia de caracteres deve ter um dos seguintes valores:
 - aaaa Ano
 - q - Trimestre
 - m - Mês
 - y - Dia do ano
 - d - Dia
 - w - Dia da semana
 - ww - Semana
 - h - Hora
 - m - Minuto
 - s - Segundo
- valor: O número de unidades que você deseja adicionar. Ele pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
- date: DateTime, representando a data à qual o intervalo é adicionado.

**Exemplo:** `DateAdd("m", 3, CDate("2001-01-01"))` adiciona três meses e retorna para um DateTime que representa "1/4/2001"

----------
### DateFromNum

**Descrição:** a função DateFromNum converte um valor, de formato de data do AD, em um tipo DateTime.

**Sintaxe:** `dt DateFromNum(num value)`

**Exemplo:** `DateFromNum([lastLogonTimestamp])` `DateFromNum(129699324000000000)` retorna um DateTime que representa 2012-01-01 23:00:00

----------
### DNComponent

**Description:** The DNComponent function returns the value of a specified DN component going from left.

**Sintaxe:** `str DNComponent(ref dn, num ComponentNumber)`

- dn: o atributo de referência a interpretar
- ComponentNumber: o componente no DN a retornar

**Exemplo:** `DNComponent([dn],1)` se dn é “cn=Pedro,ou=…, ele retorna Pedro

----------
### DNComponentRev

**Descrição:** a função DNComponentRev retornará o valor de um componente DN especificado, saindo da direita (o final).

**Sintaxe:** `str DNComponentRev(ref dn, num ComponentNumber)` `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: o atributo de referência a interpretar
- ComponentNumber - o componente no DN a retornar
- Opções: DC – ignorar todos os componentes com “dc=”

**Exemplo:** se dn é "cn = Pedro, ou = São Paulo, ou = SP, ou = BR, dc = contoso, dc = com", `DNComponentRev([dn],3)` `DNComponentRev([dn],1,"DC")` ambos retornam Brasil.

----------
### Erro

**Descrição:** a função Error é usada para retornar um erro personalizado.

**Sintaxe:** `void Error(str ErrorMessage)`

**Exemplo:** `IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))` se o atributo accountName não estiver presente, gere um erro no objeto.

----------
### EscapeDNComponent

**Descrição:** a função EscapeDNComponent usa um componente de um DN e escapa-o para que ele possa ser representado no LDAP.

**Sintaxe:** `str EscapeDNComponent(str value)`

**Exemplo:** `EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)` garante que o objeto possa ser criado em um diretório LDAP, mesmo que o atributo displayName tenha caracteres que devam ser escapados no protocolo LDAP.

----------
### FormatDateTime

**Descrição:** a função FormatDateTime é usada para formatar um DateTime para uma cadeia de caracteres com um formato especificado

**Sintaxe:** `str FormatDateTime(dt value, str format)`

- value: um valor no formato DateTime
- format: uma cadeia de caracteres que representa o formato para o qual converter.

**Comentários**: os possíveis valores para o formato podem ser encontrados aqui: [Formatos de data/hora definidos pelo usuário (função Format)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)

**Exemplo:**

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")` Resulta em "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")` Pode resultar em "20140905081453.0Z"

----------
### GUID

**Descrição:** a função GUID gera um novo GUID aleatório

**Sintaxe:** `str GUID()`

----------
### IIF

**Descrição:** a função IIF retorna um valor de um conjunto de valores possíveis com base em uma condição especificada.

**Sintaxe:** `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: qualquer valor ou expressão que pode ser avaliada como true ou false.
- valueIfTrue: um valor que será retornado se condition for avaliada como true.
- valueIfFalse: um valor que será retornado se condition for avaliada como false.

**Exemplo:** `IIF([employeeType]="Intern","t-" & [alias],[alias])` retorna o alias de um usuário com"t-" adicionado ao seu início se o usuário for um estagiário; caso contrário, retorna o alias do usuário como está.

----------
### InStr

**Descrição:** a função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:**

`num InStr(str stringcheck, str stringmatch)` `num InStr(str stringcheck, str stringmatch, num start)` `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: cadeia de caracteres a ser pesquisada
- stringmatch: cadeia de caracteres a ser localizada
- start: posição inicial para se localizar a subcadeia de caracteres
- compare: vbTextCompare ou vbBinaryCompare

**Comentários:** retorna a posição em que a subcadeia de caracteres tiver sido encontrada ou 0 se ela não tiver sido encontrada.

**Exemplo:** `InStr("The quick brown fox","quick")` é avaliado como 5

`InStr("repEated","e",3,vbBinaryCompare)` é avaliado como 7

----------
### InStrRev

**Descrição:** a função InStrRev localiza a última ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:** `num InstrRev(str stringcheck, str stringmatch)` `num InstrRev(str stringcheck, str stringmatch, num start)` `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: cadeia de caracteres a ser pesquisada
- stringmatch: cadeia de caracteres a ser localizada
- start: posição inicial para se localizar a subcadeia de caracteres
- compare: vbTextCompare ou vbBinaryCompare

**Comentários:** retorna a posição em que a subcadeia de caracteres tiver sido encontrada ou 0 se ela não tiver sido encontrada.

**Exemplo:** `InStrRev("abbcdbbbef","bb")` retorna 7.

----------
### IsBitSet

**Descrição:** a função IsBitSet testa se um bit está definido ou não

**Sintaxe:** `bool IsBitSet(num value, num flag)`

- value: um valor numérico que é evaluated.flag: um valor numérico que contém o bit a ser avaliado

**Exemplo:** `IsBitSet(&HF,4)` retorna True porque o bit "4" está definido no valor hexadecimal "F"

----------
### IsDate

**Descrição:** a função IsDate será avaliada como True se a expressão puder ser avaliada como um tipo DateTime.

**Sintaxe:** `bool IsDate(var Expression)`

**Comentários:** usada para determinar se CDate() será bem-sucedida.

----------
### IsEmpty

**Descrição:** a função IsEmpty é avaliada como True se o atributo está presente no CS ou MV, mas é avaliada como uma cadeia de caracteres vazia.

**Sintaxe:** `bool IsEmpty(var Expression)`

----------
### IsGuid

**Descrição:** a função IsGuid é avaliada como true se a cadeia de caracteres pode ser convertida em um GUID.

**Sintaxe:** `bool IsGuid(str GUID)`

**Comentários:** um GUID é definido como uma cadeia de caracteres seguindo um destes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

usada para determinar se CGuid() será bem-sucedida.

**Exemplo:** `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` se StrAttribute tiver um formato de GUID, ela retornará uma representação binária; caso contrário, retornará um valor nulo.

----------
### IsNull

**Descrição:** a função IsNull retornará true se a expressão for avaliada como nula.

**Sintaxe:** `bool IsNull(var Expression)`

**Comentários:** para um atributo, um valor nulo é expresso pela ausência do atributo.

**Exemplo:** `IsNull([displayName])` retorna True se o atributo não está presente no CS ou MV.

----------
### IsNullOrEmpty

**Descrição:** a função IsNullOrEmpty retornará true se a expressão for nula ou for uma cadeia de caracteres vazia.

**Sintaxe:** `bool IsNullOrEmpty(var Expression)`

**Comentários:** para um atributo, isso poderia ser avaliado como True se o atributo estivesse ausente, ou estivesse presente mas fosse uma cadeia de caracteres vazia.<br> O inverso dessa função é chamado de IsPresent.

**Exemplo:** `IsNullOrEmpty([displayName])` retorna True se o atributo não está presente ou é uma cadeia de caracteres vazia no CS ou MV.

----------
### IsNumeric

**Descrição:** a função IsNumeric retorna um valor booliano que indica se uma expressão pode ser avaliada como um tipo numérico.

**Sintaxe:** `bool IsNumeric(var Expression)`

**Comentários:** usada para determinar se CNum() terá sucesso ao analisar a expressão.

----------
### IsString

**Descrição:** a função IsString será avaliada como True se a expressão puder ser avaliada como um tipo de cadeia de caracteres.

**Sintaxe:** `bool IsString(var expression)`

**Comentários:** usada para determinar se CStr() terá sucesso ao analisar a expressão.

----------
### IsPresent

**Descrição:** a função IsPresent retornará true se a expressão for avaliada como uma cadeia de caracteres que não é nula e não está vazia.

**Sintaxe:** `bool IsPresent(var expression)`

**Comentários:** o inverso dessa função é chamado de IsNullOrEmpty.

**Exemplo:** `Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### Item

**Descrição:** a função Item retorna um item de um atributo/cadeia de caracteres com vários valores.

**Sintaxe:** `var Item(mvstr attribute, num index)`

- attribute: atributo com valores múltiplos
- index: índice para um item na cadeia de caracteres com vários valores.

**Comentários:** a função Item é útil com a função Contains, desde que a última função retorne o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice está fora dos limites.

**Exemplo:** `Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)` retorna o endereço de email principal.

----------
### ItemOrNull

**Descrição:** a função ItemOrNull retorna um item de um atributo/cadeia de caracteres com vários valores.

**Sintaxe:** `var ItemOrNull(mvstr attribute, num index)`

- attribute: atributo com valores múltiplos
- index: índice para um item na cadeia de caracteres com vários valores.

**Comentários:** a função ItemOrNull é útil junto à função Contains, desde a última função retorne o índice de um item no atributo de vários valores.

Retorna um valor nulo se o índice está fora dos limites.

----------
### Join

**Descrição:** a função Join usa uma cadeia de caracteres de valores múltiplos e retorna uma cadeia de caracteres de valor único com separador especificado inserido entre cada item.

**Sintaxe:** `str Join(mvstr attribute)` `str Join(mvstr attribute, str Delimiter)`

- attribute: um atributo de valores múltiplos contendo cadeias de caracteres a serem unidas.
- delimiter: qualquer cadeia de caracteres usada para separar as subcadeias de caracteres na cadeia de caracteres retornada. Se omitido, o caractere de espaço (" ") é usado. Se o Delimitador é uma cadeia de caracteres de comprimento zero ("") ou Nada, todos os itens na lista são concatenados sem delimitadores.

**Comentários** há paridade entre as funções Join e Split. A função Join pega uma matriz de cadeias de caracteres e une-as usando uma cadeia de caracteres do delimitador, para retornar uma única cadeia de caracteres. A função Split pega uma cadeia de caracteres e a separa no delimitador, para retornar uma matriz de cadeias de caracteres. No entanto, uma diferença importante é que a Join pode concatenar cadeias de caracteres com qualquer cadeia de caracteres delimitadora, enquanto Split só pode separar cadeias de caracteres usando um único caractere delimitador.

**Exemplo:** `Join([proxyAddresses],",")` pode retornar: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### LCase

**Descrição:** a função LCase converte todos os caracteres em uma cadeia de caracteres em letras minúsculas.

**Sintaxe:** `str LCase(str value)`

**Exemplo:** `LCase("TeSt")` retorna "test".

----------
### Left

**Descrição:** a função Left retorna um número especificado de caracteres do lado esquerdo de uma cadeia de caracteres.

**Sintaxe:** `str Left(str string, num NumChars)`

- string: a cadeia de caracteres da qual retornar caracteres
- NumChars: um número que identifica o número de caracteres a ser retroando do início (esquerda) da cadeia de caracteres

**Comentários:** uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos caracteres do que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia de caracteres (ou seja, que contém todos os caracteres no parâmetro 1) será retornada.

**Exemplo:** `Left("John Doe", 3)` retorna "Joh".

----------
### Len

**Descrição:** a função Len retorna o número de caracteres em uma cadeia de caracteres.

**Sintaxe:** `num Len(str value)`

**Exemplo:** `Len("John Doe")` retorna 8

----------
### LTrim

**Descrição:** a função LTrim remove espaços em branco à esquerda de uma cadeia de caracteres.

**Sintaxe:** `str LTrim(str value)`

**Exemplo:** `LTrim(" Test ")` retorna "Test"

----------
### Mid

**Descrição:** a função Mid retorna um número especificado de caracteres de uma posição especificada em uma cadeia de caracteres.

**Sintaxe:** `str Mid(str string, num start, num NumChars)`

- string: a cadeia de caracteres da qual retornar caracteres
- start: um número que identifica a posição inicial na cadeia de caracteres da qual retornar caracteres
- NumChars: um número que identifica o número de caracteres a ser retornado da posição

**Comentários:** retorna caracteres numChars desde a posição inicial na cadeia de caracteres. Uma cadeia de caracteres contendo caracteres numChars desde a posição inicial na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se start > o comprimento da cadeia de caracteres, retorne a cadeia de caracteres de entrada.
- Se start <= 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se não houver caracteres numChar restantes na cadeia de caracteres na posição inicial, serão retornados tantos caracteres quanto for possível retornar.

**Exemplo:** `Mid("John Doe", 3, 5)` retorna "hn Do".

`Mid("John Doe", 6, 999)` retorna "Doe"

----------
### Now

**Descrição:** a função Now retorna um DateTime especificando a data e a hora atual, de acordo a data e hora do sistema do seu computador.

**Sintaxe:** `dt Now()`

----------
### NumFromDate

**Descrição:** a função NumFromDate retorna uma data no formato de data do AD.

**Sintaxe:** `num NumFromDate(dt value)`

**Exemplo:** `NumFromDate(CDate("2012-01-01 23:00:00"))` retorna 129699324000000000

----------
### PadLeft

**Descrição:** a função PadLeft preenche à esquerda uma cadeia de caracteres até um tamanho especificado usando um caractere de preenchimento fornecido.

**Sintaxe:** `str PadLeft(str string, num length, str padCharacter)`

- string: a cadeia de caracteres a preencher.
- length: um inteiro que representa o comprimento da cadeia de caracteres desejado.
- padCharacter: uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários:**

- Se o comprimento da cadeia de caracteres for menor que length, padCharacter será acrescentado repetidamente ao início (esquerda) da cadeia de caracteres até que ela tenha um comprimento igual a length.
- PadCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
- Se o comprimento da cadeia de caracteres é igual ou maior que length, a cadeia de caracteres é retornada inalterada.
- Se a cadeia de caracteres tem um comprimento maior que ou igual a length, uma cadeia de caracteres idêntica à cadeia de caracteres em questão será retornada.
- Se o comprimento da cadeia de caracteres for menor que length, uma nova cadeia de caracteres do comprimento desejado é retornada, contendo a cadeia de caracteres preenchida com um padCharacter.
- Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:** `PadLeft("User", 10, "0")` retorna "000000User".

----------
### PadRight

**Descrição:** a função PadRight preenche à direita uma cadeia de caracteres até um comprimento especificado usando um caractere de preenchimento fornecido.

**Sintaxe:** `str PadRight(str string, num length, str padCharacter)`

- string: a cadeia de caracteres a preencher.
- length: um inteiro que representa o comprimento da cadeia de caracteres desejado.
- padCharacter: uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários:**

- Se o comprimento da cadeia de caracteres for menor que length, padCharacter será acrescentado repetidamente ao final (direita) da cadeia de caracteres até que ela tenha um comprimento igual a length.
- padCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
- Se o comprimento da cadeia de caracteres é igual ou maior que length, a cadeia de caracteres é retornada inalterada.
- Se a cadeia de caracteres tem um comprimento maior que ou igual a length, uma cadeia de caracteres idêntica à cadeia de caracteres em questão será retornada.
- Se o comprimento da cadeia de caracteres for menor que length, uma nova cadeia de caracteres do comprimento desejado é retornada, contendo a cadeia de caracteres preenchida com um padCharacter.
- Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:** `PadRight("User", 10, "0")` retorna "User000000".

----------
### PCase

**Descrição:** a função PCase converte em letras maiúsculas o primeiro caractere de cada palavra delimitada por espaço em uma cadeia de caracteres, enquanto todos os outros caracteres são convertidos em letras minúsculas.

**Sintaxe:** `String PCase(string)`

**Comentários:**

- Essa função atualmente não fornece o uso de maiúsculas apropriado para converter uma palavra que está totalmente em letras maiúsculas, como um acrônimo.

**Exemplo:** `PCase("TEsT")` retorna "TEST".

`PCase(LCase("TEST"))` Retorna "Teste"

----------
### RandomNum

**Descrição:** a função RandomNum retorna um número aleatório entre um intervalo especificado.

**Sintaxe:** `num RandomNum(num start, num end)`

- start: um número que identifica o limite inferior do valor aleatório a ser gerado
- end: um número que identifica o limite superior do valor aleatório a gerar

**Exemplo:** `Random(100,999)` pode retornar 734.

----------
### RemoveDuplicates

**Descrição:** a função RemoveDuplicates usa uma cadeia de caracteres com vários valores e verifica se cada valor é exclusivo.

**Sintaxe:** `mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:** `RemoveDuplicates([proxyAddresses])` retorna um atributo proxyAddress corrigido em que todos os valores duplicados foram removidos.

----------
### Substitua

**Descrição:** a função Replace substitui todas as ocorrências de uma cadeia de caracteres por outra cadeia de caracteres.

**Sintaxe:** `str Replace(str string, str OldValue, str NewValue)`

- string: uma cadeia de caracteres na qual substituir valores.
- OldValue: a cadeia de caracteres pela qual pesquisar e a qual substituir.
- NewValue: a cadeia de caracteres a substituir.

**Comentários:** a função reconhece os seguintes monikers especiais:

- \\n - Nova linha
- \\r - Retorno de carro
- \\t - Guia

**Exemplo:** `Replace([address],"\r\n",", ")` substitui CRLF por uma vírgula e um espaço e pode levar a "One Microsoft Way, Redmond, WA, USA"

----------
### ReplaceChars

**Descrição:** a função ReplaceChars substitui todas as ocorrências de caracteres encontradas na cadeia de caracteres ReplacePattern.

**Sintaxe:** `str ReplaceChars(str string, str ReplacePattern)`

- string: uma cadeia de caracteres na qual substituir caracteres.
- ReplacePattern: uma cadeia de caracteres que contém um dicionário com caracteres a substituir.

O formato é {origem1}:{destino1},{origem2}:{destino2},{origemN},{destinoN}, em que a origem é o caractere a localizar e destino é a cadeia de caracteres com a qual trabalhar.

**Comentários:**

- A função considera cada ocorrência de origens definidas e as substitui pelos destinos.
- A origem deve ter exatamente um caractere (unicode).
- A origem não pode ser vazia nem maior que um caractere (erro de análise).
- O destino pode ter vários caracteres, por exemplo, ö:oe, β:ss.
- O destino pode ser vazio, o que indica que o caractere deve ser removido.
- A origem diferencia maiúsculas de minúsculas e deve ser uma correspondência exata.
- A , (vírgula) e : (dois-pontos) são caracteres reservados e não podem ser substituídos usando essa função.
- Espaços e outros caracteres em branco na cadeia de caracteres ReplacePattern são ignorados.

**Exemplo:** `%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)` retorna Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)` retorna "ONeil", o único tique é definido para ser removido.

----------
### Right

**Descrição:** a função Right retorna um número especificado de caracteres do lado direito (final) de uma cadeia de caracteres.

**Sintaxe:** `str Right(str string, num NumChars)`

- string: a cadeia de caracteres da qual retornar caracteres
- NumChars: um número que identifica o número de caracteres a ser retornado do final (direita) da cadeia de caracteres

**Comentários:** os caracteres de NumChars são retornados desde a última posição da cadeia de caracteres.

Uma cadeia de caracteres que contém os últimos caracteres numChars na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos caracteres do que o número especificado em NumChars, uma cadeia de caracteres idêntica à cadeia de caracteres será retornada.

**Exemplo:** `Right("John Doe", 3)` retorna "Doe".

----------
### RTrim

**Descrição:** a função RTrim remove espaços em branco ao final de uma cadeia de caracteres.

**Sintaxe:** `str RTrim(str value)`

**Exemplo:** `RTrim(" Test ")` retorna "TEST".

----------
### Divisão

**Descrição:** a função Split usa uma cadeia de caracteres separada por um delimitador e a transforma em uma cadeia de caracteres de valores múltiplos.

**Sintaxe:** `mvstr Split(str value, str delimiter)` `mvstr Split(str value, str delimiter, num limit)`

- valor: a cadeia de caracteres com um caractere delimitador para separar.
- delimitador: um único caractere a ser usado como o delimitador.
- limite: número máximo de valores a serem retornados.

**Exemplo:** `Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")` retorna uma cadeia de caracteres com valores múltiplos e com 2 elementos úteis para o atributo proxyAddress.

----------
### StringFromGuid

**Descrição:** a função StringFromGuid usa um GUID binário e o converte-o em uma cadeia de caracteres

**Sintaxe:** `str StringFromGuid(bin GUID)`

----------
### StringFromSid

**Descrição:** a função StringFromSid converte uma matriz de bytes ou uma matriz de bytes com valores múltiplos que contém um identificador de segurança em uma cadeia de caracteres ou cadeia de caracteres com valores múltiplos.

**Sintaxe:** `str StringFromSid(bin ObjectSID)` `mvstr StringFromSid(mvbin ObjectSID)`

----------
### Switch

**Descrição:** a função Switch é usada para retornar um único valor com base em condições avaliadas.

**Sintaxe:** `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: expressão variante que você deseja avaliar.
- value: valor a ser retornado se a expressão correspondente for True.

**Comentários:** a lista de argumentos da função Switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita e o valor associado à primeira expressão avaliada como True é retornado. Se as partes não tiverem pares adequados, ocorrerá um erro em tempo de execução.

Por exemplo, se expr1 for True, o comutador retornará valor1. Se expr-1 for False, mas expr-2 for True, Switch retorna valor-2 e assim por diante.

Switch retorna um Nothing se:

- Nenhuma das expressões são True.
- A primeira expressão True tem um valor correspondente que é Null.

O Switch avalia todas as expressões, mesmo que retorne apenas uma delas. Por essa razão, você deve tomar cuidado com efeitos colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resulta em um erro de divisão por zero, ocorrerá um erro.

Valor também pode ser a função Error, que retornaria uma cadeia de caracteres personalizada.

**Exemplo:** `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))` retorna o idioma falado em algumas das maiores cidades; caso contrário, retorna um erro.

----------
### Trim

**Descrição:** a função Trim remove espaços em branco à esquerda e à direita de uma cadeia de caracteres.

**Sintaxe:** `str Trim(str value)` `mvstr Trim(mvstr value)`

**Exemplo:** `Trim(" Test ")` retorna "TEST".

`Trim([proxyAddresses])` remove espaços à direita e à esquerda para cada valor no atributo proxyAddress.

----------
### UCase

**Descrição:** a função UCase converte todos os caracteres de uma cadeia de caracteres em letras maiúsculas.

**Sintaxe:** `str UCase(str string)`

**Exemplo:** `UCase("TeSt")` retorna "TEST".

----------
### Word

**Descrição:** a função Word retorna uma palavra contida em uma cadeia de caracteres com base nos parâmetros que descrevem os delimitadores a serem usados e o número de palavras a serem retornadas.

**Sintaxe:** `str Word(str string, num WordNumber, str delimiters)`

- string: a cadeia de caracteres da qual retornar uma palavra
- WordNumber: um número que identifica o número de palavras que deve ser retornado.
- delimitadores: uma cadeia de caracteres que representa o delimitador(es) que deve ser usado para identificar palavras

**Comentários:** cada cadeia de caracteres separada por um dos caracteres delimitadores na cadeia de caracteres é identificada como palavra:

- Se number < 1, retorna uma cadeia de caracteres vazia.
- Se a cadeia de caracteres for nula, retorna a cadeia de caracteres vazia.

Se a cadeia de caracteres contém um número menor que o número de palavras ou cadeia de caracteres não contém quaisquer palavras identificadas por delimitadores, uma cadeia de caracteres vazia é retornada.

**Exemplo:** `Word("The quick brown fox",3," ")` retorna "brown"

`Word("This,string!has&many separators",3,",!&#")` retornaria "has"

## Recursos adicionais

* [Noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0803_2016-->