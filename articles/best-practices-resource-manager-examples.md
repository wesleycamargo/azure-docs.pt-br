<properties
	pageTitle="Exemplos contextuais de práticas recomendadas para a implementação de modelos"
	description="Mostra exemplos de modelos do Gerenciador de Recursos do Azure que ilustram as práticas recomendadas."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="mmercuri"/>

# Exemplos contextuais de práticas recomendadas para a implementação de modelos

Este tópico fornece sete exemplos contextuais de como implementar os modelos do Gerenciador de Recurso do Azure. Para obter uma visão geral dos princípios ilustrados nesses exemplos, consulte [Best practices for designing Azure Resource Manager templates (Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure)](best-practices-resource-manager-design-templates.md).

Este tópico faz parte de um whitepaper mais amplo. Para ler o documento completo, baixe [Considerações e práticas comprovadas de modelos ARM de nível mundial](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Movendo um modelo com escopo de funcionalidade para um modelo com escopo de solução de ponta a ponta

O padrão para desenvolver um modelo com escopo de funcionalidade foi compartilhado anteriormente. Uma pergunta que você pode se fazer é se existem diferentes considerações ao usar esse modelo com escopo de funcionalidade sozinho ou como parte de um modelo de solução com escopo de ponta a ponta.

Por exemplo, se houver um modelo concentrado em tecnologia que implantou o SQL Server como uma funcionalidade, quais seriam as considerações, se houver, do uso dele independentemente ou como parte de um modelo com escopo de solução de ponta a ponta mais amplo que pode usar esse SQL Server para dar suporte para um aplicativo Web.

Ao analisar esse cenário, é relevante analisar o número de recursos provavelmente envolvidos. Para uma implementação robusta, o modelo com escopo de funcionalidade não será apenas uma conta de armazenamento e uma única VM (máquina virtual) com uma instalação do SQL Server. Um modelo com escopo de funcionalidade robusto implantará várias VMs com o SQL Server implantado para alta disponibilidade. Para algumas funcionalidades, como o Analysis Services, sua topologia provavelmente também terá o Active Directory implantado com ele.

Duas considerações importantes para este cenário incluem o ciclo de vida de como o SQL Server será usado e o RBAC (Controle de Acesso Baseado em Função) que você deseja aplicar a ele. Especificamente, se o SQL Server será atualizado e excluído com o restante da solução ou se seu ciclo de vida vai variar da solução ou outras partes da solução. Se o ciclo de vida vai variar, convém considerar colocá-lo em outro grupo de recursos.

Outra consideração é como você deseja aplicar o RBAC ao seu modelo de solução com escopo de funcionalidade do SQL Server. Com base em como deseja aplicar o RBAC na sua topologia, você pode optar por grupos de recursos diferentes com base no alinhamento com essas especificações. Você pode aplicar o RBAC no nível do recurso, mas dado o número de recursos para o modelo de solução com escopo de funcionalidade do SQL Server, um grupo de recursos distintos com o RBAC aplicado a ele deve ser uma consideração.

Outra consideração é uma avaliação do modelo de solução com escopo de funcionalidade do SQL Server para identificar se atualmente ele próprio cria determinados recursos versus permite que você "Traga seus próprio recursos". Em um modelo BYOR ("Traga seu próprio recursos"), o modelo de solução com escopo do funcionalidade permitiria que seu modelo reutilizasse recursos anteriormente existentes, com os exemplos típicos sendo uma conta de armazenamento, rede virtual ou um conjunto de disponibilidade. Se não houver uma abordagem BYOR em seu modelo com escopo de funcionalidade, você pode alterá-lo usando a abordagem definida anteriormente nesse documento para modelos de recurso opcional. Nesse caso, seu modelo com escopo de solução de ponta a ponta teria um modelo de recurso compartilhado com esses recursos comuns e o modelo com escopo de funcionalidade seria estendido para dar suporte a esses recursos como opcional. Isso cria um modelo de solução com escopo de funcionalidade melhor, uma vez que agora pode ser usado independentemente ou como parte de uma composição.

Ao avaliar se a conta de armazenamento deve ser transmitida do modelo com escopo de solução de ponta a ponta, o RBAC também deve ser reavaliado. Especificamente, você precisa garantir que o RBAC seja aplicado a esse recurso específico? Em caso afirmativo, se for esperado que isso seja aplicado ao recurso quando ele for transmitido, um nível de confiança está sendo colocado não apenas no bloco de solução, mas qualquer usuário que deseje opcionalmente fornecer isso ao modelo com escopo de funcionalidade quando usado independentemente. Se o RBAC for crítico, você deve considerar se deve torná-lo um modelo opcional dentro do modelo de solução com escopo de funcionalidade ou exigir sua criação com o RBAC obrigatório de dentro do modelo de solução com escopo de funcionalidade.

Se for tomada a decisão de colocá-los em grupos de recursos diferentes, você também pode usar links de recursos para definir as relações entre os recursos, mesmo quando os recursos abrangem grupos de recursos.

## Criando um modelo com escopo de solução de ponta a ponta com vários modelos com escopo de funcionalidade

Isso é basicamente um superconjunto do exemplo anterior. Nesse cenário, uma organização tem vários modelos de solução com escopo de funcionalidade para um conjunto de tecnologias de dados, como Kafka, Apache Hadoop, Apache Spark e Apache Storm, que deseja reunir em um único bloco de solução. A composição resultante usará esses modelos de solução com escopo de funcionalidade, bem como um armazenamento compartilhado e a rede virtual com atribuições de sub-rede específicas.

Além dos modelos com escopo de funcionalidade específicos necessários, serão necessários recursos adicionais para a solução, mesmo que apenas scripts para unir os modelos com escopo de funcionalidade e configurá-los.

Nesse caso, é identificado que há uma rede virtual compartilhada e uma conta de armazenamento compartilhado. Para acomodar isso, você deve adicioná-las a um modelo de recursos compartilhado em seu modelo com escopo de solução ponta a ponta e garantir que uma abordagem "Traga seu próprio recurso" tenha suporte nos modelos com escopo de funcionalidade. Caso não tenha, você pode modificar seus modelos com escopo de funcionalidade para acomodar isso, conforme descrito no exemplo anterior.

Para os recursos adicionais que serão adicionados, você seguirá um superconjunto do padrão usado para criar um modelo com escopo de funcionalidade individual. Nesse caso, você adicionará um modelo de recursos compartilhados, modelos de recursos opcionais, modelos de nó de membro e a configuração de estado desejada (scripts, Chef, Puppet, DSC do Powershell) para os novos recursos. Onde há dependências, você otimizará para usar referências implícitas versus dependsOn, onde possível, para eliminar a possibilidade de dependências isoladas que podem afetar o paralelismo (e velocidade) da sua implantação. Você também considerará o ciclo de vida desses recursos, as considerações do RBAC e as dependências para determinar se eles devem ser colocados em grupos de recursos diferentes.

Ao adicionar recursos compartilhados, como a conta de armazenamento compartilhado, você também deve avaliar se um bloqueio de recurso é necessário para ele, pois isso pode ajudar a evitar exclusões acidentais.

Ao adicionar novos recursos, você também deve examinar se qualquer um dos próprios recursos sendo adicionados ao modelo com escopo de solução ponta a ponta poderia ser isolado como modelos com escopo de funcionalidade. Se sim, isso deve ser fortemente considerado para promover a decomposição adicional que pode fornecer benefícios para a reutilização e o teste.

Ao integrar seus blocos de solução, suas próximas considerações são, como identificado no exemplo anterior, identificar se o ciclo de vida dos modelos de solução com escopo de funcionalidade individuais é diferente do da solução mais abrangente e se algum dos requisitos do RBAC precisaria separá-los em grupos de recursos diferentes.

Finalmente, você precisará considerar se gostaria de poder definir e consultar links entre os recursos. Se sim, empregar links de recursos permitirá que você faça isso em seu modelo de solução com escopo de ponta a ponta, mesmo quando abrangendo vários grupos de recursos.

## Criando um modelo com escopo de solução de ponta a ponta com padrão ativado/desativado parcial

Esse cenário é uma variante do anterior. Nesse caso, o cliente extrai dados de um sistema local em intervalos fixos ao longo do dia. Ele tem um pipeline de dados para processar esses dados de entrada e um armazenamento de dados relacional em que os dados estão sempre disponíveis para consultas. Como a nuvem é um modelo pré-pago, o cliente gostaria que o pipeline de dados estivesse operacional somente durante esses intervalos quando os dados são apresentados para processamento.

Como parte do seu pipeline de dados, ele tem um SQL Server, que recebe os dados processados e os disponibiliza para consulta. O cliente indicou que embora gostaria de ativar e desativar as partes de ingestão e processamento do pipeline em um cronograma fixo, gostaria de ter o SQL Server sempre disponível.

Nesse cenário, há o que parecem ser diferenças explícitas no ciclo de vida e potencialmente algumas considerações adicionais que o cliente não mencionou, mas que devem ser avaliadas.

Conforme descrito, a implantação do SQL Server será mantida ativa enquanto outros recursos serão criados e excluídos. Eles serão implantados juntos inicialmente, mas, em seguida, outros membros do modelo serão destruídos e criados em um ciclo de vida diferente. Eles podem ser isolados em grupos de recursos diferentes ou ser deixados no mesmo grupo de recursos com bloqueio de recurso aplicado aos recursos do SQL Server. Como o SQL Server especificamente é, conforme descrito nos exemplos anteriores, provavelmente representado como um conjunto maior de recursos, seria apropriado separá-lo em seu próprio grupo de recursos.

A outra consideração é que embora o cliente tenha dito que deseja que o restante do pipeline de dados seja ativado e desativado em um cronograma, talvez ele não esteja considerando o comportamento inconsistente dos sistemas de relatório. A entrega agendada de dados de terceiros não é sempre precisa, a conectividade pode estar indisponível durante um período de tempo, os relógios em servidores locais ou em nuvem podem oscilar, as alterações de tempo pode ou não ocorrer como esperado etc. Deve ser avaliado se o mecanismo de ingestão deve ser usado em um padrão ativado/desativado também e, em caso afirmativo, se o ciclo de vida para isso é maior do que os componentes de processamento.

Se você estiver usando um serviço gerenciado, como o Azure Data Factory ou o Hub de Eventos, isso é um problema menor, uma vez que seus modelos operacionais e a abordagem de cobrança associada os tornam imediatamente disponíveis para ingerir os dados e colocá-los no armazenamento. Se estiver usando outra tecnologia, como Kafka, que você implantou em uma máquina virtual, talvez você queira examinar o ciclo de vida para ver como faz isso e as contas de armazenamento associadas necessárias para a ingestão disponíveis. Isso pode resultar nos recursos de ingestão e processamento sendo colocados em grupos de recursos diferentes com base em seus ciclos de vida.

## Dando suporte a ambientes diferentes em uma assinatura

Para efetivamente entregar serviços, muitas organizações têm um conjunto de escala, isolamento de cobrança, isolamento de responsabilidade e necessidades de isolamento geográfico que devem ser atendidas. Ao criar serviços para o Azure, historicamente eles têm usado o particionamento de assinatura em sua abordagem para atender a essas necessidades.

O Gerenciador de Recursos abranda as restrições sobre o número de recursos de um determinado tipo que pode ser implantado em uma assinatura e também introduz grupos de recursos, RBAC e auditoria. A combinação deles pode permitir que as organizações usem os grupos de recursos para o particionamento, permitindo que elas atendam aos seus requisitos e reduzam a quantidade, se houver, do particionamento de assinatura que talvez precisassem fazer.

Esta seção aborda os requisitos vistos para esses tipos de ambientes e fornece orientação sobre como fornecer ambientes que os satisfazem com ARM.

### Considerações sobre o isolamento

Esta seção explora os fatores comerciais comuns do cliente para o isolamento de ambiente, cobrança e geográfico em mais detalhes.

#### Isolamento de ambiente

Os proprietários de serviços têm o desejo de isolar seus diferentes ambientes. Ter cada ambiente isolado concede às equipes a capacidade de ter um controle mais refinado sobre quem pode ter acesso aos ambientes. Embora os ambientes de desenvolvimento possam ser mais abertos em termos de quem pode acessá-los, conforme o escopo do ambiente se move para mais perto da produção, o número de usuários (sejam eles humanos ou contas do sistema usadas para automação) é reduzido para ajudar na conformidade e minimizar o risco geral.

#### Isolamento de cobrança – desenvolvimento vs. execução de um serviço

Para refletir com precisão o COGS (Custo dos Produtos Vendidos ) e as OpEx (Despesas Operacionais), os proprietários de empresas desejam poder separar o custo de pesquisa e criação do serviço vs. da execução dos serviços.

Um superconjunto do isolamento de ambiente mencionado anteriormente, o objetivo seria a consolidação de desenvolvimento e teste para a cobrança individual e/ou agregadas para o primeiro enquanto a produção permaneceria independente para o último.

#### Isolamento de cobrança – adicionando transparência e responsabilidade aos custos de consumo de serviço

O isolamento de cobrança também é usado para obter transparência quanto aos custos relacionados ao consumo de plataforma por equipes específicas e introduzir os níveis apropriados de responsabilidade.

Embora a nuvem seja flexível e permita um modelo pré-pago, isso é menos familiar para alguns desenvolvedores provenientes de um modelo que não é de nuvem onde o hardware é adquirido e de propriedade. No modelo que não é de nuvem, havia limitações físicas em termos de número de "computadores" que poderia ser ativado e havia incentivos limitados para reduzir verticalmente ou desativar recursos quando não estavam em uso. A aquisição desse hardware dedicado, em muitos casos, não foi feita pelos desenvolvedores que o estavam utilizando.

Ao isolar assinaturas e atribuir responsabilidade para essas assinaturas a equipes específicas, os proprietários de serviço consideraram esse tipo de particionamento de assinatura útil ao orientar e reforçar comportamentos desejados.

#### Isolamento orientado pela região geográfica – implantações específicas para e regidas pelas leis de uma região geográfica específica

Em determinados contextos, haverá requisitos que os serviços destinados a uma região geográfica específica precisão considerar como implantar para atender a considerações de conformidade.

Embora um serviço possa ser global por natureza, as implantações que residem dentro ou fornecem serviços para determinadas regiões geográficas podem ser regidas por requisitos de pessoal operacional. Especificamente, fazendo com que apenas pessoas que são cidadãs de um país específico ou um conjunto de países e/ou passam em determinados processos de triagem de histórico operem esses serviços.

O isolamento geográfico também fornece benefícios em termos de tirar proveito de novas funcionalidades e serviços de plataforma. Algumas regiões geográficas, como a China, podem ter apenas um subconjunto dos serviços de plataforma disponíveis e/ou terem a implantação de serviços de plataforma atrasada.

O isolamento geográfico concede às equipes a capacidade de evoluir seus serviços para aproveitar funcionalidades e serviços de plataforma novos ou aprimorados onde estiverem disponíveis.

### Considerações de conformidade

Os serviços podem ser entregues em várias regiões geográficas e para e vários verticais. Esses públicos geralmente têm processos ou dados confidenciais contidos dentro de seus aplicativos e há regulamentos de conformidade associados projetados para protegê-los e auditar o envolvimento com eles.

#### Separação de funções e responsabilidades

A separação de funções e responsabilidades é um requisito fundamental para os serviços internos serem compatíveis com as políticas internas. Muitos serviços comerciais também exigem isso para permanecer em conformidade com diretrizes regulatórias do setor e de governos. Os serviços precisam limitar o acesso aos serviços e seus recursos subjacentes para funções autorizadas em circunstâncias específicas. Muitos serviços criaram o scaffolding para fornecer dois recursos: RBAC e auditoria.

#### Casos de uso do RBAC (Controle de Acesso Baseado em Função)

Em cenários de conformidade, é importante restringir o acesso a determinados recursos.

Por exemplo, ao analisar os dados confidenciais em vários cenários em que a conformidade é relevante (informações de integridade, dados financeiros, registros fiscais etc.) é importante limitar o número de pessoas que podem acessar, exibir ou manipular os dados a apenas aqueles que precisam de acesso para realizar os negócios da organização pai.

O RBAC concede a um indivíduo, sistema ou grupo distinto o acesso a recursos específicos em condições identificadas.

#### Auditoria

Além do acesso restrito fornecido pelo RBAC, as organizações também precisam auditar o acesso a recursos e a interação com recursos.

### Implementando com o Gerenciador de Recursos do Azure

Anteriormente, as organizações teriam usado o particionamento de assinatura para atingir esses objetivos. Embora possível, isso não era o ideal. Como a criação de uma assinatura é efetivamente uma atividade de comércio, a API do Gerenciamento de Serviço não expunha um mecanismo através do qual criar ou excluir novas assinaturas automaticamente e as assinaturas precisavam ser criadas manualmente. O número resultante de assinaturas poderia aumentar significativamente, para serviços muito grandes, como os serviços comerciais da própria Microsoft, e esse número poderia se estender em mais de mil assinaturas. Geralmente, isso resultaria na criação de scaffolding personalizado para criar e gerenciar assinaturas para uma organização.

Com o Gerenciador de Recursos, implantar vários ambientes dentro de uma assinatura é muito mais simples. Ele abranda os limites fixos anteriores em recursos que estavam no modelo anterior, o que reduz bastante a necessidade de particionamento devido a restrições de recursos.

Os ambientes podem ser colocados em grupos de recursos, que podem ter o RBAC específico aplicado a eles, permitindo que você forneça o isolamento de ambiente. Em cenários em que o isolamento geográfico é necessário, isso pode também ser obtido utilizando grupos de recursos. Como os grupos de recursos podem abranger regiões geográficas, o isolamento específico para uma ou mais regiões pode ser obtido.

Você pode aplicar marcas em recursos e grupos de recursos que podem ser usados em exibições resumidas e acumuladas de cobranças para fornecer o isolamento de cobrança. Você pode usar marcas para definir o tipo de ambiente (pesquisa, educação, desenvolvimento, teste, produção), indivíduo ou organização responsável ("RH", "Finanças", "Pedro Ferreira", "Janaína Bueno").

O requisito de auditoria é fornecido como parte do conjunto subjacente de funcionalidades prontas para uso do Gerenciador de Recursos do Azure e podem ser exibidos em um local central.

Os clientes finais teriam contas registradas no Active Directory do Azure que seriam usadas para autenticação e controle de acesso baseado em função para o ambiente e recursos.

#### Otimizando para a densidade

Embora os limites de recurso sejam abrandados no Gerenciador de Recursos do Azure, ainda há limites. Além de criar ambientes em si, você também deve analisar a densidade obtida dos ambientes nas assinaturas. Fornecer um ambiente é fornecer capacidade para um indivíduo ou organização e você deve avaliar quais "tamanho de camiseta" relevantes deseja fornecer. Especificamente, identificar as variantes entre os clientes pequenos, médios, grandes e extra grandes em termos de recursos necessários.

Você pode optar por usar assinaturas diferentes para diferentes tamanhos de camiseta para obter uma densidade maior. Por exemplo, você pode ser capaz de acomodar 1000 ambientes de tamanho de camiseta pequeno, 500 implantações de tamanho médio, 100 implantações grandes e 10 implantações extra grandes em uma determinada assinatura. Como não há nenhum custo cobrado para ter várias assinaturas, você pode desejar isolar os tamanhos diferentes em assinaturas diferentes para fornecer a densidade máxima. Isso pode ser feito enquanto mantém o número de assinaturas relativamente pequeno e fácil de gerenciar.

Uma consideração importante que se deve ter é identificar se você estaria disposto a permitir que um cliente aumente ou altere seu tamanho de camiseta e, em caso afirmativo, como desejaria acomodar isso.

Uma abordagem é permitir que um cliente adquira capacidade adicional em seu grupo de recursos existente. Isso pode ser facilmente acomodado tecnicamente, mas tem implicações na densidade. Em vez de tamanhos nitidamente definidos para todos os clientes, isso introduz um nível de variabilidade que adiciona mais sobrecarga para otimizar a densidade. Se cada ambiente de tamanho pequeno for de um tamanho X, você pode facilmente pré-calcular quantos ambientes de tamanho pequeno colocar em uma assinatura para a densidade ideal. Ao permitir que os clientes personalizem o ambiente, o resultado é um número imprevisível de variantes e as quantidades de ambientes que poderia ser X, X+1, X+2 etc. Com esse nível de variabilidade, você obteria uma densidade menor uma vez que precisaria reservar a capacidade em uma assinatura para acomodar essas variações.

Embora possível, isso é menos que ideal como uma abordagem geral, uma vez que isso obtém uma densidade menor e exige mais sobrecarga para gerenciar. Para ambientes de tamanho maior, essa pode ser uma opção mais viável. Como menos desses ambientes grandes e extra grandes seriam colocados em uma assinatura, você pode optar por colocar menos dessas assinaturas para acomodar o crescimento.

Outra abordagem é a em que o ambiente de tamanho atual do cliente é excluído e é criado um novo ambiente de um tamanho diferente. Embora não seja adequado para alguns cenários, isso funciona bem em ambientes que são usados temporariamente, como ambientes de desenvolvimento e teste.

A próxima abordagem mais fácil aqui é fornecer ao cliente a capacidade de adquirir um ambiente de tamanho maior e, em seguida, gerenciar a migração para o ambiente por conta própria. Por exemplo, um cliente que tinha uma implantação do SQL Server em um ambiente pequeno poderia comprar um ambiente médio e seria individualmente responsável pela transferência de dados e estado personalizado.

Uma abordagem alternativa é fornecer um serviço gerenciado em que essa transição de uma solução única para outro é acomodada. Isso é obviamente mais complicado, mas com base nas cargas de trabalho e nos clientes, isso pode ser algo que sua organização estaria disposta a acomodar.

## Fornecendo ambientes com restrições de política do cliente adicionais

Algumas organizações têm requisitos e políticas adicionais para os ambientes que elas implantam. Especificamente, elas têm políticas que restringem as portas expostas externamente e podem ter políticas que exigem o monitoramento de tráfego de entrada e saída do ambiente. Para considerações de suporte e custo, também pode haver restrições sobre quais recursos um cliente final pode criar, atualizar ou excluir. Para a organização fornecendo o ambiente, elas normalmente também exigirão acesso à assinatura para suporte.

Um superconjunto do cenário anterior, isso exigiria a adição de determinados recursos que teriam restrições adicionais sobre quem poderia e não criar recursos de um determinado tipo.

A capacidade de um usuário de criar, atualizar ou excluir determinados recursos pode ser limitada usando o controle de acesso baseado em função. Os exemplos incluem uma organização que exige uma determinada rede VNET e potencialmente sub-redes que o cliente final não poderia atualizar ou excluir.

Os bloqueios de recursos podem ser implementados para estabelecer que os recursos são somente leitura ou não podem ser excluídos. O RBAC pode ser usado para permitir que usuários ou entidades de serviço realizem determinadas atividades em relação a um recurso ou grupo de recursos.

Se a organização exigir que determinados tráfegos, por exemplo, o tráfego entre as camadas do aplicativo, primeiro passem por um intermediário, como um dispositivo de rede virtual, devem ser usadas rotas definidas pelo usuário.

Um dispositivo virtual é nada mais do que uma VM que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT. Vários terceiros fornecem dispositivos de rede virtual no Azure e as organizações também podem trazer seus próprios dispositivos.

Uma abordagem de dispositivo "traga seu próprio" que as organizações reutilizem o código existente que pode ser usado em seus ambientes locais. Essa VM de dispositivo virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você deve habilitar o Encaminhamento IP na VM.

Assim como acontece com os exemplos anteriores, as restrições de RBAC e ciclo de vida do recurso devem ser revisadas e consideradas como parte da estratégia do seu grupo de recursos.

## Protegendo os recursos de atores internos nocivos

Uma preocupação de uma organização pode ser proteger seus recursos e os modelos que os provisionam de atores nocivos.

Um exemplo disso poderia ser um banco que deseja garantir que um desenvolvedor de software invasor ou membro de sua equipe de TI não faça modificações ou extraia informações importantes que resultem nos dados indo para um ator nocivo para fins criminosos.

Um cenário corporativo típico é ter um pequeno grupo de operadores confiáveis que têm acesso aos segredos críticos nas cargas de trabalho implantadas, juntamente com um grupo mais amplo de desenvolvedores/operadores que podem criar ou atualizar implantações de VM.

O Cofre da Chave do Azure pode ser usado no ARM para orquestrar e armazenar os segredos e certificados da VM.

Uma prática recomendada é manter modelos do ARM separados para criação de cofres (que conterão o material de chave) e a implantação de VMs (com referências de URI às chaves contidas nos cofres).

Os segredos armazenados no Cofre da Chave estão sob controle RBAC total de um operador confiável. Se o operador confiável deixa a empresa ou é transferido para um novo grupo na empresa, ele deixa de ter acesso às chaves que criou no Cofre.

Os modelos do ARM para implantações contêm somente as referências de URI aos segredos, o que significa que os segredos reais não estão nos repositórios de códigos, de configurações ou de códigos-fonte. Isso reduz as oportunidades de realização de phishing de segredos e limitação da capacidade dos atores nocivos fazerem alterações.

Conforme mencionado anteriormente neste documento, não há nenhum Cofre da Chave global. Como os Cofres das Chaves são sempre regionais, os segredos mantêm sempre uma localidade (e a soberania) em relação às VMs.

Um exemplo de implementação dessa abordagem foi fornecido na seção Segredos e certificados encontrada anteriormente nesse documento.

## Habilitando um modelo "traga sua própria assinatura"

Os fornecedores de TI Corporativa, Integradores de Sistema e Serviços de Nuvem podem empregar um modelo "Traga sua própria assinatura" com seus clientes. Especificamente, a organização fornece um serviço a um cliente final e utiliza a assinatura do Azure do cliente de alguma forma.

Há diversas variantes dessa abordagem, cada uma com requisitos ligeiramente diferentes, conforme detalhado abaixo.

### Permitindo o acesso de terceiros para o monitoramento de recursos em uma conta

Uma organização com um aplicativo de monitoramento pode exigir acesso somente leitura à assinatura do cliente para recuperar dados para uso no aplicativo. Isso exigira o acesso somente leitura para um período contínuo. O acesso precisaria estar no controle do cliente, fornecendo a ele a capacidade de encerrar o acesso se a relação com o provedor de serviço de monitoramento fosse desligada.

#### Implementando com o Gerenciador de Recursos do Azure

Os detalhes sobre a implementação disso são fornecidos de forma significativa no "Guia de desenvolvedores para a autenticação com a API do Gerenciador de Recursos do Azure", que pode ser encontrado [aqui](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/). Esse documento fornece instruções passo a passo da implementação, bem como um código de exemplo.

### Habilitando o acesso de terceiros para uma implantação única de software

Em outro exemplo, uma organização pode implantar e configurar uma versão de seu software em uma conta de cliente, exigindo acesso de gravação pelo período de tempo para a implantação.

#### Implementando com o Gerenciador de Recursos do Azure

Isso deve seguir uma abordagem semelhante ao exemplo anterior.

Dependendo das necessidades específicas da instalação, a função específica atribuída à entidade de serviço deve permitir apenas o nível mínimo de acesso necessário para realizar a instalação e, então, ter o acesso revogado imediatamente após a conclusão da instalação.

### Habilitando o acesso de terceiros para usar assinaturas do cliente para o armazenamento de dados

Em outro exemplo, uma organização pode desejar executar o software em seu próprio ambiente, mas usar a conta do cliente para armazenamento. Isso coloca o cliente no controle de seus dados sempre e permite o aproveitamento de outras tecnologias na plataforma, por exemplo, Aprendizado de Máquina do Azure ou HDInsight, a seu próprio critério enquanto não adiciona sobrecarga de custo/cobrança para a TI Corporativa, o Integrador de Sistema ou o CSV fornecendo a funcionalidade. Isso exige acesso contínuo à conta de armazenamento da organização, com o cliente no controle e com acesso a informações de auditoria para os acessos a essas informações.

#### Implementando com o Gerenciador de Recursos do Azure

Isso é implementado usando o mesmo padrão dos outros exemplos. Uma entidade de serviço recebe acesso ao recurso de armazenamento. Como esse cenário exigia que a função tivesse acesso de leitura e gravação à conta de armazenamento, a função Colaborador integrada seria atribuída à entidade de serviço para obter esse nível de acesso.

Como este cenário envolve uma entidade primária e um terceiro com uma conta de armazenamento compartilhada, também haverá o desejo de garantir que a conta de armazenamento não seja excluída acidentalmente. Para esse aspecto do cenário, você aplicaria um bloqueio de recurso à conta de armazenamento.

### Permitindo o gerenciamento de serviço por um terceiro

Em outro exemplo, uma organização desejará implantar, monitorar e gerenciar o software na assinatura dos clientes. Pode haver restrições no cliente em termos das alterações que podem (ou mais explicitamente não podem) ser feitas no ambiente em que o software está implantado.

#### Implementando com o Gerenciador de Recursos do Azure

Isso segue um superconjunto do padrão identificado no início desta seção. Especificamente, uma entidade de serviço usada por um terceiro recebe acesso completo aos recursos no grupo de recursos.

Além disso, como há restrições no cliente, os usuários ou grupos do cliente recebem os direitos apropriados para utilizar o ambiente. Isso pode ser feito por meio de modelos como identificadas anteriormente nesta seção.

Por fim, pode haver um desejo de garantir que determinados recursos não sejam excluídos acidentalmente. Se esse for o caso, os bloqueios de recursos também devem ser considerados para os recursos que exigem esse tipo de proteção.

## Próximas etapas

- Para saber mais sobre a criação de modelos, consulte [Criando modelos](resource-group-authoring-templates.md).
- Para obter recomendações de como lidar com segurança no Gerenciador de Recursos do Azure, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md).
- Para saber mais sobre o estado de compartilhamento dentro e fora dos modelos, consulte [O estado de compartilhamento em modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-state.md)

<!---HONumber=August15_HO8-->