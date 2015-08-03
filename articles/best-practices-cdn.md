<properties
   pageTitle="Diretriz da Rede de Distribuição de Conteúdo (CDN) | Microsoft Azure"
   description="Diretriz na Rede de Distribuição de Conteúdo (CDN) para fornecer conteúdo de alta largura de banda hospedado no Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Diretriz da Rede de Distribuição de Conteúdo (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Visão geral

A Rede de Distribuição de Conteúdo (CDN) do Microsoft Azure oferece aos desenvolvedores uma solução global de fornecimento de conteúdo de alta largura de banda que é hospedada no Azure. O CDN armazena em cache os objetos disponíveis publicamente carregados por meio do armazenamento de Blobs do Azure ou uma pasta de aplicativos em locais estrategicamente colocados para fornecer a máxima largura de banda para o fornecimento de conteúdo aos usuários. Ele é normalmente usado para fornecimento de conteúdo estático, como imagens, folhas de estilo, documentos, arquivos, scripts do lado do cliente e páginas HTML.

As principais vantagens de usar o CDN são latência mais baixa e fornecimento mais rápido de conteúdo aos usuários independentemente de seu local geográfico em relação ao datacenter onde o aplicativo é hospedado e uma redução na carga no próprio aplicativo porque ele é liberado do processamento necessário para acessar e entregar o conteúdo. Essa redução de carga pode ajudar a aumentar o desempenho e a escalabilidade do aplicativo, bem como minimizar os custos de hospedagem, reduzindo os recursos de processamento necessários para alcançar um nível específico de desempenho e disponibilidade.

É possível usar outros sistemas de rede de distribuição de conteúdo que não são implementados pelo Azure em seus aplicativos se o CDN do Azure não atender às suas necessidades. Como alternativa, você poderá usar o CDN do Azure para aplicativos hospedados com outros provedores, expondo o conteúdo estático no armazenamento do Azure ou em instâncias de computação do Azure. ![](media/best-practices-cdn/CDN.png)

## Como e por que o CDN é usado?

Os usos típicos para o CDN incluem:

- Fornecer recursos estáticos para aplicativos cliente, geralmente por meio de um site. Eles podem ser imagens, folhas de estilo, documentos, arquivos, scripts do lado do cliente, páginas HTML, fragmentos HTML ou qualquer outro conteúdo que o servidor não precise modificar para cada solicitação. O aplicativo pode criar itens em tempo de execução e disponibilizá-los para o CDN (por exemplo, ao criar uma lista atual de manchetes de notícias), mas ele não faz isso para cada solicitação.

- Fornecer conteúdo estático e compartilhado público para dispositivos, como telefones móveis e tablet PCs, onde o aplicativo em si é um serviço Web que oferece uma API aos clientes. Além de outros conteúdos que o servidor não precisa modificar para cada solicitação, o CDN pode oferecer conjuntos de dados estáticos para o cliente usar - talvez para gerar a interface do usuário do cliente. Por exemplo, ele pode ser usado para entregar documentos XML ou JSON.

- Servir sites inteiros que consistem em apenas conteúdo estático público para os clientes, sem a necessidade de quaisquer recursos de computação dedicados.

- Fazer o streaming de arquivos de vídeo ao cliente sob demanda. Os benefícios do vídeo de baixa latência e conectividade confiável disponível em datacenters globalmente localizados que oferecem conexões do CDN.

- Geralmente, aprimorar a experiência dos usuários, especialmente aqueles localizados longe da localização do datacenter do aplicativo que, caso contrário, sofreriam com a latência mais alta. Uma grande proporção do tamanho total do conteúdo em um aplicativo Web geralmente é estática e usar o CDN pode ajudar a manter o desempenho e a experiência geral do usuário eliminando a necessidade de implantar o aplicativo em vários datacenters.

- Lidar com a carga crescente em aplicativos de dispositivos de serviço móvel e fixo que fazem parte da Internet das Coisas (IoT). O grande número de tais dispositivos e aparelhos poderia facilmente sobrecarregar o aplicativo se fosse necessário processar mensagens de difusão e gerenciar a distribuição de atualização de firmware diretamente.

