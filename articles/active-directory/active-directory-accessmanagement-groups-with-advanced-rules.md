
<properties
	pageTitle="Usar atributos para criar regras avançadas | Microsoft Azure"
	description="Instruções para criar regras avançadas para um grupo, incluindo suporte para operadores de regra de expressões e parâmetros."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="curtand"/>


# Usar atributos para criar regras avançadas
O portal do Azure fornece a flexibilidade de configurar regras avançadas no AD do Azure (Active Directory do Azure) para habilitar associações dinâmicas mais complexas de grupos do AD do Azure.

**Para criar a regra avançada**, no portal do Azure, na guia **Configurar** do grupo, selecione a opção **Regra avançada** e digite a regra avançada na caixa de texto fornecida. Você pode criar sua regra avançada usando as seguintes informações.

## Construção do corpo de uma regra avançada
A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado verdadeiro ou falso. As três partes são:

- Parâmetro da esquerda
- Operador binário
- Constante à direita

Uma regra avançada completa é semelhante a esta: (leftParameter binaryOperator "RightConstant"), em que o parêntese de abertura e fechamento são necessários para toda a expressão binária, aspas duplas são necessárias para a constante à direita e a sintaxe do parâmetro esquerdo é user.property. Uma regra avançada pode consistir em mais de uma expressão binária separada pelos operadores lógicos -and, -or e -not. A seguir é exemplos de uma regra avançada construída de maneira adequada:

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir.

O comprimento total do corpo da sua regra avançada não pode exceder 255 caracteres.
> [AZURE.NOTE]Operações de cadeia de caracteres e regex diferenciam maiúsculas de minúsculas. Você também pode executar verificações de Null, usando $null como uma constante, por exemplo, user.department - eq $null. Cadeias de caracteres que contém aspas "devem ser ignorados usando ' caracteres, por exemplo, user.department - eq" Sa'"les".

##Operadores de regra de expressão com suporte
A tabela a seguir lista todos os operadores de regra de expressão com suporte e sua sintaxe a ser usada no corpo da regra avançada:

| Operador | Sintaxe |
|-----------------|----------------|
| Não é igual a | -ne |
| É igual a | -eq |
| Não começa com | -notStartsWith |
| Começa com | -startsWith |
| Não contém | -notContains |
| Contém: | -contains |
| Não corresponde | -notMatch |
| Corresponde | -match |


| Erro de análise de consulta | Erros de uso | Uso corrigido |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Erro: O atributo não tem suportado. | (user.invalidProperty -eq "Valor") | user.department - eq ("valor") Propriedade deve corresponder a um na lista de propriedades com suporte acima. |
| Erro: Operador não é tem suportada no atributo. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true) A propriedade é do tipo booleano. Use os operadores com suporte (-eq or -ne) em um tipo booleano da lista acima. |
| Erro: Erro de compilação de consulta. | (user.department - eq "Sales")- e (user.department - eq "Marketing") (user.userPrincipalName-match "*@domain.ext") | (user.department - eq "Sales")- e o operador lógico (user.department - eq "Marketing") deve corresponder a uma lista de propriedades com suporte acima. (user.userPrincipalName-corresponder ".*@domain.ext")or (user.userPrincipalName-match"@domain.ext$")Erro na expressão regular. |
| Erro: Expressão binária não está no formato correto. | user.department – eq ("Vendas") user.department - eq ("Vendas") user.department-eq ("Vendas") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") Consulta tem vários erros. Parênteses não no lugar certo. |
| Erro: Ocorreu um erro desconhecido durante a configuração de membros dinâmicos. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") Consulta tem vários erros. Parênteses não no lugar certo. |

##Parâmetros com suporte
Estas são todas as propriedades do usuário que você pode usar na regra avançada:

**Propriedades de tipo booleano**

Operadores permitidos

* -eq


* -ne


| Propriedades | Valores permitidos | Uso |
|----------------|-----------------|--------------------------------|
| accountEnabled | verdadeiro, falso | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**Propriedades do tipo cadeia de caracteres**

Operadores permitidos

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Propriedades | Valores permitidos | Uso |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Qualquer valor de cadeia de caracteres ou $null. | (user.city -eq "valor") |
| country | Qualquer valor de cadeia de caracteres ou $null. | (user.country -eq "valor") |
| department | Qualquer valor de cadeia de caracteres ou $null. | (user.department -eq "valor") |
| displayName | Qualquer valor de cadeia de caracteres. | (user. DisplayName -eq "valor") |
| facsimileTelephoneNumber | Qualquer valor de cadeia de caracteres ou $null. | user.facsimileTelephoneNumber -eq ("valor") |
| givenName | Qualquer valor de cadeia de caracteres ou $null. | user.givenName -eq ("valor") |
| jobTitle | Qualquer valor de cadeia de caracteres ou $null. | (user.jobTitle - eq "valor") |
| mail | Qualquer valor de cadeia de caracteres ou $null. Endereço SMTP do usuário. | (user.mail - eq "valor") |
| mailNickName | Qualquer valor de cadeia de caracteres. Alias de email do usuário. | (user.mailNickName - eq "valor") |
| Serviço Móvel | Qualquer valor de cadeia de caracteres ou $null. | (user.mobile -eq "valor") |
| ID do objeto | GUID do objeto de usuário | (user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Qualquer valor de cadeia de caracteres ou $null. | (user.physicalDeliveryOfficeName -eq "valor") |
| postalCode | Qualquer valor de cadeia de caracteres ou $null. | (user.postalCode - eq "valor") |
| preferredLanguage | ISO 639-1 code | (user.preferredLanguage - eq "pt-BR") |
| sipProxyAddress | Qualquer valor de cadeia de caracteres ou $null. | (user.sipProxyAddress -eq "valor") |
| state | Qualquer valor de cadeia de caracteres ou $null. | (user.state -eq "valor") |
| streetAddress | Qualquer valor de cadeia de caracteres ou $null. | (user.streetAddress -eq "valor") |
| sobrenome | Qualquer valor de cadeia de caracteres ou $null. | (user.surname -eq "valor") |
| telephoneNumber | Qualquer valor de cadeia de caracteres ou $null. | (user.telephoneNumber -eq "valor") |
| usageLocation | Código do país indicados dois | (user.usageLocation -eq "EUA") |
| userPrincipalName | Qualquer valor de cadeia de caracteres. | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (ser.userType -eq "Membro") |

**Propriedades de coleção de cadeias de caracteres de tipo**

Operadores permitidos

* -contains


* -notContains

| Properties | Valores permitidos | Uso |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Um valor de cadeia de caracteres. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP:alias@domainsmtp:alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Regra de relatórios diretos
Agora você pode preencher os membros de um grupo com base no atributo gerenciador de um usuário.
Para configurar um grupo como "Gerenciador"
--------------------------------------------------------------------------------
1. No portal do administrador, clique na guia **Configurar** e selecione **REGRA AVANÇADA**.
2. Digite a regra com a seguinte sintaxe: Relatórios diretos para *Relatórios diretos para {UserID\_of\_manager}*
3. Ao salvar essa regra, todos os usuários que atendem à regra serão adicionados como membros do grupo. Observe que pode levar alguns minutos para o preenchimento inicial do grupo.


## Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Solucionando problemas de associações dinâmicas a grupos](active-directory-accessmanagement-troubleshooting.md)

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->