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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Gerenciando recomendações de segurança na Central de Segurança do Azure

Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Esta é uma introdução ao serviço usando um exemplo de implantação. Ela não é um guia passo a passo.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## O que são recomendações de segurança?
A Central de Segurança do Azure analisa periodicamente o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário.

## Implementando recomendações de segurança

### Recomendações de configuração

Em [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md), você aprenderá a:

- Configurar políticas de segurança
- Habilitar coleta de dados
- Escolher quais recomendações ver como parte da política de segurança

As recomendações de política atuais giram em torno de atualizações do sistema, regras de linha de base, antimalware, [ACLs para pontos de extremidade](virtual-machines-set-up-endpoints.md), [Grupos de Segurança de Rede](virtual-networks-nsg.md) em sub-redes e em interfaces de rede, auditoria de Banco de Dados SQL, Transparent Data Encryption do Banco de Dados SQL e firewall do aplicativo Web. [Configurando políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### Recomendações de monitoramento

Depois de definir uma política de segurança, a Central de Segurança do Azure analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **Recomendações** na folha **Central de Segurança** permite saber a quantidade total de recomendações identificadas pela Central de Segurança do Azure.

![][2]

Para ver os detalhes de cada recomendação:

1. Clique no **bloco Recomendações** na folha **Central de Segurança**. A folha **Recomendações** será aberta.
2. Você pode filtrar as recomendações apresentadas por estado e por severidade. Clique em **Filtro** na folha **Recomendações**. A folha Filtro se abre para você selecionar valores de gravidade e estado que deseja ver. ![][3]

3. Se você determinar que uma recomendação não se aplica, poderá ignorar a recomendação e removê-la da exibição. Há duas maneiras de ignorar uma recomendação. Clique com o botão direito do mouse em um item e selecione **Ignorar** ou passe o mouse sobre um item, clique nos três pontos que aparecem à direita e selecione **Ignorar**. Você pode exibir as recomendações ignoradas clicando em **Filtro** e selecionando **Ignoradas**. ![][4]

As recomendações são mostradas em um formato de tabela, em que cada linha representa uma recomendação específica. As colunas da tabela são:

- **DESCRIÇÃO**: uma explicação da recomendação e o que precisa ser feito para resolvê-la
- **RECURSO**: a qual(is) recurso(s) a recomendação se aplica
- **ESTADO**: o estado atual da recomendação:
  - **Aberta**: ainda não foi resolvida
  - **Em andamento**: a recomendação atualmente está sendo aplicada aos recursos; não é exigido que você realize nenhuma ação
  - **Resolvida**: a recomendação já foi concluída (quando o estado é resolvido, a cor da linha fica esmaecida)
- **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
  - **Alta**: existe uma vulnerabilidade em um recurso significativo (aplicativo, VM, grupo de segurança de rede) e ela requer atenção
  - **Média**: são necessárias etapas adicionais ou não críticas para concluir um processo ou eliminar a vulnerabilidade
  - **Baixa**: uma vulnerabilidade que deve ser abordada, mas não exige atenção imediata. (Por padrão, as recomendações de baixa severidade não são apresentadas, mas você pode filtrar por recomendações de baixa severidade se quiser exibi-las).

Use a tabela a seguir como referência para entender as recomendações disponíveis e o que cada uma delas fará se você aplicá-las:

| Recomendações | Descrição |
|----- |-----|
| Habilitar Coleta de Dados para máquinas virtuais/assinaturas | Recomenda que você habilite a coleta de dados na política de segurança para as VMs selecionadas. |
| Resolver regras de linha de base incompatíveis | Recomenda que você alinhe as configurações do sistema operacional com as linhas de base recomendadas, por exemplo, não permitir o armazenamento de senhas. |
| Aplicar atualizações do sistema | Recomenda que você implante as atualizações de sistema ausentes nas máquinas virtuais (somente VMs do Windows). |
| Configurar ACLs para pontos de extremidade | Recomenda que você configure uma lista de controle de acesso para restringir o acesso de entrada nas máquinas virtuais (somente VMs clássicas). |
| Adicione um firewall do aplicativo Web | Recomenda que você implante um WAF (Firewall do Aplicativo Web) para pontos de extremidade da Web (somente no Gerenciador de Recursos de VMs). |
| Finalizar a configuração de firewall do aplicativo Web | Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Essa recomendação concluirá as alterações de configuração necessárias. |
| Habilitar Antimalware | Recomenda que você provisione antimalware para máquinas virtuais (somente VMs do Windows). |
| Habilitar Grupos de Segurança de Rede em interfaces de rede/sub-redes | Recomenda que você habilite NSGs (Grupos de Segurança de Rede) em sub-redes e em interfaces de rede (somente no Gerenciador de Recursos de VMs). |
| Restringir o acesso por meio de pontos de extremidade externos públicos | Recomenda que você configure regras de tráfego de entrada para NSGs. |
| Habilitar a auditoria do servidor SQL | Recomenda que você habilite auditoria para servidores Azure SQL (somente serviço Azure SQL, não incluindo SQL em execução em máquinas virtuais). |
| Habilitar auditoria para Banco de Dados SQL | Recomenda que você habilite auditoria para Bancos de Dados SQL do Azure (somente serviço Azure SQL, não incluindo SQL em execução em máquinas virtuais). |
| Habilitar Transparent Data Encryption em bancos de dados SQL | Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL). |
| Implantar o Agente de VM | Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |

