#Solução de problemas no Azure

*Solução de problemas* refere-se à tarefa geral de localizar e
compreender o comportamento inesperado de aplicativos e corrigi-lo. Ao desenvolver aplicativos, os desenvolvedores testar, executar e depurar
eus aplicativos antes de implantá-los em um ambiente de
produção. Esse fato é verdadeiro se o aplicativo é executado em um computador desktop
ou em um servidor na nuvem. No entanto, um aplicativo de várias instâncias e amplamente distribuído,
projetado para fora de escala pode ser difícil de depurar,
exigindo mais de abordagens e ferramentas padrão.

Por esse motivo, os aplicativos em nuvem devem ser projetados
na solução de problemas em mente. Como criar suporte para solução de problemas em
seu aplicativo depende primeiro onde o aplicativo é executado e
segundo em quais idiomas ou tempos de execução do seu aplicativo é compilado
ou usa. 

Se você estiver criando um aplicativo que é executado em uma Máquina Virtual no Azure,
em muitos casos, você pode alcançar a solução de problemas de design, bem como
a depuração como você faria no sistema operacional se ele estiver sendo executado
em seus próprios servidores.

Aplicativos em execução no Azure são amplamente distribuídos,
aplicativos de várias instâncias que podem ser difíceis de depurar. Esses tipos de aplicativos
requerem mais que as ferramentas e abordagens padrão para a solução de problemas. Este tópico
aborda algumas práticas comprovadas de solução de problemas e contém links para
informações mais intensivas sobre as práticas descritas.

**Observação**: Este tópico pressupõe que você está desenvolvendo seu aplicativo ou
implantou com êxito seu aplicativo do Azure e
algo inesperado está ocorrendo agora. Ele não aborda como implantar
um aplicativo no Azure. Para obter mais informações sobre como desenvolver
e implantar seu aplicativo do Azure, consulte <a href="https://www.windowsazure.com/pt-br/develop/overview/">https://www.windowsazure.com/pt-BR/develop/overview/</a>

Primeiro, este tópico descreve algumas práticas recomendadas que ajudarão você a desenvolver
seu aplicativo para que você possa solucionar com eficácia problemas quando eles ocorrerem. (Se você não desenvolver seu aplicativo para permitir que você siga o fluxo
do código com antecedência, pode ser muito difícil localizar problemas quando
eles ocorrerem.) Essas práticas recomendadas são válidas para todos os tipos de
aplicativos em execução no Azure, independentemente do modelo de
aplicativo ou linguagem usada.

As seções a seguir descrevem abordagens específicas para desenvolver aplicativos
do Azure com suporte independentemente do tipo de aplicativo:
sites, serviços de nuvem ou máquinas virtuais.

Cada seção descreve as práticas recomendadas em um nível alto e contenha
ponteiros para os recursos que apresentam as práticas recomendadas em detalhes ou
descrevem como implantá-los.


##Neste documento

