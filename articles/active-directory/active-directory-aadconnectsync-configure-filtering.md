<properties
	pageTitle="Azure AD Connect Sync - Configurar a filtragem"
	description="Explica como configurar a filtragem no Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: configurar a filtragem

No Azure AD Connect Sync, você pode habilitar a filtragem a qualquer momento. Se você já executou as configurações padrão de sincronização de diretório e então configurou a filtragem, os objetos que são filtrados não são mais sincronizados ao AD do Azure. Como resultado, quaisquer objetos no AD do Azure que foram anteriormente sincronizados, mas foram filtrados, são excluídos no AD do Azure. Se os objetos tiverem sido acidentalmente excluídos devido a um erro de filtragem, você pode recriar os objetos no AD do Azure, removendo suas configurações de filtragem e sincronizando seus diretórios novamente.

> [AZURE.IMPORTANT]A Microsoft não dá suporte à modificação ou a operação do Azure AD conectar Sync fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte do Azure AD Connect Sync e, como resultado, a Microsoft não pode dar suporte técnico a tais implantações.
 
Com exceção de filtragem de saída baseada em atributos, as configurações serão mantidas quando você instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma prática recomendada verificar se a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente, antes de executar o primeiro ciclo de sincronização.


## Opções de filtragem

> [AZURE.NOTE]Neste tópico, AD de origem é usado como o nome para o seu conector de serviço de domínio do Active Directory. Se você tiver várias florestas, você terá um conector por floresta e a configuração deve ser repetida para cada floresta.
 
Os três tipos de configuração de filtragem a seguir podem ser aplicados à Ferramenta de Sincronização de Diretório:

- **Baseada em domínio**: você pode usar esse tipo de filtragem para gerenciar as propriedades do conector SourceAD no Azure AD Connect Sync. Esse tipo permite selecionar quais domínios podem ser sincronizados ao AD do Azure.

- **Configurar filtragem baseada em unidade organizacional**: você pode usar esse tipo de filtragem para gerenciar as propriedades do conector SourceAD no Azure AD Connect Sync. Esse tipo de filtragem permite selecionar quais unidades organizacionais podem ser sincronizadas ao AD do Azure.

- **Baseada em atributo**: você pode usar esse método de filtragem para especificar filtros com base em atributo. Isso habilita você a controlar quais objetos devem ser sincronizados à nuvem.

## Configurar filtragem baseada em domínio

Esta seção fornece a você as etapas necessárias para configurar seu filtro de domínio.


> [AZURE.NOTE]Se você tiver modificado o filtro de domínio, você também precisa atualizar seus perfis de execução.
 

**Para definir o filtro de domínio, execute as seguintes etapas:**

1. Faça logon no computador que está executando o Azure AD Connect Sync usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.

2. Em **Iniciar**, toque ou clique em **Serviço de Sincronização** para abrir o **Gerenciador de Serviço de Sincronização**. <br>

3. Para abrir o modo de exibição de conectores, clique em **Conectores** no menu **Ferramentas**.

4. Na lista de **Conectores**, selecione o conector que tem **Serviço de Domínio do Active Directory** como seu **Tipo**.

5. Para abrir a caixa de diálogo **Propriedades**, clique em **Propriedades** no menu **Ações**.

6. Clique em **Configurar Partições de Diretório**.

7. Na lista **Selecionar Partições de Diretório**, verifique se apenas as partições que você deseja sincronizar estão selecionadas. <br> >[AZURE.Note]Para remover um domínio do processo de sincronização, desmarque a caixa de seleção do domínio.

8. Para fechar a caixa de diálogo **Propriedades**, clique em **OK**.


Se você tiver modificado seu filtro de domínio, você também precisa atualizar os perfis de execução a seguir:

- Importação completa
- Sincronização completa
- Importação de delta
- Sincronização de delta
- Exportação


Se você tiver removido uma partição na lista de partições de diretório, você precisa certificar-se de que todas as etapas de perfil de execução que fazem referência a essa partição também sejam removidas.

**Para remover uma etapa de um perfil de execução, execute as seguintes etapas:**

1. Na lista de **Conectores**, selecione o conector que tem **Serviço de Domínio do Active Directory** como seu **Tipo**.

2. Para abrir a caixa de diálogo **Configurar Perfis de Execução para**, clique em **Configurar Perfis de Execução** no menu **Ações**.

3. Na lista **Perfis de execução do conector**, selecione o perfil de execução que você deseja configurar

4. Para cada etapa na lista de detalhes de etapa, execute as seguintes etapas:

     4\.1. Se necessário, clique na etapa para expandir seus detalhes.

     4\.2. Se o **Valor** do atributo **Partition** é um GUID, clique em Excluir Etapa.

5. Para fechar a caixa de diálogo **Configurar Perfis de Execução para**, clique em **OK**.

Se você tiver adicionado uma partição à lista de partições de diretório, você precisa certificar-se de que uma etapa de perfil de execução para essa partição está disponível em cada um dos perfis de execução na lista acima.

