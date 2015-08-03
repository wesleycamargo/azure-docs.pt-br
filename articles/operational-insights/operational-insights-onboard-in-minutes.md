<properties
    pageTitle="Integrado aos Insights Operacionais em minutos"
    description="Você pode colocar tudo em funcionamento com os Insights Operacionais em minutos."
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
    ms.date="07/09/2015"
    ms.author="banders"/>

# Integrado aos Insights Operacionais em minutos


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode colocar os Insights Operacionais em funcionamento em minutos. Você tem duas opções ao escolher como criar um espaço de trabalho dos Insights Operacionais, que é semelhante a uma conta:

- Microsoft Operations Management Suite
- Uma assinatura do Microsoft Azure

Você pode criar um espaço de trabalho do Microsoft Operations Management Suite usando o site da Web do Microsoft Operations Management Suite. Ou, você pode usar uma assinatura do Microsoft Azure para criar um espaço de trabalho de Insights Operacionais. Atualmente, os dois espaços de trabalho são funcionalmente equivalentes. A única diferença entre os dois é o nome. Se você usar uma assinatura do Azure, você também pode usar essa assinatura para acessar outros serviços do Azure. Independentemente do método que você usa para criar o espaço de trabalho, você criará o espaço de trabalho com uma conta da Microsoft ou uma conta organizacional.

## Inscreva-se em 3 etapas usando o Operations Management Suite