* [Práticas recomendadas para solução de problemas no Azure](#BestPractices)
* [Site do Azure](#Websites)
* [Serviços de nuvem do Azure](#CloudServices)
* [Máquinas Virtuais do Azure](#Vms)
* [Serviços do Azure](#PlatformServices)
* [Solução de problemas do banco de dados SQL](#SQLTroubleshooting)

<h2><a id="BestPractices"></a>Práticas recomendadas para solução de problemas no Azure</h2>

Esta seção descreve as práticas recomendadas que se aplicam aos aplicativos do Azure
independentemente de qual modelo de hospedagem ou o idioma que você usa. Ele
contém recursos para mais informações detalhadas sobre essas práticas.

Para criar a base para a solução de problemas eficiente no Azure,
concentre seus esforços nessas três áreas principais:

-   Tratamento de falhas normalmente - *cada serviço do componente deve ser capaz de suportar
a falha de serviços dependentes ou de infraestrutura*.
-   Rastreamento, logs e monitoramento - *cada serviço componente deve ter
depuração, rastreamento, eventos e log de erros adequados.*
-   Depurar erros onde você pode - *antes de promover para a produção, mas
também no nível de componente e de rede durante a execução.*

Criando um aplicativo com essas ideias em mente permite que o aplicativo forneça a você as
informações necessárias para rastrear um comportamento inesperado quando ele ocorre.

###Design para controlar erros normalmente

Aplicativos devem manipular condições de erro normalmente se eles podem. Isso
é ainda mais importante devido à natureza distribuída do Azure.
Solução de problemas eficaz começa com um design de bom tratamento
transitório falha. Erros transitórios são uma das principais áreas em que aplicativos em nuvem
não se comportam conforme o esperado devido a condições de erro transitório
inerentes aos aplicativos da internet. 

Erros transitórios são falhas relacionadas à latências e conexões de rede intermitente
inerentes a recursos compartilhados na internet. Estes são alguns
exemplos:

-   Recursos de computador compartilhado, como os serviços de nuvem do Azure e
banco de dados SQL (para dar dois exemplos) podem ser um pouco menos ou mais
responsivos de momento a momento.
-   Atraso na capacidade de resposta devido a fornecer durabilidade para serviços. Por exemplo, SQL
Azure mantém várias cópias de bancos de dados consistentes para fornecer durabilidade,
isso tem um impacto na capacidade de resposta.
-   Atrasos causados por HTTP ou outras conexões de protocolo final antes de
concluir o trabalho. Por exemplo, solicitações HTTP não podem alcançar um
ponto de extremidade e retornar antes de seu período de tempo limite.


Para ajudar a atenuar o impacto de erros transitórios,
os aplicativos do Azure devem:

-   Ser flexíveis para que os componentes não fiquem localmente dependentes mediante
serviços que falham com mais frequência do que em ambientes no local.
-   Fazer chamadas assíncronas, sempre que possível impedir que processos
tornem-se dependentes de respostas imediatas.
-   Use um abordagem que detecta certas categorias de falhas e
pode implementar o comportamento de repetir para as falhas com base em alguma
diretiva de repetição configurável de tratamento de erros transitórios.

Chamadas para serviços devem criar ou usar um camada de tratamento de erros transitórios para detectar os cenários comuns de falha e repetir a chamada com base em uma definição de configuração. Para desenvolvedores .NET, uma biblioteca recomendada é o [Pacote de integração do Microsoft Enterprise Library 5.0 para Azure]
O Microsoft Enterprise Library é uma coleção de blocos de aplicativos reutilizáveis
que abrangem preocupações comuns no desenvolvimento de software empresarial. O pacote de integração do Microsoft Enterprise Library para Azure é uma extensão do Microsoft Enterprise Library 5.0 que
pode ser usado com o Azure. Ele inclui
autoescalonamento de bloco de aplicativo, bloco de aplicativo de tratamento de falha transitória, origem de configuração de blob, provedor de configuração protegida e materiais de aprendizagem. Outra biblioteca .NET mais simples com menos recursos é a
[Estrutura de nuvem de aplicativos e extensões (CloudFx)]. CloudFx oferece um conjunto
de componentes de qualidade de produção e blocos de construção desenvolvidos para
acelerar a implantação de soluções e serviços ricos, confiáveis e extensíveis baseados no Azure.

###Realizar o rastreamento e o registro adequado

Porque a complexidade dos aplicativos distribuídos e fora de escala,
depuradores tradicionais que funcionam em um processo são de muito menos uso
ao investigar problemas que ocorrem durante a execução do seu aplicativo.
Portanto, o rastreamento e o registro são de máxima importância. A execução de
seu aplicativo e seus dados é compartilhada entre vários serviços,
que são hospedados em várias máquinas diferentes. Em um aplicativo distribuído em grande escala, é
difícil, se não é impossível determinar qual instância de serviço anexar e depurar. O rastreamento e o registro permitem que você siga a execução de aplicativos
e fluxo de dados, dando a você uma melhor compreensão sobre o estado do seu aplicativo.

Aplicativos do Azure bem-sucedidos têm um log e rastreamento
estratégia projetados no aplicativo desde o início. Isso reduz
o tempo e o esforço necessários para localizar problemas e repará-los rapidamente sem ter que
ligar para a Microsoft para obter suporte.

**Observação**: gravar rastreamento e registro em log de forma ampla tem um impacto no desempenho;
fazendo isso intensivamente tem um impacto mais forte. Portanto, o design do seu aplicativo deve incluir um rastreamento configurável e
diretiva de log que podem ser ajustados se necessário. De forma ideal, o nível de log deve ser ajustável do **ServiceConfiguration.cscfg** 
arquivo de modo que ela pode ser alterado sem precisar implantar novamente.

Ter volumes de logs não garante detecção e reparo rápidos de bug; leva-se muito tempo para decifrar e coletar seu impacto no desempenho de sua aplicação. Registro de log ajustável
controla o custo de armazenamento e o tamanho dos dados de log.

No artigo da MSDN Magazine [Assumir controle de registro em log e rastreamento no Azure],
Mike Kelly descreve quatro tipos de diagnóstico de saída a serem considerados:

-   Saída de depuração - somente na compilação de depuração inclui declarações
-   Rastreamento - rastreia o fluxo de controle durante a execução
-    Registro em log de eventos - principais eventos na execução do programa
-   Registro em log de erros - situação excepcional ou perigosa

Outros idiomas, sistemas operacionais e plataformas de aplicativos
pode ter uma terminologia diferente para rastreamento e o registro em log. Se você estiver usando
uma dessas plataformas de desenvolvimento no Azure, use as ferramentas e estratégia equivalentes
para o idioma ou a plataforma que você estiver usando.

Aplicações de modo misto são aplicações executando uma combinação de
sites, serviços de nuvem e Máquinas Virtuais do Azure. Ao criar
aplicativos desse tipo, o rastreamento e o registro em log tornam-se ainda mais importantes
porque eles são mais amplamente distribuídos. Para solucionar problemas,
esses aplicativos de modo misto, o fluxo de dados e execução geral deve ser seguido para identificar quaisquer problemas. A mecânica de rastreamento e registro em log de um aplicativo de modo misto depende do modelo de hospedagem do componente. 

###Monitorar seu aplicativo

Rastreamento e o registro em log são adequados na área maior de monitoramento. Monitoramento
permite que você:

-   Descubra aplicativos do Azure
-   Determine o status de cada instância de função
-   Colete e monitore as informações de desempenho incluindo latência e
taxa de transferência
- Colete e monitore eventos
-   Colete e monitore mensagens de rastreamento
-   Monitore o uso do recurso
-   Monitore a qualidade das métricas de serviço
-   Execute o planejamento da capacidade
-   Execute a análise de tráfego (usuários, modos de exibição, horários de pico)
-   Estime cobranças
-   Realize auditoria

O monitoramento é feito com uma ferramenta ou um conjunto de ferramentas. A ferramenta a ser
usada depende da plataforma e/ou idiomas que seu aplicativo usa e seus objetivos e requisitos de monitoramento.

**Pacote de monitoramento do centro de sistema da Microsoft para aplicativos do Azure**

Isso [Pacote de monitoramento] permite que você use o [Microsoft System Center Operations Manager] para monitorar a disponibilidade e o desempenho dos aplicativos do Azure.

Usar o Microsoft System Center Operations Manager 2007 é a melhor maneira para
monitorar a integridade do seu aplicativo do Azure.

**Ferramenta de gerenciamento de plataforma do Azure**

Outra ferramenta é a [Ferramenta de gerenciamento na plataforma (MMC) do Azure], que permite que você gerencie o Azure serviços e contas de armazenamento
hospedados. Essa ferramenta é fornecida como um exemplo com código-fonte completo para que
você veja como executar o gerenciamento de várias e tarefas de configuração usando as APIs de
diagnóstico e de gerenciamento do Azure.

**Ferramentas Cerebrata**

Cerebrata fornece uma série de ferramentas que permitem que você monitore e
gerencie seus aplicativos do Azure. Eles incluem [Gerenciador de diagnóstico do Azure], [Studio de armazenamento nuvem], e [Cmdlets de gerenciamento do Azure].

Azure Diagnostics Manager 2 é um cliente baseado no Windows (WPF) para o gerenciamento do
Azure Diagnostics. Ele permite exibir, baixar e gerenciar os dados
coletados pelos aplicativos em execução no Azure. Consulte
[http://www.cerebrata.com](http://www.cerebrata.com) para obter mais informações sobre esses produtos.

Cloud Storage Studio 2 é um cliente com base no Windows (WPF) para gerenciar
o armazenamento do Azure.

Os cmdlets de gerenciamento do Azure é um conjunto de cmdlets do Windows PowerShell para gerenciar o armazenamento do Azure, serviços hospedados, instâncias de banco de dados SQL e diagnóstico. Ele também fornece cmdlets para fazer backup e restaurar contas de armazenamento.

**Monitoramento de rede: AlertBot, Gomez, Keynote, Pingdom**

As [Gomez] Application Performance Management, [Keynote], [Pingdom],
e  [AlertBot] da Compuware são soluções monitorando o seu aplicativo Azure remotamente. Eles permitem que você monitore a disponibilidade do aplicativo e otimize o desempenho. Alguns serviços, como Pingdom, ativam a notificação por email, SMS ou um aplicativo de desktop quando um erro é detectado. Esse tipo de monitoração simula o que faz um usuário final para monitorar com êxito um aplicativo.

**Ferramentas Apica**

Apica fornece ferramentas que monitoram seu aplicativo do Azure "de fora." Para obter mais informações, consulte <a href="http://www.apicasystem.com/integration-partners/">http://www.apicasystem.com/integration-partners/</a>.


**AVIcode**

AVIcode adquirido pela Microsoft e agora é parte do centro de dados Microsoft. [AVIcode] oferece recursos com um conjunto abrangente de recursos de monitoramento de aplicativo de monitoramento de desempenho de aplicativos .NET.

**Perfil de desempenho**

Você pode [traçar o perfil] do seu aplicativo do Azure quando ele é executado no Azure para determinar problemas de desempenho. Quando você publicar seu aplicativo do Azure no Visual Studio, você pode escolher o perfil do aplicativo e selecionar as configurações de criação de perfil que você precisa.

**Assistente VM do Azure**

A ferramenta [assistente VM] é um projeto CodePlex que coleta todos os dados de solução de problemas relevantes em um local na área de trabalho remota em uma instância de máquina virtual. O botão **Health VM** fornece o status atual da instância.

###Depure erros onde você pode

Antes de implantar um aplicativo no Azure, é uma prática recomendada para depurar seu aplicativo localmente. O SDK do Azure contém emuladores que imitam o ambiente de nuvem do Azure, permitindo que você execute o aplicativo e faça testes rudimentares sem precisar implantar seu aplicativo. As ferramentas de depuração que você usar variam de acordo com a linguagem de programação e as ferramentas de desenvolvimento que você está usando.

Após a implantação de um aplicativo, você pode depurar na nuvem usando um depurador como o Visual Studio. É necessário criar uma compilação de depuração e implantá-lo. Para fazer isso, você deve se conectar a uma instância de função específica. Se você tiver um aplicativo complexo com várias funções e instâncias de função, pode ser muito difícil determinar qual instância de função para se conectar. Visual Studio Ultimate dá suporte IntelliTrace, que permite funções .NET rastrear as informações de depuração. Quando ocorre um problema você pode baixar essas informações e carregá-lo no Visual Studio. Você pode examinar o log do IntelliTrace de cada instância função para determinar onde ocorreu o problema. Embora haja algumas desvantagens para depuração na nuvem, há algumas circunstâncias em que é necessária. Nem todos os serviços do Azure tem um emulador (por exemplo, o Service Bus) e o desenvolvimento com suporte não todas as ferramentas (por exemplo, Mac e Linux) vêm com emuladores. 

Depois de ter depurado seu aplicativo localmente você provavelmente terá que contar com a instrumentação incorporada ao seu aplicativo para determinar onde estão ocorrendo problemas.

**Depuração do Node.js**

Para depurar aplicativos do Node.js, você pode usar a ferramenta Node-Inspector que está disponível em [GitHub](https://github.com/dannycoates/node-inspector) Node-Inspector pode ser executado localmente em sua máquina de desenvolvimento usando o emulador de armazenamento do Azure. Para obter mais informações consulte o blog de Jim Wang: [Emulador de nó de depuração no Azure]

Se você está depurando o aplicativo no Azure, instale a versão completa do IISNode do [GitHub](https://github.com/windowsazure/iisnode/downloads) em sua função web, uma função de trabalho ou uma instância VM. Conforme discutido anteriormente, essa não é uma maneira recomendada para depurar seu aplicativo quando ele está em produção e dimensionadas para várias instâncias, porque você pode não saber a qual instância de função ou a VM para depurar.

Para usar o Node-Inspector em uma função da web, instale o pacote na função da web e use a ferramenta Node-Inspector como faria normalmente. Para obter mais informações sobre a depuração com o Node-Inspector, consulte [Depuração com Node-Inspector]

**IntelliTrace**

Microsoft Visual Studio Ultimate contém [IntelliTrace], que pode ser ativado para depurar aplicativos antes da implantação em produção.
Suporta IntelliTrace ASP.NET e aplicativos WCF. Não há suporte para o IntelliTrace quando estiver habilitado em um serviço de produção, mas pode ser usado para obter exceções para um aplicativo após a implantação do Azure. Postagem do blog de Jim Nakashima descreve como usar o [IntelliTrace para depurar serviços de nuvem do Azure]

**Fiddler**

[Fiddler] é uma Web Debugging Proxy que registra todo o tráfego HTTP (S) entre o computador e a Internet. Fiddler permite que você inspecione o tráfego, defina pontos de interrupção e "ajuste" com os dados de entrada ou de saída. Fiddler é especialmente útil para a solução de armazenamento do Azure.

Para usar o Fiddler contra a malha de desenvolvimento local, use ipv4.fiddler em vez de 127.0.0.1:

-   Inicie Fiddler.
-    Inicie seu serviço na malha de desenvolvimento.
-   Procurar para http://ipv4.fiddler:/. Fiddler deve rastrear a solicitação.

Para usar o Fiddler contra o armazenamento local de desenvolvimento, modifique o arquivo de configuração de serviço para apontar para o Fiddler:

Abra o arquivo ServiceConfiguration e altere a sequência de conexão para:

	Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

-   Inicie Fiddler.
-   Inicie seu serviço. Fiddler deve rastrear as solicitações de armazenamento.

##Solução de problemas e modelos de hospedagem do Azure##
Esta seção aborda as práticas recomendadas para depuração de aplicativos usando os diferentes modelos de hospedagem do Azure.
<h2><a id="Websites"></a>Site do Azure</h2>

Quando estiver criando um site com suporte do Azure, siga as recomendações feitas neste documento quando possível. Isso inclui verificar e tratamento de erros, aplicando lógica de novas tentativas de falha transitória, rastreamento e log. Solução de problemas do Azure web sites é conseguida configurar sites da web para exibir erros de aplicativos, configuração de diagnóstico para um site da web, coletando dados de diagnóstico e, em seguida, analisar os dados coletados para identificar e resolver problemas.

Sites da web de Azure ativar a configuração das opções de diagnóstico a seguir:

-   Log do servidor web
-   Mensagens de erro detalhadas
-   Rastreamento de solicitação falha

Para obter mais informações, consulte [Solução de problemas no site do Azure].

Quando registros do servidor web para sites do Azure são ativados, o site registrará todas as transações de HTTP em um arquivo de log usando o [formato de arquivo de log estendido do W3C] Você pode usar [Log Parser] para consultar o arquivo de log. Algumas consultas de analisador de log de exemplo estão disponíveis em [Log Parser Plus] e [TechNet Log Parser exemplos] Se você deseja gerar o tipo de saída de gráfico em um computador que esteja executando o Office 2007/2010, instale o [Office 2003 Web Components] seguindo as instruções [Log Parser Plus](http://logparserplus.com/article/2)

Sites do Azure usam a mesma funcionalidade de rastreamento de solicitação falha disponível com o IIS 7.0 e versões posteriores. Não é, no entanto, configurável como rastreamento de solicitação com falha do IIS. Quando você ativa o rastreamento de solicitação com falha para sites do Azure, **todas as** solicitações com falha são capturadas. 

<h2><a id="Cloudservices"></a>Serviços de nuvem do Azure</h2>

Devido à natureza distribuída dos serviços de nuvem do Azure, é importante proteger seu aplicativo, fazer chamadas de forma assíncrona e lidando com tentativas de falha transitória, conforme descrito anteriormente.

A técnica de depuração usada para os serviços de nuvem do Azure depende do tipo de problema que você está enfrentando. Problemas que envolvem uma função específica ou uma instância de função, por exemplo uma função falha ao iniciar ou ciclo, são investigados melhor usando a área de trabalho remota. Nesses casos, você saberá quais funções ou instância é problemática e conectar-se à função afetada. Quando ocorre um problema e não estiver certo de qual instância de função está causando o problema, o rastreamento e o registro é um método melhor para solução de problemas. Diagnóstico do Azure oferece um mecanismo para coletar, gerenciar rastreamento e registrar as informações.

Alguns recursos de depuração novos foram adicionados para o Azure SDK 1.7 inclusive para torná-lo mais fácil de localizar os rastreamentos de pilha quando ocorrerem exceções e melhorias na conectividade da área de trabalho remota. Rastreamentos de pilha agora estão incluídos no Windows Log de eventos, tornando mais fácil ver exatamente o que deu errado. Além de conectividade da área de trabalho remota foi aprimorada. Se sua função for ciclo ou anulada, você poderá usar a área de trabalho remota para conectar-se à função problemática e investigar o problema. 

Os Azure Portal fornece acesso ao monitoramento de dados que ajuda os profissionais de TI e desenvolvedores prever e diagnosticar problemas em serviços de nuvem do Azure. Os valores padrão como "Porcentagem de CPU", "Dados em", "Dados Out", "Taxa de transferência de leitura de disco" e "Throughput de gravação de disco" são coletadas pelo host de VM. Não há nenhuma configuração necessária para habilitar essas métricas para instâncias de função e não há nenhum impacto de custo para os clientes. Informações adicionais de desempenho também podem ser coletadas. Para coletar informações detalhadas de diagnóstico, você deve ter uma sequência de conexão válida diagnóstico como essas informações serão armazenadas no armazenamento do Azure e provocará, portanto, os custos de armazenamento adicionais. Quando o usuário permite monitoramento detalhado, o portal configurar remotamente instâncias de função para coletar o conjunto padrão de contadores de desempenho. 

###Diagnóstico do Azure 

O original Azure SDK 1.0 incluído funcionalidade para coletar dados de diagnóstico e armazená-los no armazenamento do Azure conhecido coletivamente como o diagnóstico do Azure. Este software desenvolvido com o framework Event Tracing for Windows (ETW), atende a dois requisitos de design apresentados pela arquitetura do Azure fora de escala:

-   Salve dados de diagnóstico que podem ser perdidos durante uma fazer uma nova imagem da instância.
-   Fornece um repositório central para diagnóstico de várias instâncias.

Depois de configurar os diagnósticos na função, o diagnóstico coleta dados de diagnóstico de todas as instâncias dessa função específica. Você pode usar dados de diagnóstico para tarefas como depuração e solução de problemas, medição de desempenho, monitoramento de uso de recurso, análise de tráfego e planejamento de capacidade, e auditoria. Transfere para a conta de armazenamento do Azure para persistência ou pode ser programada ou sob demanda.

Diagnóstico do Azure altera o paradigma do servidor de quatro maneiras importantes:

-   Diagnóstico deve ser ativado no momento da criação de aplicativo.
-   Ferramentas/etapas específicas são necessárias para visualizar os resultados do diagnóstico.
-   Falha causará a perda de dados de diagnóstico, a menos que ele é escrito para armazenamento durável (armazenamento do Azure em vez de serem em cada instância).
-   Armazenamento diagnóstico acarreta um custo mensal quando armazenado no armazenamento do Azure.

Custo é de vital importância porque um dos principais benefícios do Azure é de redução de custos. A única maneira para eliminar o custo de utilização do diagnóstico hoje é para deixar os dados na máquina virtual.
Isso pode funcionar em uma implantação pequena, mas é impraticável, em que há muitas instâncias. Aqui estão algumas maneiras de minimizar o impacto financeiro:

-   Certifique-se de que a conta de armazenamento está no mesmo data center como seu aplicativo. Se por algum motivo não estiverem no mesmo data center, escolha o intervalo de transferências agendadas com sabedoria. Tempos mais curtos de transferência aumentará a relevância de dados, mas o negócio logoff pode não ser grande o suficiente justificar a sobrecarga de processamento e largura de banda adicional.
-   Copiar e limpar os dados de diagnóstico do Azure Storage periodicamente. Os dados de diagnóstico de trânsito por meio de armazenamento do Azure, mas não residem lá desnecessariamente. Há uma série de ferramentas para fazer isso: System Center Monitoring Pack para o Azure, gerente de diagnóstico do Azure do Cerebrata e cmdlets do PowerShell do Azure.
-   Escolha apenas os dados de diagnóstico que você precisará solucionar problemas e monitorar o seu aplicativo. Capturar dados demais pode dificultar a solução além dos custos significativamente mais.
-   Controle a coleta e a extensão dos dados de diagnóstico implementando uma opção sob demanda em seu aplicativo.
-   Utilizar o nível de registro (detalhado, informações, aviso, erro) para que todas as informações estão disponíveis, e em seguida, utilizar a configuração de diagnóstico para coletar seletivamente dados de pós-implantação.

<h2><a id="Vms"></a>Máquinas Virtuais do Azure</h2>

Solução de problemas aplicativos em execução na Máquina Virtual do Azure geralmente envolve as mesmas técnicas de solução de problemas que você usaria com os sistemas operacionais e plataformas em uso. Por exemplo:

-   Windows Server 2008 R2 x64 (RTM e SP1); O serviço Windows 8 x64 (Datacenter e núcleo). Há uma grande quantidade de informações disponíveis sobre o assunto e muitas ferramentas disponíveis para ajudá-lo a fazer o trabalho rapidamente.

-   Para obter mais informações sobre as abordagens, consulte [Como: depurar aplicativos de serviço do Windows]

-   Para obter um vídeo que ilustra as abordagens recomendadas, consulte [apresentação Windows depuração e solução de problemas de Daniel Pearson]

- Abra o Suse Linux. Também há uma enorme quantidade de recursos disponíveis para solucionar problemas de aplicativos no Suse Linux, ambos os [documentação do Suse Linux] e na internet.
- Linux Ubuntu. Novamente, existe uma grande quantidade de informações. Para iniciar com a documentação do produto, consulte [https://help.ubuntu.com/](https://help.ubuntu.com/)
-CentOS Linux. Para obter mais informações, consulte [http://centos.org/](http://centos.org/).

<h2><a id="PlatformServices"></a>Serviços Móveis - Recursos - Azure</h2>

Muitos o Azure serviços como banco de dados do Azure SQL, do Active Directory do Azure e armazenamento do Azure tem orientações que são específicas para seu uso, independentemente se o aplicativo está em execução no Azure, qual linguagem de programação ou bibliotecas ele foi criado com o, ou em execução em um sistema operacional não Microsoft. As informações a seguir fornecem as melhores práticas específicas para alguns desses serviços.

Há muitas bibliotecas com suporte que implementam práticas recomendadas para chamadas assíncronas, rastreamentos e conforme descrito na parte de design deste documento de log de eventos.

####Conta de armazenamento do Azure

Os links a seguir abordam designs ou práticas para atenuar problemas que requerem solução ou locais em que você deve adicionar o rastreamento ou registro de trabalho.

- Códigos de status e de erro armazenamento: [http://msdn.microsoft.com/pt-br/library/windowsazure/dd179382.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/dd179382.aspx)

- Análises armazenamento: [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx) (consulte a três links "para obter mais informações" no final).

- Visão geral das diretivas de repetição na biblioteca de cliente de armazenamento do Azure: [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx)

- Como obter o máximo proveito das tabelas do Azure: [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx)

	- Consulte as seguintes seções: práticas recomendadas; Dicas para a programação do Azure; e tempos limite e ServerBusy - é normal?

- Protegendo seus Blobs contra erros de aplicativo: [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx)

**Pesquisadores de armazenamento do Azure**

Há várias maneiras de explorar o armazenamento do Azure. A equipe de armazenamento do Azure acabei com um [lista de gerenciadores de armazenamento] Qualquer um desses permitirá que você veja os arquivos de diagnóstico e de análises de armazenamento do Azure. Laboratório de cloudberry [Explorer para armazenamento de BLOBs do Azure] fornece uma interface de usuário para habilitar a análise de armazenamento diretamente no aplicativo clicando em **configurações de armazenamento**

<h2><a id="Misc"></a>Solução de problemas do Service Bus no Azure</h2>

Esta seção fornece diretrizes sobre como desenvolver um aplicativo que usa o Service Bus do Azure de forma eficiente e de fácil manutenção que irá minimizar problemas comuns de alto nível. Ele também fornece detalhes sobre como identificar e erros comuns do Service Bus do endereço.

###Diretrizes gerais

O Service Bus é uma escala de internet [o Service Bus corporativo] que suporta retransmitido e orientado recursos de mensagens. O Service Bus implementa cotas e limites em um nível de sistema para ambos os tipos de mensagens. Se seu aplicativo exceder as cotas, será reduzida ou suas solicitações ou mensagens serão rejeitadas. Para obter detalhes completos sobre cotas do Service Bus e o comportamento que você verá quando são excedidos, consulte [cotas do Service Bus do Azure] Alguns cotas são usuário definido, por exemplo, o tamanho de uma fila ou tópico, que é definido quando a entidade é criada.

Para obter uma exibição dos dados em seu Service Bus mensagens entidade e como está sendo processada, você pode usar [Explorer do Service Bus] ou do Server Explorer nas ferramentas do Azure para Visual Studio (versão 1.7 ou posterior) para criar, excluir e testar filas, tópicos, assinaturas e regras. Essa é uma maneira excelente para solucionar problemas de um aplicativo que está em execução mas não processando dados da maneira esperada. Essas ferramentas incluem funcionalidade que permite que você teste filas, tópicos e entidades de retransmissão, rastrear as operações executadas pelo remetente individual e receptor tarefas, monitorar o progresso e o desempenho de uma execução de teste contínuo e geram logs detalhados dos resultados, incluindo mensagens de erro.

###Retransmissão de Service Bus

O serviço de "relay" do Service Bus é executado na nuvem e oferece suporte a uma variedade de protocolos de transporte diferentes e padrões, inclusive o SOAP, WS - serviços da Web \*e o restante. Você pode usar a retransmissão do Service Bus como um representante para ouvir as sessões de entrada e solicitações enviadas a um serviço WCF. Na mensagem retransmitida padrão, um local ou serviço baseado em nuvem conecta-se ao serviço de retransmissão por meio de uma porta de saída e cria um soquete de bi-direcional para comunicação ligado a um endereço específico rendezvous. O cliente não precisa saber onde o serviço reside, e o serviço no local não é necessário que as portas de entrada abertas no firewall. No entanto, dependendo da sua configuração de rede, você poderá encontrar problemas ao conectar-se a retransmissão do Service Bus protegido por um firewall ou um servidor proxy. [Hospedagem atrás de um Firewall com o Service Bus] descreve como solucionar problemas e resolver esses problemas de conexão.

###Filas, Tópicos e Retransmissão de Service Bus

Tópicos e filas do Service Bus fornecem comunicação funcionalidade mensagens-são enviadas para a fila do Service Bus ou o tópico onde são confiável mantidos até que o receptor esteja pronto para usá-los. Destinatários e remetentes de mensagens não precisam estar on-line ao mesmo tempo; a infraestrutura de mensagens confiável armazena as mensagens até que o participante de consumo esteja pronto para recebê-los. A API de mensagem pode encontrar uma variedade de erros que podem afetar o seu aplicativo. Elas podem ser amplamente agrupadas nas seguintes categorias:

-   Erro do usuário-por exemplo, um código indicando um argumento é inválido.
    Ação recomendada: tente corrigir o código antes de prosseguir.
-   Erro de instalação/configuração-por exemplo, uma fila ou um tópico associado a ação não existe ou foi excluído. Ação recomendada: Revise sua configuração e alterá-lo se necessário.
-   Erro transitório-por exemplo, uma resposta indicando que o serviço não pôde processar a solicitação no momento. Ação recomendada: Repetir a operação ou notificar os usuários. Para obter mais informações, consulte [Manipulando erros transitórios de comunicação]
-   Outros erros-por exemplo, erros de tempo limite ou erros indicando que um bloqueio de mensagem foi perdido. Ação recomendada: você geralmente não tratar essas exceções para executar limpezas ou anulações. Elas podem ser usadas para rastreamento.

[Exceções de mensagens] fornece uma visão geral das exceções que os usuários das bibliotecas de cliente do Service Bus para .NET pode encontrar, juntamente com recomendações sobre como lidar com cada tipo de exceção. Como as bibliotecas de cliente .NET estão totalmente alinhados à estrutura das bibliotecas do Service Bus para outros idiomas, neste guia pode ser útil mesmo que você não está programando em uma linguagem .NET. Em alguns casos, por exemplo com erros transitórios, você pode repetir a ação. Você pode seguir as diretrizes descritas anteriormente neste artigo para lidar eficientemente com erros transitórios de manipulação de erros transitórios. Além disso, para obter mais detalhes, práticas recomendadas e código de exemplo que demonstra como manipular erros transitórios do Service Bus em um aplicativo .NET, consulte o [Manipulando erros transitórios de comunicação] seção no artigo diretrizes para desenvolvedores do Azure [API de mensagem intermediário do Service Bus de práticas recomendadas para aproveitar o Azure]

Outra área para enfocar ao desenvolver um aplicativo que usa a comunicação de mensagens é para garantir que você implementar lógica que pode com precisão e eficiência manipular anomalias em mensagens de recebimento de mensagens confiável.
A seção "Implementando confiável recebe Loops de mensagem" o [API de mensagem intermediário do Service Bus de práticas recomendadas para aproveitar o Azure] artigo descreve diversas técnicas para usar o **PeekLock** modo, que é o modo que oferece suporte a várias entregas de mensagem se a mensagem não é entregue com êxito na primeira tentativa de recebimento.
O artigo fornece práticas recomendadas que ajudam a garantir que seu aplicativo não processa mensagens duplicadas. Ele também ajuda a evitar problemas que podem ocorrer devido ao tempo limite de bloqueio e melhorar o desempenho geral de **PeekLock** modo, garantindo a processar mensagens imediatamente. O artigo também fornece código de exemplo que usa as bibliotecas de cliente do Service Bus para .NET.

###Recursos adicionais de solução de problemas

Para obter detalhes adicionais sobre erros do Service Bus comuns e maneiras para investigar e solucioná-los, consulte [o Service Bus de solução de problemas]


##Active Directory e Access Control do Azure

-   Códigos de erro:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/gg185949.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/gg185949.aspx)
-   Limitações de Service ACS:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/gg185909.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/gg185909.aspx)

<h2><a id="SQLTroubleshooting"></a>Biblioteca de Bancos de dados SQL do Azure</h2>

Quando há a interação com um banco de dados do Azure SQL extra deve ter cuidado para lidar com a natureza distribuída de aplicativos de banco de dados do Azure SQL. Esta seção aborda várias áreas que garantem a atenção. Esta não é uma lista completa. A chave para escrever código com suporte de banco de dados do Azure SQL é examinar os códigos de retorno e certifique-se de que você tem código de repetição sólido para lidar com falhas.

Normalmente, seu aplicativo deve tratar falhas de login. Instâncias de banco de dados SQL requerem o uso da autenticação do SQL. Se você não conseguir fazer login, suas credenciais não são válidas ou o banco de dados que você solicitou não está disponível.

Seu aplicativo deve tratar o serviço está inacessível. Se o servidor já foi fornecido e o serviço de banco de dados do Azure SQL está disponível (você pode verificar isso usando o [Status de integridade do Azure] página), a causa provável é a problemas de configuração em sua instalação no local. Por exemplo, talvez não seja possível resolver o nome (que pode ser testado com ferramentas como tracert), talvez você tenha um firewall bloqueando a porta 1433 usado pelo banco de dados SQL ou talvez você esteja usando um servidor proxy que não está configurado corretamente. Use as mesmas técnicas para solucionar essas dificuldades que você faria para SQL Server. Para obter mais informações, consulte [guia de solução de problemas de conectividade do SQL banco de dados] e [solução de banco de dados SQL]

Seu aplicativo deve tratar erros gerais de rede. Você pode receber erros gerais de rede porque o banco de dados do Azure SQL pode desconectar usuários sob estas circunstâncias:

-   Quando uma conexão está ociosa por um longo período de tempo
-   Quando uma conexão consome uma quantidade excessiva de recursos ou mantiver uma transação por um longo período de tempo
-   Se o servidor está muito ocupado

Para melhorar o desempenho do banco de dados do Azure SQL, use as mesmas técnicas que você usaria com SQL Server. Consulte os tópicos a seguir para obter mais informações:

-   [Solução de consultas] nos Manuais Online do SQL Server
-   [Solucionar problemas e otimizar consultas com banco de dados SQL]
-   [SQL Database considerações de desempenho e solução de problemas]
-   [Melhora o desempenho de e/s]
-   [System Center Monitoring Pack para o banco de dados SQL]

Banco de dados do Azure SQL usa um subconjunto das mensagens de erro do SQL Server. Para obter mais informações sobre erros do SQL Server, consulte [(mecanismo de banco de dados) de referência de eventos e erros] nos Manuais Online do SQL Server

Se você precisar recuperar nomes de login ou senhas, contate o administrador do serviço, que poderá conceder acesso apropriado para o servidor e o banco de dados. Os administradores de serviço também podem redefinir suas próprias senhas usando o Portal de gerenciamento do Azure.

Consultas de banco de dados do SQL podem falhar por várias razões - uma consulta malformada, problemas de rede e assim por diante. Alguns erros são transitórios, ou seja, que o problema geralmente desaparece por si só. Esse subconjunto de erros, faz sentido para repetir a consulta após um breve intervalo. Se a consulta ainda falhar após várias tentativas, você deverá relatar um erro. Claro, nem todos os erros são transitórios. SQL erro 102, "Sintaxe incorreta" não vão embora não importa quantas vezes você envia a mesma consulta. Resumindo, a implementação de lógica de repetição robusta requer algumas ideias. Um símbolo de erro de fluxo (TDS) de dados tabulares é enviado antes de se desconectar usuários, quando possível. Para melhorar a experiência de um aplicativo, é recomendável que você implemente a lógica de repetição em seus aplicativos de banco de dados SQL para detectar esses erros. Quando ocorre um erro, restabelecer a conexão e, em seguida, execute novamente os comandos com falha ou a consulta. Consulte os tópicos a seguir para obter mais informações:

-   [Repetir lógica para falhas transitórias no banco de dados SQL]
-   [Exemplo de lógica de repetição de banco de dados SQL]
-   [Bloco de aplicativos de manipulação de falhas transitórias]

###Azure SQL estratégia de Backup e restauração

Banco de dados do Azure SQL requer sua própria estratégia de backup e restauração devido ao ambiente e ferramentas disponíveis. De várias maneiras os riscos têm sido mediados pelo banco de dados em data centers Microsoft. As ferramentas que temos hoje abrangem os outros fatores de risco, mas melhores ferramentas estarão disponíveis para tornar o trabalho muito mais fácil. Red-gate publicou recentemente uma ferramenta gratuita para o banco de dados SQL backup e restauração que pode ser encontrada aqui: [http://www.red-gate.com/products/dba/sql-azure-backup/](http://www.red-gate.com/products/dba/sql-azure-backup/)

Sincronização de dados de SQL permite que você facilmente criar e agendar sincronizações bidirecionais de dentro do site de sincronização de dados sem a necessidade de escrever uma única linha de código. Você pode encontrar mais informações aqui:
[http://msdn.microsoft.com/pt-br/library/windowsazure/hh456371.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/hh456371.aspx)

Para obter mais informações sobre o banco de dados SQL estratégias de restauração e backup Consulte os seguintes artigos:

-   Este tópico fornece uma visão geral das estratégias de restauração e Backup de banco de dados SQL:
    [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx](http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx)
-   Este tópico explica como fazer backup de um banco de dados para outro banco de dados no mesmo servidor:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/ff951631.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/ff951631.aspx)
-   Este tópico explica como exportar uma instância existente do banco de dados SQL para um blob em uma conta de armazenamento específica:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/hh335292.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/hh335292.aspx)
-   Este tópico explica como exportar uma instância existente do banco de dados SQL para um blob em uma conta de armazenamento específica:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/hh335291.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/hh335291.aspx)
-   Este tópico descreve os recursos de continuidade de negócios fornecidos pelo banco de dados SQL:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/hh852669.aspx](http://msdn.microsoft.com/pt-br/library/windowsazure/hh852669.aspx)

<h2><a id="Cache"></a>Caching do Azure</h2>
O Caching do Azure apresenta dois tipos: Caching compartilhado do Azure e Caching do Azure (visualização) baseado em função. Caching compartilhado é um serviço do Azure que fornece serviços Caching multitenent. Hosts de Caching do Azure (visualização) de Caching em uma função usando uma parte da memória das máquinas virtuais que hospedam as instâncias da função. Para solucionar problemas do Caching do Azure, observe o comportamento do cache de códigos de erro de verificação e capturar exceções. Ao usar o Caching com base em função (visualização), você também pode usar os contadores de desempenho. Caching de problemas geralmente se encaixam em uma das categorias a seguir:

- 	Erros relacionados a cota - cota foi excedida (Caching compartilhado)
- 	Otimização - ocorre quando não há memória física suficiente para dar suporte a outros itens armazenados em cache
- 	Remoção - itens são obrigatoriamente removido para liberar espaço para novos itens de uma maneira que reduz o desempenho do aplicativo
- 	Vencimento - tempos de expiração são definidas muito curto ou longo

Para obter mais informações sobre erros relacionados a cota, consulte as [Noções básicas sobre cotas].





















[Pacote de Monitoramento]: http://www.microsoft.com/download/en/details.aspx?id=11324
[Microsoft System Center Operations Manager]: http://www.microsoft.com/pt-br/server-cloud/system-center/operations-manager.aspx
[Ferramenta de gerenciamento na plataforma (MMC) do Azure]: http://wapmmc.codeplex.com/
[Gerenciador de diagnóstico do Azure]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[Studio de armazenamento nuvem]: http://cerebrata.com/Products/CloudStorageStudio/
[Cmdlets de gerenciamento do Azure]: http://cerebrata.com/Products/AzureManagementCmdlets/
[Gomez]: http://www.compuware.com/application-performance-management/
[Keynote]: http://www.keynote.com/solutions/
[Pingdom]: http://pingdom.com/
[AlertBot]: http://www.alertbot.com/products/website-monitoring/default.aspx
[IntelliTrace]: http://msdn.microsoft.com/pt-br/library/dd264915.aspx
[tarefas de inicialização]: http://msdn.microsoft.com/pt-br/library/gg456327.aspx
[AVIcode]: http://www.microsoft.com/pt-br/server-cloud/system-center/avicode.aspx
[traçar o perfil]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh369930.aspx
[Assistente VM]: http://azurevmassist.codeplex.com/
[Emulador de nó de depuração no Azure]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[Depuração com Node-Inspector]: http://howtonode.org/debugging-with-node-inspector

[IntelliTrace para depurar serviços de nuvem do Azure]: http://blogs.msdn.com/b/jnak/archive/2010/06/07/using-intellitrace-to-debug-windows-azure-cloud-services.aspx

[Solução de problemas no site do Azure]: /pt-br/develop/net/best-practices/troubleshooting-web-sites/
[Formato de arquivo de log estendido do W3C]: http://go.microsoft.com/fwlink/?LinkID=90561
[Log Parser]: http://go.microsoft.com/fwlink/?LinkId=246619
[Log Parser Plus]: http://logparserplus.com/Examples
[TechNet Log Parser exemplos]: http://technet.microsoft.com/pt-br/library/ee692659.aspx
[Componentes web do Office 2003]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[Como: depurar aplicativos de serviço do Windows]: http://msdn.microsoft.com/pt-br/library/7a50syb3%28v=vs.90%29.aspx
[Apresentação Windows depuração e solução de problemas de Daniel Pearson]: http://technet.microsoft.com/pt-br/edge/Video/hh867800
[Documentação do Suse Linux]: https://www.suse.com/documentation/
[lista de gerenciadores de armazenamento]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[Explorer para armazenamento de BLOBs do Azure]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[o Service Bus corporativo]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[cotas do Service Bus do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee732538.aspx
[Explorer do Service Bus]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[Hospedagem atrás de um firewall com o Service Bus]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee706729.aspx
[Manipulando erros transitórios de comunicação]: http://msdn.microsoft.com/pt-br/library/hh851746(VS.103).aspx
[Exceções de mensagens]: http://msdn.microsoft.com/pt-br/library/hh418082.aspx
[Manipulando erros transitórios de comunicação]: http://msdn.microsoft.com/pt-br/library/hh851746(VS.103).aspx
[API de mensagem intermediário do Service Bus de práticas recomendadas para aproveitar o Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh545245.aspx
[O Service Bus de solução de problemas]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee706702.aspx
[Status de integridade do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[Guia de solução de problemas de conectividade do SQL banco de dados]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[solução de banco de dados SQL]: http://msdn.microsoft.com/pt-br/library/ee730906.aspx
[Solução de consultas]: http://msdn.microsoft.com/pt-br/library/ms186351(SQL.100).aspx
[Solucionar problemas e otimizar consultas com banco de dados SQL]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[SQL Database considerações de desempenho e solução de problemas]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[Melhora o desempenho de e/s]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[System Center Monitoring Pack para o banco de dados SQL]: http://www.microsoft.com/pt-br/download/details.aspx?id=10631
[(mecanismo de banco de dados) de referência de eventos e erros]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[Repetir lógica para falhas transitórias no banco de dados SQL]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[Exemplo de lógica de repetição de banco de dados SQL]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[Bloco de aplicativos de manipulação de falhas transitórias]: http://msdn.microsoft.com/pt-br/library/hh680934(PandP.50).aspx
[Pacote de integração do Microsoft Enterprise Library 5.0 para Azure]: http://msdn.microsoft.com/pt-br/library/hh680918%28v=pandp.50%29.aspx
[Assumir controle de registro em log e rastreamento no Azure]: http://msdn.microsoft.com/pt-br/magazine/ff714589.aspx
[Estrutura de nuvem de aplicativos e extensões (CloudFx)]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[Fiddler]: http://www.fiddler2.com/fiddler2/
[Noções básicas sobre cotas]: http://msdn.microsoft.com/pt-br/library/gg185683.aspx
[Solução de problemas de cache]: http://go.microsoft.com/fwlink/?LinkId=252730 

