<properties
	pageTitle="Lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure"
	description="Uma lista de verificação de práticas comprovadas para uso com o Armazenamento do Azure no desenvolvimento de aplicativos de alto desempenho."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/18/2015" 
	ms.author="tamram"/>

# Lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure

## Visão geral
Desde os serviços de armazenamento do Microsoft Azure, a Microsoft desenvolveu diversas práticas comprovadas para usar esses serviços de modo a obter muito rendimento. Este artigo consolida as práticas mais importantes na forma de uma lista estilo lista de verificação. A finalidade deste artigo é ajudar os desenvolvedores de aplicativos a verificar se eles estão usando as práticas comprovadas com o armazenamento do Azure, bem como ajudá-lo a identificar outras práticas comprovadas que eles podem adotar. Este artigo não tem como objetivo cobrir todas as possibilidades de otimização de desempenho e escalabilidade. Aqui, não abordamos as práticas que apresentam pouco impacto ou que não se aplicam em larga escala. Na medida em que é possível prever o comportamento do aplicativo durante a criação, é útil seguir essas práticas desde o início para evitar problemas de desempenho.

Todos os desenvolvedores de aplicativos que usam o armazenamento do Azure devem ler este artigo e verificar se seu aplicativo segue as práticas comprovadas listadas abaixo.

## Lista de verificação
Este artigo organiza as práticas comprovadas nos grupos a seguir. As práticas comprovadas aplicam-se a:

-	Todos os serviços de armazenamento do Azure (blobs, tabelas, filas e arquivos)
-	Blobs
-	Tabelas
-	Filas  

O serviço Arquivos do Azure está na fase de visualização. Por isso, as práticas comprovadas dele serão adicionadas ao artigo posteriormente.

