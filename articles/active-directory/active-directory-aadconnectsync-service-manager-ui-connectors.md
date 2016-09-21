<properties
	pageTitle="Sincronização do Azure AD Connect: Interface do usuário do Synchronization Service Manager | Microsoft Azure"
	description="Entenda como usar a guia Conectores no Synchronization Service Manager para o Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="andkjell"/>


# Sincronização do Azure AD Connect: Synchronization Service Manager

[Operações](Active-Directory-aadconnectsync-Service-Manager-UI-Operations.MD) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Designer do Metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Pesquisa do Metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

A guia Conectores é usada para gerenciar todos os sistemas aos quais o mecanismo de sincronização está conectado.

## Ações do Conector

Ação | Comentário
--- | ---
Criação | Não use. Para se conectar a florestas adicionais do AD, use o assistente de instalação.
Propriedades | Usado para filtragem de domínio e de UO.
[Excluir](#delete) | Usado para excluir os dados no espaço do conector ou para excluir a conexão a uma floresta.
[Configurar perfis de execução](#configure-run-profiles) | Com exceção do domínio de filtragem, não há nada a ser configurado aqui. Você pode usar essa ação para ver os perfis de execução já configurados.
Executar | Usado para iniciar uma execução única de um perfil.
Parar | Interrompe um Conector que, atualmente, executa um perfil.
Exportar Conector | Não use.
Importar Conector | Não use.
Atualizar Conector | Não use.
Atualizar Esquema | Atualiza o esquema em cache. É preferível usar a opção no assistente de instalação, já que ela também atualiza as regras de sincronização.
[Pesquisar Espaço do Conector](#search-connector-space) | Usado para encontrar objetos e [Seguir um objeto e seus dados por meio do sistema](#follow-an-object-and-its-data-through-the-system).

### Exclusão
A ação de exclusão é usada com duas finalidades diferentes. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A opção **Excluir apenas o espaço do conector** remove todos os dados, mas mantém a configuração.

A opção **Excluir Conector e Espaço** remove os dados e a configuração. Essa opção é usada quando você não deseja mais se conectar a uma floresta.

Ambas as opções sincronizam todos os objetos e atualizam os objetos do metaverso. Essa ação é uma operação demorada.

### Configurar perfis de execução
Esta opção permite que você veja os perfis de execução configurados para um Conector.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### Pesquisar Espaço do Conector
A ação de espaço do conector de pesquisa é útil para encontrar objetos e solucionar problemas de dados.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comece selecionando um **escopo**. É possível pesquisar com base nos dados (RDN, DN, Âncora, Subárvore) ou no estado do objeto (todas as outras opções). ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png) Por exemplo, se fizer uma pesquisa em Subárvore, você obterá todos os objetos em uma UO. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) Nessa grade, é possível selecionar um objeto, selecionar **propriedades** e [segui-lo](#follow-an-object-and-its-data-through-the-system) do espaço conector de origem, passando pelo metaverso, até o espaço conector de destino.

## Seguir um objeto e seus dados por meio do sistema
Quando estiver solucionando um problema com dados, seguir um objeto do espaço do conector de origem passando pelo metaverso até o espaço do conector de destino é um procedimento fundamental para entender por que os dados não têm os valores esperados.

### Propriedades de objeto do espaço do conector
**Importação** Quando você abre um objeto CS, há várias guias na parte superior. A guia **Importação** exibe os dados preparados após uma importação. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) O **Valor Antigo** mostra o que está armazenado no sistema e o **Novo Valor**, o que foi recebido do sistema de origem e ainda não foi aplicado. Nesse caso, como há um erro de sincronização, a alteração não pode ser aplicada.

**Erro** A página de erro ficará visível apenas se houver um problema com o objeto. Veja na página de operações mais detalhes sobre como [solucionar erros de sincronização](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Linhagem** A guia de linhagem mostra como o objeto do espaço conector está relacionado ao objeto de metaverso. Você pode ver quando o Conector realizou a última importação de uma alteração do sistema conectado e quais regras foram aplicadas para popular dados no metaverso. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) Na coluna **Ação**, você pode ver que há uma regra de sincronização de **Entrada** com a ação **Provisionar**. Isso indica que, desde que esse objeto do espaço do conector esteja presente, o objeto do metaverso permanece. Se, em vez disso, a lista de regras de sincronização mostrar uma regra de sincronização com direção de **Saída** e **Provisionar**, isso indicará que o objeto será excluído quando o objeto de metaverso for excluído. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) Você também pode ver na coluna **PasswordSync** que o espaço conector de entrada pode fornecer alterações à senha, uma vez que uma regra de sincronização tem o valor **True**. Essa senha é então enviada ao Azure AD por meio da regra de saída.

Na guia de linhagem, é possível acessar o metaverso clicando em [Propriedades de Objeto do Metaverso](#metaverse-object-properties).

Na parte inferior de todas as guias, há dois botões: **Visualização** e **Log**.

**Visualização** A página de visualização é usada para sincronizar um único objeto. Isso será útil se estiver solucionando problemas de algumas regras de sincronização do cliente e desejar ver o efeito de uma alteração em um único objeto. É possível selecionar entre **Sincronização Completa** e **Sincronização delta**. Também é possível selecionar entre **Gerar Visualização**, que mantém apenas a alteração na memória, e **Confirmar Visualização**, que prepara todas as alterações para os espaços conectores de destino. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) É possível inspecionar o objeto e qual regra é aplicada a um fluxo de atributos específico.![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Log** A página Log é usada para ver o status e o histórico de sincronização de senha. Veja [Solucionar problemas de sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) para obter mais informações.

### Propriedades de objeto do metaverso
**Atributos** Na guia Atributos, é possível ver os valores e qual Conector os forneceu. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png) **Conectores** A guia Conectores mostra todos os espaços conectores que contêm uma representação do objeto. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Essa guia também permite navegar até o [objeto do espaço conector](#connector-space-object-properties).

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0907_2016-->