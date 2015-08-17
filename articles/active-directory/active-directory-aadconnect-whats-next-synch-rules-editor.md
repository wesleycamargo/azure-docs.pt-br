
<properties 
	pageTitle="Usando o editor de regras de sincronização do Azure AD Connect" 
	description="Saiba como usar o editor de regras de sincronização do Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Editor de regras de sincronização do Azure AD Connect


## Usando o editor de regras de sincronização

No Azure AD Connect, você pode configurar e ajustar o fluxo de atributos e objetos entre o AD do Azure e os diretórios locais configurando regras de sincronização.

Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que fluem quando uma condição é atendida. Ela também é usada para descrever como um objeto em um espaço de conector está relacionado a um objeto no metaverso, conhecido como associação ou correspondência. As Regras de Sincronização têm uma precedência indicando como elas se relacionam entre si. Uma Regra de Sincronização com um valor numérico de precedência menor tem precedência mais alta e, no caso de um conflito de fluxo de atributo, a maior precedência vencerá a resolução de conflito. Regras de sincronização podem ser configuradas usando o Editor de Regras de Sincronização.

Por exemplo, examinaremos a Regra de Sincronização "Do AD – AccountEnabled de usuário". Marcaremos essa linha de SRE e selecionaremos Editar. Uma Regra de Sincronização tem quatro seções de configuração: Descrição, Filtro de escopo, Regras de associação e Transformações.

### Descrição
A primeira seção fornece informações básicas, como nome e descrição.

<center>![Regra de Associação](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

Também encontramos informações sobre a qual sistema conectado essa regra está relacionada, a qual objeto de tipo no sistema conectado ela se aplica e o tipo de objeto do metaverso. O tipo de objeto do metaverso é sempre pessoa, independentemente de o tipo de objeto de origem ser usuário, iNetOrgPerson ou contato. O tipo de objeto do metaverso nunca deve ser alterado. Portanto, ele é criado como um tipo genérico. O Tipo de Link pode ser definido como Associar, StickyJoin ou Provisionar. Essa configuração funciona em conjunto com a seção de Regras de Associação. Abordaremos isso mais tarde.

### Filtro de Escopo

A seção Filtro de Escopo é usada para configurar quando uma Regra de Sincronização deve ser aplicada. Como o nome da Regra de Sincronização que estamos vendo indica que ela só deve ser aplicada para usuários habilitados, o escopo está configurado para que o atributo do AD userAccountControl não deva ter o bit 2 definido. Quando encontrarmos um usuário no AD, aplicaremos essa regra se userAccountControl for definido como o valor decimal 512 (usuário normal habilitado), mas ela não será aplicada se o usuário que encontrarmos tiver userAccountControl definido como 514 (usuário normal desabilitado).

<center>![Regra de Associação](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

O filtro de escopo tem Grupos e Cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem ser satisfeitas para que uma Regra de Sincronização seja aplicada. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para que a regra seja aplicada. Ou seja, um OR lógico é avaliado entre grupos e um AND lógico é avaliado dentro de um grupo. Um exemplo disso pode ser encontrado na saída de Regra de Sincronização de saída para AAD – Associação no Grupo, mostrada abaixo. Há dois grupos de filtro de sincronização, um para grupos de segurança (securityEnabled EQUAL True) e outro para grupos de distribuição (securityEnabled EQUAL False).

<center>![Regra de Associação](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

Essa regra é usada para definir quais grupos devem ser provisionados ao AAD. Grupos de Distribuição devem ser habilitados para email para serem sincronizados com o AAD, mas para os grupos de segurança isso não é obrigatório. Como você pode ver, muitos atributos adicionais também são avaliados.

###Regras de associação
A terceira seção é usada para configurar como os objetos no espaço do conector se relacionam aos objetos no metaverso. A regra que vimos anteriormente não tem qualquer configuração para Regras de Associação. Então, vamos examinar Entrada do AD – Associar Usuário.

<center>![Regra de Associação](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

O conteúdo das regras de associação dependerá da opção correspondente selecionada no assistente de instalação. Para uma regra de entrada, a avaliação se inicia com um objeto no espaço do conector de origem, e cada grupo de regras de associação é avaliado em sequência. Se um objeto de origem for avaliado para corresponder exatamente a um objeto no metaverso usando uma das regras de associação, os objetos serão associados. Se todas as regras forem avaliadas e não houver correspondência, o Tipo de Link na página de descrição será usado. Se essa configuração for definida como Provisionar, um novo objeto será criado no destino, o metaverso. Provisionar um novo objeto para o metaverso também é conhecido como projetar um objeto para o metaverso. As regras de associação são avaliadas apenas uma vez. Quando um objeto de espaço do conector e um objeto do metaverso são associados, eles permanecem associados enquanto o escopo da Regra de Sincronização ainda for satisfeito. Ao serem avaliadas Regras de Sincronização, apenas uma Regra de Sincronização com as regras de associação definidas deve estar no escopo. Se forem encontradas várias Regras de Sincronização com regras de associação para um objeto, um erro será gerado. Por esse motivo, a prática recomendada é ter apenas uma Regra de Sincronização com associação definida quando várias Regras de Sincronização estão no escopo de um objeto. Na configuração pronta para uso para o Azure AD Connect, essas regras podem ser encontradas examinando-se o nome para localizar aquelas com a palavra Join no fim do nome. Uma Regra de Sincronização sem regras de associação definidas aplicará os fluxos de atributo se outra Regra de Sincronização tiver associado os objetos ou provisionado um novo objeto no destino.

###Transformações
A seção de transformação define todos os fluxos de atributo que serão aplicados ao objeto de destino quando os objetos forem associados e o filtro de escopo for satisfeito. Voltando à nossa Regra de Sincronização Entrada do AD – Usuário AccountEnabled, encontraremos as seguintes transformações:

<center>![Regra de Associação](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

Para colocar isso em contexto, em uma implantação de floresta de Recurso de Conta, esperamos encontrar uma conta habilitada da floresta de contas e uma conta desabilitada na floresta de recursos com as configurações do Exchange e do Lync. A Regra de Sincronização que estamos examinando contém os atributos necessários para logon, e queremos que eles fluam da floresta em que encontramos uma conta habilitada. Todos esses fluxos de atributo são colocados juntos em uma Regra de Sincronização. Uma transformação pode ter diferentes tipos: Constante, Direta e Expressão. Um fluxo constante fluirá sempre com um determinado valor. Nesse caso, sempre definiremos o valor Verdadeiro no atributo do metaverso chamado accountEnabled. Um fluxo Direto fluirá com o valor do atributo no atributo da origem para o destino. O terceiro tipo de fluxo é Expressão e permite configurações mais avançadas. A linguagem de expressão é VBA (Visual Basic for Applications). Portanto, o usuário com a experiência no Microsoft Office ou VBScript reconhecerá o formato. Os atributos são colocados entre colchetes, [attributeName]. Os nomes de função e de atributo diferenciam maiúsculas de minúsculas, mas o Editor de Regras de Sincronização avaliará as expressões e fornecerá um aviso se a expressão não for válida. Todas as expressões são expressas em uma única linha com funções aninhadas. Para mostrar a capacidade da linguagem de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

O tópico de transformação é vasto e fornece uma grande parte da configuração personalizada possível com o Azure AD Connect. A configuração personalizada não será abordada neste documento de visão geral, mas vamos examinar alguns fluxos de atributos adicionais neste documento.
 

<!---HONumber=August15_HO6-->