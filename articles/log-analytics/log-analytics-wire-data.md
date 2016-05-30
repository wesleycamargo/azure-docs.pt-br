<properties
	pageTitle="Solução de Dados de Transmissão no Log Analytics | Microsoft Azure"
	description="Dados de transmissão são dados consolidados de rede e de desempenho recebidos de computadores com agentes do OMS, incluindo agentes do Operations Manager e agentes conectados com o Windows. Os dados de rede são combinados com os dados de log para ajudá-lo a correlacionar dados."
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
	ms.date="05/12/2016"
	ms.author="banders"/>

# Solução de Dados de Transmissão no Log Analytics

Dados de transmissão são dados consolidados de rede e de desempenho recebidos de computadores com agentes do OMS, incluindo agentes do Operations Manager e agentes conectados com o Windows. Os dados de rede são combinados com os dados de log para ajudá-lo a correlacionar dados. Os agentes do OMS instalados nos computadores de sua infraestrutura de TI monitoram os dados de rede enviados e recebidos desses computadores para os níveis de rede 2 e 3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo as várias portas e os vários protocolos usados.

>[AZURE.NOTE] Atualmente, a solução de Dados de Transmissão não está disponível para ser adicionada aos espaços de trabalho. Os clientes que já têm a solução de Dados de Transmissão habilitada podem continuar usando a solução.

Por padrão, o OMS coleta dados registrados para dados de CPU, memória, disco e desempenho de rede dos contadores internos do Windows. A coleta de dados de rede e de outros dados é feita em tempo real para cada agente, incluindo sub-redes e protocolos no nível de aplicativo usados pelo computador. É possível adicionar outros contadores de desempenho na página Configurações, na guia Logs.