**Para adicionar uma etapa a um perfil de execução, execute as seguintes etapas:**

1. Na lista de **Conectores**, selecione o conector que tem **Serviço de Domínio do Active Directory** como seu **Tipo**.

2. Para abrir a caixa de diálogo **Configurar Perfis de Execução para**, clique em **Configurar Perfis de Execução** no menu **Ações**.

3. Na lista **Perfis de execução do conector**, selecione o perfil de execução que você deseja configurar

4. Para abrir a caixa de diálogo **Configurar Perfil de Execução**, clique em **Nova Etapa**.

5. Na página **Configurar Etapa**, na lista de tipo de etapa, selecione o tipo de etapa e, em seguida, clique em **Avançar**.

6. Na página **configuração do Conector**, na lista **Partição**, selecione o nome da partição que você adicionou ao seu filtro de domínio.

7. Para fechar a caixa de diálogo **Configurar Perfis de Execução**, clique em **Concluir**.

8. Para fechar a caixa de diálogo **Configurar Perfis de Execução para**, clique em **OK**.

 

## Configurar a filtragem baseada em unidade organizacional

**Para configurar a filtragem baseada em unidade organizacional, execute as seguintes etapas:**

1. Faça logon no computador que está executando o Azure AD Connect Sync usando uma conta que seja membro do grupo de segurança **ADSyncAdmins**.

2. Em **Iniciar**, toque ou clique em **Serviço de Sincronização** para abrir o **Gerenciador de Serviço de Sincronização**.<br>

3. No **Gerenciador de Serviço de Sincronização**, clique em **Conectores** e, em seguida, clique duas vezes em **SourceAD**.

4. Clique em **Configurar Partições de Diretório**, selecione o domínio que você deseja configurar e, em seguida, clique em **Contêineres**.

5. Quando solicitado, insira suas credenciais de domínio para a floresta do Active Directory local.<br> >[AZURE.NOTE]Quando for apresentada a caixa de diálogo de credenciais, a conta usada para importar e exportar para o AD DS será exibida. Se você não souber a senha para a conta que você pode inserir outra conta a ser usada. A conta usada deve ter permissões de leitura para o domínio configurado no momento.

6. Na caixa de diálogo **Selecionar Contêineres**, desmarque as unidades organizacionais que você não deseja sincronizar com o diretório de nuvem e, em seguida, clique em **OK**.

7. Clique em **OK** na página **Propriedades do SourceAD**.

8. Realize uma importação completa e uma sincronização de delta, executando as seguintes etapas:

     8\.1. Na lista de conectores, selecione **SourceAD**

     8\.2. Para abrir a caixa de diálogo **Executar Conector**, selecione **Executar** no menu **Ações**.

     8\.3. Na lista **Executar perfis**, selecione **Importação Completa** e, em seguida, aguarde a conclusão do perfil de execução.

     8\.4. Para abrir a caixa de diálogo **Executar Conector**, selecione **Executar** no menu **Ações**.

     8\.5. Na lista **Executar perfis**, selecione **Sincronização Delta** e, em seguida, aguarde a conclusão do perfil de execução.




## Configurar a filtragem baseada em atributo

Há várias maneiras de configurar a filtragem com base em atributos. A configuração na entrada do AD é recomendável porque essas configurações serão mantidas mesmo após uma atualização para uma versão mais recente. Há suporte para configuração na saída do AAD, mas essas configurações não serão mantidas após uma atualização para uma versão mais recente e só devem ser usadas quando for necessário examinar o objeto combinado no metaverso para determinar a filtragem.

### Filtragem de entrada

A filtragem baseada em entrada está utilizando a configuração padrão, na qual objetos em direção ao AAD não devem ter o atributo metaverso cloudFiltered definido como um valor e devem ter o atributo metaverso sourceObjectType definido como "User" ou "Contact".

O atributo cloudFiltered deve estar definido como True caso o objeto não deva ser sincronizado com o AD do Azure, ou mantido vazio nos demais casos. Este método é usado quando podemos observar um objeto e dizer que não queremos sincronizá-lo (filtragem negativa).

No exemplo a seguir, vamos filtrar todos os usuários em que extensionAttribute15 tem o valor NoSync:

1. Faça logon no computador que está executando o Azure AD Connect Sync usando uma conta que seja membro do grupo de segurança ADSyncAdmins.
2. Abra o **Editor de Regras de Sincronização** do **Menu Iniciar**.
3. Certifique-se de que **Entrada** está selecionada e clique em **Adicionar Nova Regra**.
4. Atribua um nome descritivo à regra, como "\*Entrada do AD – DoNotSyncFilter do Usuário\*", selecione a floresta correta, escolha **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto de MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em tipo de precedência, digite um valor não usado atualmente por outra regra de sincronização (p. ex.: 50); em seguida, clique em **Avançar**.
5. Em **Filtro de escopo**, clique em **Adicionar Grupo**, clique em **Adicionar Cláusula** e, no atributo, selecione **ExtensionAttribute15**. Assegure-se que o Operador está definido como **IGUAL** e **digite** o valor NoSync na caixa Valor. Clique em **Avançar**.
6. Deixe as regras de **Junção** vazias e, em seguida, clique em **Avançar**.
7. Clique em **Adicionar Transformação**, selecione o **Tipo de Fluxo** como **Constante**, selecione o Atributo de Destino cloudFiltered e, na caixa de texto de origem, digite True. Clique em Adicionar para salvar a regra.
8. Execute uma sincronização completa: na guia **Conectores**, clique com botão direito do mouse em **SourceAD**, clique em **Executar**, clique em **Sincronização Completa** e, em seguida, clique em **OK**. 


