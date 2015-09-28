<properties 
	pageTitle="Solução de Problemas e Perguntas sobre o Application Insights" 
	description="Há algo no Visual Studio Application Insights que não está claro ou não está funcionando? Tente aqui." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="awills"/>
 
# Solução de problemas e perguntas - Application Insights para ASP.NET

## É possível usar o Application Insights com...?

[Consultar plataformas][platforms]

## É gratuito?

* Sim, se você escolher a [faixa de preços](app-insights-pricing.md) gratuita. Você obtém a maioria dos recursos e uma cota mais ampla de dados. 
* Você deve fornecer os dados de cartão de crédito para se registrar com o Microsoft Azure, mas nenhum encargo será cobrado, a menos que você use outro serviço do Azure pago ou explicitamente atualize para uma faixa cobrada.
* Se seu aplicativo enviar mais dados do que a cota mensal por camada gratuita, eles deixarão de ser registrados em log. Se isso acontecer, você pode optar por começar a pagar ou aguardar até que a cota seja reiniciada no final do mês.
* Dados básicos de uso e da sessão não estão sujeitos a uma cota.
* Há também uma avaliação gratuita de 30 dias, durante a qual você obtém os recursos Premium gratuitamente.
* Cada recurso de aplicativo tem uma cota separada, e você define sua faixa de preço de modo independente dos outros.

#### O que obtenho se pagar?

