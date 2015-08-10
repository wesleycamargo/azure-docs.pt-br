<properties
    pageTitle="Configurar seu espaço de trabalho e gerenciar as configurações"
    description="Saiba mais sobre como configurar seu espaço de trabalho e gerenciar as configurações nas Informações Operacionais do Microsoft Azure"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Configurar seu espaço de trabalho e gerenciar as configurações

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Para criar um novo espaço de trabalho Informações Operacionais do Microsoft Azure, você escolhe um nome do espaço de trabalho, associa-o à sua conta e escolhe um local geográfico. Um espaço de trabalho Informações Operacionais é essencialmente um contêiner que inclui informações da conta e informações de configuração simples para a conta. Você ou outros membros de sua organização podem usar vários espaços de trabalho de Informações Operacionais para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Depois do espaço de trabalho ser criado, você pode executar outras tarefas usando o espaço de trabalho, tais como, gerenciar as Informações Operacionais, exibir seus dados de uso no painel, selecionar contas de armazenamento e conectar diretamente agentes ou conectar o System Center Operations Manager. E você pode gerenciar as configurações para cada espaço de trabalho.


## De quantos espaços de trabalho você precisa?
Um espaço de trabalho é visto como um recurso do Azure no Portal de Gerenciamento do Azure.

Você pode criar um novo espaço de trabalho ou vincular um espaço de trabalho existente que você pode ter aberto anteriormente para usar com o System Center Operations Manager, mas ainda não associou a uma assinatura do Azure (necessária para a cobrança). Um espaço de trabalho representa o nível no qual os dados são coletados, agregados, analisados e apresentados no portal das Informações Operacionais. Você pode optar por ter vários espaços de trabalho para separar os dados de diferentes ambientes e sistemas; cada grupo de gerenciamento do Operations Manager (e todos os seus agentes) ou VMs/agentes individuais podem ser conectados a apenas um espaço de trabalho.

Cada espaço de trabalho pode ter várias contas de usuário associadas e cada conta de usuário (conta da Microsoft ou conta Organizacional) pode ter acesso a vários espaços de trabalho de Informações Operacionais. Por padrão, a conta da Microsoft ou a conta Organizacional usada para criar o espaço de trabalho torna-se o Administrador do espaço de trabalho. O administrador pode convidar contas adicionais da Microsoft ou selecionar os usuários em seu Active Directory do Azure.

## Vincular um espaço de trabalho existente a uma assinatura do Azure

