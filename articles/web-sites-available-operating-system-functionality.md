<properties 
	pageTitle="Funcionalidade do sistema operacional nos aplicativos Web do Serviço de Aplicativo do Azure" 
	description="Saiba mais sobre a funcionalidade do sistema operacional disponível para aplicativos Web no Serviço de Aplicativo do Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="cephalin"/>

# Funcionalidade do sistema operacional nos aplicativos Web do Serviço de Aplicativo do Azure #

Este artigo descreve a funcionalidade do sistema operacional de linha de base comum disponível a todos os aplicativos em execução em aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Essa funcionalidade inclui acesso a arquivos, redes e registros, além de logs de diagnóstico e eventos. 

<a id="tiers"></a>
## Camadas de plano de Serviço de Aplicativo
Os aplicativos Web executam aplicativos do cliente em um ambiente de hospedagem multilocatário. Os aplicativos Web implantados nas camadas **Gratuito** e **Compartilhado** são executados em processos de trabalho em máquinas virtuais compartilhadas, enquanto os aplicativos Web implantados nas camadas **Padrão** e **Premium** são executados em máquinas virtuais dedicadas especificamente para os aplicativos Web associados a um único cliente.

Como os aplicativos Web dão suporte a uma experiência de colocação em escala perfeita entre camadas diferentes, a configuração de segurança imposta a aplicativos Web permanece a mesma. Isso garante que os aplicativos Web não se comportem inesperadamente de maneira diferente, falhando de maneiras inesperadas, quando um aplicativo Web alternar de uma camada para outra.

<a id="developmentframeworks"></a>
## Estruturas de desenvolvimento

As camadas de aplicativos Web controlam a quantidade de recursos de computação (CPU, armazenamento em disco, memória e egresso de rede) disponíveis para aplicativos Web. No entanto, a amplitude da funcionalidade da estrutura disponível para aplicativos permanece a mesma, independentemente das camadas de colocação em escala.

Os aplicativos Web dão suporte a várias estruturas de desenvolvimento, inclusive ASP.NET, ASP clássico, node.js, PHP e python - todos executados como extensões no IIS. Para simplificar e normalizar a configuração de segurança, os aplicativos Web normalmente executam as diversas estruturas de desenvolvimento com suas configurações padrão. Uma abordagem para configurar os aplicativos Web poderia ser personalizar a área de superfície da API e a funcionalidade para cada estrutura de desenvolvimento individual. Em vez disso, os aplicativos Web utilizam uma abordagem mais genérica, habilitando uma linha de base comum de funcionalidade do sistema operacional, independentemente da estrutura de desenvolvimento do aplicativo Web.

As seções a seguir resumem os tipos gerais de funcionalidade do sistema operacional disponíveis para aplicativos Web no Azure.


<a id="FileAccess"></a>
##Acesso a arquivos

Existem diversas unidades dentro dos aplicativos Web, inclusive unidades locais e unidades de rede.

<a id="LocalDrives"></a>
### Unidades locais

Basicamente, os aplicativos Web formam um serviço em execução na infraestrutura do Azure PaaS (plataforma como serviço). Dessa forma, as unidades locais "anexadas" a uma máquina virtual são dos mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isso inclui uma unidade do sistema operacional (a unidade D:), uma unidade do aplicativo que contém arquivos cspkg de pacote do Azure usados exclusivamente por aplicativos Web (e inacessíveis para os clientes) e uma unidade do "usuário" (a unidade C:), cujo tamanho varia dependendo do tamanho da VM.

<a id="NetworkDrives"></a>
### Unidades de rede (também conhecidas como compartilhamentos UNC)

Um dos aspectos exclusivos dos aplicativos Web que simplifica sua implantação e manutenção é que todo o conteúdo do usuário é armazenado em um conjunto de compartilhamentos UNC. Esse modelo é mapeado muito bem para o padrão comum de armazenamento de conteúdo usado por ambientes de hospedagem na Web local com vários servidores com balanceamento de carga. 

Nos aplicativos Web, existem vários compartilhamentos UNC criados em cada data center. Uma porcentagem do conteúdo do usuário para todos os clientes em cada data center é alocada para cada compartilhamento UNC. Além disso, todo o conteúdo de arquivo de uma assinatura do cliente única é sempre colocado no mesmo compartilhamento UNC. 

