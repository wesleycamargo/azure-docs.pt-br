# Computação

O Azure permite implantar e monitorar seu código de aplicativo
em execução dentro de um datacenter da Microsoft. Quando você cria um aplicativo
e executa-o no Azure, o código e a configuração, juntos, isso é
chamado de um serviço hospedado do Azure. Serviços hospedados são fáceis de
gerenciar, escalar e reduzir verticalmente, reconfigurar e atualizar com novas versões do
código do aplicativo. Este artigo se concentra no
modelo de aplicativo de serviço hospedado do Azure.<a id="compare" name="compare"></a>

## Sumário<a id="_GoBack" name="_GoBack"></a>

-   [Benefícios do modelo de aplicativo do Azure][]
-   [Conceitos fundamentais do serviço hospedado][]
-   [Considerações de design de serviço hospedado][]
-   [Criando seu aplicativo para escala][]
-   [Definição e configuração de serviço hospedado][]
-   [Arquivo de definição do serviço][]
-   [Arquivo de configuração do serviço][]
-   [Criando e implantando um serviço hospedado][]
-   [Referências][]

## <a id="benefits"> </a>Benefícios do modelo de aplicativo do Azure

Quando você implanta seu aplicativo como um serviço hospedado, o Azure
cria uma ou mais máquinas de virtuais (VMs) que contêm o
código do aplicativo e inicializa as VMs em máquinas físicas que residem em
um dos datacenters do Azure. Conforme o cliente solicita que o seu aplicativo
hospedado seja inserido no datacenter, um balanceador de carga distribui essas
solicitações igualmente para as máquinas virtuais. Enquanto o aplicativo está hospedado no 
Azure, ele obtém três benefícios importantes:

-   **Alta disponibilidade.** Alta disponibilidade significa que o Azure garante
    que seu aplicativo está sendo executado tanto quanto possível e é capaz de
    responder às solicitações do cliente. Se o seu aplicativo for encerrado (devido a
    uma exceção sem tratamento, por exemplo), o Azure detectará
    isso e reiniciará automaticamente o seu aplicativo. Se o
    computador em que seu aplicativo está sendo executado tiver algum tipo de
    falha de hardware, o Azure também detectará isso e
    criará automaticamente uma nova VM em outro computador físico em funcionamento
    e executará seu código nela. OBSERVAÇÃO: Para que seu aplicativo
    obtenha o Contrato de Nível de Serviço da Microsoft de 99,95% disponível, você
    deve ter pelo menos duas VMs executando o código do seu aplicativo. Isso
    permite que uma VM processe solicitações de cliente enquanto o Azure move o
    seu código de uma VM com falha para uma VM nova e boa.

-   **Escalabilidade.** O Azure permite que você altere com facilidade e dinamicamente
    o número de VMs que estão executando o código do aplicativo para lidar com a
    carga real que está sendo colocada em seu aplicativo. Isso permite que você
    ajuste o seu aplicativo para a carga de trabalho que seus clientes estão
    colocando nele e pagando somente pelas VMs necessárias quando você precisa
    delas. Quando você desejar alterar o número de VMs, o Azure
    responderá em minutos, tornando possível alterar dinamicamente o
    número de VMs em execução quantas vezes quiser.

-   **Capacidade de gerenciamento.** Como o Azure é uma oferta de Plataforma como serviço
    (PaaS), ele gerencia a infraestrutura (o próprio hardware,
    energia e rede) necessária para manter essas máquinas
    em execução. O Azure também gerencia a plataforma, garantindo um
    sistema operacional atualizado com todos os patches corretos e
    atualizações de segurança, bem como atualizações de componentes, como o .NET
    Framework e o Internet Information Server. Como todas as VMs estão
    executando o Windows Server 2008, o Azure fornece
    recursos adicionais como o monitoramento de diagnóstico, suporte a área de trabalho remota,
    firewalls e configuração do repositório de certificados. Todos esses recursos
    são fornecidos sem nenhum custo extra. Na verdade, quando você executa o
    seu aplicativo no Azure, a licença do sistema operacional (SO)
    Windows Server 2008 está incluída. Como todas as VMs estão executando o
    Windows Server 2008, qualquer código que é executado no Windows Server 2008 funciona
    bem quando está em execução no Azure.