### Aplicando recomendações
Depois de examinar todas as recomendações, você poderá decidir qual delas aplicar primeiro. É recomendável usar a classificação de severidade como o parâmetro principal para avaliar quais recomendações devem ser aplicadas primeiro. Usando as recomendações de antimalware, vamos examinar um exemplo de como aplicar uma recomendação:

1. Na folha **Recomendações**, selecione **Habilitar Antimalware**. ![][5]

2. Na folha **Instalar Antimalware**, selecione da lista de máquinas virtuais sem antimalware habilitado e clique em **Instalar Antimalware**.
3. A folha **Novo recurso** será aberta para que você possa selecionar a solução antimalware que deseja usar. Selecione **Microsoft Antimalware**.
4. Informações adicionais sobre a solução antimalware são exibidas. Selecione **Criar**.
5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. ![][6]

O [Antimalware da Microsoft](azure-security-antimalware.md) agora está ativo na máquina virtual selecionada.

### Implantando soluções de parceiro integradas

A recomendação pode ser implantar uma solução integrada de segurança de um parceiro da Microsoft. Vamos examinar um exemplo de como fazer isso:

1. Volte para a folha **Recomendações**.
2.	Selecione a recomendação **Proteger o aplicativo Web usando o firewall do aplicativo Web**. Isso abre a folha **Aplicativos Web Desprotegidos**. ![][7]
3. Selecione um aplicativo Web; a folha **Adicionar um Firewall do Aplicativo Web** será aberta.
4. Selecione **Firewall do Aplicativo Web Barracuda**. Uma folha se abre fornecendo informações sobre o **Firewall do Aplicativo Web Barracuda**.
5. Clique em **Criar** na folha de informações. A folha **Novo Firewall do Aplicativo Web** é aberta e você pode executar as etapas de **Configuração da VM** e fornecer **Informações do WAF**.
6. Selecione **Configuração da VM**. Na folha **Configuração da VM**, você deve inserir as informações necessárias para criar a máquina virtual que executará o WAF. ![][8]
7. Volte para a folha **Novo Firewall do Aplicativo Web** e selecione **Informações do WAF**. Na folha **Informações do WAF**, você configura o WAF em si. A Etapa 6 permite configurar a máquina virtual na qual o WAF será executado, e a Etapa 7 permite provisionar o WAF em si.

8. Volte para a folha **Recomendações**. Uma nova entrada foi gerada depois que você criou o WAF; **finalize a configuração de firewall do aplicativo Web**. Isso permite que você saiba do que precisa para concluir o processo de conectar o WAF dentro da Rede Virtual do Azure a fim de que ele possa proteger o aplicativo. ![][9]

9. Selecione **Finalizar a configuração de firewall do aplicativo Web** para abrir uma nova folha. Você pode ver que há um aplicativo Web que precisa ter o tráfego redirecionado.
10. Selecione o aplicativo Web e uma folha será aberta fornecendo etapas para concluir a configuração de firewall do aplicativo Web. Conclua as etapas, clique em **Restringir tráfego** e a Central de Segurança do Azure fará a conexão para você. ![][10]

Os logs daquele WAF agora estão totalmente integrados. A Central de Segurança do Azure pode iniciar a coleta e a análise dos logs automaticamente para revelar alertas de segurança importantes para você.

## Próximas etapas
Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

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

<!---HONumber=AcomDC_1217_2015-->