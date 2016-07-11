<properties
	pageTitle="Monitorar aplicativos Web no Serviço de Aplicativo do Azure"
	description="Saiba como monitorar Aplicativos Web no Serviço de Aplicativo do Azure usando o Portal de Gerenciamento."
	services="app-service"
	documentationCenter=""
	authors="btardif"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="byvinyal"/>

#<a name="howtomonitor"></a>Monitorar aplicativos Web no Serviço de Aplicativo do Azure

[Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) oferecem funcionalidade de monitoramento para planos do Serviço de Aplicativo Padrão e Premium, por meio da página de gerenciamento Monitoramento. A página de gerenciamento Monitoramento fornece estatísticas de desempenho para um aplicativo Web, conforme descrito abaixo.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##Política de Retenção de Métricas

>[AZURE.NOTE] A política de retenção para a métrica do aplicativo varia de acordo com a granularidade.

- Métricas de granularidade de **minuto** são mantidas por **24 horas**
- Métricas de granularidade de **Hora** são mantidas por **7 dias**
- Métricas de granularidade de **Dia** são mantidas por **30 dias**

##<a name="websitemetrics"></a>Como: adicionar métricas de aplicativo Web

1. No [portal clássico](https://manage.windowsazure.com), na página do aplicativo Web, clique na guia **Monitoramento** para exibir a página de gerenciamento **Monitorar**. Por padrão, o gráfico na página **Monitoramento** exibe as mesmas métricas do gráfico na página **Painel**.

2. Para exibir as métricas adicionais para o aplicativo Web, clique em **Adicionar métricas** na parte inferior da página para exibir a caixa de diálogo **Escolher métricas**.

3. Clique para selecionar métricas adicionais para exibição na página **Monitoramento**.

4. Depois de selecionar as métricas que deseja incluir na página **Monitoramento**, clique na marca de seleção na parte inferior.

5. Depois de adicionar a métrica à página **Monitoramento**, clique para habilitar/desabilitar a caixa de seleção redonda ao lado de cada métrica para adicionar/remover a métrica do gráfico na parte superior da página.

6. Para remover as métricas da página **Monitor**, selecione a métrica que você deseja remover e, em seguida, clique no ícone **Excluir métrica** na parte inferior da página.



##<a name="howtoreceivealerts"></a>Como: receber alertas de métricas de aplicativo Web

No modo de aplicativo Web **Padrão**, você pode receber alertas com base nas métricas de monitoramento de seu aplicativo Web. O recurso de alerta requer que você primeiro configure um ponto de extremidade da web para monitoramento, que você pode fazer na seção **Monitoramento** da página **Configurar**. Também é possível optar por ter o email enviado quando uma métrica escolhida atingir um valor especificado. Para saber mais, confira [Como: receber notificações de alerta e gerenciar regras de alerta no Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##<a name="howtoviewusage"></a>Como: exibir cotas de uso de um site

Os aplicativos Web podem ser configurados para execução no modo **Compartilhado** ou **Padrão** na página de gerenciamento **Escala** do aplicativo Web no [portal clássico](https://manage.windowsazure.com). Cada assinatura do Azure tem acesso a um pool de recursos fornecidos com a finalidade de execução de até 100 aplicativos Web por região em modo **Compartilhado**. O pool de recursos disponíveis para cada assinatura de aplicativo Web para essa finalidade é compartilhado por outros aplicativos Web na mesma localização geográfica configurados para serem executados em modo **Compartilhado**. Como esses recursos são compartilhados para uso por outros aplicativos Web, todas as assinaturas são limitadas em termos de uso desses recursos. Limites aplicados ao uso da assinatura desses recursos são expressos como cotas de uso listadas na seção de visão geral sobre o uso da página de gerenciamento **Painel** de cada aplicativo Web.

>[AZURE.NOTE] Quando um aplicativo Web está configurado para executar no modo **Padrão**, são alocados recursos dedicados equivalentes aos tamanhos das máquinas virtuais **Pequeno** (padrão), **Médio** ou **Grande** na tabela em [Tamanhos de máquinas virtuais e do Serviço de Nuvem do Azure][vmsizes]. Não há limites para os recursos usados por uma assinatura para a execução de aplicativos Web no modo **Padrão**. No entanto, o número de aplicativos Web no modo **Padrão** que podem ser criados por região é 500.

### Como: exibir cotas de uso para aplicativos Web configurados para modo Compartilhado ###
Para determinar a extensão do impacto de um aplicativo Web sobre cotas de uso de recursos, siga estas etapas:

1. Abra a página de gerenciamento **Painel** do aplicativo Web no [portal clássico](https://manage.windowsazure.com).
2. Na seção **visão geral de uso**, as cotas de uso para seu respectivo plano de [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) são exibidas, as quais são um subconjunto dos seguintes itens:
	-	**Saída de dados**, **Tempo de CPU** e **Memória** - quando a cota é excedida, o Azure interrompe o aplicativo Web para o restante do intervalo de cota atual. O Azure iniciará o aplicativo Web no início do próximo intervalo da cota.
	-	**Armazenamento do sistema de arquivos** - quando essa cota é atingida o armazenamento do sistema de arquivos permanece acessível para operações de leitura, mas todas as operações de gravação, incluindo aquelas requeridas para atividade normal do aplicativo Web, são bloqueadas. As operações de gravação serão retomadas quando você reduzir o uso de arquivo ou mover o aplicativo Web para um Plano do Serviço de Aplicativo com uma cota mais alta.
	-	**Recursos vinculados** - cotas para quaisquer recursos vinculados do aplicativo Web, como banco de dados ou armazenamento, são exibidas aqui também.

	Algumas cotas podem ser aplicadas por plano de hospedagem da web, enquanto outas podem ser aplicadas por site. Para informações detalhadas sobre cotas de uso para cada plano de hospedagem da web, consulte [Limites de sites](azure-subscription-service-limits.md#websiteslimits).

##<a name="resourceusage"></a> Como: evitar exceder suas cotas

As cotas não são uma questão de desempenho ou custo, mas a maneira como o Azure regula o uso de recursos em um ambiente multilocatário evitando que os locatários usem em excesso os recursos compartilhados. Uma vez que exceder suas cotas significa tempo de inatividade ou funcionalidade reduzida para seu aplicativo Web, considere o seguinte se desejar manter o site operando quando as cotas estiverem prestes a serem atingidas:

- Mova seus aplicativos Web para um Plano do Serviço de Aplicativo de nível mais alto, para tirar proveito de uma cota maior. Por exemplo, a única cota para os planos **Básico** e **Padrão** é o Armazenamento de sistema de arquivos.
- Conforme o número de instâncias de um aplicativo Web aumenta, o mesmo ocorre com a probabilidade de exceder as cotas de recursos compartilhados. Se adequado, considere redimensionar instâncias adicionais de um aplicativo Web quando as cotas de recursos compartilhados estiverem sendo excedidas.

##<a name="howtoconfigdiagnostics"></a>Como: configurar o diagnóstico e baixar para um aplicativo Web

O diagnóstico é habilitado na guia **Configurar** do aplicativo Web no [portal clássico](https://manage.windowsazure.com). Há dois tipos de diagnóstico: um **diagnóstico dos aplicativos** e **diagnósticos do site**.

#### Diagnóstico de Aplicativo ####

A seção **diagnóstico de aplicativo** da página de gerenciamento **Configurar** controla o log de informações produzido pelo aplicativo, o que é útil ao registrar eventos que ocorre dentro de um aplicativo. Por exemplo, quando ocorre um erro em seu aplicativo, você poderá apresentar ao usuário um erro amigável ao gravar informações de erro mais detalhadas no log para análise posterior.

Você pode ativar ou desativar os seguintes diagnósticos de aplicativo:

- **Log de Aplicativo (Sistema de Arquivos)** - Ativa o log de informações produzidas pelo aplicativo. O campo **Nível de log** determina se as informações do nível de erro, aviso ou informações são registradas. Você também pode selecionar Detalhado, que registrará todas as informações geradas pelo aplicativo.

	Logs gerados por essa configuração são armazenadas no sistema de arquivos do seu aplicativo Web e podem ser baixados usando as etapas na seção **Baixando arquivos de log de um aplicativo Web**, abaixo.

- **Log de Aplicativo (Armazenamento de Tabela)** Ativa o log de informações geradas pelo aplicativo, semelhante à opção Log de aplicativo (Sistema de Arquivos). No entanto, as informações de log são armazenadas em uma conta de armazenamento do Azure em uma tabela.

	Para especificar a conta de armazenamento do Azure e a tabela, escolha **Ativado**, selecione o **Nível de log** e, em seguida, escolha **Gerenciar Armazenamento de Tabelas**. Especifique a conta de armazenamento e a tabela a ser usada ou crie uma nova tabela.

	As informações de log armazenadas na tabela podem ser acessadas usando um cliente de Armazenamento do Azure.

- **Log de Aplicativo (Armazenamento de blob)** - Ativa o log de informações geradas pelo aplicativo, semelhante à opção Log de Aplicativo (Armazenamento de tabela). No entanto, as informações de log são armazenadas em um blob na conta de armazenamento do Azure.

	Para especificar a Conta de Armazenamento e o blob do Azure, escolha **Ativado**, selecione o **Nível de log** e, em seguida, escolha **Gerenciar Armazenamento de Blobs**. Especifique a conta de armazenamento, o contêiner de blob e o nome de blob a ser usado ou crie um novo contêiner e blob.

Para obter mais informações sobre Contas de Armazenamento do Azure, consulte [Como gerenciar contas de armazenamento (a página pode estar em inglês)](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE] Log de aplicativo para o armazenamento de tabela ou blob só é suportado para aplicativos .NET.

Como o log do aplicativo no armazenamento requer o uso de um cliente de armazenamento para exibir os dados do log, ele será mais útil quando você planejar usar um serviço ou aplicativo que saiba como ler e processar os dados diretamente no Armazenamento de Tabela ou de Blob do Azure. O log do sistema de arquivos produz arquivos que podem ser baixados no computador local usando FTP ou outros utilitários conforme descrito mais adiante nesta seção.

**Diagnóstico de Aplicativo (sistema de arquivos)**, **Diagnóstico de Aplicativo (armazenamento de tabela)**, e **Diagnóstico de Aplicativo (armazenamento de blob)** podem ser ativados ao mesmo tempo e ter configurações de nível de log individuais. Por exemplo, você poderá registrar erros e avisos no armazenamento como uma solução a longo prazo do log, ao mesmo tempo em que habilita o log de sistema de arquivos com um nível de detalhe após instrumentar o código do aplicativo para solucionar um problema.

O diagnóstico também pode ser habilitado a partir do PowerShell do Azure usando o cmdlet **Set-AzureWebsite**. Se você não instalou o PowerShell do Azure ou não o configurou para usar sua Assinatura do Azure, consulte [Como usar o PowerShell do Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE] O log de aplicativo se baseia em informações de log geradas pelo seu aplicativo. O método usado para gerar informações de log, bem como o formato das informações, é específico para o idioma em que seu aplicativo é gravado. Para obter informações específicas de idioma sobre o uso do log de aplicativo, consulte os seguintes artigos:
>
> - **.NET** - [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js** - [Como depurar um aplicativo Node.js nos Sites do Azure](web-sites-nodejs-debug.md).
>
> Log de aplicativo para o armazenamento de tabela ou blob só é suportado para aplicativos .NET.

#### Diagnóstico de Site ####

A seção **Diagnóstico de Site** da página de gerenciamento **Configurar** controla o registro de log executado pelo servidor web, como o log de solicitações da web, falha ao servir páginas ou quanto tempo demorou para servir a uma página. Você pode ativar ou desativar as seguintes opções:

- **Log de Servidor Web** - ativar o log de servidor Web para salvar logs de site usando o formato de arquivo de log estendido do W3C. Log do servidor Web gera um registro de todas as solicitações recebidas para seu aplicativo Web, que contém informações como o endereço IP do cliente, URI solicitado, código de status HTTP de resposta e a sequência de agente do usuário do cliente. Você pode salvar os logs em uma Conta de Armazenamento do Azure ou no Sistema de Arquivos.

 Para salvar os logs de servidor web em uma Conta de Armazenamento do Azure, escolha **Armazenamento** e, em seguida, escolha **gerenciar armazenamento** para especificar uma conta de armazenamento e um Contêiner de Blob do Azure em que os logs serão mantidos. Para obter mais informações sobre Contas de Armazenamento do Azure, consulte [Como gerenciar contas de armazenamento (a página pode estar em inglês)](/manage/services/storage/how-to-manage-a-storage-account/).

   Para salvar os logs do servidor web em um sistema de arquivos, escolha **Sistema de arquivos**. Isso habilita a caixa **Cota** onde você pode definir a quantidade máxima de espaço em disco para os arquivos de log. O tamanho mínimo é de 25MB e o máximo é de 100MB. O tamanho padrão é 35MB.

 Por padrão, os logs do servidor web nunca são excluídos. Para especificar um período de tempo após o qual os logs serão excluídos automaticamente, selecione **Definir Retenção** e insira o número de dias que os logs serão mantidos na caixa **Período de Retenção**. Essa configuração está disponível para as opções de Armazenamento do Azure e Sistema de Arquivos.

- **Mensagens de Erro Detalhadas** - Ativa o log erros detalhado para registrar em log informações adicionais sobre erros HTTP (códigos de status maiores que 400).

- **Falha no Rastreamento de Solicitação** - Ativa o rastreamento de solicitação com falha para capturar informações de solicitações de cliente com falha, como um código de status HTTP da série 400. A falha no rastreamento de solicitação produz um documento XML que contém um rastreamento de quais módulos de solicitação foram passadas no IIS, detalhes retornados pelo módulo e o momento em que o módulo foi chamado. Essas informações podem ser usadas para isolar o componente em que a falha ocorreu.


Depois de habilitar o diagnóstico para um aplicativo Web, clique no ícone **Salvar** na parte inferior da página de gerenciamento **Configurar** aplicar as opções que você definiu.

> [AZURE.IMPORTANT] Mensagens de erro detalhadas e rastreamento de solicitação falha colocam exigências significativas em um aplicativo Web. É recomendável desativar o registro e o rastreamento depois de reproduzir o(s) problema(s) que você está solucionando.

### Configuração avançada ###

Diagnóstico pode ser modificado ainda mais adicionando pares de chave/valor à seção **Configurações do aplicativo** da página de gerenciamento **Configurar**. As configurações a seguir podem ser configuradas a partir das **configurações do aplicativo**:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

- O local no qual os logs do aplicativo serão salvos em relação à raiz da web.

- Valor padrão: ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

- O tamanho máximo do buffer a ser usado ao capturar logs do aplicativo. As informações inicialmente são gravadas no buffer antes de ir para o arquivo ou armazenamento. Se novas informações são gravadas no buffer antes da liberação, você poderá perder informações registradas anteriormente. Se seu aplicativo produz grandes picos de informações de log, considere aumentar o tamanho do buffer.

- Valor padrão: 10 MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- O tamanho máximo da pasta **Application** na qual o diagnóstico do aplicativo gravado em arquivo é armazenado.

- Valor padrão: 1 MB

###Baixando arquivos de log para um aplicativo Web

Os arquivos de log podem ser baixados usando FTP, PowerShell do Azure ou a CLI do Azure.

**FTP**

1. Abra a página de gerenciamento **Painel** do aplicativo Web no [portal clássico](https://manage.windowsazure.com) e anote o site de FTP listado em **Logs de Diagnóstico** e a conta listada em **Usuário da Implantação**. O site FTP é onde os arquivos de log estão localizados e a conta listada no Usuário de Implantação é usada para autenticar o site FTP.
2. Se você ainda não tiver criado credenciais de implantação, a conta listada em **Usuário de implantação** é listada como **Não definida**. Nesse caso, você deve criar credenciais de implantação conforme descrito na seção Redefinir credenciais de implantação do painel, pois essas credenciais devem ser usadas para autenticar o site FTP onde os arquivos de log são armazenados. O Azure não oferece suporte à autenticação para este site FTP usando credenciais do Live ID.
3. Considere o uso de um cliente FTP como [FileZilla][fzilla] para se conectar ao site FTP. Um cliente de FTP oferece maior facilidade de uso para especificar as credenciais e exibir pastas em um site FTP, que é normalmente possível com um navegador.
4. Copie os arquivos de log do site FTP no computador local.

**PowerShell do Azure**

1. Na **Tela Inicial** ou no **Menu Iniciar**, procure **Windows PowerShell**. Clique com o botão direito do mouse na entrada **Windows PowerShell** e selecione **Executar como Administrador**.

	> [AZURE.NOTE] Se o **PowerShell do Azure** não estiver instalado, consulte [Introdução aos cmdlets do PowerShell do Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) para obter informações sobre a instalação e a configuração.

2. No prompt do PowerShell do Azure, use o seguinte comando para baixar os arquivos de log:

		Save-AzureWebSiteLog -Name webappname

	Isso baixará os arquivos de log no site especificado por **NomeDoAplicativoWeb** e os salvará em um arquivo **log.zip** no diretório atual.

	Você também pode exibir um fluxo ao vivo de eventos de log usando o seguinte comando:

		Get-AzureWebSiteLog -Name webappname -Tail

	Isso exibirá informações de log no prompt do PowerShell do Azure conforme ocorrerem.

**CLI do Azure**

Abra um novo prompt de comando, PowerShell, bash ou sessão de terminal e use o comando a seguir para baixar os arquivos de log:

	azure site log download webappname

Isso baixará os arquivos de log no site especificado por **NomeDoAplicativoWeb** e os salvará em um arquivo **log.zip** no diretório atual.

Você também pode exibir um fluxo ao vivo de eventos de log usando o seguinte comando:

	azure site log tail webappname

Isso exibirá as informações de log para o prompt de comando do PowerShell, bash ou sessão de terminal de onde o comando é executado.

> [AZURE.NOTE] Se o comando **azure** não estiver instalado, consulte [Instalar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração.

### Leitura de arquivos de log ###

Os arquivos de log gerados depois de você ter habilitado o log e/ou rastreamento de um aplicativo Web variam de acordo com o nível de log/rastreamento que é definido na página de gerenciamento Configurar do aplicativo Web. Estes são os locais dos arquivos de log e como os arquivos de log podem ser analisados:

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

- Local: /LogFiles/http/RawLogs. As informações armazenadas nos arquivos são formatadas usando o [formato de log estendido do W3C](http://go.microsoft.com/fwlink/?LinkID=90561). Os campos s-computername, cs-version e s-ip não são usados por aplicativos Web do Azure.

- Leia os arquivos com: Analisador de Log. Usado para analisar e consultar os arquivos de log do IIS. O Log Parser 2.2 está disponível no Microsoft Download Center em <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a> Como: monitorar o status de ponto de extremidade da Web

Esse recurso, disponível apenas no modo **Padrão**, permite monitorar até dois pontos de extremidade de até três locais geográficos.

O monitoramento de ponto de extremidade configura testes da web de locais distribuídos geograficamente que testa o tempo de resposta e tempo de atividade de URLs de web. O teste executa uma operação get HTTP no URL da web para determinar o tempo de resposta e o tempo de atividade de cada local. Cada local configurado executa um teste a cada cinco minutos.

O tempo de atividade é monitorado usando códigos de resposta HTTP e o tempo de resposta é medido em milissegundos. O tempo de atividade é considerado 100% quando o tempo de resposta é menor que 30 segundos e o código de status HTTP é menor que 400. O tempo de atividade é de 0% quando o tempo de resposta é maior que 30 segundos ou o código de status HTTP é maior que 400.

Depois de configurar o monitoramento de ponto de extremidade, você pode analisar os pontos de extremidade individuais para exibir o status de tempo de atividade e tempo de resposta de detalhes sobre o intervalo de monitoramento de todos os locais de teste. Você também pode configurar uma regra de alerta quando o ponto de extremidade levar tempo demais para responder, por exemplo.

**Para configurar o ponto de extremidade:**

1.	Abra **Aplicativos Web**. Clique no nome do aplicativo Web que você deseja configurar.
2.	Clique na guia **Configurar**.
3.     Vá para a seção **Monitoramento** para inserir as configurações do ponto de extremidade.
4.	Insira um nome para o ponto de extremidade.
5.	Insira a URL para uma parte do seu aplicativo Web que você deseje monitorar. Por exemplo, [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive).
6.	Selecione uma ou mais localizações geográficas da lista.
7.	Opcionalmente, repita as etapas anteriores para criar um segundo ponto de extremidade.
8.	Clique em **Salvar**. Pode levar alguns minutos até que os dados de monitoramento do ponto de extremidade esteja disponível nas guias **Painel** e **Monitoramento**.

Para criar uma regra de email, faça o seguinte:

9.	Na barra de serviço bem à esquerda, clique em **Serviços de gerenciamento**.
10.	Clique em **Adicionar regra** na parte inferior.
11.	Em **Tipo de serviço**, selecione **Aplicativo Web** e, em seguida, selecione o aplicativo Web para o qual você configurou anteriormente o monitoramento de ponto de extremidade. Clique em **Próximo**.
12.	Em **Métrica**, agora é possível selecionar métricas adicionais para o ponto de extremidade configurado. Por exemplo: **Tempo de Resposta (home page/EUA: IL-Chicago)**. Selecione a métrica de Tempo de Resposta e digite 3 no **Valor de Limite** para especificar um limite de 3 segundos.
13.	Selecione **Enviar um email para o administrador de serviços e os coadministradores**. Clique em **Concluído**.

	O Azure agora monitorará ativamente o ponto de extremidade e enviará um alerta por email quando levar mais de 3 segundos para responder.

Para obter mais informações sobre o monitoramento de pontos de extremidade do aplicativo Web, consulte os seguintes vídeos:

- [Scott Guthrie apresenta os Sites do Azure e configura o Monitoramento do Ponto de Extremidade](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Mantendo os sites do Azure ativos e monitorando pontos de extremidade - com Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE] Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169

<!---HONumber=AcomDC_0629_2016-->