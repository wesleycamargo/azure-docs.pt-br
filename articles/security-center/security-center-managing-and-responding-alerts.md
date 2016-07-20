<properties
   pageTitle="Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a usar recursos da Central de segurança do Azure para gerenciar e responder a alertas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="yurid"/>

# Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure
Este documento ajuda você a usar recursos da Central de Segurança do Azure para gerenciar e responder a alertas de segurança.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## O que são alertas de segurança?
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e de antimalware e firewalls integrados para detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança, incluindo alertas de soluções de parceiro integradas, é mostrada na Central de Segurança, junto com as informações que você precisa investigar rapidamente e recomendações sobre como corrigir um ataque.

Os pesquisadores de segurança da Microsoft estão constantemente analisando ameaças emergentes em todo o mundo, incluindo novos padrões de ataque e tendências vistos em seus produtos de consumidor e empresariais e serviços online. Como resultado, a Central de Segurança pode atualizar seus algoritmos de detecção à medida que novas vulnerabilidades e explorações são descobertas, ajudando os clientes a acompanhar o ritmo das ameaças em constante evolução. Os exemplos de alguns dos tipos de ameaças que pode detectar a Central de Segurança incluem:

- **Detecção de força bruta em dados de rede**: usa modelos de aprendizado de máquina que entendem os padrões de tráfego de rede típica para seus aplicativos e habilita a detecção mais eficaz de tentativas de acesso que estão sendo executadas por indivíduos mal-intencionados, em vez de usuários legítimos.
- **Detecção de força bruta em dados de ponto de extremidade**: baseada em análise de logs de máquina; habilita a diferenciação entre tentativas com falha e bem-sucedidas.
- **VMs se comunicando com IPs mal-intencionados**: compara o tráfego de rede às informações da Microsoft sobre ameaças globais, descobre máquinas que estão comprometidas e se comunicando com servidores de C&C (Comando e Controle) e vice-versa.
- **VMs comprometidas**: com base na análise de comportamento de logs de máquina e na correlação com outros sinais, identifica eventos irregulares que provavelmente são resultado de comprometimento e exploração de máquinas.

## Configurando alertas de segurança

Você pode examinar os alertas atuais observando o bloco **Alertas de segurança**. Siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança**.

    ![O bloco Alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Clique no bloco para abrir a folha **Alertas de segurança**, que contém mais detalhes sobre os alertas, conforme mostrado abaixo.

    ![A folha Alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Na parte inferior dessa folha estão os detalhes de cada alerta. Para classificar, clique na coluna com base na qual você deseja classificar. A definição de cada coluna é mostrada abaixo:

- **Alerta**: uma breve explicação sobre o alerta.
- **Contagem**: uma lista de todos os alertas desse tipo específico que foram detectados em um dia específico.
- **Detectado por**: o serviço responsável por disparar o alerta.
- **Data**: a data na qual o evento ocorreu.
- **Estado**: o estado atual desse alerta. Há três tipos de estado:
    - **Ativo**: o alerta de segurança foi detectado.
    - **Descartado**: o alerta de segurança foi fechado pelo usuário. Esse status costuma ser usado para alertas que foram investigados, mas que foram atenuados ou considerados como não sendo ataques reais

- **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

Você pode filtrar com base na data, no estado e na gravidade dos alertas. A filtragem de alertas pode ser útil para cenários em que é necessário restringir o escopo da exibição de alertas de segurança. Por exemplo, convém lidar com os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.

1. Clique em **Filtrar** na folha **Alertas de Segurança**. A folha **Filtrar** é aberta e você seleciona os valores de data, estado e gravidade que deseja ver.

	![Filtrar os alertas na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	Após investigar um alerta de segurança, você pode achar que ele é um falso positivo para seu ambiente ou indica um comportamento esperado para determinado recurso. Seja qual for o caso, se determinar que um alerta de segurança não é aplicável, você poderá ignorá-lo e, em seguida, filtrá-lo da exibição. Há duas maneiras de ignorar um alerta de segurança. Clique com o botão direito do mouse em um alerta e selecione **Ignorar** ou passe o mouse sobre um item, clique nos três pontos que aparecem à direita e selecione **Ignorar**. Você pode exibir os alertas de segurança ignorados clicando em **Filtrar** e selecionando **Ignorados**.

	![Filtrar os alertas na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Responder a alertas de segurança
Selecione um alerta de segurança para saber mais sobre o evento que disparou o alerta e, se houver, as etapas necessárias para corrigir um ataque. Os alertas de segurança são agrupados por tipo e data. Um alerta de segurança permite abrir uma folha que contém uma lista dos alertas agrupados.

![Responder aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

Nesse caso, os alertas disparados referem-se à atividade suspeita do protocolo RDP (Protocolo de Área de Trabalho Remota). A primeira coluna mostra quais recursos foram atacados; a segunda mostra o horário de detecção desse ataque; a terceira mostra o estado do alerta; e a quarta mostra gravidade do ataque. Depois de revisar essas informações, clique no recurso atacado. Uma nova folha será aberta com mais sugestões sobre o que fazer em seguida, conforme mostra o exemplo a seguir.

![Sugestões sobre o que fazer com relação aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

No campo **Alerta** dessa folha, você encontrará mais detalhes sobre esse evento. Esses detalhes adicionais oferecem informações sobre o que disparou o alerta de segurança, o recurso de destino e, quando aplicável, o endereço IP de origem e as recomendações sobre como corrigir. Em alguns casos, o endereço IP de origem ficará vazio (não disponível) porque nem todos os logs de eventos de segurança do Windows incluem o endereço IP.

> [AZURE.NOTE] A correção sugerida pela Central de Segurança varia de acordo com o alerta de segurança. Em alguns casos, talvez seja necessário usar outros recursos do Azure para implementar a correção recomendada. Por exemplo, a correção para esse ataque é colocar o endereço IP que está gerando esse ataque em uma lista negra usando uma [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md).


## Próximas etapas
Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0713_2016-->