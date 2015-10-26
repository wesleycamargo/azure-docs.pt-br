# Computação

O Azure permite implantar e monitorar seu código de aplicativo em execução dentro de um data center da Microsoft. Quando você cria um aplicativo e o executa no Azure, seu código e a configuração, em conjunto, são chamados de serviço hospedado do Azure. Os serviços hospedados são fáceis de gerenciar, expandir e reduzir, reconfigurar e atualizar com novas versões do código de seu aplicativo. Este artigo aborda o modelo de aplicativo de serviço hospedado do Azure.<a id="compare" name="compare"></a>

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

Quando você implanta seu aplicativo como um serviço hospedado, o Azure cria uma ou mais VMs (máquinas virtuais) que contêm o código do seu aplicativo e inicializa as VMs em máquinas físicas que residem em um dos data centers do Azure. À medida que as solicitações de clientes para o aplicativo hospedado entram no data center, um balanceador de carga distribui essas solicitações igualmente para as VMs. Enquanto está hospedado no Azure, o aplicativo obtém três benefícios importantes:

-   **Alta disponibilidade.** Alta disponibilidade significa que o Azure garante que seu aplicativo seja executado tanto quanto possível e possa responder às solicitações de clientes. Se seu aplicativo terminar (devido a uma exceção sem tratamento, por exemplo), o Azure detectará isso e reiniciará seu aplicativo automaticamente. Se a máquina na qual seu aplicativo está executando experimentar algum tipo de falha de hardware, o Azure também detectará isso e criará automaticamente uma nova VM em outro computador físico em funcionamento e executará seu código nela. Observação: para que aplicativo obtenha o Contrato de Nível de Serviço da Microsoft de 99,95% disponível, você deve ter pelo menos duas VMs executando o código do seu aplicativo. Isso permite que uma VM processe solicitações de clientes enquanto o Azure move seu código de uma VM com falha para uma VM nova VM funcional.

-   **Escalabilidade.** O Azure permite que você altere de maneira fácil e dinâmica o número de VMs que executam o código do seu aplicativo para tratar da carga real que está sendo colocada no aplicativo. Isso permite que você ajuste seu aplicativo para a carga de trabalho que seus clientes estão colocando nele pagando somente pelas VMs necessárias quando precisarem deles. Quando você quiser alterar o número de VMs, o Azure responderá em minutos, possibilitando a alteração dinâmica do número de VMs em execução com a frequência desejada.

-   **Capacidade de gerenciamento.** Como o Azure é uma oferta de PaaS (plataforma como um serviço), ele gerencia a infraestrutura (o próprio hardware, a eletricidade e a rede) necessária para manter essas máquinas em execução. O Azure também gerencia a plataforma, garantindo um sistema operacional atualizado com todos os patches e atualizações de segurança corretos, bem como atualizações de componentes, como o .NET Framework e o Internet Information Server. Como todas as VMs estão executando o Windows Server 2008, o Azure fornece recursos adicionais, como monitoramento de diagnóstico, suporte a área de trabalho remota, firewalls e configuração do repositório de certificados. Todos esses recursos são fornecidos sem nenhum custo extra. Na verdade, quando você executa o aplicativo no Azure, a licença do SO (sistema operacional) Windows Server 2008 está incluída. Como todas as VMs estão executando o Windows Server 2008, qualquer código que seja executado no Windows Server 2008 funciona muito bem ao ser executado no Azure.

## <a id="concepts"> </a>Conceitos fundamentais do serviço hospedado

Quando seu aplicativo for implantado como um serviço hospedado no Azure, ele é executado como uma ou mais *funções.* Uma *função* simplesmente se refere aos arquivos e à configuração do aplicativo. Você pode definir uma ou mais funções para o aplicativo, cada uma com seu próprio conjunto de arquivos de aplicativo e configuração. Para cada função em seu aplicativo, você pode especificar o número de VMs ou *instâncias de função* a serem executadas. A figura a seguir mostra dois exemplos simples de um aplicativo modelado como um serviço hospedado usando funções e instâncias de função.

##### Figura 1: uma única função com três instâncias (VMs) em execução em um datacenter do Azure

![imagem][0]