## <a id="concepts"> </a>Conceitos fundamentais do serviço hospedado

Quando o seu aplicativo é implantado como um serviço hospedado no Azure,
ele é executado como uma ou mais *funções*. Uma *função* simplesmente se refere aos arquivos de 
aplicativos e configuração. Você pode definir uma ou mais funções para o
aplicativo, cada qual com seu próprio conjunto de arquivos de aplicativo e
configuração. Para cada função em seu aplicativo, você pode especificar o
número de VMs, ou *instâncias de função*, a ser executado. A figura a seguir mostra dois
exemplos simples de um aplicativo modelado como um serviço hospedado usando
funções e instâncias de função.

##### Figura 1: Uma única função com três instâncias (VMs) em execução em um datacenter do Azure

![image][0]

##### Figura 2: Duas funções, cada uma com duas instâncias (VMs), em execução em um datacenter do Azure

![image][1]

As instâncias de função geralmente processam solicitações de clientes de Internet inserindo o
datacenter por meio do que é chamado um * ponto de extremidade de entrada*. Uma única função
pode ter 0 ou mais pontos de extremidade de entrada. Cada ponto de extremidade indica um protocolo
(HTTP, HTTPS ou TCP) e uma porta. É comum para configurar uma função
ter dois pontos de extremidade de entrada: HTTP escutando na porta 80 e HTTPS escutando
a porta 443. A figura a seguir mostra um exemplo de duas funções diferentes
com diferentes pontos de extremidade de entrada direcionando solicitações de cliente para eles.

![image][2]

Quando você cria um serviço hospedado no Azure, ele é atribuído a um
endereço IP publicamente endereçável que os clientes podem usar para acessá-lo. Após
a criação do serviço hospedado, você também deve selecionar um prefixo de URL que é
mapeado para esse endereço IP. Esse prefixo deve ser exclusivo, pois você
reserva essencialmente a URL *prefix*.cloudapp.net para que ninguém mais
possa tê-la. Os clientes se comunicam com as instâncias de função usando a
URL. Normalmente, você não distribuirá ou publicará a
URL *prefix*.cloudapp.net. Em vez disso, você adquirirá um nome DNS do
registrador de DNS de sua escolha e configurará seu nome DNS para redirecionar
solicitações de clientes para a URL do Azure. Para obter mais informações, consulte
[Configurando um nome de domínio personalizado no Azure][].

## <a id="considerations"> </a>Considerações de design de serviço hospedado

Ao criar um aplicativo para execução em um ambiente de nuvem, há
várias considerações a serem feitas, como latência,
alta disponibilidade e escalabilidade.

Decidir onde localizar o código do aplicativo é uma importante
consideração ao executar um serviço hospedado no Azure. É
comum implantar seu aplicativo em datacenters mais próximos
aos seus clientes para reduzir a latência e obter o melhor desempenho possível.
No entanto, você pode escolher um datacenter mais próximo à sua empresa
para os seus dados se você tiver algumas preocupações jurídicas ou legais sobre
os dados e onde eles residem. Há seis datacenters em todo o
mundo capazes de hospedar o código do seu aplicativo. A tabela abaixo mostra
os locais disponíveis:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País/Região**

</td>
<td style="width: 200px;">
**Sub-regiões**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Centro-Sul e Centro-Norte

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Norte e Oeste

</td>
</tr>
<tr>
<td>
Ásia

</td>
<td>
Sudeste e Leste

</td>
</tr>
</tbody>
</table>
Ao criar um serviço hospedado, você seleciona uma sub-região indicando o
local no qual você deseja que seu código seja executado.

