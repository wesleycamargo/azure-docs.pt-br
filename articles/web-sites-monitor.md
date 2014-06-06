<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="Como monitorar" pageTitle="Como monitorar sites - Gerenciamento de serviços do Azure" metaKeywords="Monitoramento de sites do Azure, Monitor do Portal de Gerenciamento do Azure, monitoramento do Azure" description="Saiba como monitorar sites do Azure usando a página Monitoramento no Portal de Gerenciamento." metaCanonical="" services="web-sites" documentationCenter="" title="Como monitorar Sites" authors="" solutions="" manager="" editor="" />



#<a name="howtomonitor"></a>Como monitorar Sites

Sites oferecem recurso de monitoramento por meio da página de gerenciamento Monitoramento. A página de gerenciamento Monitoramento fornece estatísticas de desempenho para um site, conforme descrito abaixo.

## Sumário ##
- [Como adicionar métricas do site](#websitemetrics)
- [Como receber alertas de métricas de site](#howtoreceivealerts)
- [Como exibir cotas de uso de um site](#howtoviewusage)
- [Como reduzir o uso de recursos](#resourceusage)
- [O que acontece quando uma cota de uso de recursos é excedida](#exceeded)
- [Como configurar o diagnóstico e baixar para um site](#howtoconfigdiagnostics)
- [Como monitorar o status de ponto de extremidade da Web](#webendpointstatus)

##<a name="websitemetrics"></a>Como: adicionar métricas do site
1. No [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/), nas páginas de gerenciamento do site, clique na guia **Monitoramento** para exibir a página de gerenciamento **Monitoramento**. Por padrão, o gráfico na página **Monitoramento** exibe as mesmas métricas do gráfico na página **Painel**. 

2. Para exibir métricas adicionais do site, clique em **Adicionar métricas** na parte inferior da página para exibir a caixa de diálogo **Escolher métricas**. 

3. Clique para selecionar métricas adicionais para exibição na página **Monitoramento**. 

4. Depois de selecionar as métricas que deseja adicionar à página **Monitor**, clique em **Ok**. 

5. Depois de adicionar a métrica à página **Monitoramento**, clique para habilitar/desabilitar a caixa de opção ao lado de cada métrica para adicionar/remover a métrica do gráfico na parte superior da página.

6. Para remover as métricas da página **Monitoramento**, selecione a métrica que você deseja remover e, em seguida, clique no ícone **Excluir Métrica** na parte inferior da página.

A lista a seguir descreve as métricas que podem ser exibidas no gráfico na página **Monitor**:

- **CPUTime** - Uma medida do uso de CPU do site.
- **Solicitações** - Uma contagem de todas as solicitações de cliente para o site.
- **Saída de Dados** - Uma medida de dados enviados pelo site aos clientes.
- **Entrada de Dados** - Uma medida dos dados recebidos pelo site de clientes.
- **Erros do Cliente Http** - Número de mensagens Http "Erro do cliente 4xx" enviadas.
- **Erros do Servidor HTTP** - Número de mensagens HTTP "Erro de servidor 5xx" enviadas.
- **Êxito de Http** - Número de mensagens Http "2xx sucesso" enviadas.
- **Redirecionamentos Http** - Número de mensagens Http "3xx redirecionamento" enviadas.
- **Erros 401 de Http** - Número de mensagens Http "401 não autorizado" enviadas.
- **Erros 403 de Http** - Número de mensagens Http "403 Proibido" enviadas.
- **Erros 404 de Http** - Número de mensagens Http "404 não encontrado" enviadas.
- **Erros 406 de Http** - Número de mensagens Http "406 não aceitável" enviadas.

##<a name="howtoreceivealerts"></a>Como: receber alertas de métricas de site
No modo de site **padrão**, você pode receber alertas com base nas métricas de monitoramento de seu site. O recurso de alerta requer que você primeiro configure um ponto de extremidade da web para monitoramento, que você pode fazer na seção **Monitoramento** da página **Configurar**. Na página **Configurações** do Portal de Gerenciamento do Azure, você pode criar uma regra para acionar um alerta quando a métrica escolhida atingir um valor especificado. Você também pode escolher que um email seja enviado quando o alerta é disparado. Para obter mais informações, consulte [Como receber notificações de alerta e gerenciar regras de alerta no Azure](http://go.microsoft.com/fwlink/?LinkId=309356).  

##<a name="howtoviewusage"></a>Como: exibir cotas de uso de um site

Os sites podem ser configurados para serem executados em modo de site **compartilhado** ou **padrão** a partir da página de gerenciamento **Escala** do site. Cada assinatura do Azure tem acesso a um pool de recursos fornecidos com a finalidade de execução de até 100 sites por região em modo de site **compartilhado**. O pool de recursos disponíveis para cada assinatura de site para essa finalidade é compartilhado por outros sites na mesma localização geográfica configurados para serem executados em modo **compartilhado**. Como esses recursos são compartilhados para uso por outros sites, todas as assinaturas são limitadas em termos de uso desses recursos. Limites aplicados ao uso da assinatura desses recursos são expressos como cotas de uso listadas na seção de visão geral sobre o uso da página de gerenciamento **Painel** de cada site.

**Observação**  
Quando um site está configurado para executar no modo **Padrão**, são alocados recursos dedicados equivalentes aos tamanhos das máquinas virtuais **Pequeno** (padrão), **Médio** ou **Grande** na tabela em [Tamanhos de máquinas virtuais e de serviços de nuvem do Azure][vmsizes]. Não há limites para os recursos usados por uma assinatura para a execução de sites no modo **padrão**. No entanto, o número de sites no modo **padrão** que podem ser criados por região é 500.
 
### Exibindo o uso de cotas de sites configurados para o modo de site compartilhado ###
Para determinar a extensão do impacto de um site sobre cotas de uso de recursos, siga estas etapas:

1. Abra a página de gerenciamento **Painel** do site.
2. Na seção **Visão geral sobre o uso** são exibidas as cotas de uso de **saída de dados**, **Tempo de CPU** e **Armazenamento do Sistema de Arquivos**. A barra verde exibida para cada recurso indica quanto da cota de uso de recursos da assinatura está sendo consumida por um site atual e a barra cinza exibida para cada recurso indica quanto da cota de uso de recursos da assinatura está sendo consumida por todos os outros sites em modo compartilhado associados à sua assinatura.

As cotas de uso de recurso ajudam a evitar a superutilização dos recursos a seguir:

- **saída de dados** - uma medida da quantidade de dados enviados de sites em execução no modo **Compartilhado** a seus clientes no intervalo da cota atual (24 horas).
- **Tempo de CPU** - a quantidade de tempo de CPU usado por sites em execução no modo **Compartilhado** para o intervalo da cota atual.
- **Armazenamento do Sistema de Arquivos** -- A quantidade de armazenamento do sistema de arquivos em uso por sites em execução no modo **Compartilhado**.

Quando as cotas de uso da assinatura são excedidas, o Azure executa uma ação para interromper o uso excessivo de recursos. Isso é feito para evitar que qualquer assinante esgote os recursos a ponto de prejudicar outros assinantes.


##<a name="resourceusage"></a>Como: reduzir o uso de recursos

Como o Azure calcula as cotas de uso de recursos medindo os recursos usados por sites no modo compartilhado da assinatura durante um intervalo da cota de 24 horas, considere o seguinte:

- Conforme o número de sites configurados para execução no modo Compartilhado aumenta, também aumenta a probabilidade de exceder as cotas de uso do modo compartilhado.
Considere a possibilidade de reduzir o número de sites que estão configurados para execução no modo compartilhado, caso as cotas de uso de recurso sejam excedidas.
- De forma semelhante, conforme aumenta o número de instâncias de qualquer site em execução no modo Compartilhado, também aumenta a probabilidade de exceder as cotas de uso no modo compartilhado.
Considere a possibilidade de dimensionamento adicional de instâncias de sites de modo compartilhado, caso as cotas de uso de recursos sejam excedidas.


##<a name="exceeded"></a>O que acontece quando uma cota de uso de recursos é excedida

O Azure executará as seguintes ações se as cotas de uso de recursos da assinatura forem excedidas em um intervalo da cota (24 horas):

 - **saída de dados** - quando essa cota é excedida, o Azure interrompe todos os sites de uma assinatura que estão configurados para serem executados no modo **Compartilhado** durante o restante do intervalo da cota atual. O Azure iniciará os sites no início do próximo intervalo da cota.

 - **Tempo de CPU** - quando essa cota é excedida, o Azure interrompe todos os sites de uma assinatura que estão configurados para serem executados no modo **Compartilhado** durante o restante do intervalo da cota atual. O Azure iniciará os sites no início do próximo intervalo da cota.

 - **Armazenamento do Sistema de Arquivos** - O Azure impede a implantação de qualquer site de uma assinatura que esteja configurada para execução em modo Compartilhado, caso a implantação exceda a cota de uso do armazenamento do sistema de arquivos. Quando o recurso de armazenamento de sistema de arquivo chega ao tamanho máximo permitido por sua cota, o armazenamento de sistema de arquivo permanece acessível para operações de leitura, mas todos as operações de gravação, incluindo as atividades normais para a tividade do site, são bloqueadas. Quando isso ocorre, você pode configurar um ou mais sites em execução em modo compartilhado para executar em modo padrão ou reduzir o uso do armazenamento do sistema de arquivos, ficando abaixo da cota de uso de armazenamento do sistema de arquivos.




##<a name="howtoconfigdiagnostics"></a>Como configurar o diagnóstico e baixar para um site

Diagnóstico está habilitado na página de gerenciamento **Configurar** do site. Há dois tipos de diagnóstico: **um diagnóstico dos aplicativos** e **diagnósticos do site**.

####Diagnóstico de Aplicativo####

A seção **diagnóstico de aplicativo** da página de gerenciamento **Configurar** controla o log de informações produzido pelo aplicativo, o que é útil ao registrar eventos que ocorre dentro de um aplicativo. Por exemplo, quando ocorre um erro em seu aplicativo, você poderá apresentar ao usuário um erro amigável ao gravar informações de erro mais detalhadas no log para análise posterior.

Você pode ativar ou desativar os seguintes diagnósticos de aplicativo:

- **Log de Aplicativo (Sistema de Arquivos)** - Ativa o log de informações produzidas pelo aplicativo. O campo **Nível de log** determina se as informações do nível de erro, aviso ou informações são registradas. Você também pode selecionar Detalhado, que registrará todas as informações geradas pelo aplicativo.

	Logs gerados por essa configuração são armazenadas no sistema de arquivos do seu site e podem ser baixados usando as etapas na seção **Baixando arquivos de log de um site** abaixo.

- **Log de Aplicativo (Armazenamento da Tabela)** - Ativa o log de informações geradas pelo aplicativo, semelhante à opção Log de aplicativo (Sistema de Arquivos). No entanto, as informações de log são armazenadas em uma conta de armazenamento do Azure em uma tabela.

	Para especificar a conta de armazenamento do Azure e a tabela, escolha **Ativado**, selecione o **Nível de log** e, em seguida, escolha **Gerenciar Armazenamento de Tabelas**. Especifique a conta de armazenamento e a tabela a ser usada ou crie uma nova tabela.

	As informações de log armazenadas na tabela podem ser acessadas usando um cliente de Armazenamento do Azure.

- **Log de Aplicativo (Armazenamento do blob)** - Ativa o log de informações geradas pelo aplicativo, semelhante à opção Log de Aplicativo (Armazenamento de Tabela). No entanto, as informações de log são armazenadas em um blob na conta de armazenamento do Azure.

	Para especificar a Conta de Armazenamento e o blob do Azure, escolha **Ativado**, selecione o **Nível de log** e, em seguida, escolha **Gerenciar Armazenamento de Blobs**. Especifique a conta de armazenamento, o contêiner de blob e o nome de blob a ser usado ou crie um novo contêiner e blob.

Para obter mais informações sobre Contas de Armazenamento do Azure, consulte [Como gerenciar contas de armazenamento (a página pode estar em inglês)](https://www.windowsazure.com/pt-br/manage/services/storage/how-to-manage-a-storage-account/).

<div class="dev-callout"> 
<b>Observação</b> 
<p>Log de aplicativo para o armazenamento de tabela ou blob só é suportado para aplicativos .NET.</p> </div>

Como o log do aplicativo no armazenamento requer o uso de um cliente de armazenamento para exibir os dados do log, ele será mais útil quando você planejar usar um serviço ou aplicativo que saiba como ler e processar os dados diretamente no Armazenamento de Tabela ou de Blob do Azure. O log do sistema de arquivos produz arquivos que podem ser baixados no computador local usando FTP ou outros utilitários conforme descrito mais adiante nesta seção.

<div class="dev-callout"> 
	<b>Observação</b> 
	<p><b>Diagnóstico de Aplicativo (sistema de arquivos)</b>, <b>Diagnóstico de Aplicativo (armazenamento de tabela)</b>, e <b>Diagnóstico de Aplicativo (armazenamento de blob)</b> podem ser ativados ao mesmo tempo e ter configurações de nível de log individuais. Por exemplo, você poderá registrar erros e avisos no armazenamento como uma solução a longo prazo do log, ao mesmo tempo em que habilita o log de sistema de arquivos com um nível de detalhe após instrumentar o código do aplicativo para solucionar um problema.</p> </div>

<div class="dev-callout"> 
	<b>Observação</b> 
	<p>O diagnóstico também pode ser habilitado no PowerShell do Azure usando o cmdlet <b>Set-AzureWebsite</b>.</p><p>Se você não tiver instalado o PowerShell do Azure ou não o tiver configurado para usar sua assinatura do Azure, consulte <a href="http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/">Como usar o PowerShell do Azure</a>.</p></div>

<div class="dev-callout"> 
<b>Observação</b> 
<p>O log de aplicativo se baseia em informações de log geradas pelo seu aplicativo. O método usado para gerar informações de log, bem como o formato das informações, é específico para o idioma em que seu aplicativo é gravado. Para obter informações específicas de idioma sobre o uso do log de aplicativo, consulte os seguintes artigos:</p>
<ul>
<li><b>.NET</b> - <a href="/pt-br/develop/net/common-tasks/diagnostics-logging-and-instrumentation/">Habilitar o registro em log de diagnóstico para Sites do Azure</a></li>
<li><b>Node.js</b> - <a href="/pt-br/develop/nodejs/how-to-guides/Debug-Website/">Como depurar um aplicativo Node.js nos Sites do Azure</a></li>
</ul>
<p>Log de aplicativo para o armazenamento de tabela ou blob só é suportado para aplicativos .NET.</p>
</div>

####Diagnóstico de Site####

A seção **Diagnóstico de Site** da página de gerenciamento **Configurar** controla o registro de log executado pelo servidor web, como o log de solicitações da web, falha ao servir páginas ou quanto tempo demorou para servir a uma página. Você pode ativar ou desativar as seguintes opções:

- **Log de Servidor Web** - Ative o log de Servidor Web para salvar logs do site usando o formato de arquivo de log estendido W3C. Log do servidor Web gera um registro de todas as solicitações recebidas para seu site, que contém informações como o endereço IP do cliente, URI solicitado, código de status HTTP de resposta e a sequência de agente do usuário do cliente. Você pode salvar os logs em uma Conta de Armazenamento do Azure ou no Sistema de Arquivos.

 Para salvar os logs de servidor web em uma Conta de Armazenamento do Azure, escolha **Armazenamento** e, em seguida, escolha **gerenciar armazenamento** para especificar uma conta de armazenamento e um Contêiner de Blob do Azure em que os logs serão mantidos. Para obter mais informações sobre Contas de Armazenamento do Azure, consulte [Como gerenciar contas de armazenamento (a página pode estar em inglês)](https://www.windowsazure.com/pt-br/manage/services/storage/how-to-manage-a-storage-account/).

   Para salvar os logs do servidor web em um sistema de arquivos, escolha **Sistema de arquivos**. Isso habilita a caixa **Cota** onde você pode definir a quantidade máxima de espaço em disco para os arquivos de log. O tamanho mínimo é de 25MB e o máximo é de 100MB. O tamanho padrão é 35MB.

 Por padrão, os logs do servidor web nunca são excluídos. Para especificar um período de tempo após o qual os registros serão excluídos automaticamente, selecione **Definir retenção** e digite o número de dias para manter os logs na caixa **Período de retenção**. Essa configuração está disponível para as opções de Armazenamento do Azure e Sistema de Arquivos.

- **Mensagens de Erro Detalhadas** - Ative o log erros detalhado para registrar em log informações adicionais sobre erros HTTP (códigos de status maiores que 400).

- **Falha no Rastreamento de Solicitação** - Ative rastreamento de solicitação com falha para capturar informações de solicitações de cliente com falha, como um código de status HTTP da série 400.  A falha no rastreamento de solicitação produz um documento XML que contém um rastreamento de quais módulos de solicitação foram passadas no IIS, detalhes retornados pelo módulo e o momento em que o módulo foi chamado. Essas informações podem ser usadas para isolar o componente em que a falha ocorreu.


Depois de habilitar o diagnóstico para um site, clique no ícone **Salvar** na parte inferior da página de gerenciamento **Configurar** aplicar as opções que você definiu.

<div class="dev-callout">
<b>Importante</b> 
<p>Log e rastreamento colocam exigências significativas em um site. É recomendável desativar o registro e o rastreamento depois de reproduzir o problema (s) que você está solucionando.</p> 
</div>

###Configuração avançada###

Diagnóstico pode ser modificado ainda mais adicionando pares de chave/valor à seção **Configurações do aplicativo** da página de gerenciamento **Configurar**. As configurações a seguir podem ser configuradas a partir das **configurações do aplicativo**:

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- O local no qual os logs do aplicativo serão salvos em relação à raiz da web.

- Valor padrão: ..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- O tamanho máximo do buffer a ser usado ao capturar logs do aplicativo. As informações inicialmente são gravadas no buffer antes de ir para o arquivo ou armazenamento. Se novas informações são gravadas no buffer antes da liberação, você poderá perder informações registradas anteriormente. Se seu aplicativo produz grandes picos de informações de log, considere aumentar o tamanho do buffer.

- Valor padrão: 10 MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- O tamanho máximo da pasta **Application** na qual o diagnóstico do aplicativo gravado em arquivo é armazenado.

- Valor padrão: 1 MB

###Fazer o download de arquivos de log para um site###

Os arquivos de log podem ser baixados usando FTP, PowerShell do Azure ou ferramentas de linha de comando do Azure.

**FTP**

1. Abra a página de gerenciamento **Painel** do site e anote o site FTP listado em **Logs de Diagnóstico** e a conta listada em **Usuário de Implantação**. O site FTP é onde os arquivos de log estão localizados e a conta listada no Usuário de Implantação é usada para autenticar o site FTP.
2. Se você ainda não tiver criado credenciais de implantação, a conta listada em **Usuário de implantação** será listada como **Não definida**. Nesse caso, você deve criar credenciais de implantação conforme descrito na seção Redefinir credenciais de implantação do painel, pois essas credenciais devem ser usadas para autenticar o site FTP onde os arquivos de log são armazenados. O Azure não oferece suporte à autenticação para este site FTP usando credenciais do Live ID.
3. Considere o uso de um cliente FTP como [FileZilla][fzilla] para se conectar ao site FTP. Um cliente de FTP oferece maior facilidade de uso para especificar as credenciais e exibir pastas em um site FTP, que é normalmente possível com um navegador.
4. Copie os arquivos de log do site FTP no computador local.

**PowerShell do Azure**

1. Na **Tela Inicial** ou no **Menu Iniciar**, pesquise por **PowerShell do Azure**. Clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>Se o <b>PowerShell do Azure</b> não estiver instalado, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx">Introdução aos cmdlets do PowerShell do Azure</a> para obter informações sobre a instalação e a configuração.</p> 
	</div>

2. No prompt do PowerShell do Azure, use o seguinte comando para baixar os arquivos de log:

		Save-AzureWebSiteLog -Name websitename

	Isso baixará os arquivos de log no site especificado por **websitename** e os salvará em um arquivo **log.zip** no diretório atual.
	
	Você também pode exibir um fluxo ao vivo de eventos de log usando o seguinte comando:

		Get-AzureWebSiteLog -Name websitename -Tail

	Isso exibirá informações de log no prompt do PowerShell do Azure conforme ocorrerem.

**Ferramentas de linha de comando do Azure**

Abra um novo prompt de comando, PowerShell, bash ou sessão de terminal e use o comando a seguir para baixar os arquivos de log:

	azure site log download websitename

Isso baixará os arquivos de log no site especificado por **websitename** e os salvará em um arquivo **log.zip** no diretório atual.

Você também pode exibir um fluxo ao vivo de eventos de log usando o seguinte comando:

	azure site log tail websitename

Isso exibirá as informações de log para o prompt de comando do PowerShell, bash ou sessão de terminal de onde o comando é executado.

<div class="dev-callout"> 
<b>Observação</b> 
<p>Se o comando <b>azure</b> não estiver instalado, consulte <a href="http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/command-line-tools/">Como usar as ferramentas de linha de comando do Azure</a> para obter informações de instalação e configuração.</p>
</div>

###Leitura de arquivos de log###

Os arquivos de log gerados depois que você tiver ativado o log e / ou rastreamento de um site variam de acordo com o nível de log / rastreamento que é definido na página de gerenciamento Configurar do site. Estes são os locais dos arquivos de log e como os arquivos de log podem ser analisados:

**Tipo de arquivo de log: log de aplicativo**

- Local /LogFiles/Application/. Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log do aplicativo. As informações registradas incluem a data e hora, a identificação de processo (PID) do aplicativo e o valor produzido pela instrumentação do aplicativo.

- Leia os arquivos com: um editor ou um analisador de texto que entenda os valores produzidos pelo aplicativo

**Tipo de arquivo de log: Falha no Rastreamento de Solicitação**

- Local: /LogFiles/W3SVC#########/. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Baixe o arquivo XSL no mesmo diretório que o(s) arquivo(s) XML, pois o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo do(s) arquivo(s) XML quando visualizado(s) no Internet Explorer. 

- Leia os arquivos com: Internet Explorer

**Tipo de arquivo de log: Log de erro detalhado**

- Local: /LogFiles/DetailedErrors/. A pasta /LogFiles/DetailedErrors/ contém um ou mais arquivos .htm que fornecem informações abrangentes sobre quaisquer erros HTTP que ocorreram. 

- Leia os arquivos com: navegador da Web

Os arquivos .htm incluem as seguintes sessões:

- **Informações de erro detalhadas:** inclui informações sobre o erro, como <em>Módulo</em>, <em>Manipulador</em>, <em>Código do Erro</em> e <em>URL solicitada</em>.

- **Causas mais prováveis:** lista várias causas prováveis do erro.

- **O que você pode tentar:** lista soluções possíveis para resolver o problema relatado pelo erro.

- **Mais informações e links**: fornece informações de resumo adicionais sobre o erro e também pode incluir links para outros recursos, como artigos da Base de Dados de Conhecimento Microsoft.

**Tipo de arquivo de log: Log de Servidor Web**

- Local: /LogFiles/http/RawLogs. As informações armazenadas nos arquivos são formatadas usando o [formato de log estendido do W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Os campos s-computername, cs-version e s-ip não são usados por Sites do Azure.

- Leia os arquivos com: Analisador de Log. Usado para analisar e consultar os arquivos de log do IIS. O Log Parser 2.2 está disponível no Microsoft Download Center em <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Como: monitorar o status de ponto de extremidade da Web

Esse recurso, disponível apenas no modo **Padrão**, permite monitorar até dois pontos de extremidade de até três locais geográficos. 

O monitoramento de ponto de extremidade configura testes da web de locais distribuídos geograficamente que testa o tempo de resposta e tempo de atividade de URLs de web. O teste executa uma operação get HTTP no URL da web para determinar o tempo de resposta e o tempo de atividade de cada local. Cada local configurado executa um teste a cada cinco minutos.

O tempo de atividade é monitorado usando códigos de resposta HTTP e o tempo de resposta é medido em milissegundos. Tempo de atividade é considerado 100% quando o tempo de resposta é de menos de 30 segundos e o código de status HTTP for inferior a 400. Tempo de atividade é 0% quando o tempo de resposta é maior do que 30 segundos ou o código de status HTTP é maior que 400.

Depois de configurar o monitoramento de ponto de extremidade, você pode analisar os pontos de extremidade individuais para exibir o status de tempo de atividade e tempo de resposta de detalhes sobre o intervalo de monitoramento de todos os locais de teste.

**Para configurar o ponto de extremidade:**

1.	Abra **Sites**. Clique no nome do site que você deseja configurar.
2.	Clique na guia **Configurar**. 
3.     Vá para a seção **Monitoramento** para inserir as configurações do ponto de extremidade.
4.	Insira um nome para o ponto de extremidade.
5.	Digite a URL do serviço que você deseja monitorar. Por exemplo, [http://contoso.cloudapp.net](http://contoso.cloudapp.net). 
6.	Selecione uma ou mais localizações geográficas na lista.
7.	Opcionalmente, repita as etapas anteriores para criar um segundo ponto de extremidade.
8.	Clique em **Salvar**. Pode levar alguns minutos até que os dados de monitoramento do ponto de extremidade esteja disponível nas guias **Painel** e **Monitoramento**.


Para obter mais informações sobre o monitoramento de pontos de extremidade do site, consulte os seguintes vídeos:

- [Scott Guthrie apresenta os Sites do Azure e configura o Monitoramento do Ponto de Extremidade](http://www.windowsazure.com/pt-br/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Mantendo os sites do Azure ativos e monitorando pontos de extremidade - com Stefan Schackow (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)





[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169





