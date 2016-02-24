<properties
   pageTitle="Gerenciando recomendações de segurança na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Gerenciando recomendações de segurança na Central de Segurança do Azure

Este documento mostra como usar as recomendações na Central de Segurança do Azure para ajudar a proteger os recursos do Azure.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## O que são recomendações de segurança?
A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários.

## Implementando recomendações de segurança

### Definir recomendações

Em [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md), você aprenderá a:

- Configurar políticas de segurança.
- Habilitar a coleta de dados.
- Escolher quais recomendações ver como parte da política de segurança.

As recomendações de política atuais giram em torno de atualizações do sistema, regras de linha de base, programas antimalware, [ACLs para pontos de extremidade](../virtual-machines/virtual-machines-set-up-endpoints.md), [grupos de segurança de rede](../virtual-networks/virtual-networks-nsg.md) em sub-redes e em interfaces de rede, auditoria de Banco de Dados SQL, transparent data encryption do banco de dados SQL e firewalls de aplicativo Web. [Configurando políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### Monitorar as recomendações
Depois de definir uma política de segurança, a Central de Segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **Recomendações** na folha **Central de Segurança** permite saber o número total de recomendações identificadas pela Central de Segurança.

![][2]

Para ver os detalhes de cada recomendação:

1. Clique no **bloco Recomendações** na folha **Central de Segurança**. A folha **Recomendações** será aberta.

As recomendações são mostradas em um formato de tabela, em que cada linha representa uma recomendação específica. As colunas da tabela são:

- **DESCRIÇÃO**: explica a recomendação e o que precisa ser feito para resolvê-la.
- **RECURSO**: lista os recursos aos quais essa recomendação se aplica.
- **ESTADO**: descreve o estado atual da recomendação:
    - **Aberta**: a recomendação ainda não foi resolvida.
    - **Em Andamento**: a recomendação está sendo aplicada atualmente aos recursos, e não é exigido que você realize nenhuma ação.
    - **Resolvida**: a recomendação já foi concluída (nesse caso, a linha ficará esmaecida).
- **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
    - **Alta**: existe uma vulnerabilidade em um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e ela requer atenção.
    - **Média**: existe uma vulnerabilidade, e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
    - **Baixa**: existe uma vulnerabilidade que deve ser abordada, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.)

Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis e o que cada uma delas fará se você aplicá-las:

|Recomendações|Descrição|
|-----|-----|
|Habilitar Coleta de Dados para máquinas virtuais/assinaturas|Recomenda que você habilite a coleta de dados na política de segurança para cada uma de suas assinaturas ou para as VMs selecionadas.|
|Resolver regras de linha de base incompatíveis|Recomenda que você alinhe as configurações do sistema operacional com as linhas de base recomendadas, por exemplo, não permitir o armazenamento de senhas.|
|Aplicar atualizações do sistema|Recomenda que você implante a atualizações críticas e de segurança do sistema ausentes para VMs (somente VMs do Windows).|
|Configurar ACLs para pontos de extremidade|Recomenda que você configure listas de controle de acesso para restringir o acesso de entrada nas máquinas virtuais (somente VMs clássicas).|
|Adicione um firewall do aplicativo Web|Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web (somente no Gerenciador de Recursos de VMs).|
|Finalizar a configuração de firewall do aplicativo Web|Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Se essa recomendação for seguida, serão concluídas as alterações de configuração necessárias.|
|Habilitar Antimalware|Recomenda que você provisione programas antimalware para máquinas virtuais (somente VMs do Windows).|
|Habilitar Grupos de Segurança de Rede em interfaces de rede/sub-redes|Recomenda que você habilite NSGs (grupos de segurança de rede) em sub-redes e em interfaces de rede (somente VMs do Gerenciador de Recursos).|
|Restringir o acesso por meio de pontos de extremidade externos públicos|Recomenda que você configure regras de tráfego de entrada para NSGs.|
|Habilitar a auditoria do servidor SQL|Recomenda que você habilite auditoria para servidores Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|Habilitar auditoria para Banco de Dados SQL|Recomenda que você habilite auditoria para bancos de dados do Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|Habilitar Transparent Data Encryption em bancos de dados SQL|Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL).|
|Implantar o Agente de VM|Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e programas antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.|
| [Aplicar a criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda de que você criptografe os discos de VM usando o Azure Disk Encryption (VMs do Windows e do Linux). A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.|

Você pode filtrar e ignorar as recomendações.

1. Clique em **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e estado que deseja ver.

    ![][3]

2. Se você determinar que uma recomendação não se aplica, poderá ignorar a recomendação e removê-la da exibição. Há duas maneiras de ignorar uma recomendação. Uma das maneiras consiste em clicar com o botão direito do mouse em um item e selecionar **Ignorar**. A outra maneira é passar o mouse sobre um item, clicar nos três pontos que aparecem à direita e selecionar **Ignorar**. Você pode exibir as recomendações ignoradas clicando em **Filtro** e selecionando **Ignoradas**.

    ![][4]

### Aplicar recomendações
Depois de examinar todas as recomendações, decida qual delas aplicar primeiro. É recomendável usar a classificação de gravidade como o parâmetro principal para avaliar quais recomendações devem ser aplicadas primeiro. Usando a recomendação **Habilitar Antimalware**, vamos examinar um exemplo de como aplicar uma recomendação.

1. Na folha **Recomendações**, selecione **Habilitar Antimalware**. ![][5]

2. Na folha **Instalar Antimalware**, selecione da lista de máquinas virtuais sem antimalware habilitado e clique em **Instalar Antimalware**.
3. A folha **Novo recurso** será aberta para que você possa selecionar a solução antimalware que deseja usar. Selecione **Microsoft Antimalware**.
4. Informações adicionais sobre a solução antimalware são exibidas. Selecione **Criar**.
5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. ![][6]

O [Antimalware da Microsoft](../azure-security/azure-security-antimalware.md) agora está ativo na máquina virtual selecionada.

### Implantar soluções de parceiro integradas

Outra recomendação que você pode receber é implantar uma solução integrada de segurança de um parceiro da Microsoft. Vamos examinar um exemplo de como fazer isso.

1. Volte para a folha **Recomendações**.
2.	Selecione a recomendação **Proteger o aplicativo Web usando o firewall do aplicativo Web**. Isso abre a folha **Aplicativos Web Desprotegidos**. ![][7]
3. Selecione um aplicativo Web; a folha **Adicionar um Firewall do Aplicativo Web** será aberta.
4. Selecione **Firewall do Aplicativo Web Barracuda**. Uma folha se abre fornecendo informações sobre o **Firewall do Aplicativo Web Barracuda**.
5. Clique em **Criar** na folha de informações. A folha **Novo Firewall do Aplicativo Web** é aberta e você pode executar as etapas de **Configuração da VM** e fornecer **Informações do WAF**.
6. Selecione **Configuração da VM**. Na folha **Configuração da VM**, você deve inserir as informações necessárias para criar a máquina virtual que executará o WAF. ![][8]
7. Volte para a folha **Novo Firewall do Aplicativo Web** e selecione **Informações do WAF**. Na folha **Informações do WAF**, você configura o WAF em si. A Etapa 6 permite configurar a máquina virtual na qual o WAF será executado, e a Etapa 7 permite provisionar o WAF em si.

8. Volte para a folha **Recomendações**. Uma nova entrada foi gerada depois que você criou o WAF, chamada **Finalizar a configuração de firewall do aplicativo Web**. Essa entrada informa o que é necessário para concluir o processo de conectar o WAF dentro da Rede Virtual do Azure para que ele possa proteger o aplicativo. ![][9]

9. Selecione **Finalizar a configuração de firewall do aplicativo Web**. Uma nova lâmina é aberta. Você pode ver que há um aplicativo Web que precisa ter seu tráfego redirecionado.
10. Selecione o aplicativo Web. Uma folha será aberta com etapas para concluir a configuração de firewall do aplicativo Web. Conclua as etapas e, em seguida, clique em **Restringir o tráfego**. A Central de Segurança realizará as etapas para você. ![][10]

Os logs daquele WAF agora estão totalmente integrados. A Central de Segurança pode iniciar a coleta e a análise dos logs automaticamente para revelar alertas de segurança importantes para você.

## Próximas etapas
Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_0211_2016-->