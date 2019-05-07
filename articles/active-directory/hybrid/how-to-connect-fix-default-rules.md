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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067626"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras padrão modificadas no Azure AD Connect

Azure Active Directory (Azure AD) Connect usa as regras padrão para sincronização.  Infelizmente, essas regras não se aplicam universalmente para todas as organizações. De acordo com suas necessidades, talvez seja necessário modificá-los. Este artigo discute dois exemplos das personalizações mais comuns e explica a maneira correta de alcançar essas personalizações.

>[!NOTE] 
> Modificar as regras padrão existentes para alcançar uma personalização necessária não tem suporte. Se você fizer isso, impede que atualizar essas regras para a versão mais recente em futuras versões. Você não obterá as correções de bug que você precisa, ou novos recursos. Este documento explica como obter o mesmo resultado sem modificar as regras padrão existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar as regras padrão modificado
Começando pela versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra padrão modificadas. Vá para **aplicativos na área de trabalho**e selecione **Editor de regras de sincronização**.

![Azure AD Connect, com o Editor de regras de sincronização realçado](media/how-to-connect-fix-default-rules/default1.png)

No Editor de regras padrão modificadas são mostradas com um ícone de aviso na frente do nome.

![Ícone de aviso](media/how-to-connect-fix-default-rules/default2.png)

 Desabilitado de regra com o mesmo nome próximo a ele também aparece (essa é a regra padrão).

