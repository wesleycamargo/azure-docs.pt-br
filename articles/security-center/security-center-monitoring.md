<properties
   pageTitle="Monitoramento de integridade de segurança na Central de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a se familiarizar com o monitoramento de recursos na Central de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="yurid"/>

#Monitoramento de integridade de segurança na Central de segurança do Azure
Este documento o ajuda a usar recursos de monitoramento na Central de segurança do Azure para monitorar a conformidade com as políticas.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
A Central de Segurança o ajuda a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança

##O que é o monitoramento de integridade de segurança?
Costumamos pensar em monitoramento como assistir e esperar até que um evento ocorra para poder reagir à situação. Monitoramento de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou práticas recomendadas.

##Monitoramento de integridade da segurança
Depois de habilitar [políticas de segurança](security-center-policies.md) para os recursos de uma assinatura, a Central de Segurança analisará a segurança de seus recursos para identificar possíveis vulnerabilidades. As informações sobre a configuração de rede estão disponíveis imediatamente, mas pode levar uma hora ou mais para obter informações disponíveis sobre a configuração da máquina virtual, como o status de atualização de segurança e a configuração do sistema operacional. Você pode exibir o estado de segurança de seus recursos com os problemas na folha **Integridade de Segurança do Recurso**. Você também pode exibir uma lista desses problemas na folha **Recomendações**.

Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).

No bloco **Integridade da segurança de recursos**, você pode monitorar o estado de segurança de seus recursos. No exemplo a seguir, você pode ver vários problemas com gravidade média e alta que exigem atenção. As políticas de segurança que são habilitadas terão impacto sobre os tipos de controles que são monitorados.

![Integridade dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new2.png)

Se a Central de segurança identificar uma vulnerabilidade que precisa ser resolvida, como uma VM com ausência de atualizações de segurança ou uma sub-rede sem um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md), ela será listada aqui.

###Monitorar máquinas virtuais
Quando você clicar em **Máquinas virtuais** no bloco **Integridade de segurança de recursos**, a folha **Máquinas virtuais** será aberta com mais detalhes sobre as etapas de prevenção e integração, bem como uma lista de todas as VMs monitoradas pela Central de segurança, conforme mostrado abaixo.

