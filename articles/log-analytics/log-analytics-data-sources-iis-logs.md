<properties 
   pageTitle="Logs do IIS no Log Analytics | Microsoft Azure"
   description="O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Log Analytics. Este artigo descreve como configurar a coleta de logs do IIS e os detalhes dos registros que eles criam no repositório do OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Logs do IIS no Log Analytics
O IIS (Serviços de Informações da Internet) armazena a atividade do usuário em arquivos de log que podem ser coletados pelo Log Analytics.

![Logs IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## Configurando logs do IIS
O Log Analytics coleta entradas de arquivos de log criados pelo IIS, por isso você deve [configurar o IIS para o registro em log](https://technet.microsoft.com/library/hh831775.aspx) e selecionar os campos que você deseja que o Log Analytics colete. O IIS não registra todos os campos por padrão, portanto você pode desejar selecionar manualmente os campos adicionais além dos campos padrão.

O Log Analytics dá suporte apenas a arquivos de log do IIS armazenados em formato W3C. Ele não coleta logs no formato nativo IIS ou NCSA.

Configure os logs do IIS no [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md/configuring-data-sources). Não há nenhuma outra configuração necessária além da seleção de **Collect W3C format IIS log files** (Coletar arquivos de log do IIS no formato W3C).


## Coleta de dados

O Log Analytics coletará as entradas de log do IIS de cada fonte conectada aproximadamente a cada 15 minutos. O agente registrará seu lugar em cada log de eventos do qual ele realiza a coleta. Se o agente ficar offline por um período de tempo, o Log Analytics coletará os eventos de onde ele parou, mesmo que os eventos tenham sido criados enquanto o agente estava offline.


## Propriedades de registro de log do IIS

Os registros log do IIS têm um tipo de **W3CIISLog** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Computador | Nome do computador do qual o evento foi coletado. |
| cIP | Endereço IP do cliente. |
| csMethod | Método de solicitação, como GET ou POST. |
| csReferer | Site do qual o usuário seguiu um link para o site atual. |
| csUserAgent | O tipo de navegador do cliente. |
| csUserName | Nome do usuário autenticado que acessou o servidor. Os usuários anônimos são indicados por um hífen. |
| csUriStem | Destino da solicitação, como uma página da Web. |
| csUriQuery | Consulta, se houver, que o cliente estava tentando executar. |
| ManagementGroupName | Nome do grupo de gerenciamento de agentes do SCOM. Para outros agentes, ele é AOI-<ID do espaço de trabalho> |
| RemoteIPCountry | País do endereço IP do cliente. |
| RemoteIPLatitude | Latitude do endereço IP do cliente. |
| RemoteIPLongitude | Longitude do endereço IP do cliente. |
| scStatus | Código de status HTTP. |
| scSubStatus | Código de erro de substatus. |
| scWin32Status | Código de status do Windows. |
| sIP | Endereço IP do servidor Web. |
| SourceSystem | OpsMgr |
| sPort | Porta no servidor ao qual o cliente está conectado. |
| sSiteName | Nome do site do IIS. |
| TimeGenerated | Data e hora em que a entrada foi registrada. |
| TimeTaken | Período para processar a solicitação em milissegundos. |

## Pesquisas de log com logs do IIS

A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros do log do IIS.

| Consultar | Descrição |
|:--|:--|
| Type=IISLog | Todos os registros de log do IIS. |
| Type=IISLog EventLevelName=error | Todos os eventos do Windows com severidade de erro. |
| Type=W3CIISLog | Measure count() by cIP | Contagem das entradas do log do IIS por endereço IP do cliente. |
| Type=W3CIISLog csHost="www.contoso.com" | Measure count() by csUriStem | Contagem das entradas de log do IIS por URL para o host www.contoso.com. |
| Type=W3CIISLog | Measure Sum(csBytes) by Computer | top 500000| Total de bytes recebidos por cada computador com IIS. |

## Próximas etapas

- Configure o Log Analytics para coletar outras [fontes de dados](log-analytics-data-sources.md) para análise.
- Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 
- Configure alertas no Log Analytics para notificá-lo proativamente sobre condições importantes encontradas em logs do IIS.

<!---HONumber=AcomDC_0504_2016-->