|Concluído|	Área|	Categoria|	Pergunta
|----|------|-----------|-----------
||Todos os serviços de |Metas de escalabilidade |[Seu aplicativo é projetado para evitar se aproximar das metas de escalabilidade?](#subheading1) 
||Todos os serviços |Rede |[Dispositivos do lado cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#subheading2) 
||Todos os serviços |Rede |[Os dispositivos do lado cliente têm um link de qualidade alta o suficiente?](#subheading3) 
||Todos os serviços |Rede |[O aplicativo cliente está localizado perto da conta de armazenamento?](#subheading4) 
||Todos os serviços |Distribuição conteúdo|[Você está usando uma CDN para distribuição de conteúdo?](#subheading5) 
||Todos os serviços |Acesso Direta do cliente|[Você está usando SAS e CORS para permitir acesso direto ao armazenamento, em vez de proxy?](#subheading6) 
||Todos os serviços |Cache |[Seu aplicativo em cache dados que são usados repetidamente e alterações raramente?](#subheading7) 
||Todos os serviços |Cache |[O aplicativo de envio em lote atualizações (eles em cache do lado do cliente e, em seguida, carregar conjuntos maiores)?](#subheading8) 
||todos os serviços |Configuração de .NET|[Você configurou o cliente para usar um número suficiente de conexões simultâneas?](#subheading9) 
||todos os serviços |Configuração de .NET |[Você configurou .NET para usar um número suficiente de threads?](#subheading10) 
||todos os serviços |Configuração de .NET |[Estiver usando o .NET 4.5 ou posterior, que melhorou a coleta de lixo?](#subheading11) 
||Todos os serviços |Paralelismo|[Você garante que o paralelismo é limitado adequadamente para que você não sobrecarregar os recursos de cliente ou as metas de escalabilidade?](#subheading12) 
||Todos os serviços |Ferramentas |[Estão usando a versão mais recente do Microsoft fornecidas ferramentas e bibliotecas de cliente?](#subheading13) 
||Todos os serviços |Novas tentativas|[Estão usando uma retirada exponencial política para erros e tempos limite de limitação de novas tentativas?](#subheading14) 
||Todos os serviços |Novas tentativas|[Seu aplicativo evitar repetições de erros sem nova tentativa?](#subheading15) 
||BLOBs|Metas de escalabilidade |[Seu aplicativo continuam dentro a meta de escalabilidade de largura de banda ou de operações para um único blob?](#subheading16) 
||BLOBs|Copiando Blobs|[Você copiar blobs de maneira eficiente?](#subheading17) 
||BLOBs|Copiando Blobs|[Estiver usando AzCopy para cópias em massa de blobs?](#subheading18) 
||BLOBs|Copiando Blobs|[Estiver usando importação/exportação do Azure para transferir grandes volumes de dados?](#subheading19) 
||BLOBs|Usar metadados |[Você está armazenando metadados usados com frequência sobre os blobs nos respectivos metadados?](#subheading20) 
||BLOBs|Carregando rapidamente |[Ao tentar carregar um blob rapidamente, você está carregando blocos em paralelo?](#subheading21) 
||BLOBs|Carregando rapidamente |[Ao tentar carregar vários blobs rapidamente, você está carregando blobs em paralelo?](#subheading22) 
||BLOBs|Tipo Blob correto |[Você está usando blobs de página ou bloquear blobs quando apropriado?](#subheading23) 
||Tabelas|Metas de escalabilidade|[Você está abordando as metas de escalabilidade para entidades por segundo?](#subheading24) 
||Tabelas|Configuração |[Estiver usando JSON para suas solicitações de tabela?](#subheading25) 
||Tabelas|Configuração |[Você desativou o Nagle para melhorar o desempenho das solicitações pequenas?](#subheading26) 
||Tabelas|Tabelas e partições |[Você particionar seus dados adequadamente?](#subheading27) 
||Tabelas|Partições dinâmicas |[Você está evitando padrões somente de acréscimo e somente preceda?](#subheading28) 
||Tabelas|Partições dinâmicas |[As inserções/atualizações se espalham por várias partições?](#subheading29) 
||Tabelas|Escopo de consulta |[Você criou seu esquema para permitir consultas de ponto a ser usado na maioria dos casos e consultas de tabela a ser usado com moderação?](#subheading30) 
||Tabelas|Densidade da consulta |[Fazer sua verificação normalmente apenas consultas e retornar linhas que seu aplicativo usará?](#subheading31) 
||Tabelas|Limitando retornadas fonte de |[Você está usando a filtragem para evitar retornando entidades que não são necessários?](#subheading32) 
||Tables|Limitando retornadas fonte de |[Você está usando projeção para evitar o retorno de propriedades que não são necessários?](#subheading33) 
||Tabelas|Denormalization|[Têm você desnormalizado de dados que você evitar consultas ineficientes ou várias solicitações de leitura durante a tentativa de obter dados?](#subheading34) 
||Tabelas|Inserir/atualizar/excluir |[Você envio em lote solicitações que precisam ser transacional ou pode ser feito ao mesmo tempo para reduzir viagens?](#subheading35) 
||Tabelas|Inserir/atualizar/excluir |[Você estiver evitando recuperar uma entidade apenas para determinar se irá chamar insert ou update?](#subheading36) 
||Tabelas|Inserir/atualizar/excluir |[Você considerou armazenando séries de dados frequentemente serão recuperados juntos em uma única entidade como propriedades em vez de várias entidades?](#subheading37) 
||Tabelas|Inserir/atualizar/excluir |[Para entidades que sempre serão recuperadas juntos e podem ser escritas em lotes (por exemplo, dados de série temporal), você considerou usando blobs em vez de tabelas?](#subheading38) 
||Filas |Metas de escalabilidade |[Você está abordando as metas de escalabilidade para mensagens por segundo?](#subheading39) 
||Filas|Configuração |[Você desativou o Nagle para melhorar o desempenho das solicitações pequenas?](#subheading40) 
||Filas|Tamanho da mensagem |[Suas mensagens são compactas para melhorar o desempenho da fila?](#subheading41) 
||Filas|Recuperar em massa |[Você estiver recuperando várias mensagens em uma única operação "Get"?](#subheading41) 
||Filas|Frequência de sondagem |[Tem sondagem com frequência suficiente para reduzir a latência percebida do seu aplicativo?](#subheading42) 
||Filas|Mensagem para atualizar|[Estiver usando UpdateMessage para armazenar o progresso no processamento de mensagens, evitando reprocessar a mensagem inteira se ocorrer um erro?](#subheading43) 
||Filas|Arquitetura|[Você está usando filas para tornar seu aplicativo mais escalonável, mantendo as cargas de trabalho de longa duração do caminho crítico e, em seguida, dimensionada de forma independente?](#subheading44)


##<a name="allservices"></a>Todos os serviços
Esta seção apresenta as práticas comprovadas aplicáveis ao uso dos serviços de armazenamento do Azure (blobs, tabelas, filas ou arquivos).

###<a name="subheading1"></a>Metas de escalabilidade
Cada serviço de armazenamento do Azure tem metas de escalabilidade para capacidade (GB), taxa de transações e largura de banda. Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando um serviço de armazenamento restringe seu aplicativo, o serviço começa a apresentar os códigos de erro "503 Server busy" (Servidor ocupado) e "500 Operation timeout" (Tempo limite da operação) para algumas das transações de armazenamento. Esta seção trata da abordagem geral para lidar com as metas de escalabilidade e as metas de escalabilidade da largura de banda. As outras seções que tratam de cada serviço de armazenamento tratam das metas de escalabilidade no contexto de cada serviço:

-	[Largura de banda do blob e solicitações por segundo](#subheading16)
-	[Entidades de tabela por segundo](#subheading24)
-	[Fila de mensagens por segundo](#subheading39)  

####<a name="sub1bandwidth"></a>Meta de escalabilidade de largura de banda para todos os serviços
No momento da edição, as metas de largura de banda nos EUA para uma conta de GRS (armazenamento com redundância geográfica) são de 10 gigabits por segundo (Gbps) para entrada (dados enviados à conta de armazenamento) e 20 Gbps para saída (dados enviados pela conta de armazenamento). Os limites das contas de LRS (armazenamento com redundância local) são mais altos — 20 Gbps para entrada e 30 Gbps para saída. Os limites de largura de banda internacional podem ser menores e constam na nossa [página de metas de escalabilidade](http://msdn.microsoft.com/library/azure/dn249410.aspx). Para obter mais informações sobre as opções de redundância em armazenamento, confira a seção [Recursos úteis](#sub1useful) abaixo.

####O que fazer ao lidar com uma meta de escalabilidade
Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:

-	Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
-	Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, você deve criar diversas contas de armazenamento e particionar os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. No momento da edição, cada assinatura do Azure pode ter até 100 contas de armazenamento. O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
-	Se o aplicativo alcançar as metas de largura de banda, você pode compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados ao serviço de armazenamento. Apesar de economizar a largura de banda e melhorar o desempenho da rede, isso também pode ter impactos negativos. Você deve avaliar o impacto no desempenho causado por essa alteração, devido aos requisitos de processamento adicionais para compactar e descompactar dados no cliente. Além disso, o armazenamento de dados compactados pode dificultar a solução de problemas, pois pode ser mais difícil visualizar os dados armazenados por meio de ferramentas padrão.
-	Se seu aplicativo alcançar as metas de escalabilidade, você deve usar a retirada exponencial para novas tentativas (confira [Novas tentativas](#subheading14)). O mais recomendado é nunca alcançar as metas de escalabilidade, o que é possível garantir por meio de um dos métodos acima. Porém, isso garante que o aplicativo não faça novas tentativas rapidamente, piorando o problema de restrição.  

####Recursos úteis
Os links a seguir fornecem detalhes adicionais sobre metas de escalabilidade: -Você pode exibir os destinos de escalabilidade atuais na página [Metas de desempenho e escalabilidade do Armazenamento do Azure no MSDN](http://msdn.microsoft.com/library/azure/dn249410.aspx). - Você pode aprender mais sobre opções de redundância de armazenamento na postagem do blog [Opções de redundância de armazenamento do Azure e armazenamento do acesso de leitura geograficamente redundante](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) – consulte a próxima seção para obter mais detalhes. - Para obter informações atualizadas sobre os preços para os serviços do Azure, consulte [Azure na visão geral de preços](http://azure.microsoft.com/pricing/overview/).

###Rede
Embora as chamadas de API sejam importantes, muitas vezes as limitações físicas da rede do aplicativo têm impacto considerável no desempenho. A seção a seguir descreve algumas das limitações que os usuários podem enfrentar.

####Funcionalidade da rede do cliente
#####<a name="subheading2"></a>Produtividade
No caso da largura de banda, muitas vezes o problema está relacionado às funcionalidades do cliente. Por exemplo, embora uma única conta de armazenamento possa receber um fluxo de 10 Gbps ou mais (confira as [metas de escalabilidade da largura de banda](#sub1bandwidth)), a velocidade da rede em uma instância de função de trabalho "pequena" do Azure só consegue lidar com cerca de 100 Mbps. As instâncias maiores do Azure têm NICs com mais capacidade. Por isso, você pode usar uma instância maior ou mais máquinas virtuais se precisar de limites de rede mais altos em um único computador. Se você estiver acessando um serviço de armazenamento de um aplicativo no local, a mesma regra se aplica: compreender os recursos de rede do dispositivo do cliente e a conectividade de rede para o local de armazenamento do Azure e o aperfeiçoá-los conforme necessário ou projetar seu aplicativo para trabalhar dentro dos seus recursos.

#####<a name="subheading3"></a>Qualidade do vínculo
Como é necessário com qualquer uso de rede, as condições de rede que resultam em erros e na perda de pacote desaceleram a taxa de transferência. Usar WireShark ou NetMon pode ajudar a identificar esse problema.

#####Recursos úteis
Para obter mais informações sobre o tamanho das máquinas virtuais e a largura de banda alocada, confira [Práticas recomendadas para a criação de serviços em larga escala nos serviços de nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx) no MSDN.

####<a name="subheading4"></a>Local
Em todos os ambientes, colocar o cliente próximo ao servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com o mínimo de latência, o melhor local para o cliente é a região na qual o Azure se encontra. Por exemplo, se você tem um site do Azure que usa o armazenamento do Azure, ambos devem estar na mesma região (por exemplo, no oeste dos EUA ou no sudeste asiático). Isso diminui a latência e o custo. No momento da edição, o uso da largura de banda em uma única região é gratuito.

Se os aplicativos do seu cliente não estiverem hospedados no Azure (como no caso de aplicativos para dispositivos móveis ou serviços corporativos), colocar a conta de armazenamento próximo aos dispositivos que a conta acessará geralmente diminui a latência. Se seus clientes estiverem amplamente distribuídos (por exemplo, alguns na América do Norte e alguns na Europa), você deve considerar o uso de várias contas de armazenamento: uma localizada em uma região na América do Norte e outra em uma região na Europa. Isso ajuda a diminuir a latência nas duas regiões. A implementação dessa solução geralmente é mais fácil se os dados armazenados pelo aplicativo são específicos aos usuários e se não é necessário replicar os dados entre as contas de armazenamento. Para uma ampla distribuição de conteúdo, recomendamos o uso de uma CDN. Confira a seção a seguir para saber mais detalhes.

###<a name="subheading5"></a>Distribuição de conteúdo
É possível que um aplicativo precise apresentar o mesmo conteúdo a muitos usuários (por exemplo, um vídeo para demonstrar um produto, apresentado na página inicial de um site) espalhados na mesma região ou em diversas regiões. Nesse cenário, você deve usar uma CDN (rede de distribuição de conteúdo), como o Azure CDN. Por sua vez, o CDN deve usar o armazenamento do Azure como fonte dos dados. Diferente das contas de armazenamento do Azure que existem em uma única região e não podem fornecer conteúdos com baixa latência a outras regiões, a CDN do Azure usa servidores em diversos data centers pelo mundo. Além disso, a CDN geralmente tem limites de entrada muito maiores do que as contas de armazenamento individuais.

Para obter mais informações, confira [CDN do Azure](http://azure.microsoft.com/services/cdn/).

###<a name="subheading6"></a>Usando SAS e CORS
Quando você precisar autorizar o código como JavaScript no navegador da Web de um usuário ou um aplicativo de telefone celular para acessar dados no armazenamento do Azure, uma abordagem é usar um aplicativo em função da web como um proxy: autentica o dispositivo do usuário com a função web, que por sua vez é autenticado com o serviço de armazenamento. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, isso gera uma grande sobrecarga na função web porque todos os dados transferidos entre os dispositivos do usuário e o serviço de armazenamento devem ser transmitidos por meio de uma função web. Você pode evitar o uso da função web como um proxy para o serviço de armazenamento. Para isso, use SAS (assinaturas de acesso compartilhado), que podem ser combinadas a cabeçalhos de CORS (compartilhamento de recursos entre origens). Por meio das SAS, você pode permitir que o dispositivo do usuário faça solicitações diretamente a um serviço de armazenamento por meio de um token de acesso limitado. Por exemplo, se o usuário quiser carregar uma foto no seu aplicativo, sua função web pode gerar e enviar um token SAS que conceda permissão de edição a um blob ou contêiner específico pela próxima meia hora ao dispositivo do usuário. Após esse período, o token expira.

Normalmente, o navegador não permite o uso de JavaScript em páginas hospedadas por um site em um domínio para executar operações especificas, como um "PUT" em outro domínio. Por exemplo, se você hospedar uma função web em "contosomarketing.cloudapp.net" e quiser usar o JavaScript do cliente para carregar um blob em sua conta de armazenamento, em "contosoproducts.blob.core.windows.net", a "política de mesma origem” proibirá a operação. O CORS é um recurso do navegador que permite que o domínio alvo (neste caso, a conta de armazenamento) comunique-se com o navegador ao qual confia as solicitações que se originam no domínio de origem (neste caso, a função web).

As duas tecnologias podem ajudar você a evitar cargas e gargalos desnecessários no seu aplicativo Web.

####Recursos úteis
Para obter mais informações sobre SAS, consulte [Assinaturas de acesso compartilhado, parte 1: entendendo o modelo SAS](../storage-dotnet-shared-access-signature-part-1/).

Para obter mais informações sobre o CORS, confira [Suporte a CORS (Compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx) no MSDN.

###Caching
####<a name="subheading7"></a>Obtenção de dados
Em geral, obter os dados de um serviço uma única vez é melhor do que obtê-los duas vezes. Tome como exemplo um aplicativo Web MVC em execução em uma função web que já recuperou um blob de 50 MB do serviço de armazenamento para apresentar ao usuário como conteúdo. O aplicativo pode recuperar esse mesmo blob sempre que o usuário o solicitar, ou pode armazená-lo em cache localmente e reutilizar esse conteúdo em outras solicitações dos usuários. Além disso, sempre que um usuário solicitar os dados, o aplicativo pode emitir um GET com um cabeçalho condicional para tempo de modificação, o que evita obter todo o bob se ele não foi modificado. Você pode aplicar esse mesmo padrão ao trabalho com entidades em tabela.

Em alguns casos, você pode determinar que seu aplicativo parta do pressuposto de que o blob permanece válido por um curto período após a recuperação e que, durante esse período, o aplicativo não precisa verificar se o blob foi modificado.

O armazenamento em cache é ótimo para configurações, pesquisas e outros dados que são sempre usados pelo aplicativo.

Para ver como obter as propriedades de um blob e descobrir a data da última modificação por meio de .NET, confira [Definição e recuperação de propriedades e metadados](http://msdn.microsoft.com/library/azure/hh225342.aspx) no MSDN. Para obter mais informações sobre downloads condicionais, confira [Atualização condicional de uma cópia local de um blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.

####<a name="subheading8"></a>Carregamento de dados em lotes
Em algumas situações nos aplicativos, é possível agregar dados localmente e carregá-los em lotes em vez de carregar cada dado imediatamente. Por exemplo, um aplicativo Web deve manter um arquivo de registro de atividades: o aplicativo pode enviar os detalhes de cada atividade assim que elas acontecem, como uma entidade de tabela (que requer muitas operações de armazenamento), ou pode salvar os detalhes da atividade em um arquivo de log local para carregar os detalhes de todas as atividades periodicamente, como um arquivo delimitado, em um blob. Se cada entrada de log tiver 1 KB, você pode carrega milhares de transações de uma só vez (é possível carregar um blob para transações com até 64 MB). É claro que, se o computador local falhar antes do carregamento, potencialmente perderá alguns dados de log: o desenvolvedor do aplicativo deve criar a possibilidade de dispositivo cliente ou falhas de carregamento. Se for necessário carregar os dados das atividades para timespans (não apenas por atividade), os blobs são mais indicados do que as tabelas.

###Configuração .NET
Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho. Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.

####<a name="subheading9"></a>Aumentar o limite de conexão padrão
No .NET, o código a seguir aumenta o limite de conexão de padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) a 100. Normalmente, você deve definir o valor para aproximadamente igual ao número de threads utilizados pelo seu aplicativo.

	ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  

Você deve definir o limite da conexão antes de abrir as conexões.

No caso de outras linguagens de programação, confira a documentação da linguagem para verificar como definir o limite da conexão.

Para obter informações adicionais, consulte a postagem [Serviços Web: conexões simultâneas](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx) no MSDN.

####<a name="subheading10"></a>Aumentar o mínimo dos Threads de ThreadPool, caso esteja usando código síncrono com tarefas assíncronas
Este código aumenta o mínimo de threads do pool de threads:

	ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  

Para obter mais informações, confira [ThreadPool.SetMinThreads Method](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads(v=vs.110).aspx) no MSDN.

####<a name="subheading11"></a>Aproveitar a coleta de lixo do .NET 4.5
Use a versão 4.5 ou posterior do .NET para que o aplicativo cliente aproveite as melhorias de desempenho na coleta de lixo do servidor.

Para obter mais informações, confira o artigo [Uma visão geral dos aprimoramentos de desempenho no .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx) no MSDN.

###<a name="subheading12"></a>Paralelismo não associado
Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado (no qual não há limites para a quantidade de threads e/ou de solicitações paralelas) para carregar ou baixar dados, usando diversas funções de trabalho para acessar diversas partições (contêineres, filas ou tabelas) na mesma conta de armazenamento ou para acessar diversos itens na mesma partição. Se o paralelismo não estiver associado, o aplicativo pode ultrapassar as funcionalidades do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, o que resulta em latências mais longas e restrições.

###<a name="subheading13"></a>Ferramentas e bibliotecas cliente para armazenamento
Sempre use a última versão das bibliotecas e ferramentas fornecidas pela Microsoft. No momento da edição, é possível usar bibliotecas RTM disponíveis para .NET, Windows Phone, Tempo de Execução do Windows e Java, além de bibliotecas de visualização em outras linguagens, como node.js e C++. Além disso, a Microsoft liberou cmdlets do PowerShell e os comandos da CLI do Azure para trabalhar com o Armazenamento do Azure. A Microsoft desenvolve essas ferramentas pesando no desempenho, além de mantê-las atualizadas com as últimas versões do serviço e garantir que elas gerenciem muitas das práticas de desempenho comprovadas internamente.

###Novas tentativas
####<a name="subheading14"></a>Limitação/Servidor ocupado
Em alguns casos, o serviço de armazenamento pode restringir seu aplicativo ou simplesmente não conseguir atender uma solicitação devido a alguma condição transitória, e retornar uma mensagem "503 Server busy" ou "500 Timeout". Isso pode acontecer se o aplicativo estiver alcançando alguma das metas de escalabilidade ou se o sistema estiver balanceando os dados particionados a fim de aumentar a taxa de transferência. O aplicativo cliente normalmente deve tentar novamente a operação que gera um erro: a tentativa da mesma solicitação pode ser bem-sucedida mais tarde. No entanto, se o serviço de armazenamento estiver restringindo o aplicativo porque ele ultrapassou as metas de escalabilidade ou porque o serviço não pôde atender a solicitação por algum motivo, novas tentativas geralmente piorarão o problema. Por esse motivo, você deve usar uma retirada exponencial (as bibliotecas cliente são o padrão para esse comportamento). Por exemplo, o aplicativo pode fazer uma nova tentativa em 2 segundos, 4 segundos, 10 segundos e 30 segundos para então abandonar o processo. Esse comportamento faz com que o aplicativo diminua consideravelmente a sua carga no serviço, em vez de agravar qualquer problema.

Pode haver novas tentativas imediatas em caso de erros de conectividade porque esses erros não são resultado de restrições e devem ser temporários.

####<a name="subheading15"></a>Erros sem nova tentativa
As bibliotecas cliente sabem quais erros admitem novas tentativas ou não. No entanto, se você estiver escrevendo seu próprio código contra a API REST de armazenamento, lembre-se há alguns erros que você não deve tentar novamente: por exemplo, uma resposta 400 (solicitação incorreta) indica que o aplicativo cliente enviou uma solicitação que não pôde ser processada porque não estava no formato esperado. O reenvio dessa solicitação sempre resultará na mesma resposta, por isso as novas tentativas não são úteis. Se você está escrevendo o seu próprio código com base na API REST de armazenamento, conheça o significado de alguns erros e saiba o método adequado de fazer uma nova tentativa ou não para cada um deles.

####Recursos úteis
Para obter mais informações sobre os códigos de erro de armazenamento, confira [Status e códigos de erro](http://msdn.microsoft.com/library/azure/dd179382.aspx) no site do Microsoft Azure.

##Blobs
Além das práticas comprovadas para [todos os serviços](#allservices) descritos, as práticas comprovadas a seguir aplicam-se especificamente ao serviço de blob.

###Metas de escalabilidade específicas do blob
####<a name="subheading16"></a>Largura de banda e operações por blob
Você pode ler ou editar um único blob a no máximo 60 MB/segundo, o que equivale a 480 Mbps e ultrapassa a capacidade de muitas redes dos clientes, inclusive do NIC físico no dispositivo do cliente. Além disso, um único blob comporta 500 solicitações por segundo. Se vários dos seus clientes precisarem ler os mesmo blob e você talvez ultrapasse esses limites, é possível usar uma CDN para distribuir o blob.

Para obter informações sobre a taxa de transferência alvo para os blobs, confira [Escalabilidade e metas de desempenho do armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) no MSDN.

###Copiando e movendo blobs
####<a name="subheading17"></a>Copiar blob
A versão 2012-02-12 da API REST do armazenamento introduziu a capacidade útil para copiar blobs entre contas: um aplicativo cliente pode instruir o serviço de armazenamento para copiar um blob de outra origem (possivelmente em outra conta de armazenamento) e permitir que o serviço de realizar a cópia de forma assíncrona. Isso pode diminuir consideravelmente a largura de banda necessária para o aplicativo quando você estiver migrando dados de outras contas de armazenamento, porque você não precisará baixar nem carregar dados.

No entanto, não é possível garantir em quanto tempo a cópia será concluída, ao se realizar cópias entre contas de armazenamento. Se o aplicativo precisar copiar um bob completo rapidamente com a sua supervisão, pode ser melhor baixar o blob para uma máquina virtual e carregá-lo no destino. Para que seja possível prever toda a situação, a cópia deve ser feita por uma máquina virtual em execução na mesma região do Azure, ou as condições da rede podem (e provavelmente irão) afetar o desempenho da cópia. Além disso, você pode monitorar o progresso de uma cópia assíncrona de modo programático.

As cópias da mesma conta de armazenamento geralmente são rápidas.

Para obter mais informações, confira [Copiar blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) no MSDN.

####<a name="subheading18"></a>Usar AzCopy
A equipe de armazenamento do Azure lançou a ferramenta de linha de comando "AzCopy", que deve ajudar na transferência em massa de muitos blobs para, de e entre as contas de armazenamento. Essa ferramenta foi otimizada para esse cenário e pode alcançar altas taxas de transferência. Ela deve ser usada para baixar e carregar itens, bem como para copiar cenários, em massa. Você pode saber mais sobre ela e baixá-la [aqui](storage-use-azcopy.md).

####<a name="subheading19"></a>Serviço de importação/exportação do Azure
Para grandes volumes de dados (superiores a 1 TB), o armazenamento do Azure oferece o serviço de Importação/Exportação, que permite o carregamento e o download do armazenamento do blob por meio de discos rígidos. Você pode colocar os dados em um disco rígido e enviá-lo à Microsoft para que possamos carregar esses dados ou enviar um disco rígido vazio para que baixemos os dados. Saiba mais sobre isso [aqui](storage-import-export-service.md). Essa ação pode ser muito mais eficiente do que o envio/carregamento desse volume de dados pela rede.

###<a name="subheading20"></a>Uso de metadados
O serviço Blob dá suporte às principais solicitações, o que pode incluir metadados sobre o blob. Por exemplo, se o aplicativo precisar remover os dados EXIF de uma fotografia, é possível recuperá-la e extrair esses dados. Para economizar largura de banda e melhorar o desempenho, seu aplicativo pode armazenar os dados EXIF nos metadados do blob quando o aplicativo tiver carregado a foto: você pode então recuperar os dados EXIF nos metadados usando apenas uma solicitação HEAD, economizando o tempo de processamento e largura de banda significativa necessários para extrair os dados EXIF cada vez o blob for lido. Isso é útil quando você precisa apenas dos metadados, não do conteúdo completo de um blob. Observe que é possível armazenar apenas 8 KB de metadados por blob. O serviço não aceita solicitações para armazenar volumes maiores. Se esse tamanhão não for suficiente, talvez não seja possível usar essa abordagem.

Para ver como obter os metadados de um blob por meio de .NET, confira [Definição e recuperação de propriedades e metadados](http://msdn.microsoft.com/library/azure/hh225342.aspx) no MSDN.

###Carregamento rápido
Para carregar blobs de forma rápida, a primeira pergunta a responder é: você está carregando um blob ou muitos? Use as informações abaixo para identificar o método de uso correto de acordo com o seu cenário.

####<a name="subheading21"></a>Carregamento rápido de um blob grande
Para carregar um único blob grande com rapidez, seu aplicativo cliente deve carregar os blocos ou as páginas em paralelo, levando em consideração as metas de escalabilidade para cada blob e a conta de armazenamento como um todo. As bibliotecas de cliente de armazenamento RTM fornecidas pela Microsoft (.NET e Java) têm a capacidade de fazer isso. Para cada uma das bibliotecas, use a propriedade/o objeto especificado abaixo para definir o nível de simultaneidade:

-	.NET: defina ParallelOperationThreadCount em um objeto BlobRequestOptions a ser usado.
-	Java/Android: usar BlobRequestOptions.setConcurrentRequestCount()
-	Node.js: use parallelOperationThreadCount nas opções da solicitação ou no serviço Blob.
-	C++: use o método blob_request_options::set_parallelism_factor.

####<a name="subheading22"></a>Carregamento rápido de diversos blobs
Para carregar diversos blobs com rapidez, carregue-os paralelamente. Esse tipo de carregamento é mais rápido do que o carregamento de um único blob por vez com carregamento paralelo de blocos porque a carga é dividida em diversas partições do serviço de armazenamento. Cada blob dá suporte a uma taxa de transferência de 60 MB/segundo (aproximadamente, 480 Mbps). No momento da edição, uma conta LRS baseada em US dá suporte a até 20 Gbps de entrada, valor muito superior à entrada com suporte por um único blob. O [AzCopy](#subheading18) executa os carregamentos em paralelo por padrão e é a opção recomendada nesse caso.

###<a name="subheading23"></a>Escolhendo o tipo de blob certo
O armazenamento do Azure oferece suporte a dois tipos de blob: blobs de *página* e blobs de *bloco*. Em um determinado cenário de uso, o tipo de blob escolhido afeta o desempenho e a escalabilidade da solução. Os Blobs de bloco são apropriados quando você deseja carregar grandes quantidades de dados com eficiência: por exemplo, um aplicativo cliente pode precisar carregar fotos ou vídeo no armazenamento de blob. Os Blobs de página são apropriados, se o aplicativo precisa executar gravações aleatórias em dados: por exemplo, os VHDs do Azure são armazenados como blobs de página.

Para obter mais informações, confira [Noções básicas sobre blobs de bloco e blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx) no MSDN.

##Tabelas
Além das práticas comprovadas para [todos os serviços](#allservices) descritos, as práticas comprovadas a seguir aplicam-se especificamente ao serviço de tabela.

###<a name="subheading24"></a>Metas de escalabilidade específicas da tabela
Além das limitações da largura de banda de toda uma conta de armazenamento, as tabelas têm o limite de escalabilidade descrito a seguir. Observe que o sistema balanceia a carga conforme o tráfego aumento, mas se houver um pico de tráfego repentino, é possível que você não obtenha esse volume de taxa de transferência imediatamente. Se você normalmente presencia esses picos, haverá restrições e/ou eventos de tempo limite durante esses picos, pois o serviço de armazenamento balanceia a carga da tabela automaticamente. Os aumentos graduais geralmente apresentam resultados melhores, pois permitem que o sistema tenha tempo para balancear a carga corretamente.

####Entidades por segundo (conta)
O limite de escalabilidade para o acesso às tabelas é de até 20 mil entidades (1 KB para cada) por segundo em cada conta. Em geral, cada entidade inserida, atualizada, excluída ou verificada é computada nessa meta. Assim, uma inserção em lote com 100 entidades é computada como 100 entidades. Uma consulta que verifica 1000 entidades e retorna apenas 5 é computada como 1000 entidades.

####Entidades por segundo (partição)
Em uma única partição, a meta de escalabilidade para o acesso às tabelas é de 2000 entidades (1 KB para cada) por segundo, usando a mesma contagem descrita na seção anterior.

###Configuração
Esta seção lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho do serviço Tabela:

####<a name="subheading25"></a>Usar JSON
Começando na versão 2013-08-15 do serviço de armazenamento, o serviço Tabela dá suporte ao uso de JSON, em vez do formato AtomPub baseado em XML, para transferir dados de tabela. Isso pode reduzir o tamanho da carga em até 75% e pode melhorar consideravelmente o desempenho do seu aplicativo.

Para obter mais informações, consulte a postagem [Tabelas do Microsoft Azure: Introdução ao JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e [Formato de carga útil para operações do serviço tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx) no MSDN.

####<a name="subheading26"></a>Desativação do Nagle
O algoritmo de Nagle é implementado largamente em redes TCP/IP como meio de melhorar o desempeno das redes. No entanto, ele não é ideal em todas as circunstâncias (como em ambientes altamente interativos). No caso do armazenamento do Azure, o algoritmo do Nagle tem um impacto negativo sobre o desempenho das solicitações para a tabela e os serviços de fila. Por isso, desabilite-o, se possível.

Para obter mais informações, confira a postagem [Nagle’s Algorithm is Not Friendly towards Small Requests](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) (O algoritmo de Nagle não é adequado para solicitações pequenas) no nosso blog, que explica por que esse algoritmo tem baixo desempenho na interação com solicitações de tabela e fila, além de mostrar como desabilitá-lo no seu aplicativo cliente.

###Esquema
Como você representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço de tabela. Embora cada aplicativo seja único, esta seção descreve algumas práticas gerais comprovadas relacionadas:

-	Ao design da tabela
-	A consultas eficientes
-	A atualizações de dados eficientes  

####<a name="subheading27"></a>Tabelas e partições
As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva que a identifica dentro da partição em questão. As partições trazem benefícios, mas também criam limites de escalabilidade.

-	Benefícios: você pode atualizar as entidades em uma única transação atômica e em lote que contenha até 100 operações de armazenamento independentes (limite total de 4 MB). Partindo do pressuposto de que a mesma quantidade de entidades pode ser recuperada, você também pode consultar dados em uma única partição com mais eficiência do que a consulta a dados presentes em diversas partições (consulte as demais recomendações sobre consulta aos dados das tabelas).
-	Limite de escalabilidade: o acesso às entidades armazenadas em uma única partição não pode passar por balanceamento de carga porque as partições dão suporte a transações atômicas em lote. Por esse motivo, a meta de escalabilidade de cada partição da tabela é menor do que a meta do serviço Tabela como um todo.  

Devido a essas características, você deve adotar estes princípios de design:

-	Os dados atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica devem constar na mesma partição. Isso pode acontecer porque seu aplicativo agrega edições ou porque você que aproveitar a vantagem das operações atômicas em lote. Além disso, é possível consultar os dados em uma única partição com mais eficiência do que fazer a consulta em diversas partições.
-	Os dados não inseridos/atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica (consulta única ou atualização em lote) devem constar em partições diferentes. Uma observação importante é que não há limite de chaves de partição para uma única tabela. Por isso, ter milhões de chaves de partição não representa um problema e não influencia o desempenho. Por exemplo, se seu aplicativo é um site popular que requer o logon dos usuários, usar a ID do usuário como chave de partição pode ser uma boa opção.  

####Partições mais acessadas
As partições mais acessadas são aquelas que recebem um porcentual desproporcional do tráfego de uma conta e não podem passar pelo balanceamento de carga porque se trata de uma única partição. Em geral, essas partições são criadas de duas formas:

#####<a name="subheading28"></a>Padrões somente anexar e somente incluir
No padrão "somente anexar", todo (ou praticamente todo) o tráfego atribuído a um determinado PK aumenta e diminui de acordo com o momento. Um exemplo é se o aplicativo usado na data funciona como chave de partição para os dados do log. Isso faz com que todas as inserções vão para a última partição da sua tabela. O sistema não pode balancear a carga porque todas as edições vão em direção ao final da tabela. Se o volume do tráfego para a partição em questão ultrapassar a meta de escalabilidade na partição, teremos restrições como resultado. É melhor garantir que o tráfego seja enviado para diversas partições, a fim de habilitar o balanceamento da carga das solicitações na tabela.

#####<a name="subheading29"></a>Dados de tráfego intenso
Se você estiver particionando resultados de esquemas em uma única partição que tem dados mais usados do os dados presentes nas demais partições, também pode haver restrições, pois a partição em questão aproxima-se da meta de escalabilidade de uma única partição. É melhor garantir que seu esquema de partição não faça com que nenhuma partição específica aproxime-se às metas de escalabilidade.

####Consultas
Esta seção descreve as práticas comprovadas de consulta do serviço Tabela.

#####<a name="subheading30"></a>Escopo de consulta
Há diversas maneiras de especificar quais entidades devem ser consultadas. A seguir, tratamos do uso de cada uma delas.

Em geral, evite as verificações (consultas cujo escopo tem mais de uma entidade). No entanto, se a verificação for necessária, tente organizar os dados de modo que as verificações recuperem os dados necessários sem verificar ou retornar grandes quantidades de entidades desnecessárias.

######Consultas pontuais
A consulta recupera exatamente uma entidade. Ela faz isso por meio da especificação da chave de partição e da chave de linha da entidade a ser recuperada. Essas consultas são muito eficientes e você deve usá-las sempre que possível.

######Consultas às partições
Esse tipo de consulta recupera um conjunto de dados que tem uma chave de partição em comum. Geralmente, a consulta especifica diversos valores de chave de linha ou valores de propriedade de entidade, além de uma chave de partição. Elas são menos eficientes do que as consultas pontuais e devem ser usados com critério.

######Consultas às tabelas
Esse tipo de consulta recupera um conjunto de entidades que não tem uma chave de partição em comum. Essas consultas não são eficientes e você deve evitá-las sempre que possível.

#####<a name="subheading31"></a>Densidade de consulta
Outro fator importante na eficiência da consulta é a quantidade de entidades retornadas, em comparação à quantidade de entidades verificadas para localizar o conjunto retornado. Se o aplicativo ficar uma consulta de tabela filtrando por um valor da propriedade comum a apenas 1% dos dados, a consulta verificará 100 entidades para cada entidade retornada. As metas de escalabilidade de tabela discutidas anteriormente se relacionam ao número de entidades verificado e não o número de entidades retornadas: uma densidade de consulta baixa pode facilmente fazer com que o serviço Tabela acelere seu aplicativo porque ele deve verificar muitas entidades para recuperar a entidade que você está procurando. Confira a seção abaixo sobre [desnormalização](#subheading34) para obter mais informações sobre como evitar isso.

#####Limitação da quantidade de dados retornados
######<a name="subheading32"></a>Filtragem
Quando souber que uma consulta retornará entidades desnecessárias no aplicativo cliente, você pode usar um filtro para diminui a quantidade de entradas retornadas. Embora as entidades não retornadas para o cliente ainda sejam computadas nos limites de escalabilidade, o desempenho do aplicativo melhora devido à redução no tamanho da carga da rede e na quantidade de entidades que seu aplicativo cliente deve processar. Confira a observação acima sobre a [densidade das consultas](#subheading31), mas lembre-se de que as metas de escalabilidade estão relacionadas à quantidade de entidades verificadas. Por isso, as consultas que filtram muitas entidades podem resultar em restrições, mesmo se poucas entidades forem retornadas.

######<a name="subheading33"></a>Projeção
Se seu aplicativo clientes precisar apenas de um conjunto limitado de propriedades das entidades da sua tabela, você pode usar a projeção para limitar o tamanho do conjunto de dados retornado. Como no caso da filtragem, isso ajuda a diminuir a carga da rede e o processamento do cliente.

#####<a name="subheading34"></a>Desnormalização
Diferente do que acontece com os bancos de dados relacionados, as práticas recomentadas para consultar os dados da tabela com eficiência causam a desnormalização dos dados. Ou seja, a duplicação dos mesmos dados em diversas entidades (uma para cada chave, que você pode usar para localizar os dados) para minimizar a quantidade de entidades a serem verificadas por uma consulta para localizar os dados necessários para o cliente, em vez de precisar verificar muitas entidades para localizar os dados necessários para o aplicativo. Por exemplo, em um site de e-commerce, você pode querer localizar um pedido pela ID do clientes (apresentar os pedidos de um determinado cliente) e por data (apresentar os pedidos de uma determinada data). No armazenamento de tabela, é melhor armazenar a entidade (ou fazer referência a ela) duas vezes, uma com o nome da tabela, PK e RK para facilitar a localização da ID do cliente e outra para facilitar a localização por data.

####Inserção/atualização/exclusão
Esta seção descreve as práticas comprovadas para modificar as entidades armazenadas no serviço Tabela.

#####<a name="subheading35"></a>Envio em lote
As transações em lote são conhecidas como ETG (transações do grupo de entidades) no armazenamento do Azure. Todas as operações em uma ETG devem constar em uma única partição e em uma única tabela. Quando possível, use as ETGs para inserir, atualizar e excluir em lotes. Isso diminui a quantidade de viagens de ida e volta entre o aplicativo cliente e o servidor, diminui a quantidade de transações computáveis (uma ETG conta como uma única transação e pode conter até 100 operações de armazenamento) e viabiliza atualizações atômicas (nas ETGs, todas as operações têm êxito ou falham). Os ambientes com altos níveis de latência, como os dispositivos móveis, veem muitos benefícios no uso das ETGs.

#####<a name="subheading36"></a>Upsert
Use as operações de tabela **Upsert** sempre que possível. Há dois tipos de **Upsert** e os dois podem ser mais eficientes do que as operações tradicionais **Insert** e **Update**:

-	**InsertOrMerge**: use essa operação quando quiser carregar um subconjunto de propriedades da entidade, mas não souber se a entidade já existe. Se a entidade existir, essa chamada atualiza as propriedades incluídas na operação **Upsert** e deixa todas as propriedades existente como elas se encontram. Se a entidade não existir, a operação insere a nova entidade. Isso se parece com o uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão mudando.
-	**InsertOrReplace**: use essa operação quando quiser carregar uma entidade completamente nova, mas não souber se a entidade já existe. Você só deve usar essa opção quando souber que a entidade carregada está perfeita, pois ela substitui completamente a entidade anterior. Por exemplo, você quer atualizar a entidade que armazena um local atual do usuário independente de o aplicativo já ter armazenado os dados de local do usuário. A nova entidade de localização está completa e você não precisa de informações de nenhuma entidade anterior.

#####<a name="subheading37"></a>Armazenamento de séries de dados em uma única entidade
Às vezes, um aplicativo armazena uma série de dados que ele precisa recuperar uma só vez com frequência: por exemplo, um aplicativo pode controlar o uso da CPU ao longo do tempo para plotar um gráfico sem interrupção dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada uma delas representando uma hora específica e armazenando o uso da CPU para a hora em questão. Para obter esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das últimas 24 horas.

Como alternativa, o aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, seu aplicativo pode usar uma única chamada de **InsertOrMerge Upsert** para atualizar o valor para a hora mais recente. Para obter os dados, o aplicativo precisa recuperar apenas uma entidade, em vez de 24, resultando em uma consulta muito eficiente (confira acima a seção sobre o [escopo da consulta](#subheading30)).

#####<a name="subheading38"></a>Armazenando dados estruturados em blobs
Às vezes parece que os dados estruturados devem ser dispostos em tabelas, mas os intervalos das entidades sempre são recuperados em conjunto e podem ser inseridos em lote. Um bom exemplo disso são os arquivos de log. Nesse caso, você pode considerar diversos minutos de log como um lote, inseri-los e recuperar diversos minutos de uma só vez. Nesse caso, para o desempenho, é melhor usar blobs do que tabelas, pois assim é possível diminuir consideravelmente a quantidade de objetos escritos/retornados, além de diminuir a quantidade de solicitações que geralmente devem ser feitas.

##Filas
###<a name=subheading39"></a>Limites de escalabilidade
Uma única fila pode processar aproximadamente 2.000 mensagens (1 KB cada) por segundo (cada contagem AddMessage, GetMessage e DeleteMessage como uma mensagem aqui). Se isso for insuficiente para seu aplicativo, você deve utilizar várias filas e distribuir as mensagens entre elas.

Para exibir as metas de escalabilidade atuais, acesse a página Escalabilidade e [Metas de Desempenho do Armazenamento do Azure no MSDN](http://msdn.microsoft.com/library/azure/dn249410.aspx).

###<a name=subheading40"></a>Desativação do Nagle
Consulte a seção de configuração da tabela que fala sobre o algoritmo de Nagle. O uso desse algoritmo geralmente não é uma boa opção para o desempenho das solicitações de fila, e você deve desabilitá-lo.

###<a name=subheading41"></a>Tamanho da mensagem
O desempenho e a escalabilidade da fila diminui quando o tamanho de mensagem aumenta. Você deve colocar somente as informações que o receptor precisa em uma mensagem.

###<a name=subheading42"></a>Recuperação de lote
Você pode recuperar até 32 mensagens de uma fila em uma única operação. Isso pode diminuir a quantidade de viagens de ida e volta de um aplicativo cliente, o que é útil principalmente para ambientes com alta latência, como os dispositivos móveis.

###<a name=subheading43"></a>Intervalo de sondagem de fila
A maioria dos aplicativos de sondagem para mensagens de uma fila, pode ser uma das principais fontes de transações para o aplicativo. Selecione o intervalo de sondagem com sabedoria: a sondagem muito frequente pode fazer com que seu aplicativo se aproxime das metas de escalabilidade para a fila. No entanto, em 200.000 transações para US $0,01 (no momento da gravação), um único processador sondando uma vez por segundo em um mês custaria menos de 15 centavos, assim o custo de sondagem não é normalmente um fator que afeta sua opção de intervalo de sondagem.

Para obter informações de custo atualizadas, consulte [Detalhes de preços de armazenamento](http://azure.microsoft.com/pricing/details/storage/).

###<a name=subheading44"></a>UpdateMessage
Você pode usar **UpdateMessage** para aumentar o tempo limite da invisibilidade ou atualizar as informações de estado de uma mensagem. Embora isso seja útil, lembre-se de que a operação **UpdateMessage** é computada na meta de escalabilidade. No entanto, essa abordagem pode ser muito mais eficiente do que ter um fluxo de trabalho que transmite uma tarefa de uma fila para a outra, pois cada etapa da tarefa é concluída. O uso da operação **UpdateMessage** permite que o aplicativo salve o estado da tarefa na mensagem e continue trabalhando, em vez de colocar a mensagem na fila novamente para a próxima etapa a cada etapa concluída.

Para obter mais informações, consulte o artigo [Como alterar o conteúdo de uma mensagem em fila](../storage-dotnet-how-to-use-queues/#change-contents).

###<a name=subheading45"></a>Arquitetura do aplicativo
Você deve usar filas para que a arquitetura do aplicativo seja escalonável. A seguir temos algumas maneiras de usar filas para que seu aplicativo seja mais escalonável:

-	Você pode usar as filas para criar listas de pendências de trabalho para processamento e suavização das cargas de trabalho no seu aplicativo. Por exemplo, você pode colocar na fila as solicitações dos usuários para execução de trabalhos que requerem trabalho muito intenso do processador, como o redimensionamento das imagens carregadas.
-	Você pode usar as filas para desassociar parte do aplicativo, a fim de poder escaloná-las de forma independente. Por exemplo, um front-end da Web pode colocar os resultados de pesquisa dos usuários em uma fila para análise posterior e armazenamento. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila conforme necessário.  

##Conclusão
Este artigo falou sobre algumas das práticas comprovadas mais comuns para otimizar o desempenho com o uso do armazenamento do Azure. Nós recomendamos que cada desenvolvedor avalie seu aplicativo com base nas práticas descritas acima e considere seguir as recomendações para obter desempenho excelente para seus aplicativos que usam o Armazenamento do Azure.
 

<!---HONumber=July15_HO3-->