![Atualização de sistema ausente por VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Etapas para inclusão
- Recomendações de máquina virtual
- Máquinas virtuais

Em cada seção, você pode selecionar uma opção individual para ver mais detalhes sobre a etapa recomendada para resolver esse problema. As seções abaixo abordarão essas áreas em mais detalhes.

####Etapas para inclusão
Esta seção mostra o número total de VMs que foram inicializadas para coleta de dados e seu status atual. Depois que todas as VMs tiverem a coleta de dados inicializada, elas estarão prontas para receber políticas de segurança da Central de Segurança. Quando você clica nessa entrada, a folha **Inicializando coleta de dados** é aberta e você poderá ver o nome das VMs e o status atual da coleta de dados, na coluna **STATUS DA INSTALAÇÃO**, conforme mostrado abaixo.

![Status da inicialização](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recomendações de máquina virtual
Esta seção tem um conjunto de recomendações para cada VM monitorada pela Central de Segurança do Azure. A primeira coluna lista a recomendação, a segunda coluna o indica número total de VMs que são afetadas por essa recomendação e a terceira coluna mostra a gravidade do problema, conforme ilustrado abaixo.

![Recomendações de VM](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Cada recomendação tem um conjunto de ações que podem ser executadas assim que você clica nela. Por exemplo, se você clicar em **Atualizações de sistema ausentes**, a folha **Atualizações de sistema ausentes** será aberta. Ela lista as VMs que têm correções ausentes e a gravidade da atualização ausente, conforme mostrado abaixo.

![Atualizações de sistema ausentes](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

A folha **Atualizações de sistema ausentes** mostrará uma tabela com as seguintes informações:

- **MÁQUINA VIRTUAL**: o nome da máquina virtual com atualizações ausentes.
- **ATUALIZAÇÕES DO SISTEMA**: o número de atualizações do sistema que estão ausentes.
- **HORA DA ÚLTIMA VERIFICAÇÃO**: a hora em que a Central de Segurança verificou pela última vez se a VM tinha atualizações.
- **ESTADO**: o estado atual da recomendação:
	- **Aberta**: a recomendação ainda não foi resolvida
	- **Em andamento**: a recomendação atualmente está sendo aplicada aos recursos; não é exigido que você realize nenhuma ação
	- **Resolvida**: A recomendação já foi concluída (quando o problema for resolvido, a entrada será esmaecida).
- **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
	- **Alta**: existe uma vulnerabilidade em um recurso significativo (aplicativo, VM, grupo de segurança de rede) e ela requer atenção
	- **Média**: são necessárias etapas adicionais ou não críticas para concluir um processo ou eliminar a vulnerabilidade
	- **Baixa**: uma vulnerabilidade que deve ser abordada, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje exibi-las.)

Para exibir os detalhes da recomendação, clique no nome da VM. Uma nova folha dessa VM será aberta com a lista de atualizações, conforme mostrado abaixo.

![Atualizações de sistema ausentes por VM](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] As recomendações de segurança são as mesmas na folha de Recomendações. Consulte o artigo [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) para obter mais informações sobre como resolver as recomendações. Isso é aplicável não apenas para máquinas virtuais, mas para todos os recursos que estão disponíveis no bloco Integridade de Recursos.

####Seção Máquinas virtuais
A seção de máquinas virtuais fornece uma visão geral de todas as VMs e recomendações. Cada coluna representa um conjunto de recomendações, conforme mostrado abaixo:

![VMs](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

O ícone que aparece sob cada recomendação ajuda a identificar rapidamente quais VMs precisam de atenção e que tipo de recomendação.

No exemplo acima, uma VM tem uma recomendação crítica relacionada a programas antimalware. Para obter mais informações sobre a VM, clique nela. É aberta uma nova folha que representa essa VM, conforme mostrado abaixo.

![Detalhes de segurança da VM](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Essa folha tem os detalhes de segurança da VM. Na parte inferior dessa folha, você pode ver a ação recomendada e a gravidade de cada problema.

###Monitorar redes virtuais
Quando você clicar em **Rede** no bloco **Integridade de segurança de recursos**, a folha **Rede** será aberta com mais detalhes, conforme mostrado abaixo:

![Rede](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

####Recomendações de rede

Como as informações de integridade de recursos de máquinas virtuais, essa folha fornece uma lista resumida dos problemas na parte superior da folha e uma lista de redes monitoradas na parte inferior.

![Blog da rede](./media/security-center-monitoring/security-center-monitoring-fig9-new2.png)

A seção de divisão de status de rede lista os problemas de segurança potenciais e oferece recomendações. Os possíveis problemas podem incluir:

- Grupos de Segurança da Rede (NSGs) em sub-redes não habilitadas
- NSGs nas VMs não habilitadas
- Restringir o acesso externo por meio do ponto de extremidade externo público
- Sub-redes Íntegras

Quando você clica em uma dessas recomendações, uma nova folha é aberta com mais detalhes relacionados à recomendação, conforme mostrado no exemplo abaixo.

![Restringir ponto de extremidade](./media/security-center-monitoring/security-center-monitoring-fig11-new2.png)

Neste exemplo a folha **Configurar Grupos de Segurança da Rede Ausentes para Sub-redes** tem uma lista de sub-redes e máquinas virtuais que não têm a proteção NSG. Se você clicar na sub-rede à qual deseja aplicar o NSG, outra folha será aberta.

Na folha **Escolher grupo de segurança da rede**, você selecionará o Grupo de Segurança da Rede mais apropriado para a sub-rede ou poderá criar um novo Grupo de Segurança da Rede.

####Seção Rede

Na seção **Rede**, há uma exibição hierárquica dos recursos, como mostrado abaixo:

![Árvore de rede](./media/security-center-monitoring/security-center-monitoring-fig121-new2.png)

Esta tabela é classificada (VMs e Sub-redes) por gravidade, conforme descrito abaixo:
- Vermelho (no topo): alta prioridade e deve ser endereçado imediatamente 
- Laranja: prioridade média e deve ser endereçado assim que possível
- Verde (último): estado da integridade

Nessa hierarquia, o primeiro nível tem as [Redes Virtuais](../virtual-network/virtual-networks-overview.md), [Gateways da Rede Virtual](../vpn-gateway/vpn-gateway-site-to-site-create.md) e [Rede Virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). O segundo nível tem as sub-redes e o terceiro nível tem as VMs que pertencem a essas sub-redes. A coluna da direita tem o status atual do Grupo de Segurança da Rede (NSG) para esses recursos. O exemplo a seguir é o resultado da seleção da VM VM-CL-W1:

![Árvore de rede](./media/security-center-monitoring/security-center-monitoring-fig13-new2.png)

A parte inferior dessa folha tem as recomendações para essa VM, semelhante ao que está descrito acima. Você pode clicar em uma recomendação para obter mais informações ou aplicar a configuração/controle de segurança necessário.

###Monitorar recursos do SQL
Quando você clicar em **SQL** no bloco **Integridade de segurança dos recursos**, a folha SQL será aberta com recomendações para os problemas, como auditoria e criptografia de dados transparente não habilitadas. Ela também contém recomendações para o estado de integridade geral do banco de dados.

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Você pode clicar em qualquer uma das recomendações e obter mais detalhes sobre uma ação adicional para resolver o problema. O exemplo abaixo mostra a expansão da recomendação **Auditoria do Banco de Dados não habilitada**.

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

A folha **Habilitar Auditoria nos bancos de dados SQL** contém as seguintes informações:

- Uma lista de bancos de dados SQL
- O servidor no qual eles estão localizados
- Informações indicando se essa configuração foi herdada do servidor ou se é exclusiva do banco de dados
- O estado atual
- A gravidade do problema

Quando você clicar no banco de dados para endereçar essa recomendação, a folha **Auditoria e Detecção de ameaças** será aberta, conforme mostrado abaixo.

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Para habilitar a auditoria, basta selecionar **ATIVAR** na opção **Auditoria** e clicar em **Salvar**.

###Monitorar aplicativos
Se sua carga de trabalho do Azure tiver aplicativos localizados nas [VMs do gerenciador de recursos](../resource-manager-deployment-model.md) com portas Web expostas (portas TCP 80 e 443), a Central de Segurança poderá monitorá-las para identificar os problemas de segurança potenciais e as etapas recomendáveis de correção. Quando você clicar no bloco **Aplicativos**, a folha **Aplicativos** será aberta com uma série de recomendações na seção de etapas de prevenção. Ela também mostra a divisão de aplicativos por host/IP virtual, conforme mostrado abaixo.

![Integridade da segurança de aplicativos](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Como fez com as outras recomendações, você pode clicar nela para ver mais detalhes sobre o problema e como corrigi-lo. O exemplo mostrado na figura a seguir é um aplicativo que foi identificado como aplicativo Web não seguro. Quando você selecionar o aplicativo que foi considerado não seguro, outra folha será aberta com a seguinte opção disponível:

![Aplicativos](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

A folha **Aplicativos Web Não Seguros** terá uma lista de todas as VMs que contêm aplicativos que não são considerados seguros. A lista mostra o nome da VM, o estado atual do problema e a gravidade do problema. Se você clicar nesse aplicativo Web, a folha **Adicionar um Firewall do Aplicativo Web** será aberta com as opções para instalar um WAF (firewall do aplicativo Web) de terceiros, conforme mostrado abaixo.

![Adicionar WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Próximas etapas
Neste documento, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança na Central de Segurança do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Monitorando soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço de localização
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0511_2016-->