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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="yurid"/>

#Monitoramento de integridade de segurança na Central de segurança do Azure
Este documento ajuda você a usar recursos de monitoramento na Central de segurança do Azure para monitorar a conformidade com as políticas.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança

##O que é o monitoramento de integridade de segurança?
Costumamos pensar em monitoramento como assistir e esperar até que um evento ocorra para poder reagir à situação. Monitoramento de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou práticas recomendadas.

##Monitoramento de integridade da segurança
Depois de habilitar [políticas de segurança](security-center-policies.md) para recursos da assinatura, a Central de segurança do Azure analisará a segurança de seus recursos para identificar possíveis vulnerabilidades. Embora as informações sobre a configuração de rede estejam disponíveis imediatamente, pode levar uma hora ou mais para obter informações disponíveis sobre a configuração da máquina virtual, como o status de atualização de segurança e a configuração do sistema operacional. Você pode exibir o estado de segurança de seus recursos com os problemas na folha **Integridade de Segurança do Recurso**. Você também pode exibir uma lista desses problemas na folha **Recomendações**.

Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).

O bloco **Integridade de recursos** permite monitorar o estado de segurança de seus recursos. Você verá uma série de problemas com gravidade média e alta que exigem atenção, conforme mostrado abaixo:

![Integridade dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

As políticas de segurança que são habilitadas terão impacto sobre os tipos de controles que estão sendo monitorados aqui. Se a Central de segurança do Azure identificar uma vulnerabilidade que precisa ser resolvida, como uma VM com ausência de atualizações de segurança ou uma sub-rede sem um [Grupo de segurança de rede](virtual-networks-nsg.md), ela será identificada aqui.

###Máquina virtual
Quando você clica em **Máquinas virtuais** no bloco **Integridade de recursos**, a folha **Máquinas virtuais** será aberta com mais detalhes sobre as etapas de prevenção e integração, bem como uma lista de todas as VMs monitoradas pela Central de segurança do Azure, conforme mostrado abaixo:

![Atualização de sistema ausente por VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

Depois de abrir essa folha, você verá três seções:

- Etapas para inclusão
- Recomendações de máquina virtual
- Máquinas virtuais

Para cada seção, você pode selecionar uma opção individual para ver mais detalhes sobre a etapa recomendada para resolver esse problema. As seções abaixo abordarão essas áreas em mais detalhes.

####Etapas para inclusão
Esta seção mostra a quantidade total de VMs que foram inicializadas para coleta de dados e seu status atual. Depois que cada VM tiver a coleta de dados inicializada, elas estarão prontas para receber políticas de segurança da Central de Segurança do Azure. Quando você clica nessa entrada, a folha **Inicializando coleta de dados** é aberta e você poderá ver o nome das VMs e o status atual da coleta de dados, como na coluna **status da instalação**, conforme mostrado abaixo:

![Status da inicialização](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recomendações de máquina virtual
Esta seção tem um conjunto de recomendações para cada VM monitorada pela Central de Segurança do Azure. A primeira coluna tem a descrição da recomendação, a segunda coluna a quantidade total de VMs que são afetadas pelas recomendações e, na terceira coluna, você tem a gravidade, conforme mostrado abaixo.

![Recomendações de VM](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Cada recomendação tem um conjunto de ações que podem ser executadas assim que você clica nela. Por exemplo, se você clicar em **Atualizações de sistema ausentes**, a folha **Atualizações de sistema ausentes** será aberta com a lista de VMs que estão com patches ausentes e o nível de gravidade de cada uma, conforme mostrado abaixo:

![Atualizações de sistema ausentes](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

A folha Atualizações de sistema ausentes mostrará uma tabela com as seguintes informações:

- **MÁQUINA VIRTUAL**: o nome da máquina virtual com atualizações ausentes.
- **ATUALIZAÇÕES DO SISTEMA**: a quantidade de atualizações do sistema que estão faltando.
- **HORA DA ÚLTIMA VERIFICAÇÃO**: última vez que a Central de Segurança do Azure verificou a VM em busca de atualizações.
- **ESTADO**: o estado atual da recomendação: 
	- **Aberta**: ainda não foi resolvida
	- **Em andamento**: a recomendação atualmente está sendo aplicada aos recursos; não é exigido que você realize nenhuma ação
	- **Resolvida**: a recomendação já foi concluída (quando o estado é resolvido, a cor da linha fica esmaecida)
- **GRAVIDADE**: descreve a gravidade dessa recomendação específica: 
	- **Alta**: existe uma vulnerabilidade em um recurso significativo (aplicativo, VM, grupo de segurança de rede) e ela requer atenção
	- **Média**: são necessárias etapas adicionais ou não críticas para concluir um processo ou eliminar a vulnerabilidade
	- **Baixa**: uma vulnerabilidade que deve ser abordada, mas não exige atenção imediata. (Por padrão, as recomendações de baixa severidade não são apresentadas, mas você pode filtrar por recomendações de baixa severidade se quiser exibi-las).

Para exibir os detalhes de atualizações recomendados, clique no nome da VM. Uma nova folha para essa VM será aberta com a lista de atualizações que estão faltando, conforme mostrado abaixo:

![Atualizações de sistema ausentes por VM](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] As recomendações de segurança são as mesmas na folha de Recomendações. Consulte o artigo [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) para obter mais informações sobre como resolver as recomendações. Isso é aplicável não apenas para máquinas virtuais, mas para todos os recursos que estão disponíveis no bloco Integridade de Recursos.

####Máquinas virtuais
A seção de máquinas virtuais fornece uma visão geral de todas as VMs e recomendações. Cada coluna de recomendação representa um conjunto de recomendações, conforme mostrado abaixo:

![VMs](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

O ícone que aparece sob cada recomendação ajuda a identificar rapidamente quais VMs precisam de atenção e para que tipo de recomendação.

No exemplo acima, uma VM tem uma recomendação crítica relacionada ao antimalware. Para obter mais informações sobre a VM, clique nela e uma nova folha que representa essa VM será aberta, conforme mostrado abaixo:

![Detalhes de segurança da VM](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Essa folha tem os detalhes de segurança da VM. Na parte inferior dessa folha, você pode ver a ação recomendada e a gravidade de cada recomendação.

###Rede
O status de prevenção de rede mostra as redes virtuais monitoradas pela Central de segurança do Azure. Quando você clicar em **Rede** no bloco de Integridade de recursos, a folha **Rede** será aberta com mais detalhes, conforme mostrado abaixo:

![Rede](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Depois de abrir essa folha, você verá duas seções: - Recomendações de rede - Rede
 
Para cada seção, você pode selecionar uma opção individual para ver mais detalhes sobre a etapa recomendada para resolver esse problema. As seções abaixo abordarão essas áreas em mais detalhes.

####Recomendações de rede

Como as informações de integridade de recursos de máquinas virtuais, essa folha fornece uma lista resumida dos problemas na parte superior da folha e uma lista de redes monitoradas na parte inferior.

Na seção de divisão de status rede, as etapas de prevenção como [ACLs em pontos de extremidade](virtual-machines-set-up-endpoints.md) não habilitadas, [Grupos de segurança de rede](virtual-networks-nsg.md) não habilitados, Sub-redes íntegras e **Acesso no NSG não restrito** são listadas. Quando você clica em uma dessas recomendações, uma nova folha é aberta com mais detalhes relacionados à recomendação, conforme mostrado no exemplo abaixo:

![Ponto de extremidade](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

Neste exemplo, a folha **Restringir acesso por meio do ponto de extremidade externo público** tem uma lista de NSGs (Grupos de Segurança de Rede) que fazem parte desse alerta, a sub-rede e a rede às quais esse NSG está associado, o estado atual dessa recomendação e a gravidade. Se você clicar no grupo de segurança de rede, outra folha será aberta, conforme mostrado abaixo:

![Restringir ponto de extremidade](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

Essa folha tem as informações do grupo de segurança de rede, o local e a lista de regras de entrada que atualmente estão habilitados. A parte inferior dessa folha tem a VM que está associada a esse grupo de segurança de rede. Se você quiser habilitar as regras de entrada para bloquear uma porta indesejada que atualmente está aberta ou alterar a fonte da regra de entrada atual, você poderá clicar no botão **Editar regra de entrada** na parte superior da folha.

####Rede
Na seção **Rede**, você tem uma exibição hierárquica do grupo de recursos, da sub-rede e da interface de rede associada à sua VM, conforme mostrado abaixo:

![Árvore de rede](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

Esta seção divide as [VMs baseadas no Gerenciador de Recursos das VMs clássicas](resource-manager-deployment-model.md), o que ajuda a identificar rapidamente se os recursos de rede do ASM (Gerenciamento de Serviços do Azure)/Clássico ou do ARM (Gerenciamento de Recursos do Azure) estão disponíveis para a máquina virtual. Se decidir acessar as propriedades de uma placa de interface de rede nesse local, você precisará expandir a sub-rede e clicar no nome da VM. Se você executar essa ação para uma VM baseada no gerenciador de recursos, uma nova folha semelhante à mostrada abaixo será exibida:

![Árvore de rede](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Essa folha tem um resumo da placa de interface de rede e as recomendações atuais para ela. Se a VM que você selecionou for uma VM clássica, uma nova folha com diferentes opções será exibida, conforme mostrado abaixo:

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Essa folha permite fazer alterações na porta pública, na porta privada e permite criar uma ACL para essa VM.

###SQL
Quando você clicar em **SQL** no bloco **Integridade de recursos**, a folha SQL será aberta com as recomendações relacionadas a problemas como auditoria não habilitada, Transparent Data Encryption não habilitada e o estado de integridade geral do banco de dados.

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Você pode clicar em qualquer uma destas recomendações e obter mais detalhes e tomar ações futuras para resolver o problema. O exemplo abaixo tem a expensão da recomendação **Auditoria de Banco de Dados não habilitada**.

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Na folha **Habilitar Auditoria nos bancos de dados SQL**, você tem uma lista de bancos de dados SQL, o servidor no qual eles estão localizados, se essa configuração foi herdada do servidor ou se ela for exclusiva nesse banco de dados, o estado atual desse problema e a gravidade. Quando você clicar no banco de dados para atender a essa recomendação, a folha **Auditoria e detecção de ameaças** será aberta, conforme mostrado abaixo:

![Integridade de recursos do SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Para habilitar esse recurso, basta selecionar **ATIVAR** na opção **Auditoria** e clicar em **Salvar**.

###Aplicativos
Se a sua carga de trabalho do Azure tiver aplicativos localizados no [VMs de Gerenciador de recursos](resource-manager-deployment-model.md) com web exposto (portas TCP 80 e 443), a Central de segurança do Azure poderá monitorá-los para identificar problemas potenciais de segurança e etapas recomendáveis de correção. Quando você clicar no bloco **Aplicativos**, a folha **Aplicativos** será aberta com uma série de recomendações na seção Etapas de prevenção e também mostrará a divisão de aplicativos por IP virtual/host, conforme mostrado abaixo:

![Integridade da segurança de aplicativos](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Assim como qualquer uma das outras recomendações para os recursos anteriores, você pode clicar nele para ver mais detalhes sobre o problema e como corrigir. O exemplo mostrado na figura a seguir é um aplicativo que foi identificado como **Aplicativo não seguro da Web**. Quando você selecionar o aplicativo que foi considerado não seguro, outra folha será aberta com a seguinte opção disponível:

![Aplicativos](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

A folha **Aplicativos Web Não Seguros** terá uma lista de todas as VMs que contêm aplicativos que não são considerados seguros. A lista mostra o nome da VM, o estado atual do problema e a segurança. Se você clicar nesse aplicativo Web, a folha **Adicionar um Firewall do Aplicativo Web** será aberta com as opções para instalar um WAF (Firewall do Aplicativo Web) de terceiros, conforme mostrado abaixo:

![Adicionar WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Próximas etapas
Neste documento, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança na Central de Segurança do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0204_2016-->