Para obter alta disponibilidade e escalabilidade, é extremamente importante
que os dados do aplicativo sejam mantidos em um repositório central acessível
para várias instâncias de função. Para ajudar com isso, o Azure oferece
várias opções de armazenamento como blobs, tabelas e banco de dados SQL. Consulte
o artigo [Ofertas de armazenamento de dados no Azure][] para obter mais informações
informações sobre essas tecnologias de armazenamento. A figura a seguir mostra como
o balanceador de carga dentro do datacenter do Azure distribui
solicitações de clientes para instâncias de função diferentes, todas as quais têm acesso ao
mesmo armazenamento de dados.

![image][3]

Normalmente, você deseja localizar o código do aplicativo e seus dados no
mesmo datacenter, pois isso permite baixa latência (melhor desempenho)
Quando o seu código do aplicativo acessa os dados. Além disso, você não é
cobrado por largura de banda quando os dados são movidos dentro dos mesmos datacenters
.

## <a id="scale"> </a>Criando seu aplicativo para escala

Às vezes, convém utilizar um único aplicativo (como uma site simples
da web) e hospedá-lo no Azure. Mas, em geral, o
aplicativo pode conter várias funções que trabalham juntas. Por
exemplo, na figura abaixo, há duas instâncias da função do
site, três instâncias da função de Processamento de Pedidos e uma instância da
função do Gerador de Relatórios. Todas essas funções estão trabalhando juntas e o
código para todos elas pode ser reunido e implantado como uma única
unidade até o Azure.

![image][4]

O principal motivo para dividir um aplicativo em funções diferentes, cada uma
em execução em seu próprio conjunto de instâncias de função (isto é, VMs) é fazer a escala para as
funções de forma independente. Por exemplo, durante o período de férias, muitos
os clientes podem adquirir produtos da sua empresa, portanto, você pode
aumentar o número de instâncias de função que executam a sua função de site,
bem como o número de instâncias de função que executam a função de Processamento de
Pedidos. Após o período de férias, você poderá obter muitos produtos retornados,
então talvez você ainda precise de muitas instâncias do site, mas menos instâncias de
Processamento de Pedido. Durante o restante do ano, talvez sejam necessárias apenas
poucas instâncias de Site e Processamento de Pedidos. Durante isso tudo, você
pode precisar de apenas uma instância do Gerador de Relatórios. A flexibilidade das
implantações baseadas em função no Azure permite adaptar facilmente seu
aplicativo às suas necessidades comerciais.

É comum ter as instâncias de função em seu serviço hospedado
se comunicando entre si. Por exemplo, a função de site aceita um
pedido do cliente, mas, em seguida, ela descarrega a ordem de processamento nas
instâncias de função de processamento de pedido. A melhor maneira de passar trabalho de um conjunto de
instâncias de função para outro conjunto de instâncias é usando a tecnologia de enfileiramento de mensagens
fornecida pelo Azure, o Serviço Fila ou
as Filas do Barramento de Serviço. O uso de uma fila é uma parte essencial deste cenário
aqui. A fila permite que o serviço hospedado dimensione suas funções
independentemente, permitindo equilibrar a carga de trabalho com o custo. Se o
número de mensagens na fila aumentar com o tempo, você pode dimensionar para cima
o número de instâncias de função de Processamento de Pedidos. Se o número de
mensagens na fila diminuir com o tempo, você poderá reduzir o
número de instâncias de função de Processamento de Pedidos. Dessa forma, você está pagando somente
pelas instâncias necessárias para manipular a carga de trabalho real.

A fila também fornece confiabilidade. Ao reduzir o número de
instâncias de função de Processamento de Pedidos, o Azure decide quais instâncias
encerrar. Ele pode decidir encerrar uma instância que está no
meio do processamento de uma mensagem da fila. No entanto, como o processamento de
mensagem não foi concluído com êxito, a mensagem ficará visível
novamente para outra instância de função de Processamento de Pedidos que a coleta e
processa. Devido à visibilidade da mensagem de fila, as mensagens são
possuem a garantia de serem processadas no final. A fila também atua como um balanceador de
carga distribuindo, com eficiência, as suas mensagens para toda e qualquer instância
de função que solicite mensagens dela.

