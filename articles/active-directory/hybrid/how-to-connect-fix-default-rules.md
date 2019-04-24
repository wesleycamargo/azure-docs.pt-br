---
title: Como corrigir regras padrão modificadas - Azure AD Connect | Microsoft Docs
description: Saiba como corrigir as regras padrão modificadas que vêm com o Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60352748"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras padrão modificadas no Azure AD Connect

O Azure AD Connect é fornecido com as regras padrão para sincronização.  Infelizmente, essas regras não aplicam universalmente a todas as organizações e pode haver ocasiões, com base em seus requisitos, quando você precisar modificá-los.

 Se você tiver modificado as regras padrão ou estiver planejando modificá-los, reserve um tempo para ler este documento.

Este documento apresenta 2 exemplos das personalizações mais comuns realizadas pelos usuários e explica a maneira correta de alcançar essas personalizações.

>[!NOTE] 
> Modificar as regras do padrão existente para atingir uma personalização necessária não é suportada - ao fazer isso que impedirá de atualizar essas regras para a versão mais recente em versões futuras. Isso impedirá que você receber novos recursos e correções de bugs necessárias.  Este documento explicará como obter o mesmo resultado sem modificar as regras padrão existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar as regras padrão modificado?
Começando com a versão 1.3.7.0 dos **do Azure AD Connect**, agora é fácil identificar a regra padrão modificadas. Você pode ir para aplicativos na área de trabalho e clique em **Editor de regras de sincronização**.

![Editor](media/how-to-connect-fix-default-rules/default1.png)

No Editor de regras padrão modificado serão mostradas com um ícone na frente do nome conforme mostrado abaixo:

![ícone](media/how-to-connect-fix-default-rules/default2.png)

 Além disso, você verá uma regra desabilitada com o mesmo nome que é a regra padrão ao lado dele:

![regras padrão](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
A seguir estão as personalizações comuns para as regras padrão:

- [Alterando o fluxo de atributos](#changing-attribute-flow)
- [Alterar o filtro de escopo](#changing-scoping-filter)
- [Alterar a condição de junção](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Antes de alterar todas as regras
- Desabilite o Agendador de sincronização.  O Agendador é executado a cada 30 minutos por padrão. Verifique se ele não está iniciando enquanto você está fazendo alterações e solucionando os problemas das novas regras. Para desabilitar temporariamente o agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![regras padrão](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de escopo pode resultar na exclusão de objetos no diretório de destino. Por favor, tenha cuidado antes de fazer alterações em como o escopo de objetos. É recomendável fazer alterações para um servidor de preparo antes de fazer alterações no servidor do Active Directory.
- Execute uma visualização em um único objeto, conforme mencionado na [Validar regra de sincronização](#validate-sync-rule) seção, depois de adicionar qualquer nova regra.
- Execute uma sincronização completa depois de adicionar uma nova regra ou modificar qualquer regra de sincronização personalizadas. Esta sincronização aplicará as novas regras para todos os objetos.

## <a name="changing-attribute-flow"></a>Alterando o fluxo de atributos
Há 3 cenários diferentes para o fluxo de atributo, vamos dar uma olhada em como fazer isso sem alterar as regras padrão.
- Adicionando um novo atributo
- Substituindo o valor de atributo existente
- Não sincronizar atributo existente

### <a name="adding-new-attribute"></a>Adicionando um novo atributo:
Se você achar que um atributo não está fluindo do seu diretório de origem para o diretório de destino e em seguida, você pode usar o [Azure AD Connect sync: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para os novos atributos de fluxo.

Observe que a sua primeira opção deve ser usar [Azure AD Connect sync: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md), um fora do recurso de caixa fornecido pelo Azure AD Connect. No entanto, se ele não funcionar para você em seguida, percorra as seguintes etapas para fluir um atributo sem modificar a regra de sincronização padrão existente, você pode fazer isso adicionando duas novas regras de sincronização.


#### <a name="add-an-inbound-sync-rule"></a>Adicione uma regra de sincronização de entrada:
Regra de sincronização de entrada significa que a fonte para o atributo é um espaço do conector e o destino é o metaverso. Por exemplo, para fazer fluir um novo atributo de diretório Active Directory local ao Azure Active Directory, criar uma nova regra de sincronização de entrada iniciando o **Editor de regra de sincronização**, em seguida, selecione a direção como **Inbound** e clique em **adicionar nova regra**. 

 ![regras padrão](media/how-to-connect-fix-default-rules/default3a.png)

Execute sua própria convenção de nomenclatura para nomear a regra, aqui a usamos **personalizados em do AD – usuário**, isso significa que a regra é uma regra personalizada e uma regra de entrada do espaço conector do AD para o metaverso.   

 ![regras padrão](media/how-to-connect-fix-default-rules/default3b.png)

Fornecer sua própria descrição da regra para que a manutenção futura da regra é fácil, como o que é o objetivo desta regra e por que ela foi necessária.
Selecione um sistema conectado (floresta), a origem do atributo. Em seguida, selecione o tipo de objeto do sistema conectado e o tipo de objeto do metaverso.

Especifique o valor de precedência entre 0 e 99 (diminuir o número, maior a precedência). Mantenha os outros campos como 'Tag', 'Habilitar a sincronização de senha' e 'Desabilitados' como padrão.

Manter 'filtro de escopo' vazio, isso significa que a regra será aplicada a todos os objetos Unidos entre o sistema conectado do AD e o metaverso.

Mantenha a esvaziar regras de associação, que significa que essa regra será pegar carona na condição de junção definida na regra padrão. Essa é outra razão para não desabilitar/excluir a regra padrão porque se não houver nenhuma condição de junção acumule, em seguida, o atributo não fluirão. 

Adicione a transformação apropriada para seu atributo, você pode atribuir constante para um valor constante para o atributo de destino de fluxo ou direcionar o mapeamento entre o atributo de origem ou destino, ou uma expressão para o atributo. Aqui estão vários [funções de expressão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) você pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicione uma regra de sincronização de saída:
Até agora, adicionando apenas uma regra de sincronização de entrada fizemos metade do trabalho, porque o atributo ainda não está vinculado para o diretório de destino. Para vincular o atributo para o Diretor de destino, você precisará criar uma regra de saída, o que significa que o código-fonte é o metaverso e o destino é o sistema conectado. Para criar uma regra de saída, inicie **Editor de regra de sincronização**, altere o **direção** para **Outbound** e clique em **adicionar nova regra**. 

![regras padrão](media/how-to-connect-fix-default-rules/default3c.png)

Como a regra de entrada, você pode usar sua própria convenção de nomenclatura para **nome** a regra. Selecione o **sistema conectado** como locatário do AD do Azure, selecione o objeto de sistema conectado ao qual você deseja definir o valor do atributo. Definir a precedência entre 0 - 99. 

![regras padrão](media/how-to-connect-fix-default-rules/default3d.png)

Manter **filtro de escopo** vazio, keep **regras de junção** vazias, preencha a transformação como constante, direta ou expressão. 

Neste exemplo demonstramos como fluir um novo atributo para um objeto de usuário do Active Directory ao Azure Active Directory. Você pode usar estas etapas para mapear qualquer atributo de qualquer objeto de origem e destino.  Para obter mais informações, consulte [criando regras de sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [preparar para provisionar usuários](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Substituindo o valor de atributo existente
É possível que você deseja substituir o valor do atributo já mapeado, por exemplo você sempre queira definir o valor Null para um atributo no Azure AD, você pode fazer isso, simplesmente criando apenas uma regra de entrada conforme mencionado na etapa anterior e fluxo  **AuthoritativeNull** valor constante para o atributo de destino. Observe que usamos AuthoritativeNulll em vez de Null nesse caso. Isso ocorre porque o valor não nulo substituirá o valor nulo, mesmo se ele tem uma precedência mais baixa (maior valor numérico na regra). No entanto, o AuthoritativeNull será tratado como Null e não será substituído pelo valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincronizar atributo existente
Se você desejar excluir um atributo da sincronização, você pode usar o atributo de filtragem de recurso fornecido no Azure AD Connect. Inicie **do Azure AD Connect** no ícone da área de trabalho e selecione **personalizar opções de sincronização**.

![regras padrão](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se **aplicativo do Azure AD e a filtragem de atributo** está selecionado e clique em **próxima**.

![regras padrão](media/how-to-connect-fix-default-rules/default5.png)

Desmarque os atributos que você deseja excluir da sincronização.

![regras padrão](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Alterar o filtro de escopo
Sincronização do Azure AD cuida da maioria dos objetos, você pode reduzir o escopo dos objetos e reduzi-lo menos objetos a serem exportados de uma maneira com suporte sem alterar as regras de sincronização padrão. No caso de você desejar aumentar o escopo dos objetos, em seguida, você pode **editar** a regra existente, cloná-lo e desabilitar a regra original. A Microsoft recomenda que você não aumentar o escopo configurado pelo Azure AD Connect. Aumento no escopo de objetos será mais difícil para a equipe de suporte para entender as personalizações e suporte do produto.

Aqui está como você pode reduzir o escopo dos objetos sincronizados ao AD do Azure. Observe que, se você reduzir o escopo do **usuários** sendo sincronizado a sincronização de hash de senha também será interrompida para os usuários de saída filtrada. Se os objetos são já está sincronizando, em seguida, após a redução de escopo, os objetos filtrados-out será excluído do diretório de destino, trabalhe no escopo com muito cuidado.
Aqui estão as maneiras com suporte para reduzir o escopo dos objetos que você estiver sincronizando.

- [atributo cloudFiltered](#cloudfiltered-attribute)
- [Filtragem de UO](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>atributo cloudFiltered
Observe que isso não é um atributo que pode ser definido no Active Directory. Você precisa definir o valor desse atributo, adicionando uma nova regra de entrada, conforme mencionado na **substituindo o valor de atributo existente** seção. Em seguida, você pode usar o **Transformation** e use **expressão** definir esse atributo no metaverso. Aqui está um exemplo que você não deseja sincronizar tudo do usuário cujo nome de departamento começa com maiusculas e minúsculas **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Primeiro convertemos o departamento de origem (Active Directory) em letras minúsculas. Em seguida, usando a função Left, podemos levou apenas 3 primeiros caracteres e comparado-lo com HDR. Se ele tiver uma correspondência, em seguida, defina o valor como NULL caso contrário, True. Observe que definimos o valor NULL, para que outra regra com menor precedência (valor de número mais alto) pode gravar nele com condição diferente. Execute a visualização em um objeto para validar a regra de sincronização conforme mencionado na [Validar regra de sincronização](#validate-sync-rule) seção.

![regras padrão](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtragem de UO
Você pode criar uma ou mais OUs e mover os objetos que você não deseja sincronizar a essas UOs. Em seguida, configure a filtragem no Azure AD Connect iniciando da UO **do Azure AD Connect** no ícone da área de trabalho e selecione as opções conforme mostrado abaixo. Você também pode configurar a filtragem no momento da instalação do Azure AD Connect da UO. 

![regras padrão](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e, em seguida, desmarque as UOs que você não deseja sincronizar.

![regras padrão](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Alterar a condição de junção
A Microsoft recomenda que você use o padrão ingressar condições configurados pelo Azure AD Connect. Alterar as condições de junção padrão tornará difícil para a equipe de suporte para entender as personalizações e suporte do produto.

## <a name="validate-sync-rule"></a>Validar a regra de sincronização
Você pode validar a regra de sincronização adicionado recentemente usando o recurso de visualização sem executar o ciclo de sincronização completa. Inicie **serviço de sincronização** interface do usuário.

![regras padrão](media/how-to-connect-fix-default-rules/default10.png)

Clique no **pesquisa de metaverso**, selecione o objeto de escopo como **pessoa**, **Adicionar cláusula** e mencione a seus critérios de pesquisa. Clique em **pesquisa** botão e clique duas vezes no objeto do **os resultados da pesquisa** Observe que você execute a importação e sincronização na floresta antes de executar essa etapa, isso é para garantir que os dados no Azure AD Connect está atualizado para esse objeto.

![regras padrão](media/how-to-connect-fix-default-rules/default11.png)



Selecione **conectores**, selecione o objeto em connector(forest) correspondente, clique em **propriedades...** .

![regras padrão](media/how-to-connect-fix-default-rules/default12.png)

Clique no **visualização...**

![regras padrão](media/how-to-connect-fix-default-rules/default13a.png)

Clique em **gerar visualização** e **fluxo de atributos de importação** no painel esquerdo.

![regras padrão](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui você vai notar que a regra recém-adicionado é executada no objeto e tiver definido o atributo cloudFiltered como True.

![regras padrão](media/how-to-connect-fix-default-rules/default15a.png)
 
Como comparar regra modificada com a regra padrão?
Você pode exportar as regras separadamente como arquivos de texto. Essas regras serão exportadas como arquivo de script do powershell. Você pode compará-los usando qualquer ferramenta de comparação de arquivo para ver quais tipos de alterações são feitas. Aqui neste exemplo, eu usei windiff para comparar dois arquivos.
 
Você pode observar que, no usuário modificado regra, atributo msExchMailboxGuid é alterado para **expressão** em vez de **direto** com o valor como **nulo** e  **ExecuteOnce** opção. Você pode ignorar as diferenças de precedência e identificados. 

![regras padrão](media/how-to-connect-fix-default-rules/default17.png)
 
Como corrigir uma regra padrão modificado?
Para corrigir as regras para as configurações padrão, você pode excluir a regra modificada e habilitar a regra padrão, conforme mostrado abaixo e, em seguida, executar uma **sincronização completa**. Antes de fazer isso, execute ações corretivas, como mencionado acima, para que você não perca a personalização que você está tentando alcançar # # próximas etapas

## <a name="next-steps"></a>Próximas etapas
- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)

