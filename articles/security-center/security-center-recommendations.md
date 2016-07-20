<properties
   pageTitle="Gerenciando recomendações de segurança na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
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

As recomendações de política atuais giram em torno de atualizações do sistema, regras de linha de base, programas antimalware, [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) em sub-redes e em interfaces de rede, auditoria do banco de dados SQL, Transparent Data Encryption do banco de dados SQL e firewalls do aplicativo Web. [Configurando políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

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
- **SEVERIDADE**: descreve a gravidade dessa recomendação específica:
    - **Alta**: existe uma vulnerabilidade em um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e ela requer atenção.
    - **Média**: existe uma vulnerabilidade, e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
    - **Baixa**: existe uma vulnerabilidade que deve ser abordada, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.)

Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis e o que cada uma delas fará se você as aplicar.

> [AZURE.NOTE] Você deve compreender os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.

|Recomendações|Descrição|
|-----|-----|
|[Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md)|Recomenda que você ative a coleta de dados na política de segurança para cada uma de suas assinaturas e todas as VMs (máquinas virtuais) em suas assinaturas.|
|Resolver regras de linha de base incompatíveis|Recomenda que você alinhe as configurações do sistema operacional com as linhas de base recomendadas, por exemplo, não permitir o armazenamento de senhas.|
|Aplicar atualizações do sistema|Recomenda que você implante a atualizações críticas e de segurança do sistema ausentes para VMs.|
|[Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md)|Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web. Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes. Dispositivos WAF (criados usando o modelo de implantação do Gerenciador de Recursos) precisam ser implantados em uma rede virtual separada. Dispositivos WAF (criados usando o modelo de implantação clássico) são restritos ao uso de um grupo de segurança de rede. No futuro, esse suporte será estendido para uma implantação totalmente personalizada de um dispositivo WAF (clássica).|
|Finalizar a configuração de firewall do aplicativo Web|Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Se essa recomendação for seguida, serão concluídas as alterações de configuração necessárias.|
|[Habilitar Antimalware](security-center-enable-antimalware.md)|Recomenda que você provisione programas antimalware para máquinas virtuais (somente VMs do Windows).|
|Habilitar Grupos de Segurança de Rede em interfaces de rede/sub-redes|Recomenda que você habilite NSGs (grupos de segurança de rede) em sub-redes e em interfaces de rede.|
|Restringir o acesso por meio de pontos de extremidade externos públicos|Recomenda que você configure regras de tráfego de entrada para NSGs.|
|[Habilitar a auditoria do servidor SQL](security-center-enable-auditing-on-sql-servers.md)|Recomenda que você habilite auditoria para servidores Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|[Habilitar auditoria para Banco de Dados SQL](security-center-enable-auditing-on-sql-databases.md)|Recomenda que você habilite auditoria para bancos de dados do Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais).|
|[Habilitar Transparent Data Encryption em bancos de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL).|
|Implantar o Agente de VM|Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e programas antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.|
| [Aplicar a criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda de que você criptografe os discos de VM usando o Azure Disk Encryption (VMs do Windows e do Linux). A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.|
|Fornecer detalhes de contato de segurança | Recomenda que você forneça informações de contato de segurança para cada uma das suas assinaturas. Informações de contato são um número de telefone e um endereço de email. As informações serão usadas para contatá-lo se nossa equipe de segurança acreditar que os recursos estão comprometidos. |
| Atualizar a versão do sistema operacional | Recomenda que você atualize a versão do SO (sistema operacional) de seu Serviço de Nuvem para a versão mais recente disponível para a família do SO. Para saber mais sobre os Serviços de Nuvem, confira a [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |

Você pode filtrar e ignorar as recomendações.

1. Clique em **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e de estado que deseja ver.

    ![][3]

2. Se você determinar que uma recomendação não se aplica, poderá ignorar a recomendação e removê-la da exibição. Há duas maneiras de ignorar uma recomendação. Uma das maneiras consiste em clicar com o botão direito do mouse em um item e selecionar **Ignorar**. A outra maneira é passar o mouse sobre um item, clicar nos três pontos que aparecem à direita e selecionar **Ignorar**. Você pode exibir as recomendações ignoradas ao clicar em **Filtro** e selecionar **Ignoradas**.

    ![][4]

### Aplicar recomendações
Depois de examinar todas as recomendações, decida qual delas aplicar primeiro. É recomendável usar a classificação de gravidade como o parâmetro principal para avaliar quais recomendações devem ser aplicadas primeiro. Usando a recomendação **Habilitar Antimalware**, vamos examinar um exemplo de como aplicar uma recomendação.

1. Na folha **Recomendações**, selecione **Habilitar Antimalware**. ![][5]

2. Na folha **Instalar Antimalware**, selecione da lista de máquinas virtuais sem antimalware habilitado e clique em **Instalar Antimalware**.
3. A folha **Novo recurso** será aberta para que você possa selecionar a solução antimalware que deseja usar. Selecione **Microsoft Antimalware**.
4. Informações adicionais sobre a solução antimalware são exibidas. Selecione **Criar**.
5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. ![][6]

O [Antimalware da Microsoft](../security/azure-security-antimalware.md) agora está ativo na máquina virtual selecionada.


## Próximas etapas
Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciamento e resposta a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – saiba como gerenciar e responder a alertas de segurança.
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – confira postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png

<!---HONumber=AcomDC_0706_2016-->