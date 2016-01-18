<properties
	pageTitle="Configurar a sincronização do Azure AD Connect: configurar a filtragem | Microsoft Azure"
	description="Explica como configurar a filtragem no Azure AD Connect Sync."
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
	ms.date="12/30/2015"
	ms.author="andkjell;markusvi"/>


# Azure AD Connect Sync: configurar a filtragem
Com a filtragem, você pode controlar quais objetos devem aparecer no AD do Azure do seu diretório local. A configuração padrão obterá todos os objetos em todos os domínios nas florestas configuradas. Em geral, essa é a configuração recomendada. Por exemplo, os usuários finais usando as cargas de trabalho do Office 365, como o Exchange Online e o Skype for Business, serão beneficiados com uma Lista de Endereços Global completa para que possam enviar emails e fazer chamadas para todos. Com a configuração padrão, obteriam uma experiência igual à da implementação local do Exchange ou do Lync.

Em alguns casos, é necessário fazer algumas alterações na configuração padrão. Estes são alguns exemplos:

- Você planeja usar a [topologia de vários diretórios do AD do Azure](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Em seguida, você precisa aplicar um filtro para controlar quais objetos devem ser sincronizados para um determinado diretório do AD do Azure.
- Você executa um piloto para o Azure ou o Office 365 e só deseja um subconjunto de usuários no AD do Azure. No pequeno piloto, não é importante ter uma Lista de Endereços Global completa para demonstrar a funcionalidade.
- Você tem muitas contas de serviço e outras contas não pessoais que não deseja no AD do Azure.
- Por motivos de conformidade, você não excluirá nenhuma conta de usuário local, só as desabilitará. Mas no AD do Azure, você só deseja que haja contas ativas presentes.

Este artigo mostrará como configurar os diferentes métodos de filtragem.

> [AZURE.IMPORTANT]A Microsoft não dá suporte à modificação ou a operação do Azure AD Connect Sync fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte do Azure AD Connect Sync e, como resultado, a Microsoft não pode dar suporte técnico a tais implantações.

## Noções básicas e observações importantes
No Azure AD Connect Sync, você pode habilitar a filtragem a qualquer momento. Se você já tiver começado com uma configuração padrão de sincronização de diretório e então configurou a filtragem, os objetos que são filtrados não são mais sincronizados ao AD do Azure. Como resultado, quaisquer objetos no AD do Azure que foram anteriormente sincronizados, mas foram filtrados, são excluídos no AD do Azure.

Antes de começar a fazer alterações na filtragem, [desabilite a tarefa agendada](#disable-scheduled-task) para não fazer acidentalmente alterações de exportação que ainda não tenham sido verificadas como corretas.

Como a filtragem pode remover muitos objetos ao mesmo tempo, certifique-se de que os novos filtros estejam corretos antes de iniciar a exportação de todas as alterações para o AD do Azure. Depois de concluir as etapas de configuração, será altamente recomendável seguir as [etapas de verificação](#apply-and-verify-changes) antes de exportar e de fazer alterações no AD do Azure.

Para proteger você da exclusão de vários objetos por acidente, o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) é ativado por padrão. Se você excluir muitos objetos devido à filtragem (500, por padrão), precisará seguir as etapas deste artigo para permitir que as exclusões passem para o AD do Azure.

Se você usar uma versão anterior à de novembro de 2015([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), alterar a configuração de filtro e usar a sincronização de senha, precisará disparar uma sincronização completa de todas as senhas depois de concluir a configuração. Para obter etapas sobre como disparar uma senha sincronização completa, veja [Disparar uma sincronização completa de todas as senhas](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se você estiver na versão 1.0.9125 ou posterior, então a ação de **sincronização total** normal, também será calculada caso as senhas tenham de ser sincronizadas e esta etapa extra não será mais necessária.

Se os objetos de **usuário** tiverem sido acidentalmente excluídos do AD do Azure devido a um erro de filtragem, você pode recriar os objetos de usuário no AD do Azure, removendo suas configurações de filtragem e sincronizando seus diretórios novamente. Isso irá restaurar os usuários da lixeira no AD do Azure. No entanto, não é possível cancelar a exclusão de outros tipos de objeto. Por exemplo, se excluir acidentalmente um grupo de segurança e se a ACL tiver sido usada como um recurso, o grupo e suas ACLs não poderão ser recuperados.

O Azure AD Connect só excluirá os objetos que uma vez considerou como no escopo. Se houver objetos no AD do Azure que foram criados por outro mecanismo de sincronização e se eles não estiverem no escopo, a adição da filtragem não os removerá. Por exemplo, se você começar com um servidor DirSync, e se ele tiver criado uma cópia completa do diretório inteiro no AD do Azure, e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com a filtragem habilitada desde o início, ele não removerá os objetos adicionais criados pelo DirSync.

As configurações de filtragem serão mantidas quando você instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma prática recomendada verificar se a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente, antes de executar o primeiro ciclo de sincronização.

Se você tiver mais de uma floresta, as configurações de filtragem descritas neste tópico deverão ser aplicadas a todas as florestas (supondo que você queira a mesma configuração para todas elas).

### Desabilitar tarefa agendada
Para desabilitar a tarefa agendada que disparará um ciclo de sincronização a cada três horas, siga estas etapas:

- Inicie o **Agendador de Tarefas** no menu Iniciar.
- Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador do Azure AD Sync**, clique com o botão direito do mouse e selecione **Desabilitar**. ![Agendador de Tarefas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)
- Agora você pode fazer alterações de configuração e executar o mecanismo de sincronização manualmente do console **gerenciador de serviço de sincronização**.

Depois de concluir todas as alterações de filtragem, não se esqueça de voltar e de **Habilitar** a tarefa novamente.

## Opções de filtragem
Os tipos de configuração de filtragem a seguir podem ser aplicados à Ferramenta de Sincronização de Diretório:

- [**Baseada em grupo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): a filtragem baseada em um único grupo só pode ser configurada na instalação inicial usando o assistente de instalação. Ela não será mais abordada neste tópico.

- [**Baseada em domínio**](#domain-based-filtering): essa opção permite que você selecione quais domínios serão sincronizados com o AD do Azure. Ela também permite adicionar e remover domínios da configuração do mecanismo de sincronização se você fizer alterações à sua infraestrutura local após a instalação da sincronização do Azure AD Connect.

- [**Baseada em unidade organizacional**](#organizational-unitbased-filtering): essa opção permite que você selecione quais UOs serão sincronizadas com o AD do Azure. Essa opção estará em todos os tipos de objeto nas UOs selecionadas.

- [**Com base no atributo**](#attribute-based-filtering): essa opção permite que você filtre objetos com base em valores de atributos nos objetos. Você também pode ter filtros diferentes para tipos de objeto diferentes.

Você pode usar várias opções de filtragem ao mesmo tempo. Por exemplo, você pode usar a filtragem baseada em UO para incluir apenas os objetos em uma UO e, ao mesmo tempo, a filtragem baseada em atributos para filtrar os objetos ainda mais. Quando você usa vários métodos de filtragem, os filtros usam um E lógico entre os filtros.

## Filtragem baseada em domínio
Esta seção fornece a você as etapas necessárias para configurar seu filtro de domínio. Se você adicionar ou remover domínios na floresta depois de ter instalado o Azure AD Connect também será necessário atualizar a configuração de filtragem.

A configuração de filtragem baseada em domínio consiste nestas etapas:

- [Selecione os domínios](#select-domains-to-be-synchronized) que devem ser incluídos na sincronização.
- Para cada domínio adicionado e removido, ajuste os [perfis de execução](#update-run-profiles).
- [Aplicar e verificar as alterações](#apply-and-verify-changes).

### Selecionar os domínios a serem sincronizados
**Para definir o filtro de domínio, execute as seguintes etapas:**

- Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
- Inicie o **Serviço de Sincronização** no menu Iniciar.
- Selecione **Conectores** e, na lista **Conectores**, selecione o Conector com o tipo **Serviços de Domínio do Active Directory**. Em **Ações**, selecione **Propriedades**. ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
-  Clique em **Configurar Partições de Diretório**.
- Na lista **Selecionar partições de diretório**, marque e desmarque os domínios como necessário. Verifique se apenas as partições que você deseja sincronizar estão selecionadas. ![Partições](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Se você tiver alterado a infraestrutura local do AD e se tiver adicionado ou removido domínios da floresta, clique no botão **Atualizar** para obter uma lista atualizada. Quando você atualizar, suas credenciais serão solicitadas; forneça todas as credenciais com o acesso de leitura para seu Active Directory local. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo. ![Atualização necessária](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)
- Quando terminar, feche a caixa de diálogo **Propriedades** clicando em **OK**. Se você tiver removido domínios da floresta, será exibida uma mensagem informando que um domínio foi removido e que a configuração será limpa.
- Prossiga para ajustar os [perfis de execução](#update-run-profiles).

### Atualizar perfis de execução
Se você tiver modificado seu filtro de domínio, também precisará atualizar os perfis de execução.

- Na lista **Conectores**, verifique se o conector que você alterou na etapa anterior está selecionado. Em **Ações**, selecione **Configurar Perfis de Execução**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)

Você precisa ajustar os seguintes perfis:

- Importação completa
- Sincronização completa
- Importação de delta
- Sincronização de delta
- Exportação

Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **adicionado**:

- Selecione o perfil de execução e clique em **Nova Etapa**.
- Na página **Configurar Etapa**, na lista suspensa **Tipo**, selecione o tipo de etapa com o mesmo nome do perfil que você está configurando. Em seguida, clique em **Avançar**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)
- Na página **Configuração do Conector**, na lista suspensa **Partição**, selecione o nome do domínio que você adicionou ao filtro de domínio. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)
- Para fechar a caixa de diálogo **Configurar Perfil de Execução**, clique em **Concluir**.

Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **removido**:

- Selecione o perfil de execução.
- Se o **Valor** do atributo **Partition** for um GUID, selecione a etapa de execução e clique em **Excluir Etapa**. ![Perfis de execução do conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)

O resultado final deve ser que cada domínio que você deseja sincronizar deverá ser listado como uma etapa em cada perfil de execução.

Para fechar o diálogo **Configurar Perfis de Execução**, clique em **OK**.

- Para concluir a configuração, [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Filtragem baseada em unidade organizacional
**Para configurar a filtragem baseada em unidade organizacional, execute as seguintes etapas:**

- Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
- Inicie o **Serviço de Sincronização** no menu Iniciar.
- Selecione **Conectores** e, na lista **Conectores**, selecione o Conector com o tipo **Serviços de Domínio do Active Directory**. Em **Ações**, selecione **Propriedades**. ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
-  Clique em **Configurar Partições de Diretório**, selecione o domínio que você deseja configurar e, em seguida, clique em **Contêineres**.
- Quando solicitado, forneça todas as credenciais com o acesso de leitura para seu Active Directory local. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo.
- Na caixa de diálogo **Selecionar Contêineres**, desmarque as UOs que você não deseja sincronizar com o diretório de nuvem e clique em **OK**. ![UO](./media/active-directory-aadconnectsync-configure-filtering/ou.png)
	- O contêiner **Computadores** deve ser selecionado para que os computadores com o Windows 10 sejam sincronizados com êxito para o AD do Azure. Se os computadores ingressados no domínio estiverem localizados em outras UOs, verifique se eles estão selecionados.
	- O contêiner **ForeignSecurityPrincipals** deverá ser selecionado se você tiver várias florestas com relações de confiança. Isso permitirá que a associação ao grupo de segurança entre florestas seja resolvida.
	- A UO **RegisteredDevices** deverá ser selecionada caso você tenha habilitado o recurso de write-back do dispositivo. Se você usar outro recurso de write-back, como o write-back de grupo, verifique se esses locais estão selecionados.
	- Selecione qualquer outra UO, onde usuários, iNetOrgPersons, grupos, contatos e computadores estão localizados. Na figura acima, todos esses itens estão localizados na UO ManagedObjects.
- Quando terminar, feche a caixa de diálogo **Propriedades** clicando em **OK**.
- Para concluir a configuração, [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Filtragem baseada em atributo
Verifique se você está na versão ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) de novembro de 2015 ou posterior para que estas etapas funcionem.

A filtragem baseada em atributo é a maneira mais flexível de filtrar objetos. Você pode usar o poder do [provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para controlar quase todos os aspectos de quando um objeto deve ser sincronizado para o AD do Azure.

A filtragem pode ser aplicada tanto na [entrada](#inbound-filtering) do Active Directory para o metaverso quanto na [saída](#outbound-filtering) do metaverso para o AD do Azure. É recomendável aplicar a filtragem na entrada, já que essa é a mais fácil de manter. A filtragem de saída só deverá ser usada se for necessária para unir objetos de mais de uma floresta antes da avaliação.

### Filtragem de entrada
A filtragem baseada em entrada está utilizando a configuração padrão, na qual objetos em direção ao AAD não devem ter o atributo metaverso cloudFiltered definido como um valor a ser sincronizado. Se o valor desse atributo for definido como **True**, o objeto não será sincronizado. Ele não deve ser definido como **False** por design. Para garantir que outras regras tenham a capacidade de contribuir com um valor, esse atributo só deverá ter os valores **True** ou **Null** (ausente).

Na filtragem de entrada, usaremos o poder do **escopo** para determinar quais objetos devem ou não devem ser sincronizados. Aqui, você fará os ajustes para os requisitos da sua própria organização. O módulo de escopo tem **grupo** e **cláusula** para determinar se uma regra de sincronização deve estar no escopo. Um **grupo** conterá uma ou várias **cláusulas**. Há um E lógico entre várias cláusulas e um OU lógico entre vários grupos.

Vamos examinar um exemplo: ![Escopo](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Isso deve ser lido como **(departamento = TI) OU (departamento = Vendas E c = US)**.

Nos exemplos e nas etapas abaixo, usaremos o objeto de usuário como um exemplo, mas você poderá usar isso para todos os tipos de objeto.

Nos exemplos abaixo, os valores de precedência usados começam com 500. Isso garantirá que eles sejam avaliados após as regras prontas (precedência menor, maior valor numérico).

#### Filtragem negativa, “não sincronizar estes"
No exemplo a seguir, vamos filtrar (e não sincronizar) todos os usuários em que **extensionAttribute15** tem o valor **NoSync**.

- Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
- Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
- Certifique-se de que **Entrada** esteja selecionada e clique em **Adicionar Nova Regra**.
- Dê à regra um nome descritivo, como "*Entrada do AD – User DoNotSyncFilter*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em tipo de precedência, digite um valor não usado atualmente por outra regra de sincronização (p. ex.: 500); em seguida, clique em **Avançar**. ![Descrição da entrada 1](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)
- Em **Filtro de escopo**, clique em **Adicionar Grupo**, clique em **Adicionar Cláusula** e, no atributo, selecione **ExtensionAttribute15**. Verifique se o Operador está definido como **EQUAL** e digite o valor **NoSync** na caixa Valor. Clique em **Próximo**. ![Escopo da entrada 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)
- Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
- Clique em **Adicionar Transformação**, selecione o **Tipo de Fluxo** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **True**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
- Para concluir a configuração, [Aplicar e verificar as alterações](#apply-and-verify-changes).

#### Filtragem positiva, "sincronizar somente estas"
Expressar a filtragem positiva pode ser mais desafiador, já que você também precisa considerar os objetos que não são óbvios para a sincronização, como as salas de conferência.

A opção de filtragem positiva exigirá duas regras de sincronização. Um (ou vários) com o escopo de objetos correto para sincronizar e uma segunda regra de sincronização que capture tudo e que filtrará todos os objetos que ainda não foram identificados como um objeto que deve ser sincronizado.

No exemplo a seguir, você só sincronizará os objetos de usuário quando o atributo de departamento tiver o valor **Vendas**.

- Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
- Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
- Certifique-se de que **Entrada** esteja selecionada e clique em **Adicionar Nova Regra**.
- Dê à regra um nome descritivo, como "*Entrada do AD – Sincronização de Vendas de Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em tipo de precedência, digite um valor não usado atualmente por outra regra de sincronização (por exemplo: 501); em seguida, clique em **Avançar**. ![Descrição da entrada 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)
- Em **Filtro de escopo**, clique em **Adicionar Grupo**, clique em **Adicionar Cláusula** e, no atributo, selecione **departamento**. Verifique se o Operador está definido como **EQUAL** e digite o valor **Vendas** na caixa Valor. Clique em **Próximo**. ![Escopo da entrada 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)
- Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
- Clique em **Adicionar Transformação**, selecione o **Tipo de Fluxo** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **False**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Este é um caso especial em que definiremos cloudFiltered explicitamente como False.

Agora temos de criar a regra de sincronização que captura tudo.

- Dê à regra um nome descritivo, como "*Entrada do AD – Filtro Captura Tudo Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em tipo de precedência, digite um valor não usado atualmente por outra Regra de Sincronização (por exemplo: 600). Nós selecionamos um valor de precedência maior (menor precedência) do que a regra de sincronização anterior mas também deixamos espaço para podermos adicionar mais regras de sincronização de filtragem posteriormente, quando quisermos iniciar a sincronização de outros departamentos. Clique em **Próximo**. ![Descrição da entrada 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
- Deixe **Filtro de escopo** vazio e clique em **Avançar**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
- Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
- Clique em **Adicionar Transformação**, selecione o **Tipo de Fluxo** como **Constante**, selecione o Atributo de Destino **cloudFiltered** e, na caixa de texto Origem, digite **True**. Clique em **Adicionar** para salvar a regra. ![Transformação da entrada 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
- Para concluir a configuração, [Aplicar e verificar as alterações](#apply-and-verify-changes).

Se for necessário, podemos criar mais regras do primeiro tipo em que incluiremos mais e mais objetos em nossa sincronização.

### Filtragem de entrada
Em alguns casos, é necessário fazer a filtragem somente depois que os objetos tiverem ingressado no metaverso. Por exemplo, ser necessário examinar o atributo de email da floresta de recurso e o atributo userPrincipalName da floresta de conta para determinar se um objeto deve ser sincronizado. Nesses casos, criaremos a filtragem na regra de saída.

Neste exemplo, vamos alterar a filtragem para que somente usuários nos quais ambos o email e userPrincipalName terminam com @contoso.com sejam sincronizados:

- Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.
- Inicie o **Editor de Regras de Sincronização** do menu Iniciar.
- Em Tipos de Regra, clique em **Saída**.
- Encontre a regra chamada **Saída para AAD – SOAInAD Ingresso de Usuário**. Clique em **Editar**.
- No pop-up, responda **Sim** para criar uma cópia da regra.
- Na página **Descrição**, altere a precedência para um valor não usado, por exemplo, 50.
- Clique em **Filtro de escopo** na barra de navegação à esquerda. Clique em **Adicionar cláusula** e, em Atributo, selecione **mail**, em Operador, selecione **ENDSWITH** e, em Valor, digite **@contoso.com**. Clique em **Adicionar cláusula** e, em Atributo, selecione **userPrincipalName**, em Operador, selecione **ENDSWITH** e, em Valor, digite **@contoso.com**.
- Clique em **Salvar**.
- Para concluir a configuração, [Aplicar e verificar as alterações](#apply-and-verify-changes).

## Aplicar e verificar as alterações
Depois de ter feito as alterações de configuração, elas deverão ser aplicadas aos objetos já presentes no sistema. Também é possível que seja necessário que os objetos que não estejam no mecanismo de sincronização sejam processados e que tenhamos de ler o sistema de origem novamente para verificar seu conteúdo.

Se você tiver alterado a configuração usando a filtragem de **domínio** ou de **unidade organizacional**, precisará fazer a **Importação completa** seguida pela **Sincronização Delta**.

Se você tiver alterado a configuração usando a filtragem de **atributo**, precisará fazer a **Sincronização completa**.

Siga estas etapas:

- Inicie o **Serviço de Sincronização** no menu Iniciar.
- Selecione **Conectores** e, na lista **Conectores**, selecione o Conector onde você alterou uma configuração anteriormente. Em **Ações** selecione **Executar**. ![Execução do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)
- Em **Perfis de execução**, selecione a operação mencionada na seção anterior. Se você precisar executar duas ações, execute a segunda após a primeira ter sido concluída (a coluna **Estado** será **Ocioso** para o conector selecionado).

Após a sincronização, todas as alterações serão preparadas para exportação. Antes de realmente fazermos as alterações no AD do Azure, queremos verificar que todas essas alterações estejam corretas.

- Inicie um prompt de comando e vá para `%Program Files%\Microsoft Azure AD Sync\bin`
- Execute: `csexport "Name of Connector" %temp%\export.xml /f:x` O nome do Conector pode ser encontrado no serviço de sincronização. Ele terá um nome semelhante a "contoso.com – AAD" do Azure AD.
- Execute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
- Agora você tem um arquivo em % temp % chamado export.csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
- Faça as alterações necessárias na configuração ou nos dados e execute essas etapas novamente (Importar, Sincronizar e Verificar) até o momento estimado para que as alterações a serem exportadas ocorram.

Quando estiver satisfeito, exporte as alterações para o AD do Azure.

- Selecione **Conectores** e, na lista **Conectores**, selecione o Conector do AD do Azure. Em **Ações**, selecione **Executar**.
- Em **Perfis de execução**, selecione **Exportar**.
- Se as alterações de configuração forem excluir vários objetos, você verá um erro na exportação caso o número seja maior do que o limite configurado (por padrão, 500). Se isso for exibido, será necessário desabilitar temporariamente o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Agora é hora de habilitar o agendador novamente.

- Inicie o **Agendador de Tarefas** no menu Iniciar.
- Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador do Azure AD Sync**, clique com o botão direito do mouse e selecione **Habilitar**.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0107_2016-->