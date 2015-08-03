<properties 
	pageTitle="Retenção de dados e armazenamento no Application Insights" 
	description="Declaração de política de privacidade e retenção" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="awills"/>

# Coleta, retenção e armazenamento de dados no Application Insights 

*O Application Insights está em modo de visualização.*

## Visão geral

Este artigo responde a perguntas sobre os dados coletados pelo [Visual Studio Application Insights][start] e como eles são processados e armazenados.

O Application Insights é um serviço do Azure em modo de visualização. Enquanto no modo de visualização, estamos trabalhando para proteger seus dados segundo as políticas descritas no [White paper Segurança, privacidade e conformidade do Azure](http://go.microsoft.com/fwlink/?linkid=392408).


## Coleção

#### Como os dados são coletados pelo Application Insights?

Os agentes e SDKs que você combina com o seu aplicativo enviam dados para o serviço do Application Insights. Os dados são processados pelo serviço para fornecer a você relatórios, alertas e outras funcionalidades. Isso pode incluir dados que você escolha capturar usando a API, por exemplo, em propriedades e eventos personalizados.

#### Quantos dados podem ser capturados? 

**Por segundo**: até 500 pontos de dados por segundo por chave de instrumentação (ou seja, por aplicativo). Para a [faixa de preços][pricing] gratuita, o limite é 100 dp/s.

**Mensal**: entre 5 e 15 milhões de pontos de dados por mês, dependendo de seu [plano de preços](http://azure.microsoft.com/pricing/details/application-insights/). Com exceção da [faixa de preços][pricing] gratuita, você pode adquirir capacidade adicional se tiver atingido o limite.


Um *ponto de dados* é um item de telemetria, tal como:

* Chamadas `Track...` de API, como `TrackEvent` ou `trackPageView`.
* Itens de telemetria enviados por módulos do SDK, por exemplo, para relatar uma solicitação ou falha.
* Dados do contador de desempenho - um ponto para cada medida.

*Como posso saber quantos pontos de dados meu aplicativo está enviando?*

* Abra Configurações/Cotas e Preços para ver o gráfico de Volume de dados.
* Ou, no Metrics Explorer, adicione um novo gráfico e selecione **Volume de pontos de dados** como a métrica. Ative o Agrupamento e agrupe por **Tipo de dados**.


#### Por quanto tempo os dados são mantidos? 

Depende do seu [plano de preços](http://azure.microsoft.com/pricing/details/application-insights/).

Pontos de dados brutos (ou seja, itens que você pode inspecionar na pesquisa de diagnóstico): entre 7 e 30 dias.

Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Metric Explorer) são mantidos em um detalhamento de 1 minuto por 30 dias, e 1 hora ou 1 dia (dependendo do tipo) por 13 meses.

#### Que limites existem em diferentes tipos de dados?

1.	Máximo de 200 nomes de métrica exclusivos e 200 nomes de propriedade exclusivos para seu aplicativo. As métricas incluem o envio de dados por meio de TrackMetric, bem como as medidas em outros tipos de dados, como Eventos. Os[ nomes de propriedades e métricas][api] são globais por chave de instrumentação, mas não têm escopo definido segundo o tipo de dados.
2.	As [propriedades][apiproperties] podem ser usadas para filtragem e agrupamento, somente enquanto tiverem menos de 100 valores exclusivos para cada propriedade. Depois que os valores exclusivos excederem 100, a propriedade ainda pode ser usada para pesquisa e filtragem, mas não para filtros.
3.	As propriedades padrão como Solicitar Nome e URL da Página são limitadas a 1.000 valores exclusivos por semana. Após 1.000 valores exclusivos, os valores adicionais serão marcados como “Outros valores”. O valor original ainda pode ser usado para filtragem e pesquisa de texto completo.


## Access

#### Quem pode ver os dados?

Os dados são visíveis para você e, se você tiver uma conta de organização, para os membros de sua equipe.

Eles podem ser exportados por você e pelos membros da equipe e podem ser copiados para outros locais e passados para outras pessoas.

#### O que a Microsoft faz com as informações que meu aplicativo envia ao Application Insights?

A Microsoft usa os dados apenas para fornecer o serviço a você.


## Local

#### Onde os dados são mantidos? 

* Nos EUA. 

#### Eles podem ser armazenados em outro local, por exemplo na Europa? 

* Ainda não. 

## Segurança 

#### Quão seguros meus dados estão? 

Os dados são armazenados em servidores do Microsoft Azure. Para contas no Portal de Visualização do Azure, as restrições de conta são descritas no documento de [Segurança, privacidade e conformidade do Azure](http://go.microsoft.com/fwlink/?linkid=392408). Para contas no Portal Online do Visual Studio, aplicam-se as informações contidas no documento [Proteção de dados do Visual Studio Online](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf).

É restrito o acesso aos seus dados por funcionários da Microsoft. Podemos acessar seus dados com a sua permissão e se isso for necessário para o seu uso do Application Insights.

Dados agregados em aplicativos de todos os nossos clientes (como taxas de dados e tamanho médio dos rastreamentos) são usados para melhorar o Application Insights.

#### A telemetria de outra pessoa poderia interferir meus dados do Application Insights?

Eles poderiam enviar telemetria adicional para sua conta usando a chave de instrumentação, que pode ser encontrada no código de suas páginas da Web. Com dados suficientes adicionais, suas métricas não representariam corretamente o uso e o desempenho do seu aplicativo.

Se você compartilhar código com outros projetos, lembre-se de remover sua chave de instrumentação.

## Criptografia

#### Os dados são criptografados no servidores do Application Insights? 

Não dentro dos servidores no momento.

Todos os dados são criptografados conforme se movem entre datacenters.

#### Os dados são criptografados em trânsito do meu aplicativo para servidores do Application Insights?

Sim, podemos usar https para enviar dados para o portal de quase todos os SDKs, incluindo servidores Web, dispositivos e páginas da Web em HTTPS. A única exceção é que os dados enviados de páginas da Web em HTTP simples.

## Informações pessoalmente identificáveis

#### Podem ser enviadas informações pessoalmente identificáveis (PII) para o Application Insights? 

Sim.

Como orientação geral:

* A telemetria mais padrão (ou seja, telemetria enviada sem que você escreva nenhum código) não inclui PII explícitas. No entanto, é possível identificar indivíduos por inferência de uma coleção de eventos.
* Mensagens de exceção e rastreamento podem conter informações de identificação pessoal
* Telemetria personalizada - ou seja, chamadas como TrackEvent que você escreve em código usando a API ou os rastreamentos de log - podem conter qualquer dado que você escolher.


A tabela ao final deste documento contém descrições mais detalhadas dos dados coletados.



#### Eu sou responsável por manter a conformidade com as leis e regulamentações relativas às informações de identificação pessoal?

Sim. É sua responsabilidade assegurar que a coleta e uso de dados estejam em conformidade com as leis e normas e também com os termos do Microsoft Online Services.

Você deve informar os clientes adequadamente quanto aos dados que seu aplicativo coleta e como tais dados são usados.

#### Meus usuários podem desativar o Application Insights?

Não diretamente. Não fornecemos um comutador que os usuários possam operar para desativar o Application Insights.

No entanto, você pode implementar tal recurso em seu aplicativo. Todos os SDKs incluem uma configuração de API que desativa a coleta da telemetria.

#### Meu aplicativo está coletando informações confidenciais acidentalmente. O Application Insights pode eliminar esses dados para que eles não sejam retidos?

O Application Insights não filtra nem exclui seus dados. Você deve gerenciar os dados adequadamente e evitar enviar esses dados para o Application Insights.



## Dados enviados pelo Application Insights

Os SDKs variam entre diferentes plataformas, e há vários componentes que você pode instalar. (Consulte [Application Insights - introdução][start].) Cada componente envia dados diferentes.

#### Classes de dados enviados em cenários diferentes

Sua ação | Classes de dados coletados (consulte a tabela a seguir)
---|---
[Adicionar o Application Insights SDK a um projeto Web .NET][greenbrown] | ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Solicitações<br/>**Exceções**<br/>Sessão<br/>usuários
[Monitor de status de instalação no IIS][redfield]<br/>[Adicionar extensão AI a uma VM do Azure ou aplicativo Web][azure]|Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho
[Adicionar o Application Insights a um aplicativo Web Java][java]|ServerContext<br/>Inferido<br/>Solicitação<br/>Sessão<br/>usuários
[Adicionar SDK do JavaScript à página da Web][client]|ClientContext <br/>Inferido<br/>Página<br/>ClientPerf
[Adicionar o SDK a aplicativo da Windows Store][windows]|DeviceContext<br/>Usuários<br/>Falha de dados
[Definir propriedades padrão][apiproperties]|**Propriedades** em todos os eventos padrão e personalizados
[Chamar TrackMetric][api]|Valores numéricos<br/>**Propriedades**
[Chamar Track*][api]|Nome do evento<br/>**Propriedades**
[Chamar TrackException][api]|**Exceções**<br/>Despejo da pilha<br/>**Propriedades**
O SDK não é capaz de coletar dados. Por exemplo: <br/> - não é possível acessar os contadores de desempenho<br/> - exceção no inicializador de telemetria | Diagnóstico do SDK
 

Para [SDKs para outras plataformas][platforms], consulte seus respectivos documentos.



#### As classes dos dados coletados

Classe de dados coletados | Inclui (não é uma lista completa) 
---|---
**Propriedades**|**Quaisquer dados - determinados pelo seu código**
DeviceContext |ID, IP, localidade, modelo de dispositivo, rede, tipo de rede, nome OEM, resolução de tela, instância de função, nome da função, tipo de dispositivo
ClientContext |Sistema operacional, localidade, linguagem, rede, resolução da janela
Session | ID da sessão
ServerContext |Nome do computador, localidade, sistema operacional, dispositivo, sessão de usuário, contexto de usuário, operação 
Inferido |localização geográfica do endereço IP, carimbo de data/hora, sistema operacional, navegador
Métricas | Valor e nome da métrica
Eventos | Valor e nome do evento
PageViews | URL e nome da página ou o nome de tela
Desempenho do cliente | URL/nome de página, tempo de carregamento do navegador
Solicitações |URL, duração, código de resposta
Dependências|Tipo (SQL, HTTP,...), cadeia de conexão ou URI, síncrono/assíncrono, duração, sucesso, instrução SQL (com Monitor de Status)
**Exceções** | Tipo, **mensagem**, pilhas de chamadas, arquivo-fonte e número de linha, ID do thread
Falhas | ID do processo, ID do processo-pai, ID de thread de falha; patch do aplicativo, ID de compilação; tipo de exceção, endereço, motivo; símbolos e registros ofuscados, endereços binários de início e término, nome e caminho binários, tipo de CPU
Rastreamento | **Mensagem** e nível de severidade
Contadores de desempenho | Tempo do processador, memória disponível, taxa de solicitação, taxa de exceções, bytes particulares do processo, taxa de E/S, duração da solicitação, comprimento da fila de solicitações
Disponibilidade | Código de resposta de teste da Web, duração de cada etapa de teste
Diagnóstico do SDK | Mensagem de rastreamento ou exceção 

Você pode [desativar alguns dos dados editando ApplicationInsights.config][config]


## Créditos

Este produto inclui dados GeoLite2 criados pelo MaxMind, disponíveis em [http://www.maxmind.com](http://www.maxmind.com).

## <a name="video"></a>Vídeos

#### Introdução

> [AZURE.VIDEO application-insights-introduction]

#### Introdução

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO4-->