Se você já usou o [sFlow](http://www.sflow.org/) ou outro software com o [protocolo NetFlow da Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), as estatísticas e os dados que você verá dos dados de transmissão já serão conhecidos.

Entre alguns dos tipos de consultas de Pesquisa de log internas estão:

- Agentes que fornecem dados de transmissão
- Endereço IP de agentes que fornecem dados de transmissão
- Comunicações de saída por endereços IP
- Número de bytes enviados por protocolos de aplicativo
- Número de bytes enviados por um serviço de aplicativo
- Bytes recebidos por diferentes protocolos
- Total de bytes enviados e recebidos por IP
- Endereços IP que se comunicaram com agentes na sub-rede 10.0.0.0/8
- Latência média de conexões que foram medidas de forma confiável
- Processos de computador que iniciaram ou receberam tráfego de rede
- Quantidade de tráfego de rede de um processo

Ao pesquisar com os dados de transmissão, é possível filtrar e agrupar os dados para exibir informações sobre os principais agentes e protocolos. Outra opção é examinar o período em que determinados computadores (endereços IP/MAC) se comunicaram entre si, a duração dessa comunicação e a quantidade de dados enviados – basicamente, são exibidos metadados sobre o tráfego de rede, que são baseados em pesquisa.

No entanto, já que você está exibindo metadados, eles não são necessariamente úteis para uma solução de problemas detalhada. Os dados de transmissão no OMS não são uma captura completa dos dados de rede. Portanto, eles não se destinam a uma solução de problemas em profundidade no nível de pacote. A vantagem de usar o agente, em comparação com outros métodos de coleta, é que você não precisa instalar dispositivos, reconfigurar os comutadores de rede nem realizar configurações complicadas. Os dados de transmissão são simplesmente baseados em agente – você instala o agente em um computador e ele monitorará seu próprio tráfego de rede. Outra vantagem é quando você desejar monitorar cargas de trabalho em execução em provedores de nuvem, em um provedor de serviços de hospedagem ou no Microsoft Azure, em que o usuário não possui a camada de malha.

Por outro lado, você não terá visibilidade completa do que ocorre na rede se não instalar agentes em todos os computadores de sua infraestrutura de rede.

## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- A solução de Dados de Transmissão obtém dados de computadores que executam o Windows Server 2012 R2, Windows 8.1 e sistemas operacionais posteriores.
- O Microsoft .NET Framework 4.0 ou posterior é necessário nos computadores dos quais você deseja obter dados de transmissão.
- Adicione a solução de Dados de Transmissão ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.
- Se você desejar exibir os dados de transmissão de uma solução específica, será necessário ter a solução já adicionada ao seu espaço de trabalho do OMS.

## Detalhes da coleta de dados dos Dados de Transmissão

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para os Dados de Transmissão.


| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows (2012 R2/8.1 ou posterior)|![Sim](./media/log-analytics-wire-data/oms-bullet-green.png)|![Sim](./media/log-analytics-wire-data/oms-bullet-green.png)|![Não](./media/log-analytics-wire-data/oms-bullet-red.png)| ![Não](./media/log-analytics-wire-data/oms-bullet-red.png)|![Não](./media/log-analytics-wire-data/oms-bullet-red.png)| a cada minuto|


## Combinando os dados de transmissão com outros dados da solução

Os dados retornados das consultas internas mostrados acima podem ser interessantes por si só. No entanto, a utilidade dos dados de transmissão é percebida quando você os combina com informações de outras soluções do OMS. Por exemplo, é possível usar dados de eventos de segurança coletados pela solução de Segurança e Auditoria e combiná-los com os dados de transmissão, com a finalidade de procurar tentativas incomuns de logon de rede para processos nomeados. Neste exemplo, você usaria os operadores IN e DISTINCT para unir pontos de dados em sua consulta de pesquisa.

Requisitos: para usar o exemplo a seguir, você precisará ter a solução de Segurança e Auditoria instalada. No entanto, é possível usar dados de outras soluções para combiná-los com os dados de transmissão, a fim de obter resultados semelhantes.

### Para combinar os dados de transmissão com eventos de segurança

1. Na página Visão geral, clique no bloco **WireData**.
2. Na lista de **Consultas Comuns de WireData**, clique em **Quantidade de Tráfego de Rede (em Bytes) por Processo** para ver a lista de processos retornados.![consultas dos dados de transmissão](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Se a lista de processos for muito longa para ser exibida com facilidade, você poderá modificar a consulta de pesquisa para que seja parecida com esta:

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    No exemplo abaixo, é mostrado um processo chamado DancingPigs.exe, que poderia parecer suspeito.![resultados da pesquisa dos dados de transmissão](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. Usando os dados retornados em sua lista, clique em um processo nomeado. Neste exemplo, DancingPigs.exe foi clicado. Os resultados mostrados abaixo descrevem o tipo de tráfego de rede, como a comunicação de saída por meio de vários protocolos.![resultados dos dados de transmissão mostrando um processo nomeado](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Visto que a solução de Segurança e Auditoria está instalada, você pode investigar os eventos de segurança que têm o mesmo valor de campo ProcessName, modificando a consulta de pesquisa com os operadores de consulta de pesquisa IN e DISTINCT. Você poderá fazer isso em seguida, quando os dados de transmissão e outros logs da solução tiverem valores no mesmo formato. Modifique sua consulta de pesquisa para que seja parecida com esta:

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![resultados dos dados de transmissão mostrando os dados combinados](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Nos resultados indicados acima, você verá que os dados da conta são mostrados. Agora você pode refinar sua consulta de pesquisa para descobrir a frequência com que a conta, mostrando os dados de Segurança e Auditoria, era usada pelo processo com uma consulta parecida com esta:        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![resultados dos dados de transmissão mostrando os dados da conta](./media/log-analytics-wire-data/oms-wiredata-05.png)



## Próximas etapas

- [Pesquise nos logs](log-analytics-log-searches.md) para exibir registros detalhados da pesquisa de dados de transmissão.
- Veja [a postagem no blog intitulada Using Wire Data in Operations Management Suite Log Search](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) (Usando os dados de transmissão na pesquisa de log do Operations Management Suite), em que Dan mostra outras informações sobre a frequência com que os dados são coletados e como você pode modificar as propriedades de coleção dos agentes do Operations Manager.

<!---HONumber=AcomDC_0518_2016-->