1. Vá para o site da web do [Microsoft Operations Management Suite](http://microsoft.com/oms) e clique em **Experimente gratuitamente**. Entrar com sua conta da Microsoft, como Outlook.com, ou com uma conta organizacional, fornecida por sua empresa ou instituição educacional para usar com o Office 365 ou outros serviços da Microsoft.
2. Forneça um nome exclusivo do espaço de trabalho. Um espaço de trabalho é um contêiner lógico em que os dados de gerenciamento estão armazenados. Ele fornece uma forma de particionar dados entre diferentes equipes em sua organização, pois os dados são exclusivos ao seu espaço de trabalho. Especifique um endereço de email e a região onde você deseja ter os dados armazenados.![criar espaço de trabalho e vincular a assinatura](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Em seguida, você pode criar uma nova assinatura do Azure ou um link para uma assinatura do Azure existente. Se você quiser continuar usando a versão de avaliação gratuita, clique em **Agora não**.

Você está pronto para começar a usar o portal do Operations Management Suite.

Você pode aprender mais sobre a configuração de seu espaço de trabalho e vínculo de contas do Azure existentes em espaços de trabalho criados com o Operations Management Suite em [Definir seu espaço de trabalho e gerenciar configurações](operational-insights-setup-workspace.md).

## Inscreva-se rapidamente usando o Microsoft Azure

1. Vá para o [portal de gerenciamento do Azure](https://manage.windowsazure.com), entre e, em seguida, na lista de serviços, selecione **Insights Operacionais**.![Portal do Azure](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Clique em **Criar um espaço de trabalho**, em seguida, clique em **Criação rápida**, em seguida, em uma conta, digite um nome de espaço de trabalho, escolha uma camada e, em seguida, escolha um local para armazenar os dados de espaço de trabalho. Se você tiver várias assinaturas, você pode escolher qual delas usar e, em seguida, clicar em **Criar espaço de trabalho**.![Portal do Azure](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Selecione o espaço de trabalho que você criou e, em seguida, clique em **Visite sua conta dos Insights Operacionais** para abrir o site da Web do Operations Management Suite.![visite a conta](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. No site da Web do Operations Management Suite, insira seu endereço de email e clique em **Confirmar e continuar**. Você receberá um email de confirmação. Abra o email e dentro dele, clique em **Confirmar agora**.
5. O site da Web do Operations Management Suite exibe a página Visão geral. Clique em **Introdução** para continuar.

Você está pronto para começar a usar o portal do Operations Management Suite.

Você pode aprender mais sobre a configuração de seu espaço de trabalho e vínculo de espaços de trabalho criados com o Operations Management Suite nas assinaturas do Azure em [Definir seu espaço de trabalho e gerenciar configurações](operational-insights-setup-workspace.md).

## Introdução ao portal do Operations Management Suite
Para escolher as soluções e conectar os servidores que você deseja gerenciar, clique no bloco **Introdução** e siga as etapas abaixo:

![criar espaço de trabalho e vincular a assinatura](./media/operational-insights-onboard-in-minutes/get-started.png)

- Selecione as soluções que você gostaria de usar e, em seguida, clique em **Adicionar soluções selecionadas**.![soluções](./media/operational-insights-onboard-in-minutes/solutions.png)
- Escolha como você deseja se conectar ao seu ambiente de servidor para coleta de dados:
    - Conecte-se a qualquer cliente ou Windows Server diretamente ao instalar um agente.
    - Use o System Center Operations Manager para anexar seus grupos de gerenciamento ou sua implantação completa do Operations Manager.
    - Use uma conta de armazenamento do Azure configurada com a extensão VM de diagnóstico do Windows ou do Linux Azure.
- Configure pelo menos um log para preencher os dados. Você pode selecionar os logs do IIS e/ou adicionar logs de eventos e selecionar **Salvar** na parte inferior da página. Para logs de eventos, você pode especificar o tipo de mensagens, incluindo o erro, aviso e informações para monitorar.![soluções](./media/operational-insights-onboard-in-minutes/logs.png)

## Opcionalmente, conecte os servidores diretamente ao Operations Management Suite instalando um agente
1. Na exibição de Introdução, clique no nó **Conectar-se a uma fonte de dados** e clique em **Baixar o agente do Windows**. Você só pode instalar o agente no Windows Server 2008 SP 1 ou posterior, ou no Windows 7 SP1 ou superior. Os servidores precisam de uma arquitetura x64.
2. Instale o agente em um ou mais servidores. Você pode instalar agentes um a um, ou usar um método mais automatizado com um [script personalizado](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional), ou você pode usar uma solução de distribuição de software existente que você tiver.
3. Depois que você concorda com o contrato de licença e escolhe a pasta de instalação, selecione **Conectar o agente aos Insights Operacionais do Microsoft Azure**.![instalação do agente](./media/operational-insights-onboard-in-minutes/agent.png)
4. Na próxima página, serão solicitadas a ID do espaço de trabalho e a chave do espaço de trabalho. A ID do espaço de trabalho e a chave são exibidas na tela em que você baixou o arquivo do agente.![conectar servidores](./media/operational-insights-onboard-in-minutes/key.png)
5. Durante a instalação, você pode clicar em **Avançado** para instalar opcionalmente o servidor proxy e fornecer informações de autenticação. Clique no botão **Próximo** para retornar à tela de informações do espaço de trabalho.
6. Clique em **Próximo** para validar sua ID do espaço de trabalho e a chave. Se forem encontrados erros, você pode clicar em **Voltar** para fazer as correções. Quando a ID do espaço de trabalho e a chave forem validadas, clique em **Instalar** para concluir a instalação do agente.
7. Faça logon no portal do Operations Management Suite e, em seguida, clique no bloco **Configurações** na página Visão geral. Um ícone de marca de seleção verde será exibido quando os agentes se comunicarem com o serviço Operations Management Suite. Inicialmente, isso leva cerca de 5 a 10 minutos.

> [AZURE.NOTE]As soluções de avaliação de configuração e gerenciamento de capacidade não têm suporte atualmente por servidores conectados diretamente ao Operations Management Suite.

Você também pode conectar o agente ao System Center Operations Manager 2012 SP1 e posterior. Para fazer isso, você pode selecionar **Conectar o agente ao System Center Operations Manager**. Quando você escolher essa opção, você envia dados para o serviço sem a necessidade de hardware adicional ou de carregar seus grupos de gerenciamento.

Você pode ler mais sobre como conectar agentes diretamente ao Operations Management Suite em [Conectar computadores diretamente aos Insights operacionais](operational-insights-direct-agent.md).

## Opcionalmente, conecte os servidores usando o System Center Operations Manager

1. No console do Operations Manager, clique em **Administração**.
2. Expanda o nó **Insights Operacionais** e clique em **Conexão de Insights Operacionais**.
3. Clique NO LINK **Registrar-se em Insights Operacionais** na direção do canto superior direito e siga as instruções mostradas.
4. Depois de concluir o assistente de registro, clique no link **Adicionar um computador/grupo**.
5. Na caixa de diálogo **Pesquisa de computador**, você pode pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione os computadores ou grupos para carregar no Insights Operacionais, clique em **Adicionar** e em **OK**. Você pode verificar se o serviço de Insights Operacionais está recebendo dados ao acessar o bloco **Uso** no portal do Operations Management Suite. Os dados devem ser exibidos em cerca de 5 a 10 minutos.

Você pode ler mais sobre a conexão do Operations Manager ao Operations Management Suite em [Conectar-se ao Operational Insights do System Center Operations Manager](operational-insights-connect-scom.md).

## Opcionalmente, analise os dados de serviços de nuvem no Microsoft Azure

Com o Operations Management Suite, você pode rapidamente pesquisar eventos e logs do IIS para serviços de nuvem e máquinas virtuais, permitindo o diagnóstico nos Serviços de Nuvem do Azure. Você também pode receber informações adicionais para as máquinas virtuais do Azure instalando o Microsoft Monitoring Agent. Você pode ler mais sobre como configurar seu ambiente do Azure para usar o Operations Management Suite em [Analisar dados de servidores no Microsoft Azure](operational-insights-analyze-data-azure.md).


## Próximas etapas
- Comece a usar as [soluções](operational-insights-solutions.md)
- Familiarize-se com a [pesquisa](operational-insights-search.md)
- Use [painéis](operational-insights-use-dashboards.md) para salvar e exibir suas pesquisas personalizadas

<!---HONumber=July15_HO4-->