![Editor de regras de sincronização, mostrando a regra padrão e a regra padrão modificado](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
A seguir estão as personalizações comuns para as regras padrão:

- Alterar o fluxo de atributos
- Altere o filtro de escopo
- Condição de junção de alteração

Antes de alterar todas as regras:

- Desabilite o Agendador de sincronização. O Agendador é executado a cada 30 minutos por padrão. Verifique se que ele não está iniciando enquanto você estiver fazendo alterações e solução de problemas de suas novas regras. Para desabilitar temporariamente o Agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Comandos do PowerShell para desabilitar o Agendador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de escopo pode resultar na exclusão de objetos no diretório de destino. Tenha cuidado antes de fazer alterações em como o escopo de objetos. É recomendável que você faça alterações para um servidor de preparo antes de fazer alterações no servidor do Active Directory.
- Executar uma visualização em um único objeto, conforme mencionado na [Validar regra de sincronização](#validate-sync-rule) seção, depois de adicionar qualquer nova regra.
- Execute uma sincronização completa após uma nova regra de adição ou modificação de qualquer regra de sincronização personalizadas. Esta sincronização se aplica a novas regras para todos os objetos.

## <a name="change-attribute-flow"></a>Alterar o fluxo de atributos
Há três cenários diferentes para alterar o fluxo de atributo:
- Adicionando um novo atributo.
- Substituindo o valor de um atributo existente.
- Escolha não sincronizar um atributo existente.

Você pode fazer essas sem alterar as regras padrão.

### <a name="add-a-new-attribute"></a>Adicionar um novo atributo
Se você encontrar um atributo não está fluindo do seu diretório de origem para o diretório de destino, use o [Azure AD Connect sync: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para corrigir esse problema.

Se as extensões não funcionarem para você, tente adicionar duas novas regras de sincronização, descritas nas seções a seguir.


#### <a name="add-an-inbound-sync-rule"></a>Adicionar uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a fonte para o atributo é um espaço do conector e o destino é o metaverso. Por exemplo ter um novo atributo flua do local do Active Directory ao Azure Active Directory, crie uma nova regra de sincronização de entrada. Inicie o **Editor de regras de sincronização**, selecione **Inbound** como a direção e selecione **adicionar nova regra**. 

 ! Sincronização regras Editor](media/how-to-connect-fix-default-rules/default3a.png)

Execute sua própria convenção de nomenclatura para nomear a regra. Aqui, usaremos **personalizado em do AD – usuário**. Isso significa que a regra é uma regra personalizada e é uma regra de entrada do espaço do conector do Active Directory para o metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Fornece sua própria descrição da regra, para que a manutenção futura da regra é fácil. Por exemplo, a descrição pode ser com base no qual é o objetivo da regra e por que ela é necessária.

Faça suas seleções o **sistema conectado**, **tipo de objeto de sistema conectado**, e **tipo de objeto do metaverso** campos.

Especifique o valor de precedência de 0 a 99 (quanto menor o número, maior a precedência). Para o **marca**, **habilitar a sincronização de senha**, e **desabilitado** campos, use as seleções padrão.

Manter **filtro de escopo** vazio. Isso significa que a regra se aplica a todos os objetos associados entre os sistemas conectado do Active Directory e o metaverso.

Manter **regras de junção** vazio. Isso significa que essa regra usa a condição de junção definida na regra padrão. Essa é outra razão para não desabilitar ou excluir a regra padrão. Se não houver nenhuma condição de junção, o atributo de fluxo não. 

Adicione transformações apropriadas para seu atributo. Você pode atribuir uma constante, para tornar uma constante de valor de fluxo para o atributo de destino. Você pode usar o mapeamento direto entre o atributo de origem ou destino. Ou, você pode usar uma expressão para o atributo. Aqui estão vários [funções de expressão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) você pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicionar uma regra de sincronização de saída
Para vincular o atributo para o diretório de destino, você precisará criar uma regra de saída. Isso significa que a fonte é o metaverso e o destino é o sistema conectado. Para criar uma regra de saída, inicie o **Editor de regras de sincronização**, altere o **direção** para **Outbound**e selecione **adicionar nova regra**. 

![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Como com a regra de entrada, você pode usar sua própria convenção de nomenclatura para nomear a regra. Selecione o **sistema conectado** como o locatário do Azure AD e selecione o sistema conectado o objeto ao qual você deseja define o valor do atributo. Defina a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Manter **filtro de escopo** e **regras de junção** vazio. Preencha a transformação como constante, direta ou expressão. 

Agora você sabe como fazer um novo atributo para um fluxo do objeto de usuário do Active Directory ao Azure Active Directory. Você pode usar estas etapas para mapear qualquer atributo de qualquer objeto de origem e destino. Para obter mais informações, consulte [criando regras de sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [preparar para provisionar usuários](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Substituir o valor de um atributo existente
Talvez você queira substituir o valor de um atributo que já foi mapeado. Por exemplo, se você quiser sempre definir um valor nulo para um atributo no Azure AD, simplesmente crie apenas uma regra de entrada. Verifique o valor da constante, `AuthoritativeNull`, fluxo para o atributo de destino. 

>[!NOTE] 
> Use `AuthoritativeNull` em vez de `Null` nesse caso. Isso é porque o valor não nulo substitui o valor nulo, mesmo se ele tem uma precedência mais baixa (um valor mais alto número na regra). `AuthoritativeNull`, por outro lado, não é substituído por um valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincronizar atributo existente
Se você desejar excluir um atributo da sincronização, use o atributo fornecido no Azure AD Connect do recurso de filtragem. Inicie **do Azure AD Connect** do ícone da área de trabalho e, em seguida, selecione **personalizar opções de sincronização**.

![Opções de tarefas adicionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se **aplicativo do Azure AD e a filtragem de atributo** está selecionado e selecione **próxima**.

![Recursos opcionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Desmarque os atributos que você deseja excluir da sincronização.

![Atributos do Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Altere o filtro de escopo
Sincronização do Azure AD cuida da maioria dos objetos. Você pode reduzir o escopo dos objetos e reduzir o número de objetos a serem exportados, sem alterar as regras de sincronização padrão. 

Use um dos seguintes métodos para reduzir o escopo dos objetos que você está sincronizando:

- atributo cloudFiltered
- Filtragem de unidade de organização

Se você reduzir o escopo de usuários que estão sendo sincronizados, a sincronização de hash de senha também é interrompido para os usuários de saída filtrada. Se os objetos já estiver sincronizando, depois que você reduzir o escopo, os objetos filtrados horizontalmente são excluídos do diretório de destino. Por esse motivo, certifique-se de que você definir o escopo com muito cuidado.

>[!IMPORTANT] 
> Não é recomendado aumentar o escopo dos objetos configurado pelo Azure AD Connect. Isso torna difícil para a equipe de suporte da Microsoft entender as personalizações. Se você precisa aumentar o escopo dos objetos, edite a regra existente, cloná-lo e desabilitar a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo cloudFiltered
É possível definir esse atributo no Active Directory. Defina o valor desse atributo, adicionando uma nova regra de entrada. Você pode usar **Transformation** e **expressão** definir esse atributo no metaverso. O exemplo a seguir mostra que você não deseja sincronizar todos os usuários cujo nome de departamento começa com **HRD** (diferencia maiusculas de minúsculas):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Podemos convertida primeiro o departamento de origem (Active Directory) em minúsculas. Em seguida, usando o `Left` função, podemos levou apenas os três primeiros caracteres e comparado-o com `hrd`. Se ele tiver uma correspondência, o valor é definido como `True`; caso contrário, `NULL`. No definindo o valor como nulo, outra regra com menor precedência (um valor de número mais alto) pode dar a ele com uma condição diferente. Visualização de execução em um objeto para validar a regra de sincronização, conforme mencionado na [regra de sincronização de validar](#validate-sync-rule) seção.

![Criar opções de regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem de unidade organizacional
Você pode criar uma ou mais unidades organizacionais (OUs) e mover os objetos que você não deseja sincronizar a essas UOs. Em seguida, configure a Azure AD Connect de filtragem da UO. Inicie **do Azure AD Connect** no ícone da área de trabalho e selecione as opções a seguir. Você também pode configurar a filtragem no momento da instalação do Azure AD Connect da UO. 

![Tarefas adicionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e, em seguida, desmarque as UOs que você não deseja sincronizar.

![Opções de filtragem de UO e conecte-se de domínio do AD do Azure](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Condição de junção de alteração
Use as condições de junção padrão configuradas pelo Azure AD Connect. Alterar as condições de junção padrão torna difícil para o suporte da Microsoft entender as personalizações e suporte do produto.

## <a name="validate-sync-rule"></a>Validar a regra de sincronização
Você pode validar a regra de sincronização adicionado recentemente usando o recurso de visualização, sem executar o ciclo de sincronização completa. No Azure AD Connect, selecione **serviço de sincronização**.

![Azure AD Connect, com o serviço de sincronização realçado](media/how-to-connect-fix-default-rules/default10.png)

Selecione **pesquisa de metaverso**. Selecione o objeto de escopo como **pessoa**, selecione **Adicionar cláusula**e mencione a seus critérios de pesquisa. Em seguida, selecione **pesquisa**e clique duas vezes o objeto nos resultados da pesquisa. Certifique-se de que seus dados no Azure AD Connect são atualizados para o objeto, executando de importação e sincronização na floresta antes de executar esta etapa.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Na **propriedades do objeto de metaverso**, selecione **conectores**, selecione o objeto no conector correspondente (floresta) e selecione **propriedades...** .

![Propriedades de Objeto do Metaverso](media/how-to-connect-fix-default-rules/default12.png)

Selecione **visualização...**

![Propriedades de objeto do espaço do conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela de visualização, selecione **gerar visualização** e **fluxo de atributos de importação** no painel esquerdo.

![Visualização](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, observe que a regra recém-adicionado é executada no objeto e tiver definido o `cloudFiltered` atributo como true.

![Visualização](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra padrão, exporte ambos das regras o separadamente, como arquivos de texto. Essas regras são exportadas como um arquivo de script do PowerShell. Você pode compará-los usando qualquer ferramenta de comparação de arquivo (por exemplo, o windiff) para ver as alterações. 
 
Observe que a regra modificada, o `msExchMailboxGuid` atributo é alterado para o **expressão** tipo, em vez de **direto**. Além disso, o valor é alterado para **nulo** e **ExecuteOnce** opção. Você pode ignorar as diferenças de precedência e identificados. 

![saída da ferramenta WinDiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para corrigir as regras para alterá-las de volta para as configurações padrão, exclua a regra modificada e habilite a regra padrão. Certifique-se de que você não perca a personalização que você está tentando atingir. Quando estiver pronto, execute **sincronização completa**.

## <a name="next-steps"></a>Próximas etapas
- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)