* Uma maior [cota mensal de dados](http://azure.microsoft.com/pricing/details/application-insights/).
* Opção de pagamento excedente para continuar coletando dados acima da cota mensal. Se os seus dados ficarem acima da cota, você será cobrado por Mb.
* [Exportação contínua](app-insights-export-telemetry.md).

## Adicionando o SDK

#### <a name="q01"></a>Não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio

+ Certifique-se de que você tem o [Visual Studio 2013 Atualização 3 ou posterior](http://go.microsoft.com/fwlink/?LinkId=397827). Ele vem pré-instalado com as Ferramentas do Application Insights.
+ Embora as ferramentas não ofereçam suporte a todos os tipos de aplicativos, você provavelmente ainda pode adicionar um SDK do Application Insights ao seu projeto manualmente. Utilize [este procedimento][windows]. 


#### <a name="q02"></a>Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.

Isso pode acontecer se:

* A comunicação com o portal do Application Insights tiver falhado ou
* Houver algum problema com sua conta,
* Você tiver apenas [acesso de leitura para a assinatura ou grupo em que está tentando criar o novo recurso](app-insights-resources-roles-access-control.md).

Ação corretiva:

+ Confirme que você forneceu as credenciais de logon da conta certa do Azure. Em algumas versões anteriores das ferramentas, as credenciais do Microsoft Azure, que você vê na caixa de diálogo Novo Projeto, podem ser diferentes das credenciais do Visual Studio Online que você vê no canto superior direito do Visual Studio.
+ No navegador, verifique se você tem acesso ao [portal do Azure](https://portal.azure.com). Abra Configurações e veja se há alguma restrição.
+ [Adicione o Application Insights aos seus projetos existentes][start]\: no Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e selecione “Adicionar Application Insights”.
+ Se ainda não estiver funcionando, siga o [procedimento manual](app-insights-start-monitoring-app-health-usage.md) para adicionar um recurso no portal e, em seguida, adicione o SDK ao seu projeto. 

#### <a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"

Parece que algo deu errado enquanto você instalava o Application Insights, ou talvez um adaptador de registro em log.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente.


#### <a name="q14"></a>O que o Application Insights modifica no meu projeto?

Os detalhes dependem do tipo de projeto. Para um aplicativo Web:


+ Adiciona estes arquivos ao seu projeto:

 + ApplicationInsights.config.
 + ai.js


+ Instala estes pacotes NuGet:

 -  *API do Application Insights* - a API principal

 -  *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor

 -  *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente

    Os pacotes incluem os seguintes assemblies:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insere itens em:

 - Web.config

 - packages.config

+ (Somente novos projetos - se [adicionar o Application Insights a um projeto existente][start], você precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/\_Layout.cshtml

####<a name="NuGetBuild"></a> Recebo a mensagem "Pacote(s) do NuGet ausentes" no meu servidor de compilação, embora tudo seja compilado sem problemas em meus computadores de desenvolvimento

Consulte [Restauração do Pacote NuGet](http://docs.nuget.org/Consume/Package-Restore) e [Restauração Automática do Pacote](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a> Recebo a mensagem "O projeto faz referência ao(s) pacote(s) NuGet faltando no meu computador" ao tentar compilar após a atualização para 0.17 ou posterior dos pacotes NuGet.

Se você vir o erro acima após a atualização para os pacotes NuGet versão 0.17 ou posterior, você precisará editar o arquivo de projeto e remover os destinos BCL que foram deixados para trás.

Para fazer isso:

1. Clique com o botão direito do mouse no seu projeto no Gerenciador de Soluções e escolha Descarregar Projeto.
2. Clique novamente com o botão direito do mouse no projeto e escolha Editar *seuProjeto.csproj* 
3. Vá até o fim do arquivo de projeto e remova os destinos BCL semelhantes a: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Salve o arquivo.
5. Clique com o botão direito do mouse no projeto e escolha Recarregar *seuProjeto.csproj*

## Como atualizar de versões anteriores do SDK?

Consulte as [notas de versão](app-insights-release-notes.md) para o SDK adequado ao seu tipo de aplicativo.


## Sem dados

#### <a name="q03"></a>Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.

+ Na página Visão Geral, clique no bloco Pesquisa para abrir o Diagnóstico de Pesquisa. Os dados aparecem aqui pela primeira vez.
+ Clique no botão Atualizar. A folha se atualiza periodicamente, mas você também pode fazê-lo manualmente. O intervalo de atualização é maior para intervalos de tempo maiores.
+ No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
+ Verifique também o [nosso blog de status](http://blogs.msdn.com/b/applicationinsights-status/).

#### Nenhum dado desde que publiquei o aplicativo no meu servidor

+ Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.
+ Se você tiver que usar um proxy para envio fora de sua rede corporativa, defina [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) em Web.config
+ Windows Server 2008: certifique-se de ter instalado as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a>Eu não vejo nenhum dado na Análise de Uso para meu site.

+ Os dados vêm de scripts nas páginas da Web. Se tiver adicionado o Application Insights a um projeto Web existente, [você terá que adicionar os scripts manualmente][start].
+ Tenha certeza de que o Internet Explorer não está exibindo o site no modo de Compatibilidade.
+ Use o recurso de depuração do navegador (F12 em alguns navegadores; em seguida, escolha Rede) para verificar se os dados estão sendo enviados para dc.services.visualstudio.com.

#### <a name="q08"></a>Posso usar o Application Insights para monitorar um servidor Web de intranet?

Sim, você pode monitorar a integridade e o uso se o seu servidor puder enviar dados à Internet pública. No firewall, abra as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.

Se você quiser executar testes na Web para seu serviço, ele precisará ser acessível pela Internet pública na porta 80.

#### Posso monitorar um servidor Web de intranet que não tenha acesso à Internet pública?

Você precisaria preparar um proxy que pudesse retransmitir as chamadas POST https para dc.services.visualstudio.com

#### Eu costumava ver os dados, mas eles foram interrompidos

* Verifique o [blog de status](http://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra configurações/Cota e Preços para descobrir. Nesse caso, você pode atualizar seu plano ou então pagar por capacidade adicional. Consulte o [esquema de preços](http://azure.microsoft.com/pricing/details/application-insights/).

## O Monitor de Status não funciona

Consulte [Solução de problemas do Monitor de Status](app-insights-monitor-performance-live-website-now.md#troubleshooting). Portas de firewall são o problema mais comum.

## O Portal

#### <a name="q05"></a>Estou olhando para o painel inicial da Visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?

Você pode:

* Selecionar Navegar, Application Insights, o nome do seu projeto. Se não tiver nenhum projeto, você precisa [adicionar o Application Insights ao seu projeto Web no Visual Studio][start].

* No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto Web e selecione Abrir o portal do Application Insights.


#### <a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.


#### <a name="q06"></a>Na tela inicial da Visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?

Não! Ele mostra o status de serviço do Azure. Para ver os resultados de seu teste na Web, selecione Navegar > Application Insights > (seu aplicativo) e depois veja os resultados de teste na Web.


#### <a name="q07"></a>Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.

Você pode estar usando [a versão mais antiga do SDK do Application Insights](http://msdn.microsoft.com/library/dn793604.aspx), que se conecta à versão do Visual Studio Online.

As páginas de ajuda que você está consultando referem-se ao [Application Insights para a Visualização do Microsoft Azure][start], que já vem ativado no Visual Studio 2013 Atualização 3 ou posterior.

#### <a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?

Faça uma consulta a [Privacidade e Retenção de Dados][data].

## Registro em log

#### <a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?

Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

## Segurança

#### Meus dados estão seguros no portal? Por quanto tempo eles estão retidos?

Consulte [Privacidade e Retenção de Dados][data].


## <a name="q17"></a> Eu habilitei tudo no Application Insights?

<table border="1">
<tr><th>O que você deverá ver</th><th>Como obter isso</th><th>Por que você deseja isso</th></tr>
<tr><td>Gráficos de disponibilidade</td><td><a href="../app-insights-monitor-web-app-availability/">Testes da Web</a></td><td>Tenha certeza que o aplicativo Web está ativo</td></tr>
<tr><td>Desempenho do aplicativo para servidores: tempos de resposta...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Adicione o Application Insights ao seu projeto</a><br/>ou o <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar AI Status Monitor no servidor</a>(ou escreva seu próprio código para <a href="../app-insights-api-custom-events-metrics/#track-dependency">rastrear dependências</a>)</td><td>Detectar problemas de desempenho</td></tr>
<tr><td>Telemetria de dependência</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar o Monitor de Status do Application Insights no servidor</a></td><td>Diagnosticar problemas com bancos de dados ou outros componentes externos</td></tr>
<tr><td>Obter rastreamentos de pilha por meio de exceções</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserir chamadas TrackException em seu código</a> (mas alguns são informados automaticamente)</td><td>Detectar e diagnosticar exceções</td></tr>
<tr><td>Pesquisar rastreamentos de log</td><td><a href="../app-insights-search-diagnostic-logs/">Adicionar um adaptador de log</a></td><td>Diagnosticar exceções, problemas de desempenho</td></tr>
<tr><td>Noções básicas de uso do cliente: modos de exibição de página, sessões,...</td><td><a href="../app-insights-javascript/">Inicializador de JavaScript em páginas da Web</a></td><td>Análise de uso</td></tr>
<tr><td>Métricas de cliente personalizadas</td><td><a href="../app-insights-api-custom-events-metrics/">Rastreando chamadas em páginas da Web</a></td><td>Aprimorar a experiência do usuário</td></tr>
<tr><td>Métricas de servidor personalizadas</td><td><a href="../app-insights-api-custom-events-metrics/">Rastreando chamadas no código do servidor</a></td><td>Business intelligence</td></tr>
</table>

Se o serviço Web está em execução em uma VM do Azure, você também pode [obter diagnóstico][azurediagnostic] lá.

## Automação

Você pode [gravar um Script do PowerShell](app-insights-powershell-script-create-resource.md) para criar um recurso do Application Insights.

## Mais respostas

* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/pt-BR/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Sept15_HO3-->