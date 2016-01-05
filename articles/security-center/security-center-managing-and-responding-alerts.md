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
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure
Este documento ajuda você a usar recursos da Central de segurança do Azure para gerenciar e responder a alertas de segurança.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## O que são alertas de segurança?
A Central de segurança do Azure coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls para detectar ameaças reais e reduzir os falsos positivos. Uma lista de alertas prioritários é exibida como Alertas de segurança.

Você pode examinar os alertas atuais, observando o bloco de Alertas de segurança. Siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel **Central de segurança do Azure**, você verá o bloco **Alertas de segurança**. 

    ![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Clique na ocorrência do alerta de segurança no gráfico e a folha **Alertas de segurança** será aberta com mais detalhes sobre este alerta, conforme mostrado abaixo:
    
    ![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Na parte inferior nessa folha, você tem os detalhes de cada alerta. Você pode classificá-los para cada coluna clicando na coluna desejada. A definição para cada coluna é mostrada abaixo:

- **Alerta**: uma breve explicação sobre o alerta.
- **Contagem**: representa o número de todos os alertas desse tipo específico que foram detectados em um dia específico.
- **Detectado por**: o serviço responsável por disparar o alerta.
- **Data**: quando o evento ocorreu.
- **Estado**: mostra o estado atual desse alerta. Há três tipos de estado:
    - **Ativo**: o alerta de segurança foi detectado.
    - **Descartado**: o alerta de segurança foi fechado pelo usuário. Esse status costuma ser usado para alertas que foram investigados, mas concluídos como não sendo um ataque real ou investigados e atenuados

- **Gravidade**: mostra o nível de gravidade, que pode ser alta, média ou baixa.
  

### Respondendo a alertas de segurança
Várias atividades podem indicar um possível ataque em sua organização. Por exemplo, um administrador de rede executando uma captura de rede legítima pode parecer com alguém iniciando alguma forma de ataque. Em outros casos, um sistema mal configurado pode levar a um número de falsos positivos em um sistema de detecção de invasão, o que pode dificultar o reconhecimento de incidentes genuínos. Depois de examinar os alertas de segurança usando a Central de segurança do Azure, você pode começar a executar ações com base na gravidade do alerta.

Para tomar uma ação, selecione o alerta que você deseja responder e uma nova folha será aberta à direita com mais detalhes, conforme mostrado abaixo:

![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

Nesse caso, o alerta disparado refere-se a atividade suspeita do protocolo RDP (Protocolo de Área de Trabalho Remota). A primeira coluna mostra quais recursos foram atacados, seguido pelo horário em que esse ataque foi detectado, o estado do alerta e a gravidade. Depois de revisar essas informações, você deve clicar no recurso que foi atacado e uma nova folha será aberta com mais sugestões do que você deve fazer em seguida, como mostra o exemplo a seguir:

![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]A correção sugerida pelo Centro de segurança do Azure variará de acordo com o alerta de segurança e, em alguns casos, você talvez precise usar outros recursos do Azure para implementar a correção recomendada. Por exemplo, a correção para esse ataque é criar uma lista negra do endereço IP que está gerando esse ataque usando a [ACL de rede](virtual-networks-acl.md) ou uma regra de [Grupo de segurança de rede](virtual-networks-nsg.md).

### Configurando alertas de segurança
Você pode filtrar com base na data, no estado e na gravidade dos alertas. Na folha de Alertas de segurança, clique em Filtro e habilite as opções que você deseja, conforme mostrado abaixo:

![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

A filtragem de alertas pode ser útil para cenários em que é necessário restringir o escopo de exibir no painel. Por exemplo, você deseja verificar alertas de segurança que ocorreram nas últimas 24 horas, porque você está investigando uma possível falha no sistema. Embora a maioria dos alertas de segurança tenha recomendações que devem ser aplicadas, em algumas circunstâncias, você precisará ignorar um alerta, pois, para o seu ambiente, esse é um comportamento esperado para um determinado recurso ou um falso positivo. Seja qual for o caso, você pode ocultar as recomendações para um determinado recurso usando a opção **Descartar**.

Para descartar uma tarefa, clique no recurso para aplicar as informações adicionais de modo de exibição ou a configuração recomendada. Clique com o botão direito do mouse na tarefa para descartá-la e você verá a opção **Descartar** semelhante à imagem a seguir:

![Habilitar coleta de dados](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

Em uma coleção de alertas, cada um deles terá correção e uma descrição muito específica. Os alertas que você vê na Central de segurança do Azure baseiam-se no cenário de ataque. Os cenários de ataque que seguem são disparados pelo mecanismo da Microsoft:

- **Detecção de força bruta em dados de rede**: essas detecções são baseadas em modelos de aprendizado de máquina que aprenderem com os dados de tráfego de rede. 
- **Detecção de força bruta em dados de ponto de extremidade**: essas detecções são baseadas em consultas da Central de segurança do Azure dos logs de computador, o que permite a diferenciação entre falha e uma tentativa bem-sucedida. 
- **VMs se comunicando IPs mal-intencionados**: essas detecções são baseadas no Centro de segurança do Azure que descobre quais máquinas estão comprometidas com bots e se comunica com seus servidores de controle e comando (C & C) (e vice-versa). 

## Próximas etapas
Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

<!---HONumber=AcomDC_1217_2015-->