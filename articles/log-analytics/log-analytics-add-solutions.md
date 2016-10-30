<properties
    pageTitle="Adicionar soluções do Log Analytics por meio da Galeria de Soluções | Microsoft Azure"
    description="As soluções do Log Analytics são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas que giram em torno de uma área de problema específica."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Adicionar soluções do Log Analytics por meio da Galeria de Soluções

As soluções do Log Analytics são uma coleção de **lógica**, **visualização** e **regras de aquisição de dados** que fornecem as métricas que giram em torno de uma área de problema específica. Este artigo lista as soluções com suporte do Log Analytics e explica como adicionar e removê-las usando a Galeria de Soluções.

As soluções permitem análises mais profundas em relação ao seguinte:

- ajudar a investigar e resolver problemas operacionais com mais rapidez
- coletar e correlacionar vários tipos de dados de computador
- ajudá-lo a ser proativo com atividades tais como planejamento de capacidade, relatórios de status de patch e auditoria de segurança.


>[AZURE.NOTE] O Log Analytics inclui a funcionalidade Pesquisa de Log, por isso não é necessário instalar uma solução para habilitá-la. No entanto, você pode obter visualizações de dados, pesquisas sugeridas e insights adicionando soluções de Galeria de Soluções.

Depois de adicionar uma solução, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço do OMS. O processamento do serviço do OMS normalmente leva de alguns minutos a uma hora. Depois que o serviço processar os dados, será possível exibi-los no OMS.

Você pode facilmente remover uma solução quando ela não for mais necessário. Quando você remove uma solução, seus dados não são enviados ao OMS, o que pode reduzir a quantidade de dados usados pela sua cota diária, caso você tenha uma.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Soluções com suporte no Microsoft Monitoring Agent

Neste momento, os servidores conectados diretamente ao OMS com o uso do Microsoft Monitoring Agent podem usar a maioria das soluções disponíveis, incluindo:

- Avaliação do Active Directory
- Gerenciamento de alertas (excluindo alertas SCOM)
- Antimalware
- Controle de Alterações
- Segurança
- Avaliação do SQL
- Atualizações do Sistema

No entanto, as soluções a seguir *não* têm suporte do Microsoft Monitoring Agent e exigem o agente SCOM (System Center Operations Manager).

- Gerenciamento de alertas (incluindo alertas SCOM)
- Gerenciamento de Capacidade
- Avaliação de Configuração

Consulte [Conectando o Operations Manager ao Log Analytics](log-analytics-om-agents.md) para obter informações sobre como conectar o agente SCOM ao Log Analytics.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Para adicionar uma solução usando a Galeria de Soluções

1. Na página Visão geral do OMS, clique no bloco **Solutions Gallery** .    
    ![galeria de soluções](./media/log-analytics-add-solutions/sol-gallery.png)
2. Na página Galeria de Soluções do OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que deseja adicionar ao OMS.
3. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.
4. Um novo bloco para a solução que você adicionou é mostrado na página Visão geral no OMS e será possível começar a usá-lo depois que o serviço de OMS processar seus dados.

## <a name="to-configure-solutions"></a>Para configurar as soluções
1. Você precisará configurar algumas soluções. Por exemplo, você precisará configurar a Automação, o Azure Site Recovery e o Backup antes de usá-los.
2. Para ver qualquer uma dessas soluções, clique em seu bloco na página Visão geral.  
    ![configurar solução](./media/log-analytics-add-solutions/configure-additional.png)
