<properties
	pageTitle="Sincronização de identidades e resiliência do atributo duplicado | Microsoft Azure"
	description="Novo comportamento de como lidar com objetos com conflitos de UPN ou ProxyAddress durante a sincronização de diretórios usando o Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="markusvi"/>



# Sincronização de identidades e resiliência do atributo duplicado
A Resiliência do Atributo Duplicado é um recurso do Azure Active Directory cuja finalidade é eliminar o atrito causado por conflitos de **UserPrincipalName** e **ProxyAddress** ao executar uma das ferramentas de sincronização da Microsoft.

Esses dois atributos geralmente precisam ser exclusivos em todos os objetos **User**, **Group** ou **Contact** em um determinado diretório do Azure Active Directory.

> [AZURE.NOTE] Somente os Usuários podem ter UPNs.

O novo comportamento permite que esse recurso esteja na parte de nuvem do pipeline de sincronização, portanto, é cliente independente e relevante para qualquer produto de sincronização da Microsoft, incluindo o Azure AD Connect, o DirSync e o MIM + Conector. O termo genérico "cliente de sincronização" é usado neste documento para representar qualquer um desses produtos.

## Comportamento atual
Se houver uma tentativa de provisionar um novo objeto com um valor ProxyAddress ou UPN que viola a restrição de exclusividade, o Azure Active Directory impedirá que esse objeto seja criado. Da mesma forma, se um objeto for atualizado com um UPN ou ProxyAddress não exclusivo, a atualização falhará. A tentativa de provisionamento ou a atualização é repetida pelo cliente de sincronização em cada ciclo de exportação e continua a falhar até que o conflito seja resolvido. Um email de relatório de erro é gerado após cada tentativa e o erro é registrado pelo cliente de sincronização.

## Comportamento com Resiliência do Atributo Duplicado
Em vez de falhar completamente em provisionar ou atualizar um objeto com um atributo duplicado, o Azure Active Directory "coloca em quarentena" o atributo duplicado que viola a restrição de exclusividade. Se esse atributo for necessário para o provisionamento, como um UserPrincipalName, o serviço atribuirá um valor de espaço reservado. O formato desses valores temporários é “***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”. Se o atributo não for necessário, como um **ProxyAddress**, o Azure Active Directory simplesmente colocará em quarentena o atributo em conflito e prosseguirá com a criação ou atualização do objeto.

Ao colocar em quarentena o atributo, as informações sobre o conflito são enviadas no mesmo email de relatório de erro usado no antigo comportamento. No entanto, essas informações só aparecem no relatório de erro uma vez, quando ocorre a quarentena; elas não continuam a ser registradas em log em emails futuros. Além disso, uma vez que a exportação deste objeto foi bem-sucedida, o cliente de sincronização não registra em log um erro nem tenta repetir a operação para criar/atualizar nos ciclos de sincronização subsequentes.

Para dar suporte a esse comportamento, foi adicionado um novo atributo às classes de objeto User, Group e Contact: **DirSyncProvisioningErrors**

Este é um atributo com valores múltiplos usado para armazenar os atributos conflitantes que violariam a restrição de exclusividade, caso adicionados normalmente. Uma tarefa de temporizador em segundo plano foi habilitada no Azure Active Directory, que é executada a cada hora para procurar por conflitos de atributo duplicado que foram resolvidos e remove automaticamente os atributos em questão da quarentena.

### Habilitando a Resiliência do Atributo Duplicado
O recurso pode ser habilitado baixando a versão mais recente do módulo PowerShell do Azure Active Directory e executando:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

No momento, o a resiliência do ProxyAddress e do UPN pode ser habilitada e desabilitada individualmente. Esse comportamento será alterado no futuro e ambos serão habilitados para todos os diretórios do Azure AD e não poderão ser desabilitados.

## Identificação de objetos com DirSyncProvisioningErrors
Atualmente, existem dois métodos para identificar os objetos que têm esses erros devido a conflitos de propriedade duplicada: o Azure Active Directory PowerShell e o Portal de Administração do Office 365. No futuro, há planos de estender para relatórios adicionais baseados no portal.