É possível criar um espaço de trabalho em [microsoft.com/oms](https://microsoft.com/oms). No entanto, existem certos limites para esses espaços de trabalho, o mais notável sendo um limite de 500 MB/dia dos carregamentos de dados se você estiver usando uma conta gratuita. Para fazer alterações nesse espaço de trabalho, você precisará **vincular seu espaço de trabalho existente a uma assinatura do Azure**.

>[AZURE.IMPORTANT]Para vincular um espaço de trabalho, sua conta do Azure já deve ter acesso ao espaço de trabalho que você deseja vincular. Em outras palavras, a conta usada para acessar o portal do Azure deve ser **igual** à conta usada para acessar seu espaço de trabalho Informações Operacionais. Se este não for o caso, consulte [Adicionar um usuário a um espaço de trabalho existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Entre no Portal de Gerenciamento do Azure.
2. À esquerda inferior do portal, clique em **+ Novo**
3. Clique em **Serviços do Aplicativo**, vá para **Informações Operacionais** e selecione.
4. Clique em **Criação Rápida**.
5. Na lista **Conta**, você deverá ver uma lista dos espaços de trabalho existentes que *ainda não* foram vinculados à sua assinatura do Azure. Selecione uma conta.

  >[AZURE.NOTE]Se você não vir o espaço de trabalho que deseja vincular aqui, significa que sua assinatura do Azure não tem acesso ao espaço de trabalho de Informações Operacionais. Você precisará conceder acesso a essa conta de dentro de seu espaço de trabalho de Informações Operacionais. Para fazer isso, consulte [Adicionar um usuário a um espaço de trabalho existente](#add-a-user-to-an-existing-workspace).

  ![vincular conta](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Preencha os campos restantes, em seguida, selecione **Criar Espaço de Trabalho**.

## Atualizar o espaço de trabalho para um plano pago

Há três tipos de plano de espaço de trabalho para as Informações Operacionais: **Gratuito**, **Standard** e **Premium**. Se você estiver usando um plano *gratuito*, poderá ter atingido o limite de dados de 500 MB. Você precisará atualizar seu espaço de trabalho para um '\*\*plano pré-pago\*\*' para coletar dados além desse limite. A qualquer momento, você pode converter seu tipo de plano. Para obter mais informações sobre os preços das Informações Operacionais, consulte [Detalhes dos Preços](http://azure.microsoft.com/pricing/operational-insights/)

>[AZURE.IMPORTANT]Os planos do espaço de trabalho podem ser alterados apenas se eles estiverem *vinculados* a uma assinatura do Azure. Se você criou seu espaço de trabalho no Azure ou se *já* vinculou seu espaço de trabalho, poderá ignorar essa mensagem. Se você criou seu espaço de trabalho a partir do [opinsights.azure.com](http://opinsights.azure.com), precisará seguir as etapas em [Vincular um espaço de trabalho existente a uma assinatura do Azure](#link-an-existing-workspace-to-an-Azure-subscription).

### Alterar o tipo de plano

No Portal de Gerenciamento do Azure, navegue até o espaço de trabalho Informações Operacionais para o qual você gostaria de atualizar:

![Escala](./media/operational-insights-setup-workspace/find-workspace.png)

Selecione esse espaço de trabalho e selecione **ESCALA** nas guias na parte superior da tela

![selecionar escala](./media/operational-insights-setup-workspace/select-scale.png)

Por fim, escolha o plano para o qual você gostaria de atualizar e clique em **SALVAR**. Você verá as alterações refletidas no portal e, agora, será capaz de coletar dados além do limite de dados "gratuito".

![selecionar plano](./media/operational-insights-setup-workspace/plan-select.png)

## Alterar o nome do espaço de trabalho

Se você for o administrador de um espaço de trabalho Informações Operacionais do Microsoft Azure, poderá alterar o nome do espaço.

### Para alterar o nome do espaço de trabalho

1. Clique no nome do espaço de trabalho.![nome do espaço de trabalho](./media/operational-insights-setup-workspace/settings01.png)
2. Clique no ícone de configuração.![ícone de configuração](./media/operational-insights-setup-workspace/settings02.png)
3. Na página **Configurações** em Informações Operacionais, na seção **Gerenciar Contas do Usuário**, clique em **Gerenciar usuários**.![gerenciar usuários](./media/operational-insights-setup-workspace/settings03.png)
4. No portal de Informações Operacionais, na página **Configurações**, digite o novo nome no campo **Nome do espaço de trabalho**.
5. Clique em **Salvar**.

## Alterar as informações do usuário

Você pode alterar o nome associado a um usuário de Informações Operacionais, mas não pode alterar o nome da conta da Microsoft associada ao usuário.

Para os usuários com uma conta da Microsoft, você também pode alterar as configurações de notificação. Se você usou uma conta organizacional por meio do Active Directory do Azure, não poderá usar atualmente o recurso **Notificações** em Informações Operacionais.

### Para alterar as informações do usuário
1. Na página **Configurações** em Informações Operacionais, na seção **Informações do Usuário**, digite o nome nos campos **Primeiro nome** e **Sobrenome**.

2. Para os usuários da conta da Microsoft, altere as configurações de notificação. Por padrão, todos os usuários da conta são notificados quando um alerta é gerado. Se você quiser parar de receber essas notificações, desmarque a opção **Receber notificações por email de novos alertas das Informações Operacionais**.

3. Clique em **Salvar**.

## Alterar configurações de notificação

Por padrão, todos os usuários associados a um espaço de trabalho Informações Operacionais recebem um email que resume quaisquer alertas de avaliação da configuração que foram gerados nos últimos sete dias. Na página **Configurações**, os usuários podem controlar se eles recebem essas notificações por email.

>[AZURE.NOTE]As notificações estão disponíveis apenas para os usuários com uma conta da Microsoft. Se você usou uma conta organizacional por meio do Active Directory do Azure, não poderá usar atualmente o recurso **Notificações** em Informações Operacionais.

Você não está vendo os emails que acha que deveria? Tente verificar seus filtros de spam. Verifique se os emails de *operationalinsights@opinsights.azure.com* não estão sendo filtrados.

1. Na página **Configurações** em Informações Operacionais, na seção **Informações do Usuário**, limpe a opção **Receber notificações por email de novos alertas das Informações Operacionais**.

2. Clique em **Salvar**.

## Adicionar um usuário a um espaço de trabalho existente


Use as seguintes etapas para adicionar um usuário ou grupo a um espaço de trabalho Informações Operacionais. O usuário ou o grupo poderá exibir e atuar em todos os alertas associados a esse espaço de trabalho.

>[AZURE.NOTE]Se você quiser adicionar um usuário ou grupo a partir de sua conta organizacional do Active Directory do Azure, primeiro deverá assegurar que associou sua conta Informações Operacionais a seu domínio do Active Directory. Consulte [Adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente](#).

### Para adicionar um usuário a um espaço de trabalho existente
1. Clique no nome do espaço de trabalho.
2. Clique no ícone de configuração.
3. Na página **Configurações** em Informações Operacionais, na seção **Gerenciar Contas do Usuário**, clique em **Gerenciar usuários**.![gerenciar usuários](./media/operational-insights-setup-workspace/settings04.png)
4. Na janela **Gerenciar Usuários**, clique em **Adicionar**. ![página de configurações](./media/operational-insights-setup-workspace/manage-users01.png)
5. Se sua conta Informações Operacionais estiver associada ao Active Directory do Azure, especifique a **conta Organizacional**.>[AZURE.NOTE]Você não verá essa etapa se sua conta de Informações Operacionais usar somente contas da Microsoft. ![adicionar tipo de conta do usuário](./media/operational-insights-setup-workspace/manage-users02.png)
6. Insira as novas informações do usuário para a conta da Microsoft ou a conta organizacional. Se você estiver adicionando uma conta organizacional, poderá digitar parte do nome do usuário ou do grupo, ou o alias de email, em seguida, clicar em **Verificar Nomes** para localizar o usuário ou grupo específico.>[AZURE.NOTE]Para ter melhores resultados de desempenho, limite o número de grupos do Active Directory associados a uma única conta Informações Operacionais a dois — um para os administradores e outro para os usuários. Usar mais grupos pode afetar o desempenho das Informações Operacionais.
7. Selecione a função desse novo usuário: **Administrador** ou **Usuário**. ![adicionar função do espaço de trabalho do usuário](./media/operational-insights-setup-workspace/manage-users03.png)
8. Clique em **OK**.

  Se você estiver adicionando uma conta da Microsoft, um convite para ingressar em sua conta será enviado para o email fornecido. Depois do usuário seguir as instruções no convite para ingressar nas Informações Operacionais, o usuário poderá exibir os alertas e as informações da conta para essa conta Informações Operacionais, e você poderá exibir as informações do usuário na janela **Gerenciar Usuários**. Se você estiver adicionando uma conta organizacional, o usuário será capaz de acessar as Informações Operacionais imediatamente. ![convite](./media/operational-insights-setup-workspace/manage-users04.png)


## Adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente

Você pode associar seu espaço de trabalho Informações Operacionais a um domínio do Active Directory do Azure. Isso permite que você adicione usuários do Active Directory diretamente ao espaço de trabalho Informações Operacionais sem a necessidade de uma conta da Microsoft separada.

### Para adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente

1. Na página Configurações em Informações Operacionais, clique em **Adicionar Organização**. ![convite](./media/operational-insights-setup-workspace/add-org.png)
2. Revise as informações sobre as contas organizacionais, em seguida, clique em **Próximo**.
3. Insira as informações de identidade para o administrador do domínio do Active Directory do Azure, em seguida, clique em **Entrar**.
4. Clique em **Conceder acesso** para habilitar as Informações Operacionais para usarem as informações de identidade em seu domínio do Active Directory. ![vinculado](./media/operational-insights-setup-workspace/ad-existing01.png)


## Editar uma conta do usuário existente

Você pode alterar a função da conta de um usuário associado à sua conta Informações Operacionais. Você tem as seguintes opções de função:

 - *Administrador*: pode gerenciar os usuários, exibir e agir em todos os alertas, adicionar e remover servidores

 - *Usuário*: pode exibir e agir em todos os alertas, adicionar e remover servidores

### Para editar uma conta
1. Na página **Configurações** em Informações Operacionais, na seção **Gerenciar Contas do Usuário**, clique em **Gerenciar usuários**.

2. Na janela **Gerenciar Usuários**, selecione o nome do usuário que você deseja alterar, em seguida, clique em **Editar usuário**.

3. Selecione a função desse novo usuário: **Administrador** ou **Usuário**.

4. Clique em **OK**.

## Remover um usuário de um espaço de trabalho Informações Operacionais

Use as seguintes etapas para remover um usuário de um espaço de trabalho Informações Operacionais. Observe que isso não fecha o espaço de trabalho do usuário. Em vez disso, remove a associação entre o usuário e o espaço de trabalho. Se um usuário estiver associado a vários espaços de trabalho, esse usuário ainda poderá entrar nas Informações Operacionais.

### Para remover um usuário de um espaço de trabalho

1. Na página **Configurações** em Informações Operacionais, na seção **Gerenciar Conta do Usuário**, clique em **Gerenciar usuários**.

2. Na janela **Gerenciar Usuários**, clique no nome do usuário que você deseja remover, em seguida, clique em **Remover usuário**.

3. Clique em **OK** para confirmar que você deseja remover o usuário.

## Fechar seu espaço de trabalho Informações Operacionais

Quando você fecha um espaço de trabalho Informações Operacionais, todos os dados relacionados ao espaço de trabalho são excluídos do serviço Informações Operacionais apenas 30 dias depois de fechar o espaço de trabalho.

Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando as Informações Operacionais com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, então, precisarão criar um novo espaço de trabalho para usar as Informações Operacionais.

### Para fechar um espaço de trabalho Informações Operacionais

1. Na página **Configurações** em Informações Operacionais, na seção **Fechar Espaço de Trabalho**, clique em **Fechar Espaço de Trabalho**.

2. Selecione um dos motivos para fechar seu espaço de trabalho ou insira um motivo diferente na caixa de texto.

3. Clique em **Fechar espaço de trabalho**.

<!---HONumber=July15_HO5-->