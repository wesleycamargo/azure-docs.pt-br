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
    ms.date="08/05/2015"
    ms.author="banders"/>

# Configurar seu espaço de trabalho e gerenciar as configurações

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Para criar um novo espaço de trabalho Informações Operacionais do Microsoft Azure, você escolhe um nome do espaço de trabalho, associa-o à sua conta e escolhe um local geográfico. Um espaço de trabalho Informações Operacionais é essencialmente um contêiner que inclui informações da conta e informações de configuração simples para a conta. Você ou outros membros de sua organização podem usar vários espaços de trabalho de Informações Operacionais para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Após a criação do espaço de trabalho, você poderá executar outras tarefas usando o espaço de trabalho, como gerenciar o Insights Operacionais, adicionar soluções, conectar fontes de dados, adicionar logs, selecionar contas de armazenamento, exibir seus dados de uso no painel. Além disso, é possível gerenciar as configurações de cada espaço de trabalho.

O artigo [Integração em minutos](./operational-insights-onboard-in-minutes.md) mostra como começar rapidamente e o restante deste artigo descreve em detalhes algumas das ações necessárias para começar e para gerenciar seu espaço de trabalho.

Abordaremos todas as tarefas mais comuns que você usará nas seções a seguir:

1. Adicionar soluções
2. Conectar fontes de dados
3. Adicionar e gerenciar logs
4. Gerenciar contas e usuários

![etapas](./media/operational-insights-setup-workspace/steps.png)
## 1 Adicionar soluções

O Insights Operacionais do Microsoft Azure incluem a funcionalidade de Avaliação de Configuração básica, para que não seja necessário instalar uma solução a fim de habilitá-la. No entanto, você pode obter mais funcionalidade com a adição de soluções das páginas Configurações e Galeria de Soluções.

Depois de adicionar uma solução, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço Insights Operacionais. O processamento pelo serviço Insights Operacionais pode levar de alguns minutos a várias horas. Depois que o serviço processa os dados, você pode exibi-los no Insights Operacionais.

Você pode facilmente remover uma solução quando ela não for mais necessário. Quando você remove uma solução, seus dados não são enviados ao Insights Operacionais, o que reduz a quantidade de dados usados pela sua cota diária.

### Soluções com suporte no Microsoft Monitoring Agent

Neste momento, servidores conectados diretamente ao Insights Operacionais do Microsoft Azure usando o Microsoft Monitoring Agent podem usar a maioria das soluções disponíveis, incluindo:

- [Atualizações do Sistema](operational-insights-updates.md)
- [Antimalware](operational-insights-antimalware.md)
- [Controle de Alterações](operational-insights-change-tracking.md)
- [Avaliação do SQL e do Active Directory](operational-insights-assessment.md)

No entanto, as soluções a seguir *não* têm suporte do Microsoft Monitoring Agent e exigem o SCOM (System Center Operations Manager).

