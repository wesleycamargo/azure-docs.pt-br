<properties
   pageTitle="Sincronização do Azure AD Connect: práticas noções básicas sobre a configuração padrão | Microsoft Azure"
   description="Este artigo descreve a configuração padrão na sincronização do Azure AD Connect."
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
   ms.date="12/21/2015"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: noções básicas sobre a configuração padrão

Este artigo explica as regras da configuração pronta para uso. Ele documenta as regras e como elas afetarão a configuração. Ele também o orienta durante a configuração padrão da sincronização do Azure AD Connect. O objetivo é que o leitor compreenda como o modelo de configuração, chamado de provisionamento declarativo, está funcionando em um exemplo do mundo real. Este artigo pressupõe que você já instalou e configurou a sincronização do Azure AD Connect usando o assistente de instalação.

## Regras prontas para uso do local ao AD do Azure

As expressões a seguir podem ser encontradas na configuração pronta para uso.

As regras são expressas como regras que devem ser atendidas e objeto que deve ser filtrado (se a regra for atendida, **não** sincronize).

### Regras prontas para uso do usuário

Essas regras também serão aplicadas ao tipo de objeto iNetOrgPerson.

Um objeto de usuário deve atender ao seguinte para ser sincronizado:

- Deve ter um sourceAnchor.
- Depois de o objeto ser criado no AD do Azure, o sourceAnchor não poderá ser alterado. Se o valor for alterado no local, o objeto interromperá a sincronização até que o sourceAnchor seja alterado para seu valor anterior.
- Deve ter o atributo accountEnabled (userAccountControl) preenchido. Com um local no Active Directory, este atributo sempre estará presente e preenchido.

Os seguintes objetos de usuário **não** estão sincronizados ao AD do Azure:

- `IsPresent([isCriticalSystemObject])`. Certifique-se de vários objetos prontos para uso no Active Directory, como a conta de administrador interna, não sejam sincronizados.
- `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos de usuário sem o atributo sAMAccountName não estejam sincronizados. Isso ocorrerá na prática apenas em um domínio atualizado do NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço usada pelo Azure AD Connect sync e suas versões anteriores.
- Não sincronize contas do Exchange que não funcionem no Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Não sincronize objetos que não funcionem no Exchange Online. `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))` Essa máscara de bit (& H21C07000) filtraria os objetos a seguir:
    - Pasta pública habilitada para email
    - Caixa de correio do Atendedor do sistema
    - Caixa de correio do banco de dados de correio (caixa de correio do sistema)
    - Grupo de segurança universal (não se aplica a um usuário, mas está presente por motivos herdados)
    - Grupo não universal (não se aplica a um usuário, mas está presente por motivos herdados)
    - Plano de caixa de correio
    - Caixa de correio de descoberta
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

As regras de atributos a seguir se aplicam:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. A caixa de correio vinculada não contribuirá com o atributo sourceAnchor. Supõe-se que, se uma caixa de correio vinculada tiver sido encontrada, a conta real será adicionada posteriormente.
- Atributos relacionados ao Exchange só são sincronizados se o atributo **mailNickName** tiver um valor.
- Quando houver várias florestas, os atributos serão consumidos na seguinte ordem:
    - A floresta com uma conta habilitada contribuirá com atributos relacionados ao logon (por exemplo, userPrincipalName).
    - A floresta com uma caixa de correio do Exchange contribuirá com um atributo que pode ser encontrado em uma GAL (Lista de Endereços Global).
    - Se não for possível localizar nenhuma caixa de correio, esses atributos poderão vir de qualquer floresta.
    - Atributos relacionados ao Exchange são a contribuição da floresta em que `mailNickname ISNOTNULL`.
    - Se houver várias florestas que atendam a uma dessas regras, a ordem de criação (data/hora) dos conectores (florestas) será usada para determinar qual floresta contribuirá com os atributos.

### Regras prontas para uso de contato

Um objeto de contato deve atender ao seguinte para ser sincronizado:

- O contato deve ser habilitado para email. É verificado com as seguintes regras:
    - `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser preenchido.
    - Um endereço de email principal pode ser encontrado no atributo proxyAddresses ou no atributo de email. A precedência de um @ é usada para verificar se o conteúdo é um endereço de email. Um destes dois deve ser avaliado como True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e, se houver, um @ pode ser encontrado na cadeia de caracteres?.
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. O atributo de email está preenchido e, se estiver, um @ pode ser encontrado na cadeia de caracteres?

