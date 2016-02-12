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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="yurid"/>

# Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure
Este documento ajuda você a usar recursos da Central de Segurança do Azure para gerenciar e responder a alertas de segurança.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## O que são alertas de segurança?
 A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls para detectar ameaças reais e reduzir os falsos positivos. Uma lista de alertas prioritários é exibida como alertas de segurança.

Você pode examinar os alertas atuais observando o bloco **Alertas de segurança**. Siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança**.

    ![O bloco Alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Clique no ícone de ocorrência de alertas de segurança. A folha **Alertas de segurança** será aberta com mais detalhes sobre os alertas, conforme mostrado abaixo.

    ![A folha Alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Na parte inferior dessa folha estão os detalhes de cada alerta. Para classificar, clique na coluna com base na qual você deseja classificar. A definição de cada coluna é mostrada abaixo:

- **Alerta**: uma breve explicação sobre o alerta.
- **Contagem**: uma lista de todos os alertas desse tipo específico que foram detectados em um dia específico.
- **Detectado por**: o serviço responsável por disparar o alerta.
- **Data**: a data na qual o evento ocorreu.
- **Estado**: o estado atual desse alerta. Há três tipos de estado:
    - **Ativo**: o alerta de segurança foi detectado.
    - **Descartado**: o alerta de segurança foi fechado pelo usuário. Esse status costuma ser usado para alertas que foram investigados, mas que foram atenuados ou considerados como não sendo ataques reais

- **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

### Responder a alertas de segurança
Várias atividades podem indicar um possível ataque em sua organização. Por exemplo, um administrador de rede executando uma captura de rede legítima pode parecer com alguém iniciando alguma forma de ataque. Em outros casos, um sistema mal configurado pode gerar diversos falsos positivos em um sistema de detecção de invasão, o que pode dificultar o reconhecimento de incidentes genuínos. Depois de examinar os alertas de segurança usando a Central de Segurança, você pode começar a executar ações com base na gravidade do alerta.

Para executar uma ação, selecione o alerta ao qual você deseja responder, e uma nova folha será aberta à direita com mais detalhes, conforme mostrado abaixo.

![Responder aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

Nesse caso, os alertas disparados referem-se à atividade suspeita do protocolo RDP (Protocolo de Área de Trabalho Remota). A primeira coluna mostra quais recursos foram atacados; a segunda mostra o horário de detecção desse ataque; a terceira mostra o estado do alerta; e a quarta mostra gravidade do ataque. Depois de revisar essas informações, clique no recurso atacado. Uma nova folha será aberta com mais sugestões sobre o que fazer em seguida, conforme mostra o exemplo a seguir.

![Sugestões sobre o que fazer com relação aos alertas de segurança na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] A correção sugerida pela Central de Segurança varia de acordo com o alerta de segurança. Em alguns casos, talvez seja necessário usar outros recursos do Azure para implementar a correção recomendada. Por exemplo, a correção para esse ataque é colocar o endereço IP que está gerando esse ataque em uma lista negra usando uma [ACL de rede](virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](virtual-networks-nsg.md).

### Gerenciar alertas de segurança
Você pode filtrar com base na data, no estado e na gravidade dos alertas. Na folha de **Alertas de segurança**, clique em **Filtro** e habilite as opções que você deseja, conforme mostrado abaixo.

![Filtrar os alertas na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

A filtragem de alertas pode ser útil para cenários em que é necessário restringir o escopo de exibir no painel. Por exemplo, convém verificar os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.

Na maioria dos casos, você deve aplicar as recomendações nos alertas de segurança. No entanto, em algumas circunstâncias, pode ser necessário ignorar um alerta, pois ele é um falso positivo para seu ambiente ou indica um comportamento esperado para um determinado recurso. Seja qual for o caso, você pode ocultar as recomendações para um determinado recurso usando a opção **Descartar**.

Para descartar uma tarefa, clique com o botão direito nela e você verá a opção **Descartar**, assim como na imagem abaixo.

![A opção Descartar na Central de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

Em uma coleção de alertas, cada um deles terá uma descrição muito específica e uma correção. Os alertas que você vê na Central de Segurança têm base no cenário de ataque. Os alertas sobre os cenários de ataque a seguir são disparados pelo mecanismo da Microsoft:

- **Detecção de força bruta em dados de rede**: baseada em modelos de aprendizado de máquina que aprendem com os dados de tráfego de rede.
- **Detecção de força bruta em dados de ponto de extremidade**: baseada em consultas da Central de Segurança dos logs da máquina; permite a diferenciação entre tentativas com falha e bem-sucedidas.
- **VMs se comunicando IPs mal-intencionados**: baseada na descoberta por parte da Central de Segurança de quais máquinas estão comprometidas com bots e se comunicando com seus servidores C&C (Controle e comando) (e vice-versa).

## Próximas etapas
Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_0204_2016-->