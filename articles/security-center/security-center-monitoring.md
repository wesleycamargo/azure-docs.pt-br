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
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Monitoramento de integridade de segurança na Central de segurança do Azure
Este documento ajuda você a usar recursos de monitoramento na Central de segurança do Azure para monitorar a conformidade com as políticas.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança

##O que é o monitoramento de integridade de segurança?
Costumamos pensar em monitoramento como assistir e esperar até que um evento ocorra para poder reagir à situação. Monitoramento de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou práticas recomendadas.

##Monitoramento de integridade da segurança
Depois de habilitar [políticas de segurança](security-center-policies.md) para recursos da assinatura, a Central de segurança do Azure analisará a segurança de seus recursos para identificar possíveis vulnerabilidades. Embora as informações sobre a configuração de rede estejam disponíveis imediatamente, pode levar uma hora ou mais para obter informações disponíveis sobre a configuração da máquina virtual, como o status de atualização de segurança e a configuração do sistema operacional. Você pode exibir o estado de segurança de seus recursos, junto com os problemas nas folhas de Integridade de segurança do recurso. Você também pode exibir uma lista desses problemas nas folhas de Recomendações.

Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).

O bloco **Integridade de recursos** permite monitorar o estado de segurança de seus recursos. Você verá uma série de problemas com gravidade média e alta que exigem atenção, conforme mostrado abaixo:

![Integridade dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1.png)

As políticas de segurança que são habilitadas terão impacto sobre os tipos de controles que estão sendo monitorados aqui. Se a Central de segurança do Azure identificar uma vulnerabilidade que precisa ser resolvida, como uma VM com ausência de atualizações de segurança ou uma sub-rede sem um [Grupo de segurança de rede](virtual-networks-nsg.md), ela será identificada aqui.

###Máquina virtual
Quando você clica em **Máquinas virtuais** no bloco **Integridade de recursos**, a folha **Máquinas virtuais** será aberta com mais detalhes sobre as etapas de prevenção e integração, bem como uma lista de todas as VMs monitoradas pela Central de segurança do Azure, conforme mostrado abaixo:

![Atualização de sistema ausente por VM](./media/security-center-monitoring/security-center-monitoring-fig2.png)

Depois que você abrir esta folha, você verá três seções. Para cada seção, você pode selecionar uma opção individual para ver mais detalhes sobre a etapa recomendada para resolver esse problema.

Por exemplo, se você clicar em **Atualizações de sistema ausentes** em **Etapas de prevenção**, a folha **Atualizações de sistema ausentes** será aberta com a lista de máquinas virtuais que estão com patches ausentes e o nível de gravidade para cada um deles conforme mostrado abaixo:

![Prevenção de atualização de sistema ausente](./media/security-center-monitoring/security-center-monitoring-fig3.png)

Para exibir os detalhes de atualizações recomendados, clique no nome da VM. Uma nova folha dessa VM será aberta com a lista de atualizações que estão faltando:

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]As recomendações de segurança são as mesmas na folha de Recomendações. Consulte o artigo [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) para obter mais informações sobre como resolver as recomendações. Isso é aplicável não apenas para máquinas virtuais, mas para todos os recursos que estão disponíveis no bloco Integridade de Recursos.

###Rede
O status de prevenção de rede mostra as redes virtuais monitoradas pela Central de segurança do Azure. Quando você clicar em **Rede** no bloco de Integridade de recursos, a folha **Rede** será aberta com mais detalhes, conforme mostrado abaixo:

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig5.png)

Como as informações de integridade de recursos de máquinas virtuais, essa folha fornece uma lista resumida dos problemas na parte superior da folha e uma lista de redes monitoradas na parte inferior.

Na seção de divisão de status rede, as etapas de prevenção como [ACLs em pontos de extremidade](virtual-machines-set-up-endpoints.md) não está habilitada, os [Grupos de segurança de rede](virtual-networks-nsg.md) não estão habilitados e as sub-redes íntegras são listadas. Você pode clicar em qualquer um deles e obter mais detalhes e tomar ações futuras para resolver o problema.

Um exemplo disso será o alerta **NSGs em sub-redes não habilitadas**. Se você clicar nesse alerta, a folha **Configurar grupos de segurança de rede ausentes para sub-redes** será aberta. Lá, você verá uma descrição de uma sub-rede que não está com o Grupo de segurança de rede habilitado e a lista das placas de interface de rede que pertencem a essa sub-rede. Selecione a sub-rede na qual você deseja aplicar o grupo de segurança e clique na opção **Configurar NSG**. A folha de grupo **Escolher segurança de rede** será aberta conforme mostrado abaixo:

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
Quando você clicar em **SQL** no bloco **Integridade de recursos**, a folha **SQL** será aberta com as recomendações relacionadas a questões como a auditoria não habilitada, Transparent Data Encryption não habilitada e o estado de integridade geral do banco de dados. A parte inferior da folha terá a análise do SQL Server e, para cada servidor, os bancos de dados que pertencem a ele, como mostrado abaixo:

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig7.png)

Você pode clicar em qualquer uma destas recomendações e obter mais detalhes e tomar ações futuras para resolver o problema. O exemplo abaixo tem a expansão de Habilitar auditoria em servidores SQL.

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###Aplicativos
Se a sua carga de trabalho do Azure tiver aplicativos localizados no [VMs de Gerenciador de recursos](resource-manager-deployment-model.md) com web exposto (portas TCP 80 e 443), a Central de segurança do Azure poderá monitorá-los para identificar problemas potenciais de segurança e etapas recomendáveis de correção. Quando você clicar no bloco **Aplicativos**, a folha **Aplicativos** será aberta com uma série de recomendações na seção Etapas de prevenção e também mostrará a divisão de aplicativos por IP virtual/host, conforme mostrado abaixo:

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig9.png)

Assim como qualquer uma das outras recomendações para os recursos anteriores, você pode clicar nele para ver mais detalhes sobre o problema e como corrigir. O exemplo mostrado na figura a seguir é um aplicativo que foi identificado como **Aplicativo não seguro da Web**. Quando você seleciona o aplicativo que foi considerado não seguro, outra folha será aberta com a opção disponível, que nesse caso é habilitar o Firewall do Aplicativo Web.

![Atualização do sistema por VM recomendada](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## Próximas etapas
Neste documento, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança na Central de Segurança do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_1217_2015-->