Os seguintes objetos de contato **não** estão sincronizados ao AD do Azure:

- `IsPresent([isCriticalSystemObject])`. Certifique-se de que nenhum objeto de contato marcado como crítico seja sincronizado. Não deve ser nenhum com uma configuração padrão.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Isso não funciona no Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

### Grupo prontas para uso de grupo

Um objeto de grupo deve atender ao seguinte para ser sincronizado:

- Deve ter menos de 50 mil membros. Isso é contado como o número de membros no grupo local.
    - Se tiver mais membros antes de a sincronização iniciar pela primeira vez, o grupo não será sincronizado.
    - Se o número de membros crescer desde quando ele foi inicialmente criado, quando ele atingir 50 mil membros, a sincronização será interrompida até que a contagem de associação seja inferior a 50 mil novamente.
    - Observação: a contagem de 50 mil associações também é imposta pelo AD do Azure. Você não poderá sincronizar grupos com mais membros mesmo que modifique ou remova essa regra.
- Se o grupo for um **Grupo de Distribuição**, ele também deverá ser habilitado para email. Consulte [Regras prontas para uso de contato](#contact-out-of-box-rules) para ver como essa regra é aplicada.

Os seguintes objetos de grupo **não** estão sincronizados ao AD do Azure:

- `IsPresent([isCriticalSystemObject])`. Certifique-se de vários objetos prontos para uso no Active Directory, como o grupo de administrador interno, não sejam sincronizados.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo herdado usado pelo DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de funções.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

### Regras prontas para uso de ForeignSecurityPrincipal

FSPs são associados a "qualquer" objeto (*) no metaverso. Isso na verdade somente ocorrerá para usuários e grupos de segurança. Isso garante que as associações entre florestas sejam resolvidas e representadas corretamente no AD do Azure.

### Regras prontas para uso de computador

Um objeto de computador deve atender ao seguinte para ser sincronizado:

- `userCertificate ISNOTNULL`. Somente objetos de computador do Windows 10 preencherão esse atributo. Todos os objetos de computador com um valor nesse atributo serão sincronizados.

## Noções básicas sobre o cenário de regras prontas para uso

Neste exemplo, estamos usando uma implantação com uma floresta de contas (A), uma floresta de recursos (R) e um diretório do AD do Azure.

![cenário](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Nessa configuração, pressupomos que há uma conta habilitada na floresta de contas e uma conta desabilitada na floresta de recursos com uma caixa de correio vinculada.

Nosso objetivo com a configuração padrão é:

- Informações de atributo relacionadas ao logon serão sincronizadas da floresta com a conta habilitada.
- Atributos que podem ser encontrados na GAL (Lista de Endereços Global) serão sincronizados da floresta com a caixa de correio. Se nenhuma caixa de correio puder ser encontrada, qualquer outra floresta será usada.
- Se uma caixa de correio vinculada for encontrada, a conta habilitada vinculada deverá encontrada para que o objeto seja exportado para o AD do Azure.

### Editor de Regra de Sincronização

O SRE (Editor de Regras de Sincronização) pode exibir e alterar a configuração, e um atalho para ele pode ser encontrado no menu Iniciar.

![Editor de Regras de Sincronização](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

O SRE é uma ferramenta do kit de recursos e é instalado com a sincronização do Azure AD Connect. Para iniciá-lo, você deve ser um membro do grupo ADSyncAdmins. Quando ele é iniciado, você vê algo assim:

![Regras de Sincronização Entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Nesse painel, você verá todas as regras de sincronização criadas para sua configuração. Cada linha na tabela é uma regra de sincronização. À esquerda, em Tipos de Regra, são listados os dois tipos diferentes: entrada e saída. Entrada e saída é da exibição do metaverso. Nessa visão geral, nos concentraremos principalmente nas regras de entrada. A lista atual de Regras de Sincronização dependem do esquema detectado no AD. Na figura acima, a conta de florestas (fabrikamonline.com) não tem serviços, como o Exchange e o Lync, e nenhuma regra de sincronização foi criada para esses serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) descobriremos Regras de Sincronização para esses serviços. O conteúdo das regras será diferente dependendo da versão detectada. Por exemplo, em uma implantação do Exchange 2013, teremos mais fluxos de atributo configurados que no Exchange 2010 e no Exchange 2007.

### Regra de Sincronização

Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que fluem quando uma condição é atendida. Ela também é usado para descrever como um objeto em um espaço de conector está relacionado a um objeto no metaverso, conhecido como **associação** ou **correspondência**. As Regras de Sincronização têm um valor de precedência indicando como elas se relacionam entre si. Uma Regra de Sincronização com um valor numérico menor em precedência tem maior precedência e, em caso de um conflito de fluxo de atributo, a maior precedência vencerá a resolução de conflitos.

Por exemplo, examinaremos a Regra de Sincronização **Entrada do AD – usuário AccountEnabled**. Vamos marcar essa linha no SRE e selecionar **Editar**.

Como se trata de uma regra integrada, receberemos um aviso quando abrimos a regra. Você não deve fazer qualquer [alteração em regras integradas](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), então você está sendo questionado sobre suas intenções. Nesse caso, você deseja somente exibir a regra; portanto, selecione **Não**.

![Regras de Sincronização Entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Uma Regra de Sincronização tem quatro seções de configuração: descrição, filtro de escopo, regras de associação e transformações.

#### Descrição

A primeira seção fornece informações básicas, como nome e descrição.

![Editar regra de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Também encontramos informações sobre a qual sistema conectado essa regra está relacionada, a qual objeto de tipo no sistema conectado ela se aplica e o tipo de objeto do metaverso. O tipo de objeto do metaverso é sempre pessoa, independentemente de o tipo de objeto de origem ser usuário, iNetOrgPerson ou contato. O tipo de objeto do metaverso nunca deve ser alterado. Portanto, ele é criado como um tipo genérico. O Tipo de Link pode ser definido como Associar, StickyJoin ou Provisionar. Essa configuração funciona em conjunto com a seção de Regras de Associação. Abordaremos isso mais tarde.

Você também pode ver que essa regra de sincronização é usada para a sincronização de senha. Se um usuário estiver no escopo dessa regra de sincronização, a senha será sincronizada do local para a nuvem (supondo que você tenha habilitado o recurso de sincronização de senha).

#### Filtro de escopo

A seção Filtro de Escopo é usada para configurar quando uma Regra de Sincronização deve ser aplicada. Como o nome da Regra de Sincronização que estamos vendo indica que ela só deve ser aplicada para usuários habilitados, o escopo está configurado para que o atributo do AD **userAccountControl** não precise ter o bit 2 definido. Quando encontrarmos um usuário no AD, aplicaremos essa regra de sincronização se **userAccountControl** for definido como o valor decimal 512 (usuário normal habilitado), mas ela não será aplicada se o usuário que encontrarmos tiver **userAccountControl** definido como 514 (usuário normal desabilitado).

![Editar regra de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

O filtro de escopo tem Grupos e Cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem ser satisfeitas para que uma Regra de Sincronização seja aplicada. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para que a regra seja aplicada. Ou seja, um OR lógico é avaliado entre grupos e um AND lógico é avaliado dentro de um grupo. Um exemplo disso pode ser encontrado na saída de Regra de Sincronização **Saída para AAD – Associação no Grupo**, mostrada abaixo. Há vários grupos de filtro de sincronização, por exemplo, um para grupos de segurança (securityEnabled EQUAL True) e outro para grupos de distribuição (securityEnabled EQUAL False).

![Editar regra de sincronização de saída](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Essa regra é usada para definir quais grupos devem ser provisionados ao AAD. Grupos de Distribuição devem ser habilitados para email para serem sincronizados com o AAD, mas para os grupos de segurança isso não é obrigatório. Como você pode ver, muitos atributos adicionais também são avaliados.

#### Regras de associação

A terceira seção é usada para configurar como os objetos no espaço do conector se relacionam aos objetos no metaverso. A regra que vimos anteriormente não tem qualquer configuração para Regras de Associação. Então, vamos examinar **Entrada do AD – Associar Usuário**.

![Editar regra de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

O conteúdo das regras de associação dependerá da opção correspondente selecionada no assistente de instalação. Para uma regra de entrada, a avaliação se inicia com um objeto no espaço do conector de origem, e cada grupo de regras de associação é avaliado em sequência. Se um objeto de origem for avaliado para corresponder exatamente a um objeto no metaverso usando uma das regras de associação, os objetos serão associados. Se todas as regras forem avaliadas e não houver correspondência, o Tipo de Link na página de descrição será usado. Se essa configuração for definida como Provisionar, um novo objeto será criado no destino, o metaverso. Provisionar um novo objeto para o metaverso também é conhecido como projetar um objeto para o metaverso.

As regras de associação são avaliadas apenas uma vez. Quando um objeto de espaço do conector e um objeto do metaverso são associados, eles permanecem associados enquanto o escopo da Regra de Sincronização ainda for satisfeito.

Ao serem avaliadas Regras de Sincronização, apenas uma Regra de Sincronização com as regras de associação definidas deve estar no escopo. Se forem encontradas várias Regras de Sincronização com regras de associação para um objeto, um erro será gerado. Por esse motivo, a prática recomendada é ter apenas uma Regra de Sincronização com associação definida quando várias Regras de Sincronização estão no escopo de um objeto. Na configuração pronta para uso para a sincronização do Active Directory do Azure, essas regras podem ser encontradas examinando-se o nome para localizar aquelas com a palavra Join no fim do nome. Uma Regra de Sincronização sem regras de associação definidas aplicará os fluxos de atributo se outra Regra de Sincronização tiver associado os objetos ou provisionado um novo objeto no destino.

Se você examinar a figura acima, poderá ver que a regra está tentando associar **objectSID** a **msExchMasterAccountSid** (Exchange) e a **msRTCSIP OriginatorSid** (Lync), que é o que esperamos em uma topologia de floresta de recurso de conta. Podemos encontrar a mesma regra em todas as florestas, ou seja, pressupõe-se que cada floresta pode ser uma floresta de contas ou de recursos. Isso também funcionará se você tiver contas que residem em uma única floresta e não precisam ser associadas.

#### Transformações

A seção de transformação define todos os fluxos de atributo que serão aplicados ao objeto de destino quando os objetos forem associados e o filtro de escopo for satisfeito. Voltando à nossa Regra de Sincronização **Entrada do AD – usuário AccountEnabled**, encontraremos as seguintes transformações:

![Editar regra de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Para colocar isso em contexto, em uma implantação de floresta de Recurso de Conta, esperamos encontrar uma conta habilitada da floresta de contas e uma conta desabilitada na floresta de recursos com as configurações do Exchange e do Lync. A Regra de Sincronização que estamos examinando contém os atributos necessários para logon, e queremos que eles fluam da floresta em que encontramos uma conta habilitada. Todos esses fluxos de atributo são colocados juntos em uma Regra de Sincronização.

Uma transformação pode ter diferentes tipos: constante, direta e expressão.

- Um fluxo constante fluirá sempre com um determinado valor. Nesse caso, sempre definiremos o valor Verdadeiro no atributo do metaverso chamado accountEnabled.
- Um fluxo Direto fluirá com o valor do atributo no atributo da origem para o destino.
- O terceiro tipo de fluxo é Expressão e permite configurações mais avançadas.

A linguagem de expressão é VBA (Visual Basic for Applications). Portanto, o usuário com a experiência no Microsoft Office ou VBScript reconhecerá o formato. Os atributos são colocados entre colchetes, [attributeName]. Os nomes de função e de atributo diferenciam maiúsculas de minúsculas, mas o Editor de Regras de Sincronização avaliará as expressões e fornecerá um aviso se a expressão não for válida. Todas as expressões são expressas em uma única linha com funções aninhadas. Para mostrar a capacidade da linguagem de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

O tópico de transformação é vasto e fornece uma grande parte da configuração personalizada possível com a sincronização do Azure AD Connect. Mais sobre isso pode ser encontrado em outros artigos sobre a sincronização do Azure AD Connect.

### Precedência

Temos analisado algumas Regras de Sincronização individuais, mas as regras trabalham juntas na configuração. Em alguns casos, um valor de atributo vêm de várias regras de sincronização para o mesmo atributo de destino. Nesse caso, precedência do atributo é usada para determinar qual atributo vencerá. Como exemplo, vamos examinar o atributo sourceAnchor. Ele é um atributo importante para se conseguir fazer logon no AD do Azure. Podemos encontrar um fluxo de atributo para esse atributo em duas diferentes regras de sincronização, **Entrada do AD – usuário AccountEnabled** e **Entrada do AD – usuário comum**. Devido à precedência de Regra de Sincronização, o atributo sourceAnchor terá a contribuição da floresta com uma conta habilitada primeiro se houver vários objetos associados ao objeto metaverso. Se não houver nenhuma conta habilitada, utilizaremos a Regra de Sincronização detectora **Entrada do AD – usuário comum**. Isso garantirá que forneceremos uma sourceAnchor até mesmo para contas que estão desabilitadas.

![Regras de Sincronização Entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A precedência de Regras de Sincronização é definida em grupos pelo assistente de instalação. Os grupos de regras têm todos o mesmo nome, mas estão conectados a diferentes diretórios conectados. O assistente de instalação dará à regra **Entrada do AD – associação de usuário** maior precedência e irá iterar por todos os diretórios do AD conectados. Em seguida, ele continuará com os próximos grupos de regras em uma ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem em que os conectores foram adicionados ao assistente. Se outro conector for adicionado usando o assistente, as Regras de Sincronização serão reordenadas e as novas regras do conector serão inseridas por último em cada grupo.

### Juntando as peças

Agora sabemos o suficiente sobre Regras de Sincronização para poder entender como a configuração funciona com diferentes Regras de Sincronização. Se observarmos um usuário e os atributos que contribuíram para o metaverso, as regras são aplicadas na seguinte ordem:

| Nome | Comentário |
| :------------- | :------------- |
| Entrada do AD – associação do usuário | Regra para associar objetos de espaço conector com metaverso. |
| Entrada do AD – UserAccount habilitada | Atributos necessários para entrar no AD do Azure e no Office 365. Queremos esses atributos da conta habilitada. |
| Entrada do AD – usuário comum do Exchange | Atributos encontrados na Lista de Endereços Global. Vamos supor que a qualidade dos dados é melhor na floresta em que achamos a caixa de correio do usuário. |
| Entrada do AD – usuário comum | Atributos encontrados na Lista de Endereços Global. No caso de não encontramos uma caixa de correio, outros objetos associados podem contribuir com o valor do atributo. |
| Entrada do AD – usuário do Exchange | Só existirá se o Exchange tiver sido detectado. Fluirá todos os atributos do Exchange de infraestrutura. |
| Entrada do AD – usuário Lync | Só existirá se o Lync tiver sido detectado. Fluirá todos os atributos do Lync de infraestrutura. |

## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_1223_2015-->