<properties
	pageTitle="Operações de Azure AD Connect Health."
	description="Este artigo descreve as outras operações que podem ser executadas após a implantação do Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="billmath"/>

# Operações de Azure AD Connect Health

O tópico a seguir descreve as várias operações que podem ser executadas usando o Azure AD Connect Health.

## Habilitar notificações por email
Você pode configurar o serviço do Azure AD Connect Health para enviar notificações por email quando os alertas forem gerados, indicando que sua infraestrutura não está íntegra. Isso ocorrerá quando um alerta for gerado, bem como quando ele é marcado como resolvido. Siga as instruções abaixo para configurar notificações por email.
>[AZURE.NOTE]As notificações por email são desabilitadas por padrão.


### Para habilitar notificações por Email do Azure AD Connect Health

1. Abra a folha Alertas do serviço para o qual você deseja receber notificação por email.
2. Clique no botão "Configurações de notificação" na barra de ação.
3. Ative a opção de Notificação por email como ON.
4. Marque a caixa de seleção para configurar todos os administradores globais para receber notificações por email.
5. Se você deseja receber notificações por email em qualquer outro endereço de email, pode especificá-los na caixa Destinatário de Email Adicional. Para remover um endereço de email da lista, clique com o botão direito na entrada e selecione Excluir.
6. Para finalizar as alterações clique em "Salvar". Todas as alterações entrarão em vigor somente depois que você selecionar "Salvar".

## Excluir uma instância de serviço ou servidor

### Excluir um servidor do serviço de Azure AD Connect Health
Em alguns casos, você poderá remover um servidor que está sendo monitorado. Siga as instruções abaixo para remover um servidor do serviço de Azure AD Connect Health.

Ao excluir um servidor, esteja ciente das seguintes opções:

- Esta ação irá PARAR de coletar todos os demais dados desse servidor. Esse servidor será removido do serviço de monitoramento. Após essa ação, você não poderá exibir os novos alertas, monitoramento ou o uso de dados de análise para esse servidor.
- Essa ação NÃO irá desinstalar ou remover o agente de integridade do servidor. Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, será possível ver os eventos de erro no servidor relacionados ao agente de integridade.
- Essa ação NÃO excluirá os dados já coletados deste servidor. Esses dados serão excluídos de acordo com a política de retenção de dados do Microsoft Azure.
- Depois de executar essa ação, se você quiser iniciar o monitoramento do mesmo servidor novamente, será necessário desinstalar e reinstalar o agente de integridade neste servidor.


#### Para excluir um servidor do serviço do Azure AD Connect Health

1. Abra a folha do servidor da folha da lista do servidor, selecionando o nome do servidor a ser removido.
2. Na folha do servidor, clique no botão "Excluir" na barra de ação.
3. Confirme a ação para excluir o servidor digitando o nome do servidor na caixa de confirmação.
4. Clique no botão "Excluir".


### Exclua uma instância de serviço do serviço do Azure AD Connect Health Service

Em alguns casos, será possível remover uma instância do serviço. Siga as instruções abaixo para remover uma instância de serviço do Azure AD Connect Health.

Ao excluir uma instância de serviço, esteja ciente do seguinte:

- Esta ação irá remover a instância do serviço atual do serviço de monitoramento.
- Essa ação NÃO irá desinstalar ou remover o agente de integridade de nenhum um dos servidores que foram monitorados como parte desta instância de serviço. Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, poderá ver os eventos de erro no(s) servidor(es) relacionado(s) ao agente de integridade.
- Todos os dados dessa instância do serviço serão excluídos de acordo com a política de retenção de dados do Microsoft Azure.
- Depois de executar essa ação, se você quiser iniciar o serviço de monitoramento, desinstale e reinstale o agente de integridade em todos os servidores que serão monitorados. Depois de executar essa ação, se você quiser iniciar o monitoramento do mesmo servidor novamente, será necessário desinstalar e reinstalar o agente de integridade neste servidor.


#### Para excluir uma instância de serviço do Azure AD Connect Health Service

1. Abra uma folha de serviço na folha da lista do serviço, selecionando o identificador de serviço (nome do farm) que você deseja remover.
2. Na folha do servidor, clique no botão "Excluir" na barra de ação.
3. Confirme o nome do serviço digitando-o na caixa de confirmação. (por exemplo: sts.contoso.com)
4. Clique no botão "Excluir". <br><br>