3. Em seguida, configure a solução com as informações necessárias e clique em **Salvar**.  
    ![configurar solução](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Para remover uma solução usando a Galeria de Soluções

1. Na página Visão geral do OMS, clique no bloco **Configurações** .
2. Na página Configurações, sob a guia Soluções, clique em **Remover** para a solução que deseja remover.
3. No diálogo de confirmação, clique em **Sim** para remover a solução.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Detalhes da coleta de dados para recursos e soluções do OMS

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para as soluções e os recursos do OMS. Agentes diretos e agentes do SCOM são essencialmente os mesmos, no entanto, o agente direto inclui uma funcionalidade adicional para permitir a conexão com o espaço de trabalho do OMS e o encaminhamento por um proxy. Se você usar um agente do SCOM, ele deve ser direcionado como um agente do OMS para se comunicar com o OMS. Agentes do SCOM nesta tabela são agentes do OMS que estão conectados ao SCOM. Consulte [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md) para obter informações sobre como conectar seu ambiente existente do SCOM ao OMS.

>[AZURE.NOTE] O tipo de agente que você usa determina como os dados são enviados ao OMS, com as seguintes condições:

- Você usa o agente direto ou um agente do OMS anexado ao SCOM.
- Quando o SCOM é necessário, os dados do agente do SCOM para a solução são sempre enviados ao OMS usando o grupo de gerenciamento do SCOM. Além disso, quando o SCOM é necessário, apenas o agente do SCOM é usado pela solução.
- Quando o SCOM não é necessário e a tabela mostra que os dados do agente do SCOM são enviados ao OMS usando o grupo de gerenciamento, os dados do agente do SCOM são sempre enviados ao OMS usando grupos de gerenciamento. Agentes diretos ignoram o grupo de gerenciamento e enviam seus dados diretamente ao OMS.
- Quando os dados do agente do SCOM não são enviados usando um grupo de gerenciamento, os dados são enviados diretamente ao OMS, ignorando o grupo de gerenciamento.


|tipo de dados| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|---|
|Avaliação do AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 dias|
|Status de replicação do AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 dias|
|Alertas (Nagios)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|na chegada|
|Alertas (Zabbix)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Alertas (Operations Manager)|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutos|
|Antimalware|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Gerenciamento de Capacidade|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Controle de Alterações|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Controle de Alterações|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|por hora|
|Avaliação de configuração (Supervisor herdado)|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| duas vezes por dia|
|ETW|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Logs IIS|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Cofres de Chaves|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Gateways do aplicativo de rede|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Grupos de segurança de rede|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Office 365|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|após a notificação|
|Contadores de desempenho|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|conforme agendado, mínimo de 10 segundos|
|Contadores de desempenho|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|conforme agendado, mínimo de 10 segundos|
|Service Fabric|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Avaliação do SQL|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 dias|
|SurfaceHub|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|na chegada|
|Syslog|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|do armazenamento do Azure: 10 minutos; do agente: na chegada|
|Atualizações do Sistema|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização|
|Logs de eventos de segurança do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| para o armazenamento do Azure: 10 min; para o agente: na chegada|
|Logs do firewall do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| na chegada|
|Logs de eventos do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| para o armazenamento do Azure: 1 min; para o agente: na chegada|
|Dados durante a transmissão|Windows (2012 R2/8.1 ou posterior)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| a cada minuto|

## <a name="log-analytics-preview-solutions-and-features"></a>Recursos e Soluções do Log Analytics em preview

Executando um serviço e seguindo as práticas recomendadas de DevOps, somos capazes de firmar parceria com os clientes para desenvolver recursos e soluções.

Durante a preview particular, concedemos a um pequeno grupo de clientes o acesso a uma implementação antecipada do recurso ou da solução para obter comentários e realizar melhorias. Essa implementação antecipada tem recursos e funcionalidades operacionais mínimas.

Nosso objetivo é testar as coisas rapidamente para descobrir o que funciona ou não. Iteramos por esse processo até que os comentários dos clientes de preview particular nos informem que estamos prontos para uma preview pública.

Durante a preview pública, disponibilizamos o recurso ou solução para todos os usuários a fim de obter mais comentários e validar nossa escala e eficiência. Durante essa fase:

- Os recursos de preview aparecerão na guia Configurações e podem ser habilitados por qualquer usuário
- As soluções de preview podem ser adicionadas por meio da galeria ou usando um script publicado

### <a name="what-should-i-know-about-preview-features-and-solutions?"></a>O que devo saber sobre Recursos e Soluções em preview?

Estamos empolgados com os novos recursos e soluções e adoraríamos trabalhar junto com você para desenvolvê-los.

No entanto, os recursos e soluções de preview não são ideais para todos, portanto, antes de pedir para participar de uma preview particular ou habilitar uma preview pública, assegure que você concorda em trabalhar com algo que está em desenvolvimento.

Ao habilitar um recurso de preview por meio do portal, você verá um aviso para lembrar que o recurso está em preview.

#### <a name="for-both-*private*-and-*public*-preview"></a>Para previews *particulares* e *públicas*

O seguinte se aplica a previews públicas e particulares:

- As coisas podem não funcionar corretamente sempre.
  - Os problemas variam de pequenos incômodos até algo parar totalmente de funcionar
- Há um potencial para a preview ter um impacto negativo em seus sistemas / ambiente
  - Tentamos evitar que coisas negativas aconteçam com os sistemas que você está usando com o OMS, mas às vezes pode ocorrer algo inesperado
- Perda / corrupção de dados pode ocorrer
- Podemos pedir que você colete logs de diagnóstico ou outros dados para ajudar a solucionar problemas
- O recurso ou a solução podem ser removidos (temporária ou permanentemente)
  - Com base em nossas lições aprendidas durante a preview, podemos decidir que não lançaremos o recurso ou a solução
- Previews podem não funcionar ou não ter sido testadas com todas as configurações, e podemos limitar:
  - Os sistemas operacionais que podem ser usados (por exemplo, um recurso pode aplicar-se somente ao Linux enquanto estiver em preview)
  - O tipo de agente (MMA, SCOM) que pode ser usado (por exemplo, um recurso pode não funcionar com SCOM enquanto estiver na preview)  
- Soluções e recursos em preview não são cobertos pelo Contrato de Nível de Serviço
- O uso de recursos de preview incorrerá encargos de uso
- Recursos ou funcionalidades que você precisa para o recurso / solução ser útil podem estar ausentes ou incompletos
- Recursos / soluções podem não estar disponíveis em todas as regiões
- Recursos / soluções podem não estar traduzidos
- Recursos / soluções podem ter um limite do número de clientes ou dispositivos que podem usá-los
- Pode ser necessário usar scripts para realizar a configuração e para habilitar a solução/recurso
- A IU (interface do usuário) estará incompleta e pode ser alterada diariamente
- Previews públicas podem não ser apropriadas para seus sistemas de produção / críticos

#### <a name="for-*private*-preview"></a>Para preview *particular*

Além dos itens acima, o seguinte é específico para previews particulares:

- Esperamos que você nos forneça comentários sobre sua experiência para que possamos melhorar o recurso/solução
- Poderemos entrar em contato para solicitar comentários usando pesquisas, ligações telefônicas ou email
- As coisas não funcionarão corretamente sempre
- Podemos pode exigir um NDA (acordo de confidencialidade) para a participação ou podemos incluir conteúdo confidencial
  - Antes de postar em blogs, tuitar ou comunicar-se de outra forma com terceiros, confirme com o Gerente do Programa responsável para a preview para entender possíveis restrições de divulgação
- Não execute em sistemas de produção / críticos


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions?"></a>Como obter acesso a recursos e soluções de preview particular?

Nós o convidamos clientes para previews particulares por meio de várias maneiras diferentes dependendo da preview.

- Responder a pesquisa de cliente mensal e conceder permissão para acompanhar você melhoram suas chances de ser convidado para uma preview particular.
- A equipe de contas da Microsoft pode indicar você.
- Você pode se inscrever com base nos detalhes publicados no twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- Inscreva-se com base em eventos da comunidade que compartilham detalhes, encontre-nos em reuniões, conferências e nas comunidades online.


## <a name="next-steps"></a>Próximas etapas

- [Pesquise logs](log-analytics-log-searches.md) para exibir informações detalhadas coletadas pelas soluções.



<!--HONumber=Oct16_HO2-->