Para as instâncias de função do site, você pode monitorar o tráfego de entrada
e decidir escalar o número de aplicativos ativos ou inativos também. A
fila permite que você escale o número de instâncias de função do site
independentemente das instâncias de função de Processamento de Pedidos. Isso é muito
poderoso e oferece muita flexibilidade. É claro que, se o seu
aplicativo consistir em funções adicionais, você pode adicionar
filas adicionais como o canal de comunicação entre eles para aproveitar
a mesma escala e benefícios de custo.

## <a id="defandcfg"> </a>Definição e configuração de serviço hospedado

Implantar um serviço hospedado no Azure requer que você também tenha um
arquivo de definição de serviço e um arquivo de configuração de serviço. Ambos
os arquivos são arquivos XML e permitem que você especifique declarativamente
as opções de implantação para seu serviço hospedado. O arquivo de definição de serviço
descreve todas as funções que compõem seu serviço hospedado e como eles
se comunicam. O arquivo de configuração de serviço descreve o número de
instâncias de cada função e as configurações usadas para configurar cada
instância de função.

## <a id="def"> </a>Arquivo de definição do serviço

Como mencionado anteriormente, o arquivo de definição de serviço (CSDEF) é um arquivo XML
que descreve as várias funções que compõem seu aplicativo
completo. O esquema completo do arquivo XML pode ser encontrado aqui:
[http://msdn.microsoft.com/pt-br/library/windowsazure/ee758711.aspx][].
O arquivo CSDEF contém um elemento WebRole ou WorkerRole para cada função
que você deseja em seu aplicativo. A implantação de uma função como uma função web (usando
o elemento WebRole) significa que o código será executado em uma instância de função
com o Windows Server 2008 e o Internet Information Server (IIS).
A implantação de uma função como uma função de trabalho (usando o elemento WorkerRole) significa
que a instância de função terá o Windows Server 2008 nela (o IIS não será
instalado).

Certamente, você pode criar e implantar uma função de trabalho que use algum outro
mecanismo para escutar as solicitações da web (por exemplo, seu código
pode criar e usar um HttpListener .NET). Como as instâncias de função estão
todas executando o Windows Server 2008, seu código pode realizar qualquer operação
que esteja normalmente disponível para um aplicativo em execução no Windows Server
2008.

Para cada função, você indica o tamanho desejado da VM que as instâncias
dessa função devem usar. A tabela a seguir mostra os vários tamanhos de VM disponíveis
hoje e os atributos de cada um:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamanho da VM**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**Pico  
E/S da rede**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extrapequena**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequena**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Média**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extragrande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Você é cobrado por hora para cada VM que usa como uma instância de função e você
também é cobrado por todos os dados enviados por suas instâncias de função para fora do
datacenter. Você não é cobrado por dados que entram no data center. Para
obter mais informações, consulte [Preços do Azure][]. Em geral,
recomendamos usar muitas instâncias de função pequenas em vez de algumas grandes
instâncias de modo que seu aplicativo seja mais resiliente a falhas. Afinal,
quanto menos instâncias de função você tiver, mais desastrosa será uma falha em
uma delas para o seu aplicativo geral. Além disso, conforme mencionado anteriormente,
Você deve implantar pelo menos duas instâncias para cada função para obter o
contrato de nível de serviço de 99,95% que a Microsoft fornece.

O arquivo de definição do serviço (CSDEF) também está onde você especifica muitos
atributos de cada função em seu aplicativo. Aqui estão alguns dos
itens mais úteis disponíveis:

-   **Certificados**. Você usa certificados para criptografar dados ou se
    o serviço Web oferece suporte a SSL. Todos os certificados devem ser carregados
    no Azure. Para obter mais informações, consulte [Gerenciando Certificados
    no Azure][]. Essa configuração XML instala os certificados
    previamente carregados no repositório de certificados da instância de função, de modo que
    eles sejam utilizáveis pelo código do aplicativo.

-   **Nomes dos parâmetros de configuração**. Para obter os valores que você deseja que o(s) seu(s)
    aplicativo(s) leia(m) durante a execução em uma instância de função. O valor real
    dos parâmetros de configuração é definido no
    arquivo de configuração do serviço (CSCFG) que pode ser atualizado a qualquer momento sem
    necessidade de reimplantar seu código. Na verdade, você pode codificar os seus
    aplicativos de forma a detectar os valores de configuração
    alterados sem incorrer em nenhum tempo de inatividade.

-   **Pontos de extremidade de entrada**. Aqui você especifica quaisquer pontos de extremidade HTTP, HTTPS ou TCP
    (com portas) que você deseja expor ao mundo exterior
    por meio de sua URL *prefix*.cloadapp.net. Quando o Azure implanta sua
    função, ele configurará o firewall na instância de função
    automaticamente.

-   **Pontos de extremidade internos**. Aqui você pode especificar quaisquer pontos de extremidade HTTP ou TCP
    que você deseja expor a outras instâncias de função que são implantadas como
    parte do seu aplicativo. Os pontos de extremidade internos permitem que todas as
    instâncias de função no seu aplicativo se comuniquem umas com as outras, mas não estão
    acessíveis para quaisquer instâncias de função que estão fora de seu aplicativo.

-   **Módulos de importação**. Eles instalam opcionalmente os componentes úteis em
    suas instâncias de função. Existem componentes para monitoramento diagnóstico,
    área de trabalho remota e o Azure Connect (que permite que sua instância de função
    acesse os recursos locais através de um canal seguro).

-   **Armazenamento local**. Aloca um subdiretório na instância de
    função para uso de seu aplicativo. Isso é descrito mais detalhadamente
    no artigo [Ofertas de Armazenamento de Dados no Azure][].

-   **Tarefas de inicialização**. As tarefas de inicialização fornecem uma maneira de instalar
    os componentes de pré-requisito em uma instância de função conforme ela é inicializada. As tarefas
    podem ser executadas com privilégios elevados como um administrador, se necessário.

## <a id="cfg"> </a>Arquivo de configuração do serviço

O arquivo de configuração de serviço (CSCFG) é um arquivo XML que descreve
as configurações que podem ser alteradas sem reimplantar o aplicativo. O
esquema completo para o arquivo XML pode ser encontrado aqui:
[http://msdn.microsoft.com/pt-br/library/windowsazure/ee758710.aspx][].
O arquivo CSCFG contém um elemento de Função para cada função de seu
aplicativo. Estes são alguns dos itens que você pode especificar no arquivo
CSCFG:

-   **Versão do Sistema Operacional**. Este atributo permite que você selecione a versão do
    sistema operacional (SO) a ser usado para todas as instâncias de função que executam
    o código do aplicativo. Esse sistema operacional é conhecido como o *SO convidado* e cada
    nova versão inclui os últimos patches de segurança e atualizações
    disponíveis no momento em que o sistema operacional convidado é liberado. Se você definir o
    valor do atributo osVersion para "\*", automaticamente, o Azure
    atualizará o sistema operacional convidado em cada uma das suas instâncias de função conforme as novas versões do sistema operacional
    convidado se tornam disponíveis. No entanto, você pode optar por atualizações
    automáticas selecionando uma versão de sistema operacional convidado específica. Por exemplo, 
    a definição do atributo osVersion como um valor de
    "WA-GUEST-OS-2.8\_201109-01" faz com que todas as instâncias de função obtenham
    o que é descrito nesta página da Web:
    [http://msdn.microsoft.com/pt-br/library/hh560567.aspx][]. Para obter mais
    informações sobre as versões de sistema operacional convidado, consulte [Gerenciar atualizações para o
    Sistema Operacional Convidado do Azure].

-   **Instâncias**. O valor desse elemento indica o número de instâncias
    de função de provisionamento que está executando o código para uma determinada
    função. Como você pode carregar um novo arquivo CSCFG no Azure
    (sem reimplantar o aplicativo), é muito simples
    alterar o valor para esse elemento e carregar um novo arquivo CSCFG
    para aumentar ou diminuir dinamicamente o número de instâncias de função
    que executam o código do aplicativo. Isso permite que você aumente ou diminua facilmente o seu
    aplicativo até a carga de trabalho real atender às demandas enquanto também
    controla o quanto você será cobrado para executar as instâncias de função.

-   **Valores dos parâmetros de configuração**. Esse elemento indica valores para
    as configurações (conforme definido no arquivo CSDEF). Sua função pode ler esses
    valores enquanto ela é executada. Esses valores de definição de configurações
    são normalmente usados para cadeias de caracteres de conexão para o banco de dados SQL ou para o 
    Armazenamento do Azure, mas eles podem ser usados para qualquer finalidade desejada.

## <a id="hostedservices"> </a>Criando e implantando um serviço hospedado

Criar um serviço hospedado requer que você primeiro vá para o [Portal de gerenciamento do Azure] e provisione um serviço hospedado, especificando
um prefixo DNS e o datacenter no qual você realmente deseja que seu código seja
executado. Em seu ambiente de desenvolvimento, você cria seu arquivo de definição de
serviço (CSDEF), compila o código do aplicativo e faz o pacote (zip)
de todos esses arquivos em um arquivo de pacote do serviço (CSPKG). Também é necessário
preparar o arquivo de configuração do serviço (CSCFG). Para implantar a sua função,
carregue os arquivos CSPKG e CSCFG com a API de Gerenciamento de Serviço do
Azure. Uma vez implantado, o Azure provisionará as instâncias de
função no datacenter (com base nos dados de configuração),
extrairá o código do aplicativo do pacote, vai copiá-lo para as instâncias de função
e inicializará as instâncias. Agora, seu código está em execução.

A figura abaixo mostra os arquivos CSPKG e CSCFG que você cria em seu
computador de desenvolvimento. O arquivo CSPKG contém o arquivo CSDEF e o
código para duas funções. Depois de carregar os arquivos CSPKG e CSCFG com a
API de Gerenciamento de Serviços do Azure, o Azure cria as instâncias de
função no datacenter. Neste exemplo, o arquivo CSCFG indicou
que o Azure deve criar três instâncias de função \#1 e duas
instâncias de função \#2.

![image][5]

Para obter mais informações sobre como implantar, atualizar e reconfigurar suas
funções, consulte o artigo [Implantando e atualizando aplicativos do Azure][]
.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referências

-   [Criando um serviço hospedado para o Azure][]

-   [Gerenciando serviços hospedados no Azure][]

-   [Migrando aplicativos para o Azure][]

-   [Configurar um aplicativo do Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escrito por Jeffrey Richter (Wintellect)</p>

</div>

  [Benefícios do modelo de aplicativo do Azure]: #benefits
  [Conceitos fundamentais do serviço hospedado]: #concepts
  [Considerações de design de serviço hospedado]: #considerations
  [Criando seu aplicativo para escala]: #scale
  [Definição e configuração de serviço hospedado]: #defandcfg
  [Arquivo de definição do serviço]: #def
  [Arquivo de configuração do serviço]: #cfg
  [Criando e implantando um serviço hospedado]: #hostedservices
  [Referências]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/pt-br/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Preços do Azure]: http://www.windowsazure.com/pt-br/pricing/calculator/
  [Gerenciando certificados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/pt-br/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/pt-br/library/hh560567.aspx]: http://msdn.microsoft.com/pt-br/library/hh560567.aspx
  [Gerenciando atualizações para os SOs de convidados do Azure]: http://msdn.microsoft.com/pt-br/library/ee924680.aspx
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implantando e atualizando aplicativos do Azure]: http://www.windowsazure.com/pt-br/develop/net/fundamentals/deploying-applications/
  [Criando um serviço hospedado para o Azure]: http://msdn.microsoft.com/pt-br/library/gg432967.aspx
  [Gerenciando serviços hospedados no Azure]: http://msdn.microsoft.com/pt-br/library/gg433038.aspx
  [Migrando aplicativos para o Azure]: http://msdn.microsoft.com/pt-br/library/gg186051.aspx
  [Configurar um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405486.aspx
