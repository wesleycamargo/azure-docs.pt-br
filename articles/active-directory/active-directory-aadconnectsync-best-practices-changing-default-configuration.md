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
	ms.date="11/11/2015"
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

## Alterações nas regras de sincronização

O assistente de instalação fornece uma configuração que deve funcionar nos cenários mais comuns. No caso de precisar fazer alterações na configuração, você deve seguir estas regras para continuar com uma configuração com suporte.

- A única alteração com suporte a uma regra de sincronização integrada é desabilitá-la. Qualquer outra alteração pode ser perdida em uma atualização.
- Se você precisar fazer qualquer outra alteração a uma regra integrada, faça uma cópia dela e desabilite a regra original. O Editor de Regra de Sincronização fará uma solicitação e o ajudará com isso.
- Exporte suas regras de sincronização personalizadas usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.

>[AZURE.WARNING]As regras de sincronização integrada têm uma impressão digital. Se fizer uma alteração nessas regras, a impressão digital não corresponderá mais e você pode ter problemas no futuro quando tentar aplicar uma nova versão do Azure AD Connect. Faça alterações somente como é descrito neste artigo.

### Excluir uma Regra de Sincronização indesejada
Não exclua uma regra de sincronização integrada; ela será recriada durante a próxima atualização.

Em alguns casos, o assistente de instalação produz uma configuração que não funcionará para a sua topologia. Por exemplo, se houver uma topologia de floresta de recurso de conta, mas você estendeu o esquema na floresta de conta com o esquema do Exchange, as regras do Exchange serão criadas para a floresta de contas e para a floresta de recursos. Nesse caso, precisamos desabilitar a Regra de Sincronização do Exchange.

![Regra de sincronização desabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na figura acima, o assistente de instalação encontrou um esquema antigo do Exchange 2003 na floresta de contas. Isso foi adicionado antes da floresta de recursos ter sido introduzida no ambiente da Fabrikam. Para garantir que nenhum atributo da implementação do Exchange antigo é sincronizado, a regra de sincronização deve ser desabilitada conforme mostrado.

### Alterar regras integradas
Se você precisar fazer alterações em uma regra integrada, deverá fazer uma cópia da regra integrada e desabilitar a regra original. Em seguida, faça as alterações desejadas à regra clonada. O Editor de Regra de Sincronização ajudará com isso. Quando você abre uma regra integrada, vê essa caixa de diálogo:

![Aviso de regra integrada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada é aberta.

![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Na regra clonada, faça as alterações necessárias no escopo, na associação e nas transformações.

### Não faça um atributo fluir
Há duas maneiras de não fazer um atributo fluir. A primeira está disponível no assistente de instalação e permite que você [remova os atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Essa opção funciona se você nunca tiver sincronizado o atributo antes. No entanto, se você tiver começado a sincronizar esse atributo e removê-lo mais tarde com esse recurso, o mecanismo de sincronização deixará de gerenciar o atributo e os valores existentes serão deixados no AD do Azure.

Se você quiser remover o valor de um atributo e fazê-lo não fluir, será necessário criar uma regra personalizada.

Na Fabrikam, percebemos que alguns dos atributos que sincronizamos para a nuvem não deveriam estar lá. Queremos removê-los do AD do Azure.

![Atributos de Extensão](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Crie uma nova Regra de Sincronização de entrada e preencha a descrição ![Descrições](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Crie fluxos de atributo do tipo **Expression** e com a fonte **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve ser vazio na MV mesmo se uma regra de sincronização de precedência inferior tentar preencher o valor. ![Atributos de Extensão](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Salve a Regra de Sincronização. Inicie o **Serviço de Sincronização**, localize o Conector, selecione **Executar** e **Sincronização Completa**. Isso recalculará todos os fluxos de atributo.
- Verifique se as alterações pretendidas estão prestes a ser exportadas pesquisando o espaço conector. ![Exclusão em etapas](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->