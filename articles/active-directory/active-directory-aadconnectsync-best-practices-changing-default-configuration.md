<properties
	pageTitle="Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão | Microsoft Azure"
	description="Fornece práticas recomendadas para alterar a configuração padrão da sincronização do Azure AD Connect."
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
	ms.date="12/28/2015"
	ms.author="markusvi;andkjell"/>


# Sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão
O objetivo deste tópico é descrever as alterações com e sem suporte para a sincronização do Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "da forma como é" para a maioria dos ambientes que sincronizam o Active Directory local com o AD do Azure. No entanto, em alguns casos, é necessário aplicar alterações a uma configuração para atender a uma necessidade ou requisito específico.

## Alterações na conta de serviço
A sincronização do Azure AD Connect é executada em uma conta de serviço criada pelo assistente de instalação. Essa conta de serviço mantém as chaves de criptografia no banco de dados usado pela sincronização. Ela é criada com uma senha de 127 caracteres que é definida para não expirar.

- **Não há suporte** para alteração ou redefinição da senha da conta de serviço. Se isso ocorrer, as chaves de criptografia serão destruídas e o serviço não poderá acessar o banco de dados ou ser iniciado.

## Alterações no agendador
A sincronização do Azure AD Connect está definida para sincronizar dados de identidade a cada três horas. Durante a instalação, uma tarefa agendada é criada em execução em uma conta de serviço com permissões para operar o servidor de sincronização.