##### Figura 2: duas funções, cada uma com duas instâncias (VMs), em execução em um datacenter do Azure

![imagem][1]

Instâncias de função geralmente processam solicitações de clientes da internet que entram no data center por meio do que é chamado de um *ponto de extremidade de entrada*. Uma única função pode ter 0 ou mais pontos de extremidade de entrada. Cada ponto de extremidade indica um protocolo (HTTP, HTTPS ou TCP) e uma porta. É comum, para configurar uma função, ter dois pontos de extremidade de entrada: HTTP escutando na porta 80 e HTTPS escutando na porta 443. A figura a seguir mostra um exemplo de duas funções diferentes com diferentes pontos de extremidade de entrada direcionando solicitações de cliente para elas.

![imagem][2]

Quando você cria um serviço hospedado no Azure, ele recebe um endereço IP publicamente endereçável que os clientes podem usar para acessá-lo. Ao criar o serviço hospedado, você também deve selecionar um prefixo de URL que é mapeado para aquele endereço IP. Esse prefixo deve ser exclusivo uma vez que você está essencialmente reservando a URL *prefixo*.cloudapp.net para que ninguém possa tê-la. Os clientes se comunicam com as instâncias de função usando a URL. Normalmente, você não vai distribuir ou publicar a URL *prefixo*.cloudapp.net do Azure. Em vez disso, você compra um nome DNS do registrador DNS de sua escolha e configura seu nome DNS para redirecionar solicitações do cliente para a URL do Azure. Para obter mais detalhes, consulte [Configurando um nome de domínio personalizado no Azure][].

## <a id="considerations"> </a>Considerações de design de serviço hospedado

Ao criar um aplicativo para execução em um ambiente de nuvem, há várias considerações a serem feitas, como latência, alta disponibilidade e escalabilidade.

Decidir onde localizar o código do aplicativo é uma consideração importante ao executar um serviço hospedado no Azure. É comum implantar seu aplicativo em data centers que estão mais próximos de seus clientes para reduzir a latência e obter o melhor desempenho possível. No entanto, você pode escolher um data center mais perto de sua empresa ou mais perto de seus dados, se você tiver alguma preocupação legal ou jurídica sobre seus dados e onde eles residem. Há seis data centers em todo o mundo que podem hospedar o código de seu aplicativo. A tabela abaixo mostra os locais disponíveis:

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
Ao criar um serviço hospedado, você seleciona uma sub-região indicando o local em que deseja que seu código seja executado.

Para obter alta disponibilidade e escalabilidade, é extremamente importante que os dados de seu aplicativo sejam mantidos em um repositório central acessível a várias instâncias de função. Para ajudar com isso, o Azure oferece várias opções de armazenamento, como blobs, tabelas e o banco de dados SQL. Consulte o artigo [Ofertas de armazenamento de dados no Azure][] para obter mais informações sobre essas tecnologias de armazenamento. A figura a seguir mostra como o balanceador de carga dentro do data center do Azure distribui solicitações de clientes para instâncias de função diferentes, todas as quais têm acesso ao mesmo armazenamento de dados.

![imagem][3]

Normalmente, você deseja localizar o código de seu aplicativo e seus dados no mesmo data center, pois isso permite baixa latência (melhor desempenho) quando o código do aplicativo acessa os dados. Além disso, você não é cobrado por largura de banda quando os dados são movidos dentro do mesmo data center.

## <a id="scale"> </a>Criando seu aplicativo para escala

Às vezes, você pode desejar utilizar um único aplicativo (como um site) e hospedá-lo no Azure. Mas, com frequência, seu aplicativo pode consistir em várias funções que trabalham juntas. Por exemplo, na figura abaixo, existem duas instâncias da função de Site, três instâncias da função de Processamento de Pedidos e uma instância da função de Gerador de Relatórios. Essas funções estão todas trabalhando juntas e o código de todas elas pode ser empacotado e implantado como uma única unidade para o Azure.

![imagem][4]