Por conta da maneira como os serviços de nuvem funcionam, a máquina virtual específica responsável por hospedar um compartilhamento UNC mudará com o passar do tempo. Existe a garantia de que compartilhamentos UNC serão montados por máquinas virtuais diferentes à medida que elas forem ligadas e desligadas durante o curso normal das operações em nuvem. Por esse motivo, aplicativos Web jamais devem fazer pressuposições codificadas de que as informações da máquina em um caminho de arquivo UNC permanecerão estáveis com o passar do tempo. Em vez disso, eles devem usar o caminho absoluto *faux* prático **D:\home\site** fornecido pelos aplicativos Web. Esse caminho absoluto faux oferece um método portátil, independente do aplicativo e do usuário, para se referir ao seu próprio aplicativo. Usando **D:\home\site**, uma pessoa pode transferir arquivos compartilhados de um aplicativo Web para outro sem que seja necessário configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>
### Tipos de acesso a arquivos concedido a um aplicativo Web

A assinatura de cada cliente tem uma estrutura de diretório reservado em um compartilhamento UNC específico dentro de um data center. Como um cliente pode ter vários aplicativos Web criados dentro de um data center específico, todos os diretórios pertencentes a uma única assinatura de cliente são criados no mesmo compartilhamento UNC. O compartilhamento pode incluir diretórios como os de conteúdo, erro e logs de diagnóstico, e as versões anteriores do aplicativo Web foram criadas pelo controle do código-fonte. Conforme esperado, os diretórios do aplicativo Web de um cliente estão disponíveis para acesso de leitura e gravação em tempo de execução pelo código de um aplicativo Web.

Nas unidades locais anexadas à máquina virtual que executa um aplicativo Web, os aplicativos Web reservam uma parte do espaço na unidade C:\ para armazenamento local temporário específico de aplicativo Web. Embora um aplicativo Web tenha acesso de leitura/gravação completo em seu próprio armazenamento local temporário, esse armazenamento não se destina, de fato, a ser usado diretamente pelo código do aplicativo. Em vez disso, o objetivo é fornecer um armazenamento de arquivo temporário para o IIS e para as estruturas do aplicativo Web. Os aplicativos Web também limitam a quantidade de armazenamento local temporário disponível a cada aplicativo Web para evitar que aplicativos Web individuais consumam quantidades excessivas de armazenamento de arquivo local.

Dois exemplos de como os aplicativos Web do Azure utilizam armazenamento local temporário são o diretório para arquivos do ASP.NET temporários e o diretório para arquivos compactados do IIS. O sistema de compilação do ASP.NET usa o diretório "Arquivos do ASP.NET Temporários" como um local de cache de compilação temporária. O IIS usa o diretório "Arquivos Compactados Temporários do IIS" para armazenar a saída de resposta compactada. Os usos desses tipos de arquivo (bem como outros) são remapeados nos aplicativos Web do Azure para o armazenamento local temporário por aplicativo. Esse remapeamento garante que a funcionalidade continua conforme esperado.

Cada aplicativo nos aplicativos Web é executado como uma identidade de processo trabalhador aleatório com poucos privilégios exclusiva chamada de "identidade pool de aplicativos", descrita mais detalhadamente aqui: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). O código do aplicativo usa essa identidade no acesso somente leitura básico à unidade do sistema operacional (a unidade D:). Isso significa que o código do aplicativo pode listar estruturas de diretório comuns e ler arquivos comuns na unidade do sistema operacional. Embora isso possa parecer ser um nível de acesso um pouco mais amplo, os mesmos diretórios e arquivos permanecem acessíveis quando você provisiona uma função de trabalho em um serviço hospedado do Azure e ler o conteúdo da unidade. 

<a name="multipleinstances"></a>
### Acesso a arquivos em várias instâncias

O diretório base apresenta o conteúdo de um aplicativo, e os aplicativos Web podem gravar nele. Se um aplicativo Web for executado em várias instâncias, o diretório base será compartilhado entre todas as instâncias para que todas as instâncias vejam o mesmo diretório. Assim, por exemplo, se um aplicativo Web salvar arquivos carregados no diretório base, esses arquivos estarão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>
## Acesso à rede
O código do aplicativo pode usar protocolos com base em TCP/IP e UDP para estabelecer conexões de rede de saída com pontos de extremidade acessíveis pela Internet que expõem serviços externos. Os aplicativos podem usar esses mesmos protocolos para se conectar aos serviços dentro do Azure&#151; por exemplo, estabelecendo conexões HTTPS com o SQL Azure.

Também existe uma capacidade limitada para que aplicativos estabeleçam uma conexão de loopback local e um aplicativo escute nesse soquete de loopback local. Esse recurso existe principalmente para permitir que os aplicativos escutem em soquetes de loopback locais como parte de sua funcionalidade. O aplicativo de cada cliente vê uma conexão de loopback "particular"; o aplicativo "A" não pode escutar um soquete de loopback local estabelecido pelo aplicativo "B".

Os pipes nomeados também são compatíveis como um mecanismo de comunicação entre processos (IPC) entre processos diferentes que executem coletivamente um aplicativo Web. Por exemplo, o módulo FastCGI do IIS depende de pipes nomeados para coordenar os processos individuais que executam páginas PHP.