- Lidar com picos e sobretensão de energia sob demanda sem exigir que o aplicativo dimensione, evitando o consequente aumento dos custos de execução. Por exemplo, quando uma atualização é liberada para um sistema operacional, para um dispositivo de hardware, como um modelo específico do roteador ou para um dispositivo do consumidor, como uma smart TV, haverá um grande pico na demanda conforme ele é baixado por milhões de usuários e dispositivos em um curto período de tempo.

- A tabela a seguir mostra exemplos do tempo médio para primeiro byte de vários locais geográficos. A função web de destino é implantada ao Azure no Oeste dos EUA. Há uma forte correlação entre o maior aumento devido ao CDN e a proximidade com um nó CDN. Para obter uma lista com as localizações dos nós do CDN disponíveis, consulte [Locais de Nó da Rede de Distribuição de Conteúdo (CDN) do Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Tempo para o primeiro byte (Origem)</p></th><th><p>Tempo para o primeiro byte (CDN)</p></th><th><p>% mais rápido para CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* Londres, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Xangai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapura</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tóquio, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Tem um nó CDN do Azure na mesma cidade. * Tem em um nó CDN do Azure em uma cidade próxima.


## Desafios  

Há vários desafios a serem considerados ao planejar usar o CDN:

- **Implantação** você deve decidir de onde carregar conteúdo para o CDN (a origem da qual o CDN buscará o conteúdo) e se é necessário implantar o conteúdo em mais de um sistema de armazenamento (como no CDN e em um local alternativo).

- Seu mecanismo de implantação de aplicativo deve considerar a implantação de conteúdo e recursos estáticos bem como implantar os arquivos do aplicativo, como páginas ASPX. Por exemplo, ele pode exigir uma etapa separada para carregar conteúdo no armazenamento de blob do Azure.

- **Controle de versão e controle de cache** Você deve considerar como atualizará o conteúdo estático e implantará novas versões. Atualmente, o CDN não fornece um mecanismo para liberar o conteúdo para que novas versões sejam disponibilizadas. Esse é um desafio semelhante ao gerenciamento de cache do lado do cliente, como em um navegador da Web.

- **Testes** Pode ser difícil executar teste local de suas configurações de CDN ao desenvolver e testar um aplicativo localmente ou em processo de preparo.

- **SEO** Conteúdo, como documentos e imagens, são servidos de um domínio diferente quando você usa o CDN, que terá um efeito no SEO para este conteúdo.

- **Segurança** Atualmente, muitos serviços de CDN, como o Azure CDN, não oferecem qualquer tipo de controle de acesso para o conteúdo.

- **Resiliência** O CDN é um ponto único de falha em potencial de um aplicativo. Ele tem uma menor disponibilidade de SLA que o armazenamento de blob (que pode ser usado para entregar conteúdo diretamente), então, talvez seja necessário considerar a implementação de um mecanismo de fallback para conteúdo essencial.

- Os clientes podem se conectar por meio de um ambiente que não permite acesso a recursos no CDN. Isso pode ser um ambiente restrito de segurança que limita o acesso a apenas um conjunto de fontes conhecidas ou que impede o carregamento de recursos por meio de qualquer coisa que não seja a origem da página. Portanto, uma implementação de fallback será necessária.

- Você deve implementar um mecanismo para monitorar a disponibilidade do seu conteúdo por meio do CDN.

Os cenários onde o CDN pode ser menos útil incluem:

- Quando o conteúdo tem uma baixa taxa de acertos e pode ser acessado algumas vezes, ou apenas uma vez, durante o período de validade da vida útil. Na primeira vez que um item é baixado, isso incorre em duas taxas de transação (da origem para o CDN e, depois, do CDN para o cliente).

- Quando os dados são particulares, como para empresas de grande porte ou ecossistemas de cadeia de suprimentos.


## Diretrizes gerais e práticas recomendadas

Usar o CDN é uma boa maneira de reduzir a carga no seu aplicativo e maximizar a disponibilidade e o desempenho. Você deve considerar isso para todos os recursos e o conteúdo apropriado que o seu aplicativo usa. Considere os seguintes pontos ao projetar a sua estratégia de usar o CDN:

- **Origem ** Simplesmente implantar o conteúdo por meio da CDN requer que você especifique um ponto de extremidade HTTP (porta 80) que o serviço CDN usará para acessar e armazenar em cache o conteúdo. + O ponto de extremidade pode especificar um contêiner de armazenamento de blob do Azure que contém o conteúdo estático que você deseja enviar por meio do CDN. O contêiner deve ser marcado como público. Somente os blobs em um contêiner público que têm acesso público de leitura estarão disponíveis por meio do CDN. 
- O ponto de extremidade pode especificar uma pasta chamada **cdn** na raiz de uma das camadas de computação do aplicativo (por exemplo, uma função web ou uma máquina virtual). Os resultados das solicitações de recursos, incluindo recursos dinâmicos, como páginas ASPX, serão armazenados em cache no CDN. O período de cache mínimo é de 300 segundos. Qualquer período mais curto impedirá que o conteúdo seja implantado no CDN (consulte a seção "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cache control</a>" para obter mais informações).

- Se você estiver usando Sites do Azure, o ponto de extremidade é definido para a pasta raiz do site, selecione o site ao criar a instância do CDN. Todo o conteúdo do site estará disponível por meio do CDN.

- Na maioria dos casos, apontar o seu ponto de extremidade CDN em uma pasta dentro de uma das camadas de computação do seu aplicativo oferecerá mais flexibilidade e controle. Por exemplo, ele facilita o gerenciamento das necessidades atuais e futuras de roteamento e gera dinamicamente o conteúdo estático como miniaturas de imagem.

- Você pode usar as cadeias de caracteres de consulta para diferenciar objetos no cache quando o conteúdo for entregue por meio de origens dinâmicas como páginas ASPX. No entanto, esse comportamento pode ser desabilitado por uma configuração no portal de gerenciamento quando você especifica o ponto de extremidade do CDN. Quando o fornecimento de conteúdo do armazenamento de blob e cadeias de caracteres de consulta for tratado como literais de cadeia de caracteres para que dois itens que têm o mesmo nome, mas cadeias de caracteres de consulta diferentes, sejam armazenados como itens separados no CDN.

- Você pode utilizar a regravação de URL para recursos como scripts e outros conteúdos que evitem mover os arquivos para a pasta de origem do CDN.

- Ao usar os blobs de armazenamento do Azure para manter o conteúdo para a CDN, a URL dos recursos em blobs diferencia maiúsculas de minúsculas para o nome do contêiner e do blob.

- Ao usar Sites do Azure, você especifica o caminho para a instância CDN nos links para recursos. Por exemplo, a seguir está especificado um arquivo de imagem na pasta **Imagens** do site que será entregue por meio do CDN:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Implantação** O conteúdo estático pode precisar ser provisionado e implantado independentemente do aplicativo se você não incluí-lo no pacote ou processo de implantação do aplicativo. Considere como isso afetará a abordagem do controle de versão usada para gerenciar os componentes do aplicativo e o conteúdo de recursos estáticos.

- Considere como agrupamento (combinar vários arquivos em um arquivo) e minificação (remover caracteres desnecessários, como espaço em branco, caracteres de nova linha, comentários e outros caracteres) para arquivos de script e CSS serão tratados. Essas técnicas normalmente usadas podem reduzir tempos de carregamento para clientes e são compatíveis com o fornecimento de conteúdo por meio do CDN. Para obter mais informações, consulte [Agrupamento e minificação](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Se você precisar implantar o conteúdo em um local adicional, isso será uma etapa extra no processo de implantação. Se o aplicativo atualizar o conteúdo para o CDN, talvez em intervalos regulares ou em resposta a um evento, ele deve armazenar o conteúdo atualizado em todos os locais adicionais, bem como o ponto de extremidade para o CDN.

- Você não pode configurar um ponto de extremidade do CDN para um aplicativo implantado no preparo do Azure ou no emulador local do Azure no Visual Studio. Isso afetará o teste de unidade, testes funcionais e testes de pré-implantação final. Você deve permitir isso implementando um mecanismo alternativo. Por exemplo, você pode implantar previamente o conteúdo ao CDN usando um aplicativo personalizado temporário ou o utilitário e realizar testes durante o período em que ele é armazenado em cache. Como alternativa, use diretivas de compilação ou constantes globais para controlar de onde o aplicativo carrega os recursos. Por exemplo, quando em execução no modo de depuração, ele poderia carregar recursos como pacotes de script do lado do cliente e outros tipos de conteúdo de uma pasta local e usar o CDN ao ser executado no modo de versão.

- O CDN não oferece suporte a todos os recursos nativos de compactação. No entanto, ele exibirá o conteúdo que já estiver compactado, como arquivos. zip e .gzip. Ao usar uma pasta de aplicativo como o ponto de extremidade do CDN, o servidor pode compactar algum conteúdo por padrão da mesma maneira como quando fornecê-lo diretamente em um navegador da Web ou outro tipo de cliente. Isso depende do valor **Accept-Encoding** enviado pelo cliente. No Azure, o padrão é compactar automaticamente o conteúdo quando a utilização da CPU for inferior a 50%. Alterar as configurações e poder exigir o uso de uma tarefa de inicialização para ativar a compactação de saída dinâmica no IIS se você estiver usando os Serviços de Nuvem para hospedar o aplicativo. Consulte [Habilitar a compactação gzip com o Azure CDN por meio de uma função web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) para obter mais informações.

- **Roteamento e controle de versão** Talvez seja necessário usar diferentes instâncias do CDN; por exemplo, quando você implanta uma nova versão do aplicativo, talvez você queira usar um CDN diferente. Se você usar o armazenamento de blob do Azure como a origem de conteúdo, basta criar uma conta de armazenamento separada ou um contêiner separado e direcionar o ponto de extremidade do CDN a ele. Se você usar a pasta raiz **cdn** dentro do aplicativo, como o ponto de extremidade do CDN, é possível usar técnicas de regravação de URL para direcionar solicitações para uma pasta diferente.

- Não use a cadeia de caracteres de consulta para denotar versões diferentes do aplicativo nos links para recursos no CDN porque, ao desenhar o conteúdo do armazenamento de blob do Azure, a cadeia de caracteres de consulta é parte do nome do recurso (o nome de blob). Ele também pode afetar como o cliente armazena em cache os recursos.

- Implantar novas versões do conteúdo estático quando você atualiza um aplicativo pode ser um desafio se os recursos anteriores forem armazenados em cache no CDN. Para obter mais informações, consulte a seção "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cache control</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Cache control** + Considere como você deseja gerenciar cache e em quais camadas do aplicativo. Por exemplo, ao usar uma pasta como a origem do CDN, você pode especificar a capacidade de cache de páginas que geram o conteúdo e a expiração do conteúdo para todos os recursos em uma pasta específica. Você também pode especificar propriedades de cache para o CDN e para o cliente usando os cabeçalhos HTTP padrão. Embora você já deva estar gerenciando armazenamento em cache no servidor e no cliente, usar o CDN o ajudará a se tornar mais consciente de como o conteúdo é armazenado em cache e onde isso é feito.

- Para impedir que os objetos estejam disponíveis no CDN, você pode excluí-los da origem (pasta raiz **cdn** do aplicativo ou contêiner de blob), remover ou excluir o ponto de extremidade do CDN, ou — no caso do armazenamento de blob — tornar o contêiner ou blob particular. No entanto, os itens serão removidos do CDN somente quando expirar a vida útil.

- Se nenhum período de expiração do cache for especificado (por exemplo, quando o conteúdo é carregado do armazenamento de blob), ele será armazenado em cache no CDN em até 72 horas.

- Em um aplicativo Web, você pode definir o cache e a expiração de todo o conteúdo usando o elemento **clientCache** na seção **system.webServer/staticContent** de um arquivo web.config. Você pode colocar um arquivo web.config em qualquer pasta, para que ele afete os arquivos nessa pasta e os arquivos em todas as subpastas.

- Se você usar uma técnica dinâmica, como ASP.NET, para criar o conteúdo para o CDN, certifique-se de que você especifique a propriedade **Cache.SetExpires** em cada página. O CDN não armazenará em cache a saída de páginas que usam a configuração de cache padrão do público. Defina o período de expiração do cache com um valor apropriado para garantir que o conteúdo não seja descartado e recarregado do aplicativo em intervalos muito curtos.

- **Segurança** O CDN pode entregar conteúdo sobre HTTPS (SSL) usando o certificado fornecido pelo CDN, mas ele também estará disponível em HTTP. Você não pode bloquear o acesso HTTP aos itens no CDN. Talvez seja necessário usar HTTPS para solicitar que o conteúdo estático seja exibido nas páginas carregadas por meio de HTTPS (como um carrinho de compras) para evitar avisos de navegador sobre o conteúdo misto.

- Atualmente, muitos serviços de CDN, como o Azure CDN, não oferecem quaisquer instalações para controle de acesso a fim de proteger o acesso ao conteúdo. Você não pode usar Assinaturas de Acesso Compartilhado (SAS) com o CDN.

- Se entregar scripts do lado do cliente usando o CDN, você pode encontrar problemas se esses scripts usarem uma chamada **XMLHttpRequest** para fazer solicitações HTTP para outros recursos, como fontes, imagens ou dados em um domínio diferente. Muitos navegadores da web impedem o compartilhamento de recursos de origens cruzadas (CORS), a menos que o servidor Web esteja configurado para definir os cabeçalhos de resposta apropriada. Para obter mais informações sobre CORS, consulte a seção "Threat mitigation" no guia <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">API security considerations</span>. Você pode configurar o CDN para dar suporte a CORS: + Se a origem da qual você está entregando conteúdo for o armazenamento de blob do Azure, você pode adicionar um **CorsRule** às propriedades do serviço. A regra pode especificar as origens permitidas para solicitações de CORS, os métodos permitidos, como GET, e a duração máxima em segundos para a regra (o período em que o cliente deve solicitar os recursos vinculados depois de carregar o conteúdo original). Para obter mais informações, consulte [Suporte de Compartilhamento de Recursos de Origens Cruzadas (CORS) para os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Se a origem por meio da qual você está fornecendo conteúdo for uma pasta dentro do aplicativo, como a pasta raiz **cdn**, você pode configurar regras de saída no arquivo de configuração de aplicativo para definir um cabeçalho **Acessar-Controlar-Permitir-Originar** em todas as respostas. Para obter mais informações sobre como usar regras de reescrita, consulte [Módulo de Reescrita de URL](http://www.iis.net/learn/extensions/url-rewrite-module). Observe que essa técnica não é possível ao usar Sites do Azure.

- **Domínios personalizados**+ A maioria dos CDNs, incluindo o Azure CDN, permite que você especifique um nome de domínio personalizado e use-o para acessar recursos por meio do CDN. Você também pode configurar um nome de subdomínio personalizado usando um registro **CNAME** no seu DNS. Usar essa abordagem pode fornecer uma camada adicional de abstração e controle.

- Se usar um **CNAME**, você não pode (no momento em que este guia foi elaborado) também usar SSL, pois o CDN usa o seu próprio certificado SSL único e não haverá correspondência para os nomes de domínio/subdomínio personalizado.

- **Fallback de CDN** Você deve considerar como seu aplicativo lidará com uma falha ou indisponibilidade temporária do CDN. Os aplicativos cliente poderão usar cópias dos recursos que foram armazenados em cache localmente (no cliente) durante solicitações anteriores, ou eles podem usar o código que detecta falhas e solicita, em vez disso, os recursos de origem (a pasta de aplicativo ou o contêiner de blob do Azure que contém os recursos) se o CDN não estiver disponível.

- **SEO** Se o SEO for uma consideração importante em seu aplicativo, certifique-se de:+ Incluir um cabeçalho canônico **Rel** em cada página ou recurso.

- Use um registro de subdomínio **CNAME** e acesse os recursos usando esse nome.

- Considere o impacto do fato de que o endereço IP do CDN pode ser um país ou região diferente daquele do aplicativo em si.

- Ao usar o armazenamento de blob do Azure como a origem, mantenha a mesma estrutura de arquivo para recursos no CDN como nas pastas de aplicativo.


- **Monitoramento e registro em log** Incluem o CDN como parte da sua estratégia para detectar e medir as falhas de monitoramento de aplicativos ou as ocorrências de latência estendidas.

- Habilitar o registro em log para o CDN e incluí-lo como parte de suas operações diárias.

- **Implicação de custos** Você é cobrado pelas transferências de dados de saída do CDN e pelas transações de armazenamento quando o CDN carrega dados do seu aplicativo. Você deve definir períodos de expiração de cache realistas para o conteúdo a fim de garantir a atualização, mas não tão rápido que cause o carregamento de conteúdo do aplicativo repetidas vezes ou o armazenamento de blob para o CDN. No entanto, períodos de expiração muito longos dificultam a remoção de itens do CDN, pois você deve aguardar a expiração deles.

- Os itens que raramente são baixados incorrerão em encargos de duas transações sem fornecer qualquer redução significativa na carga do servidor.



## Código de exemplo
Esta seção contém alguns exemplos de código e técnicas para trabalhar com o CDN.


## Regravação de URL
Segue abaixo, exceto de um arquivo Web.config na raiz de um aplicativo hospedado de Serviços de Nuvem, uma demonstração de como executar a regravação de URL ao usar o CDN. As solicitações do CDN para o conteúdo que ele armazenará em cache serão redirecionadas para pastas específicas dentro da raiz do aplicativo com base no tipo de recurso (como scripts e imagens).

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*).(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

A adição de regras de regravação realiza os redirecionamentos a seguir:

- A primeira regra permite que você insira uma versão no nome do arquivo de um recurso, o que, em seguida, é ignorado. Por exemplo, o **Filename_v123.jpg ** é regravado como **Filename.jpg**. 
- As próximas quatro regras mostram como redirecionar as solicitações se você não deseja armazenar os recursos em uma pasta chamada **cdn** na raiz da função web. As regras de mapeiam as URLs **cdn/Images**, **cdn/Content**, **cdn/Scripts** e **cdn/bundles** para as suas respectivas pastas raiz na função web. Usar a regravação de URL exige que você faça algumas alterações ao agrupamento de recursos.


## Agrupamento e minificação ##

O agrupamento e a minimização podem ser manipulados pelo ASP.NET. Em um projeto do MVC, você define os seus pacotes em **BundleConfig.cs**. É criada uma referência ao conjunto de script minificado chamando o método **Script.Render**, normalmente em código na classe de exibição. Esta referência contém uma cadeia de caracteres de consulta que inclui um hash, que se baseia no conteúdo do pacote. Se o conteúdo do pacote for alterado, o hash gerado também será alterado. Por padrão, as instâncias do Azure CDN têm a configuração **Status de cadeia de caracteres de consulta** desabilitada. Para os pacotes de script atualizado serem tratados corretamente pelo CDN, você deve habilitar a configuração **Status de cadeia de caracteres de consulta** para a instância CDN. Observe que isso pode ser uma hora ou mais antes de criar o CDN e de as alterações de configurações entrarem em vigor.


## Mais informações
+ [Azure CDN](http://azure.microsoft.com/services/cdn/)
+ [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Fornecer conteúdo da CDN do Azure em seu aplicativo Web](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Integrar um serviço de nuvem à CDN do Azure](cdn-cloud-service-with-cdn.md)
+ [Práticas recomendadas para a Rede de Distribuição de Conteúdo do Azure](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=July15_HO4-->