O principal motivo para dividir um aplicativo em diferentes funções, cada uma executando em seu próprio conjunto de instâncias de função (isto é, VMs) é dimensionar as funções de maneira independente. Por exemplo, durante o período de férias, muitos clientes podem adquirir produtos da sua empresa, portanto, você pode querer aumentar o número de instâncias de função que executam sua função de Site, bem como o número de instâncias de função que executam sua função de Processamento de Pedidos. Depois do período de férias, você poderá receber a devolução de muitos produtos, portanto, talvez ainda sejam necessárias muitas instâncias de Site, mas menos instâncias de Processamento de Pedidos. Durante o resto do ano, talvez sejam necessárias apenas algumas instâncias de Site e de Processamento de Pedidos. Durante tudo isso, talvez você precise apenas de uma instância do Gerador de Relatórios. A flexibilidade das implantações baseadas em função no Azure permite adaptar facilmente seu aplicativo às necessidades dos negócios.

É comum fazer com que as instâncias de função no serviço hospedado se comuniquem umas com as outras. Por exemplo, a função de site aceita o pedido de um cliente, mas, em seguida, descarrega o processamento do pedido para as instâncias da função Processamento de Pedidos. A melhor maneira de passar trabalho de um conjunto de instâncias de função para outro conjunto de instâncias é usar a tecnologia de enfileiramento fornecida pelo Azure, o Serviço Fila ou as Filas do Barramento de Serviço. O uso de uma fila é uma parte essencial deste cenário. A fila permite que o serviço hospedado dimensione suas funções de maneira independente, permitindo que você equilibre a carga de trabalho com o custo. Se o número de mensagens na fila aumentar com o tempo, você poderá dimensionar o número de instâncias de função Processamento de Pedidos. Se o número de mensagens na fila diminuir com o tempo, você poderá dimensionar o número de instâncias de função Processamento de Pedidos. Dessa maneira, você estará pagando somente pelas instâncias necessárias para tratar da carga de trabalho real.

A fila também fornece confiabilidade. Ao reduzir o número de instâncias da função Processamento de Pedidos, o Azure decide quais instâncias terminar. Ele pode decidir terminar uma instância que está no meio do processamento de uma mensagem da fila. No entanto, como o processamento da mensagem não é concluído com êxito, a mensagem fica visível novamente para outra instância da função Processamento de Pedidos que a coleta e processa. Devido à visibilidade das mensagens na fila, é garantido que as mensagens serão processadas no final. A fila também atua como um balanceador de carga distribuindo suas mensagens efetivamente para instâncias de toda e qualquer função que solicite mensagens.

Para as instâncias da função Site, você pode monitorar o tráfego de entrada e decidir aumentar ou reduzir o seu número. A fila permite que você dimensione o número de instâncias de função do site independentemente das instâncias de função de Processamento de Pedidos. Isso é muito eficiente e oferece muita flexibilidade. Naturalmente, se seu aplicativo consistir em funções adicionais, você poderá adicionar mais filas como o canal de comunicação entre elas para utilizar a mesma escala e benefícios de custo.

## <a id="defandcfg"> </a>Definição e configuração de serviço hospedado

A implantação de um serviço hospedado no Azure requer que você também tenha um arquivo de definição do serviço e um arquivo de configuração do serviço. Esses dois são arquivos XML e permitem que você especifique declarativamente as opções de implantação de seu serviço hospedado. O arquivo de definição do serviço descreve todas as funções que compõem o serviço hospedado e como elas se comunicam. O arquivo de configuração do serviço descreve o número de instâncias de cada função e as configurações usadas para configurar cada instância de função.

## <a id="def"> </a>Arquivo de definição do serviço

Como mencionei anteriormente, o arquivo de definição do serviço (CSDEF) é um arquivo XML que descreve as várias funções que compõem seu aplicativo completo. O esquema completo para o arquivo XML pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][]. O arquivo CSDEF contém um elemento WebRole ou WorkerRole para cada função que você deseja em seu aplicativo. A implantação de uma função como uma função web (usando o elemento WebRole) significa que o código será executado em uma instância de função que contém o Windows Server 2008 e o Internet Information Server (IIS). A implantação de uma função como uma função de trabalho (usando o elemento WorkerRole) significa que a instância da função terá o Windows Server 2008 (o IIS não será instalado).