- **Não há suporte** para alterações na tarefa agendada. A senha da conta de serviço não é conhecida. Consulte [Alterações na conta de serviço](#changes-to-the-service-account)
- **Não há suporte** para frequência de sincronização menor do que o padrão de 3 horas.
	- Há suporte para fazer sincronizações individuais ao testar uma nova configuração. Mas você não deve executar exportações ao AD do Azure de forma mais frequente.

## Alterações nas regras de sincronização
O assistente de instalação fornece uma configuração que deve funcionar nos cenários mais comuns. No caso de precisar fazer alterações na configuração, você deve seguir estas regras para continuar com uma configuração com suporte.

- Você pode [alterar fluxos de atributos](#change-attribute-flows) se os fluxos de atributos diretos padrão não forem adequados à sua organização.
- Se optar por [não fluir um atributo](#do-not-flow-an-attribute) e remover quaisquer valores do atributo existentes no AD do Azure, você precisará criar uma regra para isso.
- [Desabilite uma Regra de sincronização indesejada](#disable-an-unwanted-sync-rule) em vez de excluí-la. Uma regra excluída será recriada durante a atualização.
- Para [alterar uma regra integrada](#change-an-out-of-box-rule), você deverá fazer uma cópia da regra original e desabilitar a regra integrada. O Editor de Regra de Sincronização fará uma solicitação e o ajudará com isso.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.

>[AZURE.WARNING]As regras de sincronização integrada têm uma impressão digital. Se fizer uma alteração nessas regras, a impressão digital não corresponderá mais e você pode ter problemas no futuro quando tentar aplicar uma nova versão do Azure AD Connect. Faça alterações somente como é descrito neste artigo.

### Alterar fluxos de atributos
Em alguns casos, os fluxos de atributos padrão não funcionam para uma organização.

Você deve seguir estas regras:

- Crie uma nova regra de sincronização com seus fluxos de atributos. Ao dar a ela uma precedência mais alta (valor numérico menor), suas regras substituirão quaisquer fluxos de atributos integrados.
- Não adicione fluxos adicionais a uma regra integrada. Essas alterações serão perdidas na atualização.

Na Fabrikam, há uma floresta em que o alfabeto local é usado para primeiro nome, sobrenome e nome de exibição. A representação de caracteres latinos desses atributos é armazenada nos atributos de extensão. Ao criar a lista de endereços global no AD do Azure e no Office 365, a organização deseja que eles sejam usados.

Com uma configuração padrão, um objeto da floresta local tem esta aparência:

![Fluxo de atributos 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

- Inicie o **Editor de Regra de Sincronização** do menu Iniciar.
- Com **Entrada** ainda selecionado à esquerda, clique no botão **Adicionar nova regra**.
- Dê à regra um nome e uma descrição. Selecione o Active Directory local e os tipos de objetos relevantes. Em **Tipo de Link**, selecione **Junção**. Para a precedência, escolha um número que não seja usado por outra regra. As regras integradas começar com 100, então o valor 50 pode ser usado neste exemplo. ![Fluxo de atributos 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- Deixe o escopo vazio (ou seja, deve ser aplicada a todos os objetos de usuário na floresta).
- Deixe as regras de junção vazias (ou seja, permita que a regra integrada trate de quaisquer junções).
- Em Transformações, crie os seguintes fluxos. ![Fluxo de atributos 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- Clique em **Adicionar** para salvar a regra.
- Vá para o **Synchronization Service Manager**. Em **Conectores**, selecione o Conector ao qual adicionamos a regra. Selecione **Executar** e **Sincronização Completa**. Uma Sincronização Completa calculará novamente todos os objetos usando as regras atuais.

Este é o resultado final para o mesmo objeto com essa regra personalizada:

![Fluxo de atributos 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### Não faça um atributo fluir
Há duas maneiras de não fazer um atributo fluir. A primeira está disponível no assistente de instalação e permite que você [remova os atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Essa opção funciona se você nunca tiver sincronizado o atributo antes. No entanto, se você tiver começado a sincronizar esse atributo e removê-lo mais tarde com esse recurso, o mecanismo de sincronização deixará de gerenciar o atributo e os valores existentes serão deixados no AD do Azure.

Se você quiser remover o valor de um atributo e fazê-lo não fluir, será necessário criar uma regra personalizada.

Na Fabrikam, percebemos que alguns dos atributos que sincronizamos para a nuvem não deveriam estar lá. Queremos removê-los do AD do Azure.

![Atributos de Extensão](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Crie uma nova Regra de Sincronização de entrada e preencha a descrição ![Descrições](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Crie fluxos de atributo do tipo **Expression** e com a fonte **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve ser vazio na MV mesmo se uma regra de sincronização de precedência inferior tentar preencher o valor. ![Atributos de Extensão](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Salve a Regra de Sincronização. Inicie o **Serviço de Sincronização**, localize o Conector, selecione **Executar** e **Sincronização Completa**. Isso recalculará todos os fluxos de atributo.
- Verifique se as alterações pretendidas estão prestes a ser exportadas pesquisando o espaço conector. ![Exclusão em etapas](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### Desabilitar uma Regra de Sincronização indesejada
Não exclua uma regra de sincronização integrada; ela será recriada durante a próxima atualização.

Em alguns casos, o assistente de instalação produz uma configuração que não funcionará para a sua topologia. Por exemplo, se houver uma topologia de floresta de recurso de conta, mas você estendeu o esquema na floresta de conta com o esquema do Exchange, as regras do Exchange serão criadas para a floresta de contas e para a floresta de recursos. Nesse caso, precisamos desabilitar a Regra de Sincronização do Exchange.

![Regra de sincronização desabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na figura acima, o assistente de instalação encontrou um esquema antigo do Exchange 2003 na floresta de contas. Isso foi adicionado antes da floresta de recursos ter sido introduzida no ambiente da Fabrikam. Para garantir que nenhum atributo da implementação do Exchange antigo é sincronizado, a regra de sincronização deve ser desabilitada conforme mostrado.

### Alterar uma regra integrada
Se você precisar fazer alterações em uma regra integrada, deverá fazer uma cópia da regra integrada e desabilitar a regra original. Em seguida, faça as alterações desejadas à regra clonada. O Editor de Regra de Sincronização ajudará com isso. Quando você abre uma regra integrada, vê essa caixa de diálogo:

![Aviso de regra integrada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada é aberta.

![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Na regra clonada, faça as alterações necessárias no escopo, na associação e nas transformações.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0107_2016-->