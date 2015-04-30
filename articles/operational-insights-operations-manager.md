<properties 
   pageTitle="Considerações sobre o Operations Manager com Insights Operacionais"
   description="Se você usar o Insights Operacionais do Microsoft Azure com o Operations Manager, sua configuração utiliza uma distribuição de agentes do Operations Manager e grupos de gerenciamento para coletar e enviar dados para o serviço de informações operacionais para análise"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Considerações sobre o Operations Manager com Insights Operacionais

Se você usar o Insights Operacionais do Microsoft Azure com o Operations Manager, sua configuração se baseia em uma distribuição de grupos de gerenciamento e agentes do Operations para coletar e enviar dados para o serviço de Insights Operacionais para análise. No entanto, se você usar agentes que se conectam diretamente ao serviço Web, você não precisa do Operations Manager. Considere os seguintes problemas ao usar o Insights Operacionais com o Operations Manager.

## Requisitos e funções do software Insights Operacionais

O Insights Operacionais consiste em um serviço Web na nuvem e os agentes que se conectam diretamente ao serviço Web ou os agentes do Operations Manager e grupos de gerenciamento do Operations Manager que estão gerenciando computadores no seu ambiente.

Antes de selecionar os agentes do Operations Manager (para coletar dados) e grupos de gerenciamento (para gerenciar agentes e enviar dados para o serviço de informações operacionais), certifique-se de compreender o seguinte:

- O agente do Operations Manager é instalado em qualquer servidor do qual você deseja coletar e analisar dados.

- O grupo de gerenciamento do Operations Manager transfere dados dos agentes para o serviço Web de Insights Operacionais. Ele não analisa nenhum dos dados.

- O grupo de gerenciamento do Operations Manager deve ter acesso à Internet para carregar dados para o serviço Web.

- Para obter melhores resultados, não instale o servidor de gerenciamento do Operations Manager em um computador que também seja um controlador de domínio.

- Um agente do Operations Manager deve ter conectividade de rede para o grupo de gerenciamento do Operations Manager para que possa transferir dados.

O Insights Operacionais pode usar o serviço de integridade do System Center para coletar e analisar dados. O Operations Manager depende do serviço de integridade do System Center. Ao exibir os programas instalados no seu servidor, você verá o software de agente do System Center Operations Manager, especialmente em Adicionar/Remover Programas. Não os remova, pois o Insights Operacionais depende deles. Se você remover o software do agente do Operations Manager, o Insights Operacionais deixará de funcionar.

## Coexistência com o Operations Manager

Ao usar o Operations Manager, o Insights Operacionais dá suporte apenas com o agente do Operations Manager no System Center Operations Manager 2012 R2 ou no System Center Operations Manager 2012 SP1. Não há suporte para as versões anteriores do System Center Operations Manager. Como o agente do Operations Manager é usado para coletar dados, ele usa credenciais específicas (contas de ação ou contas Executar como) para dar suporte a algumas das cargas de trabalho analisadas, como o SharePoint 2012.

## Insights operacionais e SQL Server 2012

Ao usar o Operations Manager, o serviço de integridade do System Center é executado sob a conta do Sistema Local. Em versões do SQL Server anteriores ao SQL Server 2008 R2, a conta do Sistema Local era habilitada por padrão e era um membro da função de servidor do administrador do sistema. No SQL Server 2012, o logon de Sistema Local não fazia parte da função de servidor do administrador do sistema. Como resultado, quando você usa o Insights Operacionais, ele não pode monitorar a instância do SQL Server 2012 completamente e nem todas as regras podem gerar alertas.

## Conectividade de rede interna e da Internet

Ao usar agentes que se conectam diretamente com o serviço Web, eles precisam acessar a Internet para enviar dados para o serviço Web e receber informações de configuração atualizada do serviço Web.

Ao usar o Operations Manager, o servidor de gerenciamento precisa acessar a Internet para enviar dados para o serviço Web e receber informações de configuração atualizada do serviço Web. No entanto, os seus agentes não precisa acessar a Internet. Se você tiver agentes do Operations Manager em servidores que não estejam conectados à Internet, pode usar o serviço Web se eles puderem se comunicar com um servidor de gerenciamento conectado à Internet.

## Suporte a cluster

Há suporte para o agente do Operations Manager em computadores que executam o Windows Server 2012, o Windows Server 2008 R2 ou o Windows Server 2008 que está configurado para fazer parte de um cluster de failover do Windows. Você pode exibir os clusters no portal de Insights Operacionais. Na página servidores, os clusters são identificados como TYPE=CLUSTER (em vez de TYPE=AGENT, que é a maneira como computadores físicos são identificados).

As regras de descoberta e configuração são executadas nos nós ativos e passivos do cluster, mas nenhum alerta gerado em nós passivos é ignorado. Se um nó passar de passivo para ativo, os alertas para esse nó são exibidos automaticamente, sem nenhuma intervenção humana.

Alguns alertas podem ser gerados duas vezes, dependendo da regra que gerou o alerta. Por exemplo, uma regra que detecta um driver inadequado examinando o sistema operacional gera alertas para o servidor físico em cluster.

Não há suporte para a análise de configuração de nós passivos.

O Insights Operacionais não dá suporte para agrupar ou vincular computadores executando o Windows Server que fazem parte do mesmo cluster de failover.

## Colocação em escala do ambiente do Insights Operacionais

Ao planejar sua implantação do Insights Operacionais (especialmente ao analisar o número de agentes do Operations Manager que você deseja transferir dados através de um único grupo de gerenciamento), considere a capacidade do servidor em termos de espaço de arquivo.

Considere as seguintes variáveis:

- Número de agentes por grupo de gerenciamento

- O tamanho médio dos dados transferidos do agente para o grupo de gerenciamento por dia. Por padrão, cada agente carrega arquivos CAB para o grupo de gerenciamento duas vezes por dia. O tamanho dos arquivos CAB depende da configuração do servidor (como o número de mecanismos de SQL Server e bancos de dados de números) e a integridade do servidor (como o número de alertas gerados). Na maioria dos casos, o tamanho de carregamento diário normalmente é menor que 100 KB.

- Período de arquivamento para manter os dados no grupo de gerenciamento (o padrão é de 5 dias)

Por exemplo, se você presumir um tamanho de carregamento diário de 100 KB por agente e o período de arquivamento padrão, precisará do armazenamento a seguir para o grupo de gerenciamento:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Número de agentes</b></td>
		<td><b>Espaço estimado necessário para o grupo de gerenciamento</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2,5 MB (5 agentes x 100 KB dados/dia x 5 dias = 2.500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 agentes x 100 KB dados/dia x 5 dias = 25.000 KB)</td>
    </tr>

    </tbody>
    </table>

## Localizações geográficas

Se você deseja analisar os dados dos servidores em diferentes localizações geográficas, considere ter um grupo de gerenciamento por local. Isso pode melhorar o desempenho de transferência de dados do agente para o grupo de gerenciamento.


<!--HONumber=52-->