<a id="Code"></a>
## Execução de código, processos e memória
Conforme observado anteriormente, os aplicativos Web são executados dentro de processos de trabalho com poucos privilégios usando uma identidade de pool de aplicativos aleatória. O código do aplicativo tem acesso ao espaço da memória associado ao processo de trabalho, bem como a todos os processos filho que possam ter sido gerados por processos CGI ou outros aplicativos. No entanto, o aplicativo Web de um cliente não pode acessar a memória ou os dados do aplicativo Web de outro cliente, mesmo que ele esteja na mesma máquina virtual.

Os aplicativos podem executar scripts ou páginas gravadas com estruturas de desenvolvimento de aplicativos Web compatíveis. Os aplicativos Web não definem configurações de estrutura do aplicativo Web como modos mais restritos. Por exemplo, aplicativos Web do ASP.NET em execução nos aplicativos Web do Azure são executados com confiança "total", e não como um modo de confiança mais restrito. As estruturas de aplicativo, inclusive o ASP clássico e o ASP.NET, podem chamar componentes COM em processo (mas não componentes COM fora de processo) como ADO (ActiveX Data Objects) registrados por padrão no sistema operacional Windows.

Os aplicativos Web podem gerar e executar códigos arbitrários. É aceitável que um aplicativo Web faça coisas como gerar um shell de comando ou executar um script do PowerShell. No entanto, embora códigos e processos arbitrários possam ser gerados com base em um aplicativo Web, programas executáveis e scripts continuam restritos aos privilégios concedidos ao pool de aplicativos pai. Por exemplo, um aplicativo Web pode gerar um executável que faz uma chamada HTTP de saída, mas esse mesmo executável não pode tentar desassociar o endereço IP de uma máquina virtual de seu NIC. Fazer uma chamada de rede de saída é permitido para código com poucos privilégios, mas tentar redefinir as configurações de rede em uma máquina virtual exige privilégios administrativos.


<a id="Diagnostics"></a>
## Logs de diagnóstico e eventos
As informações do log são outro conjunto de dados que alguns aplicativos Web tentam acessar. Entre os tipos de informações de log disponíveis para código em execução em aplicativos Web estão informações de diagnóstico e log geradas por um aplicativo Web que também são facilmente acessíveis para um aplicativo Web. 

Por exemplo, os logs HTTP do W3C gerados por um aplicativo Web ativo estão disponíveis em um diretório de log no local do compartilhamento de rede criado para o aplicativo Web ou disponíveis no armazenamento de blob, caso um cliente tenha definido o registro em log do W3C como armazenamento. A última opção permite que grandes quantidades de logs sejam coletadas sem o risco de exceder os limites de armazenamento de arquivo associados a um compartilhamento de rede.

Do mesmo modo, as informações de diagnóstico em tempo real de aplicativos do .NET também podem ser registradas em log usando-se a infraestrutura de rastreamento e diagnóstico do .NET com opções para gravar as informações de rastreamento no compartilhamento de rede do aplicativo Web ou em um local de armazenamento de blob.

As áreas de registro em log de diagnóstico e rastreamento que não estão disponíveis para aplicativos Web no Azure são eventos ETW do Windows e logs de evento do Windows comuns (por exemplo, logs de evento de sistema, aplicativo e segurança). Como as informações de rastreamento ETW podem abranger todo o computador (com as ACLs certas), o acesso de leitura e gravação para eventos ETW permanece bloqueado. Os desenvolvedores podem notar que chamadas à API para ler e gravar eventos ETW e logs de eventos do Windows estão aparentemente funcionando, mas isso é porque os aplicativos Web estão "falsificando" as chamadas para que pareçam ter êxito. Na realidade, o código do aplicativo Web não tem acesso a esses dados de evento.

<a id="RegistryAccess"></a>
## Acesso ao Registro
Os aplicativos têm acesso somente leitura a grande parte (mas nem todo) do Registro da máquina virtual em que estão sendo executados. Na prática, isso significa que as chaves do Registro que permitem acesso somente leitura ao grupo Usuários local são acessíveis por aplicativos Web. Uma área do Registro que atualmente não é compatível com acesso de leitura ou gravação é a seção HKEY_CURRENT_USER.

O acesso de gravação ao Registro está bloqueado, inclusive o acesso a chave do Registro por usuário. Do ponto de vista de um aplicativo, o acesso de gravação ao Registro jamais deve ser usado em um ambiente de nuvem porque os aplicativos podem (e vão) ser migrados entre máquinas virtuais diferentes. O único armazenamento gravável persistente que pode ser usado por um aplicativo Web é a estrutura do diretório de conteúdo por aplicativo armazenada nos compartilhamentos UNC dos aplicativos Web. 

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência à navegação pelo portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->