- [Gerenciamento de Capacidade](operational-insights-capacity.md)
- [Gerenciamento de alertas](operational-insights-alerts.md)
- [Avaliação de Configuração](operational-insights-solutions.md#configuration-assessment)

Consulte [Considerações sobre o Operations Manager com Insights Operacionais](operational-insights-operations-manager.md) para obter orientação sobre como usar essas soluções com o Operations Manager.

Há suporte para coleta de log do IIS em computadores com:

- Windows Server 2012
- Windows Server 2012 R2

### Para adicionar soluções usando a página Configurações

- Selecione as soluções que você deseja adicionar e clique em **Adicionar as Soluções selecionadas**. Nem todas as soluções disponíveis serão exibidas aqui. Se você quiser adicionar soluções que não estejam listadas, use o procedimento a seguir.![adicionar soluções](./media/operational-insights-setup-workspace/settings-add-sol.png)

### Para adicionar uma solução usando a Galeria de Soluções

1. Na página Visão Geral no Insights Operacionais, clique no bloco **Galeria de Soluções**. ![imagem do ícone de soluções](./media/operational-insights-setup-workspace/sol-gallery.png)
2. Na página Galeria de Soluções de Insights Operacionais, você pode aprender sobre cada solução disponível. Clique no nome da solução que deseja adicionar a Insights Operacionais.
3. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.
4. Na página Confirmação, clique em **Aceitar** para concordar com a política de privacidade e os termos de uso.
5. Um novo bloco para a solução que você adicionou aparece na visão geral da página em Insights Operacionais e você pode começar a usá-lo depois que o serviço de informações operacionais processar seus dados.

### Para remover uma solução usando a Galeria de Soluções

1. Na página Visão Geral no Insights Operacionais, clique no bloco **Galeria de Soluções**.
2. Na página da Galeria de Soluções do Insights Operacionais, sob a solução que deseja remover, clique em **Remover**.
3. Na página de confirmação, clique em **Sim** para remover a solução.

## 2 Conectar fontes de dados

Há três maneiras de conectar fontes de dados:

- Conectar computadores diretamente ao Insights Operacionais. Consulte [Conectar computadores diretamente ao Insights Operacionais](./operational-insights-direct-agent.md) para saber mais. ![anexar diretamente](./media/operational-insights-setup-workspace/attach-directly.png)
- Anexar os grupos de gerenciamento do Operations Manager. Consulte [Conectar-se ao Insights Operacionais do System Center Operations Manager](./operational-insights-connect-scom.md) para saber mais. ![anexar o Operations Manager](./media/operational-insights-setup-workspace/attach-om.png)
- Anexar uma conta de armazenamento do Azure. Consulte [Analisar dados de servidores no Microsoft Azure](./operational-insights-analyze-data-azure.md) para saber mais. ![anexar o Azure](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 Adicionar e gerenciar logs

Antes de adicionar logs, você precisa ter instalada uma solução que usará os dados de log. Em seguida, você pode adicionar novos logs para coletar eventos e escolher quais níveis de evento ou de gravidade você deseja coletar para os logs. Você pode coletar:

- Logs de eventos do Windows
- Logs do IIS
- Outros logs que você adicionou

![adicionar logs](./media/operational-insights-setup-workspace/collect-logs.png)

### Formato de arquivo de log do IIS

O único formato de log do IIS com suporte no momento é W3C. Não se preocupe – é o formato mais comum e o formato padrão no IIS 7 e no IIS 8. Assim, se você efetuar logon em formato nativo NCSA ou IIS, o Insights Operacionais não coletará esses logs de modo algum. Mesmo em formato W3C, você verá que nem todos os campos são registrados por padrão. Leia mais sobre o formato em [Selecionar campos do W3C para o log (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx).


> [AZURE.TIP]Para obter a melhor experiência de pesquisa, recomendamos selecionar todos os campos de log para cada site usando **Log** no IIS. Também é recomendável alterar a agenda de **Sobreposição de Arquivo de Log** para novos logs para **Por hora** - assim, os arquivos menores serão carregados na nuvem, economizando largura de banda.


### Para coletar logs de eventos do Windows do Operations Manager ou de agentes conectados diretamente

1. Na página **Visão geral**, clique no bloco **Configurações** e, em seguida, clique na guia **Logs**.
2. Digite o nome do log de eventos do qual deseja coletar informações. Se não tiver certeza de qual nome usar, selecione as propriedades do log de eventos do Windows no **Visualizador de Eventos**, copie o nome no campo **FullName** e cole-o na caixa **Coletar eventos dos seguintes Logs de Eventos**.
3. Clique em **+** para adicionar o log.
4. Selecione os níveis de eventos ou a gravidade que deseja coletar para o log. Não há suporte para os eventos **Êxito na Auditoria** e **Falha na Auditoria** nesta versão.
5. Repita as etapas anteriores para cada log do qual deseja coletar informações e, em seguida, clique em **Salvar**.
6. Os eventos devem aparecer no Insights Operacionais em alguns minutos e, em seguida, você poderá pesquisar os dados.

### Para coletar logs do IIS do Operations Manager ou agentes conectados diretamente

1. Na página **Visão geral**, clique no bloco **Configurações** e, em seguida, clique na guia **Logs**.
2. Na guia **Logs**, em **Logs de evento**, selecione **Coletar logs do Operations Manager**.


### Para coletar logs do IIS e/ou eventos do Windows do Diagnóstico do Azure
Isso é configurado no Portal de Gerenciamento do Azure e não no portal do Insights Operacionais. No seu espaço de trabalho, vá para a guia **Armazenamento** e habilite a coleta de log dessa conta de armazenamento.

### Após a configuração da coleta de log
Após a configuração da coleta de log, a política de coleta de log será enviada aos agentes, ou por meio dos grupos de gerenciamento para os agentes, e o serviço começará a coletar eventos.

Você pode acessar algumas partes iniciais de eventos de log coletados dos servidores monitorados exibindo a página **Uso**.

![imagem do bloco da página de uso](./media/operational-insights-setup-workspace/usage.png)


## 4 Gerenciar contas e usuários
Gerencie as contas e usuários com a guia **Contas** na página Configurações. Lá, você poderá executar as seguintes tarefas.

![guia contas](./media/operational-insights-setup-workspace/manage-users.png)

## Adicionar um usuário a um espaço de trabalho existente


Use as seguintes etapas para adicionar um usuário ou grupo a um espaço de trabalho Informações Operacionais. O usuário ou o grupo poderá exibir e atuar em todos os alertas associados a esse espaço de trabalho.

>[AZURE.NOTE]Se você quiser adicionar um usuário ou grupo a partir de sua conta organizacional do Active Directory do Azure, primeiro deverá assegurar que associou sua conta Informações Operacionais a seu domínio do Active Directory. Consulte [Adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente](#).

### Para adicionar um usuário a um espaço de trabalho existente
1. No Insights Operacionais, clique no bloco **Configurações**.
2. Clique na guia **Contas**.
3. Na seção **Gerenciar Usuários**, escolha o tipo de conta a ser adicionado: **Conta Organizacional** ou **Conta da Microsoft**.
    - Se você escolher Conta da Microsoft, digite o endereço de email do usuário associado à Conta da Microsoft.
    - Se você escolher Conta Organizacional, poderá inserir parte do nome ou do alias de email do grupo ou do usuário e uma lista de usuários e de grupos será exibida. Selecione um usuário ou um grupo.
        >[AZURE.NOTE]Para ter melhores resultados de desempenho, limite o número de grupos do Active Directory associados a uma única conta Informações Operacionais a dois — um para os administradores e outro para os usuários. Usar mais grupos pode afetar o desempenho das Informações Operacionais.
7. Escolha o tipo de usuário ou de grupo a ser adicionado: **Administrador** ou **Usuário**.  
8. Clique em **Adicionar**.

  Se você estiver adicionando uma Conta da Microsoft, um convite para ingressar no espaço de trabalho será enviado ao email fornecido. Depois de o usuário seguir as instruções no convite para ingressar no Insights Operacionais, o usuário poderá exibir os alertas e as informações da conta para essa conta do Insights Operacionais, e você poderá exibir as informações do usuário na guia **Contas** da página **Configurações**. Se você estiver adicionando uma conta organizacional, o usuário será capaz de acessar as Informações Operacionais imediatamente. ![convite](./media/operational-insights-setup-workspace/manage-users04.png)


### De quantos espaços de trabalho você precisa?
Um espaço de trabalho é visto como um recurso do Azure no Portal de Gerenciamento do Azure.

Você pode criar um novo espaço de trabalho ou vincular um espaço de trabalho existente que você pode ter aberto anteriormente para usar com o System Center Operations Manager, mas ainda não associou a uma assinatura do Azure (necessária para a cobrança). Um espaço de trabalho representa o nível no qual os dados são coletados, agregados, analisados e apresentados no portal das Informações Operacionais. Você pode optar por ter vários espaços de trabalho para separar os dados de diferentes ambientes e sistemas; cada grupo de gerenciamento do Operations Manager (e todos os seus agentes) ou VMs/agentes individuais podem ser conectados a apenas um espaço de trabalho.

Cada espaço de trabalho pode ter várias contas de usuário associadas e cada conta de usuário (conta da Microsoft ou conta Organizacional) pode ter acesso a vários espaços de trabalho de Informações Operacionais. Por padrão, a conta da Microsoft ou a conta Organizacional usada para criar o espaço de trabalho torna-se o Administrador do espaço de trabalho. O administrador pode convidar contas adicionais da Microsoft ou selecionar os usuários em seu Active Directory do Azure.

## Vincular um espaço de trabalho existente a uma assinatura do Azure

É possível criar um espaço de trabalho de [microsoft.com/oms](https://microsoft.com/oms). No entanto, existem certos limites para esses espaços de trabalho, o mais notável sendo um limite de 500 MB/dia dos carregamentos de dados se você estiver usando uma conta gratuita. Para fazer alterações nesse espaço de trabalho, você precisará **vincular seu espaço de trabalho existente a uma assinatura do Azure**.

>[AZURE.IMPORTANT]Para vincular um espaço de trabalho, sua conta do Azure já deve ter acesso ao espaço de trabalho que você deseja vincular. Em outras palavras, a conta usada para acessar o portal do Azure deve ser **igual** à conta usada para acessar seu espaço de trabalho Informações Operacionais. Se este não for o caso, consulte [Adicionar um usuário a um espaço de trabalho existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Entre no Portal de Gerenciamento do Azure.
2. À esquerda inferior do portal, clique em **+ Novo**
3. Clique em **Serviços do Aplicativo**, vá para **Informações Operacionais** e selecione.
4. Clique em **Criação Rápida**.
5. Na lista **Conta**, você deverá ver uma lista dos espaços de trabalho existentes que *ainda não* foram vinculados à sua assinatura do Azure. Selecione uma conta.

  >[AZURE.NOTE]Se você não vir o espaço de trabalho que deseja vincular aqui, significa que sua assinatura do Azure não tem acesso ao espaço de trabalho de Informações Operacionais. Você precisará conceder acesso a essa conta de dentro de seu espaço de trabalho de Informações Operacionais. Para fazer isso, consulte [Adicionar um usuário a um espaço de trabalho existente](#add-a-user-to-an-existing-workspace).

  ![vincular conta](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Preencha os campos restantes, em seguida, selecione **Criar Espaço de Trabalho**.

## Atualizar o espaço de trabalho para um plano de dados pago

Há três tipos de plano de dados de espaço de trabalho para o Insights Operacionais: **Gratuito**, **Standard** e **Premium**. Se você estiver usando um plano *gratuito*, poderá ter atingido o limite de dados de 500 MB. Você precisará atualizar seu espaço de trabalho para um '**plano pré-pago**' para coletar dados além desse limite. A qualquer momento, você pode converter seu tipo de plano. Para obter mais informações sobre os preços das Informações Operacionais, consulte [Detalhes dos Preços](http://azure.microsoft.com/pricing/operational-insights/)

>[AZURE.IMPORTANT]Os planos do espaço de trabalho podem ser alterados apenas se eles estiverem *vinculados* a uma assinatura do Azure. Se você criou seu espaço de trabalho no Azure ou se *já* vinculou seu espaço de trabalho, poderá ignorar essa mensagem. Se você criou seu espaço de trabalho a partir do [opinsights.azure.com](http://opinsights.azure.com), precisará seguir as etapas em [Vincular um espaço de trabalho existente a uma assinatura do Azure](#link-an-existing-workspace-to-an-Azure-subscription).

### Alterar o tipo de plano

No Portal de Gerenciamento do Azure, navegue até o espaço de trabalho Informações Operacionais para o qual você gostaria de atualizar:

![Escala](./media/operational-insights-setup-workspace/find-workspace.png)

Selecione esse espaço de trabalho e selecione **ESCALA** nas guias na parte superior da tela

![selecionar escala](./media/operational-insights-setup-workspace/select-scale.png)

Por fim, escolha o plano para o qual você gostaria de atualizar e clique em **SALVAR**. Você verá as alterações refletidas no portal e, agora, será capaz de coletar dados além do limite de dados "gratuito".

![selecionar plano](./media/operational-insights-setup-workspace/plan-select.png)

## Adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente

Você pode associar seu espaço de trabalho Informações Operacionais a um domínio do Active Directory do Azure. Isso permite que você adicione usuários do Active Directory diretamente ao espaço de trabalho Informações Operacionais sem a necessidade de uma conta da Microsoft separada.

### Para adicionar uma Organização do Active Directory do Azure a um espaço de trabalho existente

1. Na página Configurações no Insights Operacionais, clique em **Contas** e clique em **Adicionar Organização**. ![convite](./media/operational-insights-setup-workspace/add-org.png)
2. Revise as informações sobre as contas organizacionais, em seguida, clique em **Próximo**.
3. Insira as informações de identidade para o administrador do domínio do Active Directory do Azure, em seguida, clique em **Entrar**.
4. Clique em **Conceder acesso** para habilitar as Informações Operacionais para usarem as informações de identidade em seu domínio do Active Directory. ![vinculado](./media/operational-insights-setup-workspace/ad-existing01.png)


## Editar um tipo de usuário existente

Você pode alterar a função da conta de um usuário associado à sua conta Informações Operacionais. Você tem as seguintes opções de função:

 - *Administrador*: pode gerenciar os usuários, exibir e agir em todos os alertas, adicionar e remover servidores

 - *Usuário*: pode exibir e agir em todos os alertas, adicionar e remover servidores

### Para editar uma conta
1. Na página **Configurações**, na guia **Contas** no Insights Operacionais, selecione a função do usuário que você deseja alterar.
2. Clique em **OK**.

## Remover um usuário de um espaço de trabalho Informações Operacionais

Use as seguintes etapas para remover um usuário de um espaço de trabalho Informações Operacionais. Observe que isso não fecha o espaço de trabalho do usuário. Em vez disso, remove a associação entre o usuário e o espaço de trabalho. Se um usuário estiver associado a vários espaços de trabalho, esse usuário ainda poderá entrar nas Informações Operacionais.

### Para remover um usuário de um espaço de trabalho

1. Na página **Configurações**, na guia **Contas** do Insights Operacionais, clique em Remover ao lado do nome do usuário que você quer remover.
2. Clique em **OK** para confirmar que você deseja remover o usuário.

## Fechar seu espaço de trabalho Informações Operacionais

Quando você fecha um espaço de trabalho Informações Operacionais, todos os dados relacionados ao espaço de trabalho são excluídos do serviço Informações Operacionais apenas 30 dias depois de fechar o espaço de trabalho.

Se você for um administrador e houver vários usuários associados ao espaço de trabalho, a associação entre os usuários e o espaço de trabalho será interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando as Informações Operacionais com esses outros espaços. No entanto, se eles não estiverem associados a outros espaços de trabalho, então, precisarão criar um novo espaço de trabalho para usar as Informações Operacionais.

### Para fechar um espaço de trabalho Informações Operacionais

1. Na página **Configurações**, na guia **Contas** do Insights Operacionais, clique em **Fechar Espaço de Trabalho**.

2. Selecione um dos motivos para fechar seu espaço de trabalho ou insira um motivo diferente na caixa de texto.

3. Clique em **Fechar espaço de trabalho**.

## Recursos adicionais
- [Requisitos de formato de Log do IIS no Insights Operacionais do Azure](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Veja que outras fontes de dados e tipos de logs a comunidade está nos pedindo para implementar no [Fórum de comentários](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

<!---HONumber=06-->