Certamente, você pode criar e implantar uma função de trabalho que usa outro mecanismo para escutar solicitações de entrada da Web (por exemplo, seu código pode criar e usar um HttpListener .NET). Como as instâncias de função estão todas executando o Windows Server 2008, seu código pode executar todas as operações que normalmente estão disponíveis para um aplicativo em execução no Windows Server 2008.

Para cada função, você indica o tamanho desejado da MV que as instâncias dessa função devem usar. A tabela abaixo mostra os vários tamanhos de MV disponíveis atualmente e os atributos de cada uma:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamanho da MV**

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
E/S de pico da rede

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequena**

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
~5 Mbps

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
~100 Mbps

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
~200 Mbps

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
~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra Grande**

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
~800 Mbps

</td>
</tr>
</tbody>
</table>
Você é cobrado por hora para cada MV que usa como uma instância de função e também por quaisquer dados que as instâncias de função enviam para fora do data center. Você não é cobrado por dados que entram no data center. obter mais informações, consulte [Preços do Azure][]. Em geral, é aconselhável usar muitas instâncias de função pequenas em vez de algumas instâncias grandes para que seu aplicativo seja mais resiliente a falhas. No final, quanto menos instâncias de função você tiver, mais desastrosa será uma falha em uma delas para seu aplicativo geral. Além disso, conforme mencionado anteriormente, você deve implantar pelo menos duas instâncias para cada função para obter o contrato de nível de serviço de 99,95% que a Microsoft fornece.

O arquivo de definição do serviço (CSDEF) também é onde você especifica muitos atributos sobre cada função de seu aplicativo. Estes são alguns dos itens mais úteis disponíveis para você:

-   **Certificados**. Você usa certificados para criptografar dados ou se seu serviço da Web oferecer suporte a SSL. Todos os certificados precisam ser carregadas no Azure. Para saber naus, consulte [Gerenciando certificados no Azure][]. Essa configuração XML instala certificados carregados anteriormente no repositório de certificados da instância da função para que possam ser usados pelo código de seu aplicativo.

-   **Nomes dos parâmetros de configuração**. Os valores que você deseja que seus aplicativos leiam durante a execução em uma instância de função. O valor real dos parâmetros de configuração é definido no arquivo de configuração do serviço (CSCFG) que pode ser atualizado a qualquer momento sem necessidade de reimplantar o código. Na verdade, você pode codificar seus aplicativos de maneira a detectarem os valores alterados na configuração sem incorrer em nenhum tempo de inatividade.

-   **Pontos de extremidade de entrada**. Aqui você pode especificar qualquer ponto de extremidade HTTP, HTTPS ou TCP (com as portas) que você deseja expor para o mundo exterior por meio de sua URL *prefixo*.cloadapp.net. Quando o Azure implanta sua função, ele configura o firewall na instância da função automaticamente.

-   **Pontos de extremidade internos**. Aqui você pode especificar qualquer ponto de extremidade HTTP ou TCP que você queira expor para outras instâncias de função que são implantadas como parte de seu aplicativo. O pontos de extremidade internos permitem que todas as instâncias de função dentro de seu aplicativo falem entre si, mas não sejam acessíveis para quaisquer instâncias de função que estejam fora de seu aplicativo.

-   **Módulos de importação**. Opcionalmente, esse módulos instalam componentes úteis em suas instâncias de função. Existem componentes de monitoramento diagnóstico, área de trabalho remota e o Connect do Azure (que permite que a instância de função acesse recursos locais através de um canal seguro).

-   **Armazenamento local**. Esse armazenamento local aloca um subdiretório na instância de função para uso de seu aplicativo. É descrito em mais detalhes no artigo [Ofertas de armazenamento de dados no Azure][].

-   **Tarefas de inicialização**. A tarefas de inicialização fornecem uma maneira para instalar componentes de pré-requisito em uma instância de função quando ela é inicializada. As tarefas podem ser executadas com privilégios elevados como um administrador se necessário.

## <a id="cfg"> </a>Arquivo de configuração do serviço

O arquivo de configuração do serviço (CSCFG) é um arquivo XML que descreve as configurações que podem ser alteradas sem reimplantar o aplicativo. O esquema completo para o arquivo XML pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][]. O arquivo CSCFG contém um elemento Role para cada função de seu aplicativo. Estes são alguns dos itens que você pode especificar no arquivo CSCFG:

-   **Versão do Sistema Operacional**. Este atributo permite que você selecione a versão do SO (sistema operacional) que você deseja que seja usada em todas as instâncias de função que estão executando o código de seu aplicativo. Esse sistema operacional é conhecido como o *SO convidado*, e cada nova versão inclui os patches de segurança e as atualizações mais recentes disponíveis no momento em que o SO convidado é liberado. Se você definir o valor do atributo osVersion como "*", o Azure atualizará automaticamente o SO convidado em cada uma de suas instâncias de função à medida que novas versões do SO convidado se tornarem disponíveis. No entanto, você pode recusar as atualizações automáticas, selecionando uma versão específica do SO convidado. Por exemplo, a definição do atributo osVersion como um valor de "WA-GUEST-OS-2.8\_201109-01" faz com que todas as instâncias de função obtenham o que é descrito nesta página da Web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Para saber mais sobre as versões de SO convidado, consulte [Gerenciando as atualizações no SO convidado do Azure].

-   **Instâncias**. O valor desse elemento indica o número de instâncias de função que você deseja que sejam provisionadas para a execução do código de uma função específica. Como você pode carregar um novo arquivo CSCFG no Azure (sem reimplantar o aplicativo), é muito simples alterar o valor desse elemento e carregar um novo arquivo CSCFG para aumentar ou diminuir dinamicamente o número de instâncias de função que executam o código do seu aplicativo. Isso permite que você expanda ou reduza facilmente seu aplicativo para atender às demandas reais da carga de trabalho enquanto também controla o quanto você será cobrado para executar as instâncias de função.

-   **Valores dos parâmetros de configuração**. Esse elemento indica valores para as configurações (conforme definido no arquivo CSDEF). Sua função pode ler esses valores enquanto está em execução. Esses valores de configurações normalmente são usados para cadeias de conexão ao banco de dados SQL ou ao armazenamento do Azure, mas podem ser usados para qualquer finalidade desejada.

## <a id="hostedservices"> </a>Criando e implantando um serviço hospedado

Criar um serviço hospedado requer que você primeiro vá para o [Portal de gerenciamento do Azure] e provisione um serviço hospedado, especificando um prefixo de DNS e o data center no qual você realmente deseja que seu código seja executado. Em seguida, em seu ambiente de desenvolvimento, você cria o arquivo de definição de serviço (CSDEF), cria seu código do aplicativo e comprime (zip) todos esses arquivos em um arquivo de service pack (CSPKG). Você também deve preparar o arquivo de configuração do serviço (CSCFG). Para implantar a sua função, você carrega os arquivos CSPKG e CSCFG com a API de Gerenciamento de Serviços do Azure. Uma vez implantado, o Azure provisionará as instâncias de função no data center (com base nos dados de configuração), extrairá o código de seu aplicativo do pacote, o copiará nas instâncias de função e inicializará as instâncias. Agora, seu código está em execução.

A figura abaixo mostra os arquivos CSPKG e CSCFG que você cria em seu computador de desenvolvimento. O arquivo CSPKG contém o arquivo CSDEF e o código das duas funções. Após carregar os arquivos CSPKG e CSCFG com a API de Gerenciamento de Serviços do Azure, o Azure cria as instâncias de função no data center. Neste exemplo, o arquivo CSCFG indicou que o Azure deve criar três instâncias da função nº 1 e duas instâncias da função nº 2.

![imagem][5]

Para obter mais informações sobre como implantar, atualizar e reconfigurar suas funções, consulte [Implantando e atualizando aplicativos do Azure][].<a id="Ref" name="Ref"></a>

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
  [Configurando um nome de domínio personalizado no Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Ofertas de armazenamento de dados no Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Preços do Azure]: http://www.windowsazure.com/pricing/calculator/
  [Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portal de gerenciamento do Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implantando e atualizando aplicativos do Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Criando um serviço hospedado para o Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gerenciando serviços hospedados no Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrando aplicativos para o Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurar um aplicativo do Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx

<!---HONumber=Oct15_HO3-->