O atributo **sourceObjectType** provisionará um **Usuário** ou **Contato** ao AD do Azure se este atributo tiver o valor **Usuário** ou **Contato**, respectivamente. Ao criar uma Regra de Sincronização com uma precedência maior do que as iniciais, você pode substituir o comportamento padrão. Esse método também oferece uma oportunidade para expressar tanto as regras positivas quanto as negativas.

No exemplo a seguir, você só sincronizará os usuários quando o atributo department é "\*Vendas\*" ou quando estiver vazio:

1. Faça logon no computador que está executando o Azure AD Connect Sync usando uma conta que seja membro do grupo de segurança ADSyncAdmins.
2. Abra o **Editor de Regras de Sincronização** do **Menu Iniciar**.
3. Certifique-se de que **Entrada** está selecionada, então clique em **Adicionar Nova Regra**.
4. Atribua um nome descritivo à regra (por exemplo, "\*Entrada do AD – DoNotSyncFilter do Usuário\*"), selecione a floresta correta, escolha **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto de MV**. Como **Tipo de Vínculo**, selecione **Junção** e, em **tipo de precedência**, digite um valor não usado atualmente por outra regra de sincronização (p. ex.: 60). Clique em **Avançar**.
5. Deixe o **filtro de escopo** e as **regras de junção** vazios e clique duas vezes em **Avançar**.
6. Clique em **Adicionar Transformação**, selecione o **Tipo de Fluxo** como **Expressão** e selecione o **Atributo de Destino** como **sourceObjectType**. Na **Fonte**, digite a seguinte expressão:<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Clique em Adicionar para salvar a regra.
8. Execute uma sincronização completa: na guia **Conectores**, clique com botão direito do mouse em **SourceAD**, clique em **Executar**, clique em **Sincronização Completa** e, em seguida, clique em **OK**. Eia aqui um resultado de como seria a aparência disso:<br>

> [AZURE.NOTE]Observe que estamos usando uma combinação de cloudFiltered e sourceObjectType para determinar quais objetos desejamos sincronizar ao AAD.
 
Com o uso de expressões, você tem opções de filtragem muito poderosas. Na expressão acima, observe que fornecemos o literal NULL quando o atributo department não estiver presente e também quando departament for Vendas. Isso indica que esse atributo não contribui com um valor e as regras iniciais serão avaliadas. Queremos isso para que eles possam determinar se é um **Usuário** ou **Contato** que devemos criar no AD do Azure.


## Filtragem com base em saída

Em alguns casos, é necessário fazer a filtragem somente depois que os objetos tiverem ingressado no metaverso. Por exemplo, ser necessário examinar o atributo mail da floresta de recurso e o atributo userPrincipalName da floresta de conta para determinar se um objeto deve ser sincronizado. Nesses casos, criaremos a filtragem na regra de saída.

> [AZURE.NOTE]Esse método exige uma alteração às regras de sincronização iniciais. Há suporte para alterar o escopo de uma regra de sincronização, mas a alteração não pode ser preservada após a atualização para uma versão mais recente do Azure AD Connect. Se você usar a filtragem baseada em saída, tome nota das alterações a fazer e, após uma atualização, certifique-se de que a filtragem ainda está lá e aplique-a novamente se necessário.
 

Neste exemplo, vamos alterar a filtragem para que somente usuários nos quais ambos o email e userPrincipalName terminam com @contoso.com sejam sincronizados:

1. Faça logon no computador que está executando o Azure AD Connect Sync usando uma conta que seja membro do grupo de segurança ADSyncAdmins.
2. Abra o Editor de Regras de Sincronização, localizando-o no Menu Iniciar.
3. Em Tipos de Regra, clique em Saída.
4. Encontre a regra chamada Saída para AAD – Ingresso de Usuário. Clique em Editar.
5. Clique em Filtro de escopo na barra de navegação à esquerda. Clique em Adicionar cláusula e, em Atributo, selecione mail, em Operador, selecione ENDSWITH e por fim, em Valor, digite @contoso.com. Clique em Adicionar cláusula e, em Atributo, selecione userPrincipalName, em Operador, selecione ENDSWITH e, por fim, em Valor, digite @contoso.com.
6. Clique em Salvar.
7. Execute uma sincronização completa: na guia Conectores, clique com botão direito do mouse em SourceAD, clique em Executar, clique em Sincronização Completa e, em seguida, clique em OK.



## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

 

<!---HONumber=July15_HO5-->