### Azure Active Directory PowerShell
Para os cmdlets do PowerShell neste tópico, o seguinte é verdadeiro:

- Todos os cmdlets a seguir diferenciam maiúsculas de minúsculas.
- O **–ErrorCategory PropertyConflict** sempre deve ser incluído. Atualmente, não há outros tipos de **ErrorCategory**, mas isso pode ser estendido no futuro.

Primeiro, comece executando **Connect-MsolService** e inserindo as credenciais para um administrador de locatário.

Em seguida, use os cmdlets e os operadores a seguir para exibir os erros de maneiras diferentes:

1. [Ver tudo](#see-all)

2. [Por Tipo de Propriedade](#by-property-type)

3. [Por Valor Conflitante](#by-conflicting-value)

4. [Usando uma Pesquisa da Cadeia de Caracteres](#using-a-string-search)

5. [Classificado](#sorted)

6. [Em uma Quantidade Limitada ou Todos](#in-a-limited-quantity-or-all)


#### Ver tudo
Uma vez conectado, para ver uma lista geral dos erros de provisionamento do atributo no locatário, execute:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isso produz um resultado semelhante ao seguinte: ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")


#### Por tipo de propriedade
Para ver os erros por tipo de propriedade, adicione o sinalizador **- PropertyName** com o argumento **UserPrincipalName** ou **ProxyAddresses**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### Por valor conflitante
Para ver os erros relacionados a uma propriedade específica, adicione o sinalizador **- PropertyValue** (**- PropertyName** também deve ser usado ao adicionar esse sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### Usando uma pesquisa da cadeia de caracteres
Para fazer uma pesquisa ampla da cadeia de caracteres, use o sinalizador **-SearchString**. Isso pode ser usado independentemente de todos os sinalizadores acima, com exceção de **-ErrorCategory PropertyConflict**, que é sempre necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### Classificado
Há dois sinalizadores que podem ser usados para classificar os resultados de uma determinada consulta:

1.	**SortField**: os argumentos válidos incluem DisplayName e UserPrincipalName

2.	**SortDirection**: os argumentos válidos incluem Ascending e Descending

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`

#### Em uma quantidade limitada ou todos
1. **MaxResults <Int>** pode ser usado para limitar a consulta a um número específico de valores.

2. **All** pode ser usado para garantir que todos os resultados sejam recuperados, no caso de existir um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Portal de administração do Office 365

Você pode exibir os erros de sincronização de diretórios no centro de administração do Office 365. O relatório no portal do Office 365 exibe apenas os objetos **User** que têm esses erros. Ele não mostra informações sobre conflitos entre **Groups**, **Contacts** ou **PublicFolders**.


![Usuários Ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Usuários Ativos")

Para obter instruções sobre como exibir erros de sincronização de diretórios no centro de administração do Office 365, confira [Identificar erros de sincronização de diretórios no Office 365](https://support.office.com/pt-BR/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### Relatório de erros de sincronização de identidades
Quando um objeto com um conflito de atributo duplicado é tratado com esse novo comportamento, uma notificação é incluída no email padrão do Relatório de Erros de Sincronização de Identidades enviado para o contato de Notificação Técnica do locatário. No entanto, há uma alteração importante nesse comportamento. No passado, as informações sobre um conflito de atributo duplicado eram incluídas em todos os relatórios de erro subsequentes até o conflito ser resolvido. Com esse novo comportamento, a notificação de erro para determinado conflito só aparece uma vez, no momento em que o atributo conflitante está de quarentena.

Este é exemplo da aparência da notificação por email de um conflito ProxyAddress: ![Usuários Ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Usuários Ativos")

## Resolução de conflitos
A estratégia da solução de problemas e as táticas de resolução desses erros não devem diferir da maneira como os erros de atributo duplicado eram tratados no passado. A única diferença é que a tarefa de temporizador examina o locatário no lado do serviço para adicionar automaticamente o atributo em questão ao devido objeto, assim que o conflito é resolvido.

O seguinte artigo descreve diversas estratégias de solução de problemas: [Atributos inválidos ou duplicados impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098).

## Problemas conhecidos
Nenhum desses problemas conhecidos causa degradação do serviço nem a perda de dados. Muitos são estéticos, outros fazem com que os erros de atributo duplicado de "*pré-resiliência*" padrão sejam gerados, em vez de colocarem em quarentena o atributo em conflito, e também fazem com que certos erros exijam uma correção manual extra.

**Comportamento básico:**

1. O usuário com uma configuração de atributo específica continua recebendo os erros de exportação, em vez dos atributos serem colocados em quarentena. Por exemplo:

    a. Um novo usuário é criado no AD com o UPN **Joe@contoso.com** e o ProxyAddress **smtp:Joe@contoso.com**

    b. Propriedades desse conflito de objetos com um Group existente, em que o ProxyAddress é **SMTP:Joe@contoso.com**.

    c. Durante a exportação, um erro de **conflito de ProxyAddress** é gerado, em vez de colocar os atributos em conflito de quarentena. A operação é repetida em cada ciclo de sincronização subsequente, como era antes do recurso de resiliência ser habilitado.

2. A tarefa de temporizador que procura por conflitos de atributo duplicado resolvidos compara apenas os conflitos UPN com outros conflitos UPN. Isso causa o problema mostrado na etapa 4 do cenário a seguir:

    a. **UserA@contoso.com** tem um UPN não exclusivo porque o ProxyAddress de outro objeto também tem esse valor.

    b. O Usuário A recebe um **UPN MOERA** temporário, **UserA1234@contoso.onmicrosoft.com**, e o valor real do UPN é colocado em quarentena (conforme o esperado).

    c. O outro objeto conflitante terá o ProxyAddress removido posteriormente.

    d. O UPN de Usuário A nunca é corrigido automaticamente; ele deve ser atualizado manualmente.

3. Se dois Groups forem criados localmente com o mesmo endereço SMTP, um falhará ao provisionar na primeira tentativa com um erro de **ProxyAddress** duplicado padrão. No entanto, o valor duplicado é devidamente colocado em quarentena após o próximo ciclo de sincronização.

**Cmdlets do PowerShell**:

1. **ImmutableId**/**LastDirSyncTime** não são exibidos para a classe de objeto User.

2. Os sinalizadores **SortField** e **SortDirection** não afetam os resultados.

3. Usar o sinalizador **PropertyValue** sem adicionar o sinalizador **PropertyName** gera um erro ambíguo.

4. O sinalizador **SearchString** retornará resultados extras se for executado sem os sinalizadores **PropertyValue** e **PropertyName**.

**Relatório do Portal do Office**:

1. A mensagem de erro detalhada para dois objetos em um conjunto de conflitos UPN é a mesma. Isso indica que ambos tiveram seus UPNS alterados/colocados em quarentena, quando, na verdade, apenas um deles teve os dados alterados.

2. A mensagem de erro detalhada de um conflito UPN mostra o displayName incorreto para um usuário que teve seu UPN alterado/colocado em quarentena. Por exemplo:

    a. O **Usuário A** é sincronizado primeiro com **UPN = User@contoso.com**.

    b. O **Usuário B** tenta ser sincronizado em seguida com **UPN = User@contoso.com**.

    c. O UPN do **Usuário B** é alterado para **User1234@contoso.onmicrosoft.com** e **User@contoso.com** é adicionado a **DirSyncProvisioningErrors**.

    d. A mensagem de erro para o **Usuário B** deve indicar que o **Usuário A** já tem **User@contoso.com** como um UPN, mas mostra o próprio displayName do **Usuário B**.

3. O relatório só pode exibir informações de erro detalhadas para os usuários com conflitos de **UPN**, não para aqueles com erros de **ProxyAddress** (ainda estamos investigando se isso é consistente ou ambiental).

## Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

- [Identificar erros de sincronização de diretórios no Office 365](https://support.office.com/pt-BR/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

<!---HONumber=AcomDC_0601_2016-->