[//]: # "Início da seção de RBAC"
## Gerenciar o acesso com o controle de acesso baseado em função
### Visão geral
[Controle de acesso baseado em função](role-based-access-control-configure.md) para Azure AD Connect Health fornece acesso ao serviço Azure AD Connect Health para usuários e/ou grupos fora dos administradores globais. Isso é feito atribuindo funções aos grupos e/ou aos usuários desejados e fornece um mecanismo para limitar os administradores globais no diretório.

#### Funções
O Azure AD Connect Health suporta as seguintes funções internas.

| Função | Permissões |
| ----------- | ---------- |
| Proprietário | Os proprietários podem ***Gerenciar acesso*** (por exemplo, atribuir função a um usuário/grupo), ***Exibir todas as informações*** (por exemplo, exibir alertas) a partir do portal e ***Alterar configurações*** (por exemplo, notificações por email) no Azure AD Connect Health. <br>Por padrão, os administradores globais do AD do Azure são atribuídos a essa função e isso não pode ser alterado. |
|Colaborador| Colaboradores podem ***Exibir todas as informações*** (por exemplo, exibir alertas) a partir do portal e ***Alterar configurações*** (por exemplo, notificações por email) no Azure AD Connect Health.|
|Leitor| Os leitores podem ***Exibir todas as informações*** (por exemplo, exibir alertas) a partir do portal no Azure AD Connect Health.|

Todas as outras funções (como 'Administradores de acesso do usuário' ou 'Usuários de laboratório de testes e desenvolvimento'), mesmo que estejam disponíveis na experiência do portal, não têm impacto de acesso no Azure AD Connect Health.

#### Escopo de acesso

O Azure AD Connect oferece suporte ao gerenciamento de acesso em dois níveis:

- ***Todas as instâncias de serviço***: este é o caminho recomendado para a maioria dos clientes e controla o acesso para todas as instâncias do serviço (por exemplo, um farm do ADFS) em todos os tipos de função monitorados pelo Azure AD Connect Health.

- ***Instância de serviço***: em alguns casos, talvez seja necessário separar o acesso com base em tipos de função ou por uma instância de serviço. Nesse caso, você pode gerenciar o acesso no nível da instância de serviço.

A permissão é concedida se um usuário final tem acesso no nível do diretório ou da instância do serviço.


### Como permitir o acesso de usuários ou grupos ao Azure AD Connect Health
#### Etapa 1: Selecione o escopo de acesso apropriado
Para permitir a um usuário o acesso no nível *todas as instâncias de serviço* no Azure AD Connect Health, abra a folha principal no Azure AD Connect Health.<br>
#### Etapa 2: Adicione usuários, grupos e atribua funções
1. Clique na parte "Usuários" na seção Configurar.<br> ![Folha Principal do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selecione "Adicionar"
3. Selecione a "Função", por exemplo, "Proprietário"<br> ![Adicionar Usuários do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Digite o nome ou identificador do usuário ou grupo de destino. Você pode selecionar um ou mais usuários ou grupos ao mesmo tempo. Clique em "selecionar". ![Selecionar Usuário do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selecione "Ok".<br>

6. Uma vez concluída a atribuição da função, os usuários e/ou grupos aparecerão na lista.<br> ![Lista de Usuários do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Essas etapas permitirão acesso aos usuários e grupos listados de acordo com suas funções.
>[AZURE.NOTE]- Administradores globais sempre têm acesso total a todas as operações, mas as contas de administrador global não estarão presentes na lista acima. - Não há suporte para o recurso "Convidar usuários" no Azure AD Connect Health.

#### Etapa 3: Compartilhe o local da folha com usuários ou grupos
1. Depois de atribuir permissões, um usuário poderá acessar o Azure AD Connect Health em [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Uma vez na folha, o usuário poderá fixar a folha, ou partes diferentes, no painel clicando em "Fixar no painel"<br> ![fixar folha do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE]Um usuário com a função "Leitor" atribuída não poderá executar a operação "criar" para obter a extensão do Azure AD Connect Health no Azure Marketplace. Esse usuário ainda pode obter a folha indo para o link acima. Para uso subsequente, o usuário pode fixar a folha no painel.

### Remover usuários e/ou grupos
Você pode remover um usuário ou grupo adicionado à parte de controle de acesso baseado em função do Azure AD Connect Health clicando com botão direito e selecionando remover.<br> ![Remover Usuário do RBAC do Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # "Fim da seção de RBAC"

## Links relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health para AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO4-->