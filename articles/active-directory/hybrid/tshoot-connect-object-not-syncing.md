---
title: Solucionar problemas de um objeto que não está sincronizando com o Azure AD | Microsoft Docs
description: Solucione problemas de um objeto que não está sincronizando com o Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7b43b0e0676cc31938bf64cf84f9e6799c2dd3dd
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296589"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Solucionar problemas de um objeto que não está sincronizando com o Azure AD

Se um objeto não está sincronizando com o Azure AD como esperado, isso pode ser devido a vários motivos. Se você recebeu uma mensagem de erro do Azure AD ou está vendo o erro no Azure AD Connect Health, leia [Solucionar problemas de erros de exportação](tshoot-connect-sync-errors.md). Mas se estiver solucionando um problema em que o objeto não está no Azure AD, este tópico será ideal para você. Ele descreve como encontrar erros na sincronização do Azure AD Connect do componente local.

>[!IMPORTANT]
>Para a implantação do AAD (Azure Active Directory) Connect com a versão 1.1.749.0 ou posterior, use a [tarefa de solução de problemas](tshoot-connect-objectsync.md) no assistente para solucionar problemas de sincronização de objetos. 

## <a name="synchronization-process"></a>Processo de Sincronização

Antes de investigar problemas de sincronização, vamos entender o processo de Sincronização do **Azure AD Connect**:

  ![Processo de Sincronização do Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço do Conector, uma tabela no banco de dados.
* **MV:** Metaverso, uma tabela no banco de dados.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Etapas de Sincronização**
O processo de sincronização envolve as seguintes etapas:

1. **Importar do AD:** objetos do **Active Directory** são trazidos para o **AD CS**.

2. **Importar do AAD:** objetos do **Azure Active Directory** são trazidos para o **AAD CS**.

3. **Sincronização:** **Regras de Sincronização de Entrada** e **Regras de Sincronização de Saída** são executadas na ordem de número de precedência da menor para maior. Para exibir as Regras de Sincronização, você pode ir para **Editor de Regras de Sincronização** dos aplicativos da área de trabalho. As **Regras de Sincronização de Entrada** trazem dados de CS para MV. As **Regras de Sincronização de Saída** movem dados de MV para CS.

4. **Exportar para o AD:** depois de executar a sincronização, os objetos são exportados do AD CS para o **Active Directory**.

5. **Exportar para o AAD:** depois de executar a sincronização, os objetos são exportados do AAD CS para o **Azure Active Directory**.

## <a name="troubleshooting"></a>solução de problemas

Para encontrar os erros, você observará alguns lugares diferentes na seguinte ordem:

1. Os [logs de operação](#operations), para encontrar erros identificados pelo mecanismo de sincronização durante a importação e sincronização.
2. O [espaço conector](#connector-space-object-properties), para encontrar objetos ausentes e erros de sincronização.
3. O [metaverso](#metaverse-object-properties), para encontrar problemas relacionados a dados.

Inicie o [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) antes de começar estas etapas.

## <a name="operations"></a>Operações
A guia de operações no Synchronization Service Manager é o local em que você deve iniciar a solução de problemas. A guia Operações mostra os resultados das operações mais recentes.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

A metade superior mostra todas as execuções em ordem cronológica. Por padrão, as operações de log mantêm informações sobre os últimos sete dias, mas essa configuração pode ser alterada com o [agendador](how-to-connect-sync-feature-scheduler.md). Você deseja procurar qualquer execução que não mostre um status bem-sucedido. É possível alterar a classificação clicando nos cabeçalhos.

A coluna **Status** traz as informações mais importantes e mostra o problema mais grave de uma execução. Aqui está um resumo rápido dos status mais comuns em ordem de prioridade para investigação (em que * indica várias cadeias de caracteres de erro possíveis).

| Status | Comentário |
| --- | --- |
| stopped-* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto está inoperante e não pode ser contatado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| completed-\*-errors |A execução foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| completed-\*-warnings |A execução foi concluída, mas alguns dados não estão no estado esperado. Se houver erros, geralmente, essa mensagem indicará apenas um sintoma. Até que tenha resolvido os erros, você não deverá investigar os avisos. |
| sucesso |Nenhum problema. |

Quando você seleciona uma linha, a parte inferior é atualizada para mostrar os detalhes dessa execução. À extrema esquerda da parte inferior, talvez você veja uma lista indicando **Etapa nº**. Essa lista só será exibida se você tiver vários domínios na floresta, em que cada domínio é representado por uma etapa. O nome de domínio pode ser encontrado sob o título **Partição**. Em **Estatísticas de Sincronização**, é possível encontrar mais informações sobre o número de alterações que foram processadas. É possível clicar nos links para obter uma lista dos objetos alterados. Se você tiver objetos com erros, eles aparecerão em **erros de sincronização**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Solucionar problemas de erros na guia Operações
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Quando houver erros, o objeto com erro e o próprio erro serão exibidos como links que fornecerão mais informações.

Comece clicando na cadeia de caracteres de erro (**sync-rule-error-function-triggered** na imagem). Primeiro, você vê uma visão geral do objeto. Para ver o erro real, clique no botão **Rastreamento de Pilha**. Esse rastreamento fornece informações de nível de depuração sobre o erro.

Clique com o botão direito do mouse na caixa **informações da pilha de chamadas**, escolha **selecionar tudo** e **copiar**. Em seguida, é possível copiar a pilha e examinar o erro em seu editor favorito, como o Bloco de Notas.

* Se o erro for proveniente de **SyncRulesEngine**, as informações da pilha de chamadas primeiro terão uma lista de todos os atributos no objeto. Role para baixo até ver o cabeçalho **InnerException =>**.  
  ![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
   A linha posterior mostra o erro. Na figura acima, o erro é proveniente de uma Regra de Sincronização personalizada criada pela Fabrikam.

Se o erro em si não fornecer informações suficientes, será o momento de examinar os próprios dados. É possível clicar no link com o identificador de objeto e continuar solucionando problemas do [objeto importado do espaço conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades do objeto do espaço conector
Se você não encontrar nenhum erro na guia [operações](#operations), a próxima etapa será seguir o objeto do espaço conector do Active Directory, para o metaverso e para o Azure AD. Nesse caminho, você deverá encontrar onde está o problema.

### <a name="search-for-an-object-in-the-cs"></a>Pesquisar um objeto no CS

No **Synchronization Service Manager**, clique em **Conectores**, selecione o Active Directory Connector e **Pesquisar Espaço Conector**.

Em **Escopo**, selecione **RDN** (quando desejar pesquisar o atributo CN) ou **DN ou âncora** (quando desejar pesquisar o atributo distinguishedName). Insira um valor e clique em **Pesquisar**.  
![Pesquisa do Espaço Conector](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se você não encontrar o objeto que está procurando, talvez ele tenha sido filtrado com a [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou a [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Leia o tópico [Configurar a filtragem](how-to-connect-sync-configure-filtering.md) para verificar se a filtragem está configurada como esperado.

Outra pesquisa útil é selecionar o Conector do Azure AD, em **Escopo**, selecionar **Importação Pendente** e marcar a caixa de seleção **Adicionar**. Essa pesquisa fornece todos os objetos sincronizados no Azure AD que não podem ser associados a um objeto local.  
![Órfão de pesquisa do Espaço Conector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Esses objetos foram criados por outro mecanismo de sincronização ou por um mecanismo de sincronização com outra configuração de filtragem. Essa exibição é uma lista de objetos **órfãos** que não são mais gerenciados. Examine essa lista e considere a remoção desses objetos usando os cmdlets do [PowerShell do Azure AD](https://aka.ms/aadposh).

### <a name="cs-import"></a>Importação do CS
 Quando você abre um objeto CS, há várias guias na parte superior. A guia **Importação** exibe os dados preparados após uma importação.  
![Objeto do CS](./media/tshoot-connect-object-not-syncing/csobject.png)    
O **Valor Antigo** mostra o que está atualmente armazenado no Connect e o **Novo Valor**, o que foi recebido do sistema de origem e ainda não foi aplicado. Se houver um erro no objeto, as alterações não serão processadas.

**Erro**  
![Objeto do CS](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
A guia **Erro de Sincronização** só estará visível se houver um problema com o objeto. Para obter mais informações, consulte [Solucionar problemas de erros de sincronização](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Linhagem do CS
 A guia de linhagem mostra como o objeto do espaço conector está relacionado ao objeto de metaverso. Você pode ver quando o Conector realizou a última importação de uma alteração do sistema conectado e quais regras foram aplicadas para popular dados no metaverso.  
![Linhagem do CS](./media/tshoot-connect-object-not-syncing/cslineage.png)  
Na coluna **Ação**, você pode ver que há uma regra de sincronização de **Entrada** com a ação **Provisionar**. Isso indica que, desde que esse objeto do espaço do conector esteja presente, o objeto do metaverso permanece. Se, em vez disso, a lista de regras de sincronização mostrar uma regra de sincronização com direção de **Saída** e **Provisionar**, isso indicará que o objeto será excluído quando o objeto de metaverso for excluído.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Você também pode ver na coluna **PasswordSync** que o espaço conector de entrada pode contribuir com alterações para a senha, uma vez que uma regra de sincronização tem o valor **True**. Essa senha é então enviada ao Azure AD por meio da regra de saída.

Na guia de linhagem, é possível acessar o metaverso clicando em [Propriedades de Objeto do Metaverso](#mv-attributes).

Há dois botões na parte inferior de todas as guias: **Versão prévia** e **Log**.

### <a name="preview"></a>Visualização
A página de visualização é usada para sincronizar um único objeto. Isso será útil se você estiver solucionando problemas de algumas regras de sincronização personalizadas e desejar ver o efeito de uma alteração em um único objeto. É possível selecionar entre **Sincronização completa** e **Sincronização delta**. Também é possível selecionar entre **Gerar Visualização**, que mantém apenas a alteração na memória e **Confirmar Visualização**, que atualiza o metaverso e prepara todas as alterações para os espaços conectores de destino.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
É possível inspecionar o objeto e qual regra é aplicada a um fluxo de atributos específico.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Registro
A página de Registro é usada para verificar o status de sincronização da senha e o histórico. Para obter mais informações, consulte [Solucionar problemas de sincronização de senha hash](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades do objeto do metaverso
Em geral, é melhor começar a pesquisa no [espaço conector](#connector-space) de origem do Active Directory. Mas também é possível começar a pesquisa no metaverso.

### <a name="search-for-an-object-in-the-mv"></a>Pesquisar um objeto no MV
No **Synchronization Service Manager**, clique em **Pesquisa de Metaverso**. Crie uma consulta que você sabe que encontrará o usuário. É possível pesquisar atributos comuns, como accountName (sAMAccountName) e userPrincipalName. Para obter mais informações, consulte [Pesquisa de metaverso](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Na janela **Resultados da Pesquisa**, clique no objeto.

Se você não encontrou o objeto, isso indica que ele ainda não chegou ao metaverso. Continue pesquisando o objeto no **espaço conector** do [Active Directory](#connector-space-object-properties). Se você localizar o objeto no espaço do conector do **Active Directory**, poderá haver um erro de sincronização impedindo o objeto de vir para o metaverso ou pode haver um filtro de definição de escopo de regra de sincronização aplicado.

### <a name="object-not-found-in-the-mv"></a>Objeto não encontrado no MV
Se o objeto estiver no **Active Directory** CS, porém não estiver presente no MV, o filtro de escopo será aplicado. 

* Para examinar o filtro de escopo vá para o menu de aplicativo da área de trabalho e clique em **Editor de Regras de Sincronização**. Filtre as regras aplicáveis ao objeto ajustando o filtro abaixo.

  ![Pesquisa de Regras de Sincronização de Entrada](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* Exiba cada regra na lista acima e verifique o **Filtro de escopo**. No filtro de escopo abaixo, se o valor **isCriticalSystemObject** for nulo ou FALSE ou estiver vazio, ele estará no escopo.

  ![Pesquisa de Regras de Sincronização de Entrada](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* Vá para a lista de atributos [Importação do CS](#cs-import) e verifique qual filtro está impedindo o objeto de ir para o MV. Essa parte da lista de atributos do **Espaço do Conector** mostrará somente atributos não nulos e não vazios. Por exemplo, se **isCriticalSystemObject** não está aparecendo na lista, isso significa que o valor desse atributo é nulo ou vazio.

### <a name="object-not-found-in-the-aad-cs"></a>Objeto não encontrado no AAD CS
Se o objeto não está presente no **Espaço do Conector** do **Azure Active Directory**. No entanto, está presente no MV, então, examine o filtro de escopo das regras de **Saída** do **Espaço do Conector** correspondente e verifique se o objeto é filtrado porque os [Atributos do MV](#mv-attributes) não atendem aos critérios.

* Para ver o filtro de escopo de saída, selecione as regras aplicáveis ao objeto ajustando o filtro abaixo. Exiba cada regra e veja o valor do [atributo do MV](#mv-attributes) correspondente.

  ![Pesquisa de Regras de Sincronização de Saída](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributos do MV
 Na guia Atributos, é possível ver os valores e qual Conector os forneceu.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se um objeto não está sincronizando, observe os seguintes atributos no metaverso:
- O atributo **cloudFiltered** está presente e está definido como **true**? Nesse caso, ele foi filtrado de acordo com as etapas descritas em [filtragem baseada em atributo](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- O atributo **sourceAnchor** está presente? Caso contrário, você tem uma topologia de floresta de conta-recurso? Se um objeto for identificado como uma caixa de correio vinculada (o atributo **msExchRecipientTypeDetails** tem o valor 2), sourceAnchor será uma contribuição da floresta com uma conta do Active Directory habilitada. Verifique se a conta mestra foi importada e sincronizada corretamente. A conta mestra deve estar listada nos [conectores](#mv-connectors) do objeto.

### <a name="mv-connectors"></a>Conectores do MV
 A guia Conectores mostra todos os espaços conectores que contêm uma representação do objeto.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
É necessário ter um conector para:

- Cada floresta do Active Directory em que o usuário está representado. Essa representação pode incluir os objetos foreignSecurityPrincipals e Contact.
- Um conector no Azure AD.

Se você não tiver o conector do Azure AD, leia [Atributos do MV](#mv-attributes) para verificar os critérios de provisionamento no Azure AD.

Essa guia também permite navegar até o [objeto do espaço conector](#connector-space-object-properties). Selecione uma linha e clique em **Propriedades**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
