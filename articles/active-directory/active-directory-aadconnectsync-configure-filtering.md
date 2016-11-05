---
title: 'Configurar a sincronização do Azure AD Connect: configurar a filtragem | Microsoft Docs'
description: Explica como configurar a filtragem no Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: andkjell
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: andkjell;markvi

---
# Azure AD Connect Sync: configurar a filtragem
Com a filtragem, você pode controlar quais objetos devem aparecer no Azure AD do seu diretório local. A configuração padrão obtém todos os objetos em todos os domínios nas florestas configuradas. Em geral, essa é a configuração recomendada. Os usuários finais usando as cargas de trabalho do Office 365, como o Exchange Online e o Skype for Business, são beneficiados com uma Lista de Endereços Global completa para que possam enviar emails e fazer chamadas para todos. Com a configuração padrão, obteriam uma experiência igual à da implementação local do Exchange ou do Lync.

Em alguns casos, é necessário fazer algumas alterações na configuração padrão. Estes são alguns exemplos:

* Você planeja usar a [topologia de vários diretórios do AD do Azure](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Em seguida, você precisa aplicar um filtro para controlar quais objetos devem ser sincronizados para um determinado diretório do AD do Azure.
* Você executa um piloto para o Azure ou o Office 365 e só deseja um subconjunto de usuários no Azure AD. No pequeno piloto, não é importante ter uma Lista de Endereços Global completa para demonstrar a funcionalidade.
* Você tem muitas contas de serviço e outras contas não pessoais que não deseja no Azure AD.
* Por motivos de conformidade, não exclua contas de usuário locais. Você só pode desabilitá-las. Mas no AD do Azure, você só deseja que haja contas ativas presentes.

Este artigo mostra como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> A Microsoft não dá suporte à modificação ou a operação do Azure AD Connect Sync fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte do Azure AD Connect Sync e, como resultado, a Microsoft não pode dar suporte técnico a tais implantações.
> 
> 

## Noções básicas e observações importantes
No Azure AD Connect Sync, você pode habilitar a filtragem a qualquer momento. Se você já tiver começado com uma configuração padrão de sincronização de diretório e então configurou a filtragem, os objetos que são filtrados não são mais sincronizados ao AD do Azure. Como resultado dessa alteração, quaisquer objetos no Azure AD que foram anteriormente sincronizados, mas foram filtrados, são excluídos no Azure AD.

Antes de começar a fazer alterações na filtragem, [desabilite a tarefa agendada](#disable-scheduled-task) para não fazer acidentalmente alterações de exportação que ainda não tenham sido verificadas como corretas.

Como a filtragem pode remover muitos objetos ao mesmo tempo, certifique-se de que os novos filtros estejam corretos antes de iniciar a exportação de todas as alterações para o Azure AD. Depois de concluir as etapas de configuração, será altamente recomendável seguir as [etapas de verificação](#apply-and-verify-changes) antes de exportar e de fazer alterações no Azure AD.

Para proteger você da exclusão de vários objetos por acidente, o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) é ativado por padrão. Se você excluir muitos objetos devido à filtragem (500, por padrão), precisará seguir as etapas deste artigo para permitir que as exclusões passem para o Azure AD.

Se você usar uma versão anterior à de novembro de 2015([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), alterar a configuração de filtro e usar a sincronização de senha, precisará disparar uma sincronização completa de todas as senhas depois de concluir a configuração. Para obter etapas sobre como disparar uma senha sincronização completa, veja [Disparar uma sincronização completa de todas as senhas](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se você estiver na versão 1.0.9125 ou posterior, então a ação de **sincronização total** normal, também será calculada caso as senhas tenham de ser sincronizadas e esta etapa extra não será mais necessária.

Se os objetos de **usuário** tiverem sido acidentalmente excluídos do Azure AD devido a um erro de filtragem, você pode recriar os objetos de usuário no Azure AD, removendo suas configurações de filtragem e sincronizando seus diretórios novamente. Essa ação restaura os usuários da lixeira no Azure AD. No entanto, não é possível cancelar a exclusão de outros tipos de objeto. Por exemplo, se excluir acidentalmente um grupo de segurança e se a ACL tiver sido usada como um recurso, o grupo e suas ACLs não poderão ser recuperados.

O Azure AD Connect só exclui os objetos que uma vez considerou como no escopo. Se houver objetos no Azure AD que foram criados por outro mecanismo de sincronização e se eles não estiverem no escopo, a adição da filtragem não os removerá. Por exemplo, se você começar com um servidor DirSync, e se ele tiver criado uma cópia completa do diretório inteiro no Azure AD, e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com a filtragem habilitada desde o início, ele não removerá os objetos adicionais criados pelo DirSync.

As configurações de filtragem serão mantidas quando você instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma prática recomendada verificar se a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente, antes de executar o primeiro ciclo de sincronização.

Se você tiver mais de uma floresta, as configurações de filtragem descritas neste tópico deverão ser aplicadas a todas as florestas (supondo que você queira a mesma configuração para todas elas).

### Desabilitar tarefa agendada
Para desabilitar o agendador interno, dispara um ciclo de sincronização a cada 30 minutos, siga estas etapas:

1. Vá até um prompt do PowerShell.
2. Execute `Set-ADSyncScheduler -SyncCycleEnabled $False` para desabilitar o agendador.
3. Faça as alterações conforme documentado neste tópico.
4. Execute `Set-ADSyncScheduler -SyncCycleEnabled $True` para habilitar o agendador novamente.

**Se você usa um build do Azure AD Connect anterior ao 1.1.105.0** Para desabilitar a tarefa agendada que dispara um ciclo de sincronização a cada três horas, siga estas etapas:

1. Inicie o **Agendador de Tarefas** no menu Iniciar.
2. Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador de Sincronização do Azure AD**, clique com o botão direito do mouse e selecione **Desabilitar**. ![Agendador de Tarefas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)
3. Agora você pode fazer alterações de configuração e executar o mecanismo de sincronização manualmente do console do **Synchronization Service Manager**.

Depois de concluir todas as alterações de filtragem, não se esqueça de voltar e de **Habilitar** a tarefa novamente.

## Opções de filtragem
Os tipos de configuração de filtragem a seguir podem ser aplicados à Ferramenta de Sincronização de Diretório:

* [**Baseada no grupo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): a filtragem baseada em um único grupo só pode ser configurada na instalação inicial usando o assistente de instalação. Ela não será mais abordada neste tópico.
* [**Baseada no domínio**](#domain-based-filtering): essa opção permite que você selecione quais domínios serão sincronizados com o Azure AD. Ela também permite adicionar e remover domínios da configuração do mecanismo de sincronização se você fizer alterações à sua infraestrutura local após a instalação da sincronização do Azure AD Connect.
* [**Baseada na unidade organizacional**](#organizational-unitbased-filtering): essa opção permite que você selecione quais UOs são sincronizadas com o Azure AD. Essa opção é para todos os tipos de objeto em UOs selecionadas.
* [**Baseada no atributo**](#attribute-based-filtering): essa opção permite que você filtre objetos com base nos valores de atributos nos objetos. Você também pode ter filtros diferentes para tipos de objeto diferentes.

Você pode usar várias opções de filtragem ao mesmo tempo. Por exemplo, você pode usar a filtragem baseada em UO para incluir apenas os objetos em uma UO e, ao mesmo tempo, a filtragem baseada em atributos para filtrar os objetos ainda mais. Quando você usa vários métodos de filtragem, os filtros usam um E lógico entre os filtros.

## Filtragem baseada em domínio
Esta seção fornece as etapas para configurar o filtro de domínio. Se você adicionar ou remover domínios na floresta depois de ter instalado o Azure AD Connect também será necessário atualizar a configuração de filtragem.

A melhor maneira de alterar a filtragem baseada em domínio é executando o assistente de instalação para alterar a [filtragem de domínio e UOs](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O assistente de instalação está automatizando todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se por algum motivo não conseguir executar o assistente de instalação.

A configuração de filtragem baseada em domínio consiste nestas etapas:

* [Selecionar os domínios](#select-domains-to-be-synchronized) que devem ser incluídos na sincronização.
* Para cada domínio adicionado e removido, ajuste os [perfis de execução](#update-run-profiles).
* [Aplicar e verificar as alterações](#apply-and-verify-changes).

### Selecionar os domínios a serem sincronizados
**Para definir o filtro de domínio, siga estas etapas:**

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
2. Inicie o **Serviço de Sincronização** no menu Iniciar.
3. Selecione **Conectores** e, na lista **Conectores**, selecione o Conector com o tipo **Serviços de Domínio do Active Directory**. Em **Ações**, selecione **Propriedades**. ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
4. Clique em **Configurar Partições de Diretório**.
5. Na lista **Selecionar partições de diretório**, marque e desmarque os domínios como necessário. Verifique se apenas as partições que você deseja sincronizar estão selecionadas. ![Partições](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Se você tiver alterado a infraestrutura local do AD e tiver adicionado ou removido domínios da floresta, clique no botão **Atualizar** para obter uma lista atualizada. Quando você atualizar, precisará fornecer as credenciais. Forneça todas as credenciais com o acesso de leitura para seu Active Directory local. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo ![Atualização necessária](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)
6. Quando terminar, feche a caixa de diálogo **Propriedades** clicando em **OK**. Se você tiver removido domínios da floresta, será exibida uma mensagem informando que um domínio foi removido e que a configuração será limpa.
7. Prossiga para ajustar os [perfis de execução](#update-run-profiles).

### Atualizar perfis de execução
Se você tiver modificado seu filtro de domínio, também precisará atualizar os perfis de execução.

1. Na lista **Conectores**, verifique se o Conector que você alterou na etapa anterior está selecionado. Em **Ações**, selecione **Configurar Perfis de Execução**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)

Você precisa ajustar os seguintes perfis:

* Importação completa
* Sincronização completa
* Importação de delta
* Sincronização de delta
* Exportação

Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **adicionado**:

1. Selecione o perfil de execução e clique em **Nova Etapa**.
2. Na página **Configurar Etapa**, na lista suspensa **Tipo**, selecione o tipo de etapa com o mesmo nome do perfil que você está configurando. Em seguida, clique em **Avançar**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)
3. Na página **Configuração do Conector**, na lista suspensa **Partição**, selecione o nome do domínio que você adicionou ao filtro de domínio. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)
4. Para fechar a caixa de diálogo **Configurar Perfil de Execução**, clique em **Concluir**.

Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **removido**:

1. Selecione o perfil de execução.
2. Se o **Valor** do atributo **Partition** for um GUID, selecione a etapa de execução e clique em **Excluir Etapa**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)

O resultado deve ser que cada domínio que você deseja sincronizar deverá ser listado como uma etapa em cada perfil de execução.

Para fechar o diálogo **Configurar Perfis de Execução**, clique em **OK**.

* Para concluir a configuração, você precisa [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Filtragem baseada em unidade organizacional
A melhor maneira de alterar a filtragem baseada em UOs é executando o assistente de instalação para alterar a [filtragem de domínio e UOs](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O assistente de instalação está automatizando todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se por algum motivo não conseguir executar o assistente de instalação.

**Para configurar a filtragem baseada em unidade organizacional, execute as seguintes etapas:**

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
2. Inicie o **Serviço de Sincronização** no menu Iniciar.
3. Selecione **Conectores** e, na lista **Conectores**, selecione o Conector com o tipo **Serviços de Domínio do Active Directory**. Em **Ações**, selecione **Propriedades**. ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
4. Clique em **Configurar Partições de Diretório**, selecione o domínio que você deseja configurar e, em seguida, clique em **Contêineres**.
5. Quando solicitado, forneça todas as credenciais com o acesso de leitura para seu Active Directory local. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo.
6. Na caixa de diálogo **Selecionar Contêineres**, desmarque as UOs que você não deseja sincronizar com o diretório de nuvem e clique em **OK**. ![UO](./media/active-directory-aadconnectsync-configure-filtering/ou.png)
   * O contêiner **Computadores** deve ser selecionado para que os computadores com Windows 10 sejam sincronizados com êxito com o Azure AD. Se os computadores ingressados no domínio estiverem localizados em outras UOs, verifique se eles estão selecionados.
   * O contêiner **ForeignSecurityPrincipals** deverá ser selecionado se você tiver várias florestas com relações de confiança. Esse contêiner permite que a associação de grupo de segurança entre florestas seja resolvida.
   * A UO **RegisteredDevices** deverá ser selecionada caso você tenha habilitado o recurso de write-back do dispositivo. Se você usar outro recurso de write-back, como o write-back de grupo, verifique se esses locais estão selecionados.
   * Selecione qualquer outra UO, onde usuários, iNetOrgPersons, grupos, contatos e computadores estão localizados. Na figura, todos esses itens estão localizados na UO ManagedObjects.
7. Quando terminar, feche a caixa de diálogo **Propriedades** clicando em **OK**.
8. Para concluir a configuração, você precisa [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Filtragem baseada em atributo
Verifique se você está no build de novembro de 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou posterior para que estas etapas funcionem.

A filtragem baseada em atributo é a maneira mais flexível de filtrar objetos. Você pode usar o poder do [provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) para controlar quase todos os aspectos de quando um objeto deve ser sincronizado com o Azure AD.

A filtragem pode ser aplicada tanto na [entrada](#inbound-filtering) do Active Directory para o metaverso quanto na [saída](#outbound-filtering) do metaverso para o Azure AD. É recomendável aplicar a filtragem na entrada, já que essa é a mais fácil de manter. A filtragem de saída só deverá ser usada se for necessária para unir objetos de mais de uma floresta antes da avaliação.

### Filtragem de entrada
A filtragem baseada em entrada está utilizando a configuração padrão, na qual objetos em direção ao Azure AD não devem ter o atributo metaverso cloudFiltered definido como um valor a ser sincronizado. Se o valor desse atributo for definido como **True**, o objeto não será sincronizado. Ele não deve ser definido como **False** por design. Para garantir que outras regras tenham a capacidade de contribuir com um valor, esse atributo só deverá ter os valores **True** ou **NULL** (ausente).

Na filtragem de entrada, você usa o poder do **escopo** para determinar quais objetos devem ou não ser sincronizados. Aqui, você faz os ajustes para os requisitos da sua própria organização. O módulo de escopo tem o **grupo** e a **cláusula** para determinar se uma regra de sincronização deve estar no escopo. Um **grupo** contém uma ou muitas **cláusulas**. Há um E lógico entre várias cláusulas e um OU lógico entre vários grupos.

Vamos examinar um exemplo: ![Escopo](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Isso deve ser lido como **(departamento = TI) OU (departamento = Vendas E c = EUA)**.

Nos exemplos e nas etapas abaixo, você usa o objeto de usuário como um exemplo, mas você poderá usar isso para todos os tipos de objeto.

Nos exemplos abaixo, o valor de precedência começa com 500. Esse valor garante que essas regras sejam avaliadas após as regras prontas para uso (precedência menor, valor numérico maior).

#### Filtragem negativa, “não sincronizar estes"
No exemplo a seguir, você filtra (e não sincroniza) todos os usuários em que **extensionAttribute15** tem o valor **NoSync**.

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
2. Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
3. Certifique-se de que **Entrada** esteja selecionada e clique em **Adicionar Nova Regra**.
4. Dê à regra um nome descritivo, como "*Entrada do AD – User DoNotSyncFilter*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, para precedência, digite um valor não usado atualmente por outra Regra de sincronização (por exemplo: 500), em seguida, clique em **Avançar**. ![Descrição da entrada 1](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)
5. Em **Filtro de escopo**, clique em **Adicionar Grupo**, clique em **Adicionar Cláusula** e, no atributo, selecione **ExtensionAttribute15**. Verifique se o Operador está definido como **EQUAL** e digite o valor **NoSync** na caixa Valor. Clique em **Avançar**. ![Escopo da entrada 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)
6. Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
7. Clique em **Adicionar Transformação**, selecione **FlowType** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **True**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para concluir a configuração, você precisa [Aplicar e verificar as alterações](#apply-and-verify-changes).

#### Filtragem positiva, "sincronizar somente estas"
Expressar a filtragem positiva pode ser mais desafiador, já que você também precisa considerar os objetos que não são óbvios para a sincronização, como as salas de conferência.

A opção de filtragem positiva requer duas regras de sincronização. Um (ou vários) com o escopo de objetos correto para sincronizar e uma segunda regra de sincronização que capture tudo e que filtrará todos os objetos que ainda não foram identificados como um objeto que deve ser sincronizado.

No exemplo a seguir, você só sincroniza os objetos de usuário quando o atributo de departamento tiver o valor **Vendas**.

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
2. Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
3. Certifique-se de que **Entrada** esteja selecionada e clique em **Adicionar Nova Regra**.
4. Dê à regra um nome descritivo, como "*Entrada do AD – Sincronização de Vendas de Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, para precedência, digite um valor não usado atualmente por outra Regra de sincronização (por exemplo: 501), em seguida, clique em **Avançar**. ![Descrição da entrada 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)
5. Em **Filtro de escopo**, clique em **Adicionar Grupo**, clique em **Adicionar Cláusula** e, no atributo, selecione **departamento**. Verifique se o Operador está definido como **EQUAL** e digite o valor **Vendas** na caixa Valor. Clique em **Avançar**. ![Escopo da entrada 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)
6. Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
7. Clique em **Adicionar Transformação**, selecione o **FlowType** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **False**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Este é um caso especial em que você define cloudFiltered explicitamente como False.
   
    Agora temos de criar a regra de sincronização que captura tudo.
8. Dê à regra um nome descritivo, como "*Entrada do AD – Filtro Captura Tudo Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em precedência, digite um valor não usado atualmente por outra Regra de Sincronização (por exemplo: 600). Você selecionou um valor de precedência maior (menor precedência) do que a regra de sincronização anterior, mas também deixou espaço para poder adicionar mais regras de sincronização de filtragem posteriormente, quando quiser iniciar a sincronização de outros departamentos. Clique em **Avançar**. ![Descrição da entrada 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
9. Deixe **Filtro de escopo** vazio e clique em **Avançar**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
11. Clique em **Adicionar Transformação**, selecione **FlowType** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **True**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
12. Para concluir a configuração, você precisa [Aplicar e verificar as alterações](#apply-and-verify-changes).

Se necessário, você pode criar mais regras do primeiro tipo, em que inclui mais objetos em nossa sincronização.

### Filtragem de entrada
Em alguns casos, é necessário fazer a filtragem somente depois que os objetos tiverem ingressado no metaverso. Por exemplo, ser necessário examinar o atributo de email da floresta de recurso e o atributo userPrincipalName da floresta de conta para determinar se um objeto deve ser sincronizado. Nesses casos, você cria a filtragem na regra de saída.

Neste exemplo, você altera a filtragem para que somente usuários em que o email e userPrincipalName terminam com @contoso.com são sincronizados:

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
2. Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
3. Em **Tipos de Regra**, clique em **Saída**.
4. Encontre a regra chamada **Saída para AAD – SOAInAD Ingresso de Usuário**. Clique em **Editar**.
5. No pop-up, responda **Sim** para criar uma cópia da regra.
6. Na página **Descrição**, altere a precedência para um valor não usado, por exemplo, 50.
7. Clique em **Filtro de escopo** na barra de navegação à esquerda. Clique em **Adicionar cláusula** e, em Atributo, selecione **mail**, em Operador, selecione **ENDSWITH** e, em Valor, digite **@contoso.com**. Clique em **Adicionar cláusula** e, em Atributo, selecione **userPrincipalName**, em Operador, selecione **ENDSWITH** e, em Valor, digite **@contoso.com**.
8. Clique em **Salvar**.
9. Para concluir a configuração, você precisa [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Aplicar e verificar as alterações
Depois de ter feito as alterações de configuração, elas deverão ser aplicadas aos objetos já presentes no sistema. Também é possível que seja necessário que os objetos que não estão no mecanismo de sincronização sejam processados e o mecanismo de sincronização tenha de ler o sistema de origem novamente para verificar seu conteúdo.

Se você tiver alterado a configuração usando a filtragem de **domínio** ou de **unidade organizacional**, precisará fazer a **Importação completa** seguida pela **Sincronização Delta**.

Se você tiver alterado a configuração usando a filtragem de **atributo**, precisará fazer a **Sincronização completa**.

Siga estas etapas:

1. Inicie o **Serviço de Sincronização** no menu Iniciar.
2. Selecione **Conectores** e, na lista **Conectores**, selecione o Conector no qual você alterou uma configuração anteriormente. Em **Ações**, selecione **Executar**. ![Execução do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)
3. Em **Perfis de execução**, selecione a operação mencionada na seção anterior. Se você precisar executar duas ações, execute a segunda após a primeira ter sido concluída (a coluna **Estado** será **Ocioso** para o Conector selecionado).

Após a sincronização, todas as alterações serão preparadas para exportação. Antes de realmente fazer as alterações no Azure AD, convém verificar se todas essas alterações estão corretas.

1. Inicie um prompt de comando e vá para `%Program Files%\Microsoft Azure AD Sync\bin`
2. Execute: `csexport "Name of Connector" %temp%\export.xml /f:x` o nome do Conector pode ser encontrado no Serviço de Sincronização. Ele tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Agora você tem um arquivo em %temp% chamado export.csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
5. Faça as alterações necessárias na configuração ou nos dados e execute essas etapas novamente (importar, sincronizar e verificar) até o momento estimado para que as alterações a serem exportadas ocorram.

Quando estiver satisfeito, exporte as alterações para o AD do Azure.

1. Selecione **Conectores** e, na lista **Conectores**, selecione o Conector do Azure AD. Em **Ações**, selecione **Executar**.
2. Em **Perfis de execução**, selecione **Exportar**.
3. Se as alterações de configuração forem excluir vários objetos, você verá um erro na exportação quando o número for maior do que o limite configurado (por padrão, 500). Se esse erro for exibido, será necessário desabilitar temporariamente o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Agora é hora de habilitar o agendador novamente.

1. Inicie o **Agendador de Tarefas** no menu Iniciar.
2. Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador de Sincronização do Azure AD**, clique com o botão direito do mouse e selecione **Habilitar**.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre como [Integrar suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0914_2016-->