<properties
	pageTitle="Práticas recomendadas para criação de modelos do Gerenciador de Recursos do Azure"
	description="Mostrar os padrões de design para modelos de Gerenciador de Recursos do Azure"
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
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Práticas recomendadas para criação de modelos do Gerenciador de Recursos do Azure

Em nosso trabalho com empresas, SIs (integradores de sistemas), CSVs (fornecedores de serviço de nuvem) e equipes de projeto de OSS (software livre), muitas vezes é necessário implantar rapidamente ambientes, cargas de trabalho ou unidades de escala. É necessário que haja suporte para essas implantações, que elas sigam práticas comprovadas e cumpram as políticas identificadas. Usando uma abordagem flexível com base em modelos do Gerenciador de Recursos do Azure, você pode implantar topologias complexas rápida e consistentemente e adaptar essas implantações facilmente conforme as ofertas de núcleo evoluem, ou para acomodar grades para cenários de exceção ou clientes.

Modelos combinam os benefícios do Gerenciador de Recursos do Azure subjacente com a capacidade de adaptação e legibilidade de JSON (JavaScript Object Notation). Usando modelos, você pode:

- Implantar consistentemente topologias e suas cargas de trabalho.
- Gerencie todos os seus recursos em um aplicativo coletivamente usando grupos de recursos.
- Aplique o RBAC (controle de acesso baseado em função) para conceder acesso apropriado aos usuários, grupos e serviços.
- Use associações de marcação para simplificar tarefas como cobrança de valores acumulados.

Este artigo fornece detalhes sobre cenários de consumo, arquitetura e padrões de implementação identificados durante nossas sessões de design e implementações de modelos do mundo real com os clientes da AzureCAT (Azure Customer Advisory Team). Longe de serem acadêmicas, estas são práticas comprovadas informadas pelo desenvolvimento de modelos para 12 das principais tecnologias de software livre baseado em Linux, incluindo: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, Enterprise DataStax da plataforma Apache Cassandra, Elasticsearch, Jenkins, MongoDB, Nagios, PostgreSQL, Redis e Nagios. A maioria desses modelos foram desenvolvidos com um fornecedor conhecido de uma determinada distribuição e influenciados pelos requisitos dos clientes corporativos e de integradores de sistemas da Microsoft durante projetos recentes.

Este artigo compartilha essas práticas comprovadas para ajudá-lo a projetar modelos do Gerenciador de Recursos do Azure de classe mundial.

Em nosso trabalho com clientes, identificamos várias experiências de consumo de modelos do Gerenciador de Recursos entre empresas, SIs (integradores de sistemas) e CSVs (fornecedores de serviço de nuvem). As seções a seguir fornecem uma visão geral de alto nível dos cenários e padrões comuns para diferentes tipos de clientes.

## Empresas e integradores de sistemas

Em grandes organizações, normalmente observamos dois consumidores de modelos ARM: as equipes de desenvolvimento interno de software e o TI empresarial. Os cenários para o SIs com os quais trabalhamos foram mapeados àqueles das empresas, portanto, as mesmas considerações se aplicam.

### Equipes de desenvolvimento interno de software

Se sua equipe desenvolve software para dar suporte a seus negócios, os modelos fornecem uma maneira fácil de implantar rapidamente as tecnologias para uso em soluções específicas para o negócio. Você também pode usar modelos para criar rapidamente ambientes de treinamento que permitam aos membros da equipe obter as habilidades necessárias.

Você pode usar os modelos “como são”, ou então estendê-los ou integrá-los para atender às suas necessidades. Usando marcação nos modelos, você pode fornecer um resumo de cobrança com várias exibições, como equipe, projeto, indivíduo e educação.

As empresas geralmente desejam que as equipes de desenvolvimento de software criem um modelo de implantação consistente de uma solução, oferecendo simultaneamente restrições para que determinados itens dentro desse ambiente permaneçam fixos e não possam ser substituídos. Por exemplo, um banco pode exigir que um modelo inclua RBAC, para que um programador não possa revisar uma solução bancária para assim enviar dados para uma conta de armazenamento pessoal.

### TI Empresarial

As organizações de TI empresarial normalmente usam modelos para fornecer capacidade de nuvem e recursos hospedados na nuvem.

#### Capacidade de nuvem

Uma maneira comum de grupos de TI empresarial fornecerem capacidade de nuvem para as equipes em sua organização é com "tamanhos de camiseta", que são tamanhos padrão de oferta como pequeno, médio e grande. As ofertas em tamanhos de camiseta podem combinar diferentes tipos de recursos e quantidades, fornecendo um nível de padronização que torna possível o uso dos modelos. Os modelos oferecem capacidade de uma maneira consistente, que impõe políticas corporativas e usa marcação para fornecer estorno a organizações consumidoras.

Por exemplo, talvez seja necessário fornecer ambientes de desenvolvimento, teste ou produção em que as equipes de desenvolvimento de software possam implantar suas soluções. O ambiente tem uma topologia de rede predefinida e elementos que as equipes de desenvolvimento de software não podem alterar, como as regras que regem o acesso para a inspeção de pacotes e de Internet pública. Você também pode ter funções específicas da organização para esses ambientes, com direitos de acesso distintos para o ambiente utilizado.

#### Recursos hospedados na nuvem

Você pode usar modelos para dar suporte a recursos hospedados na nuvem, incluindo pacotes de software individuais ou ofertas compostas feitas a linhas de negócios internas. Um exemplo de uma oferta composta seria análise como serviço - análise, visualização e outras tecnologias - entregue em uma configuração otimizada e conectada, em uma topologia de rede predefinida.

Os recursos hospedados na nuvem são afetados pelas considerações de segurança e função estabelecidas pela oferta de capacidade de nuvem em que eles são criados, conforme descrito acima. Esses recursos são oferecidos como são ou como um serviço gerenciado. Para essa última opção, as funções de acesso restrito são necessárias para habilitar o acesso ao ambiente para fins de gerenciamento.

## Fornecedores de serviço de nuvem

Depois de conversar com vários CSVs, identificamos diversas abordagens que você pode tomar para implantar serviços para seus clientes e os requisitos associados.

### Oferta hospedada em CSV

Se você hospeda sua oferta em sua própria assinatura do Azure, há duas abordagens de hospedagem comuns: uma implantação distinta para cada cliente ou então a implantação de unidades de escala, que são a base de uma infraestrutura compartilhada usada para todos os clientes.

- **Implantações distintas para cada cliente.** Implantações distintas para cada cliente exigem topologias fixas de diferentes configurações conhecidas. Estas podem ter diferentes tamanhos de VM (máquina virtual), números variados de nós e quantidades diferentes de armazenamento associado. A marcação de implantações é usada para cobrança de valores acumulados de cada cliente. O RBAC pode ser habilitado para permitir que clientes acessem os aspectos do seu ambiente de nuvem.
- **Unidades de escala em ambientes multilocatário compartilhados.** Um modelo pode representar uma unidade de escala para ambientes multilocatário. Nesse caso, a mesma infraestrutura é usada para dar suporte a todos os clientes. As implantações representam um grupo de recursos que fornecem um nível de capacidade para a oferta hospedada, como o número de usuários e o número de transações. Essas unidades de escala são aumentadas ou reduzidas conforme a demanda.

### Oferta de CSV introduzida na assinatura do cliente

Você talvez queira implantar seu software em assinaturas pertencentes a clientes finais. Você pode usar modelos para realizar implantações distintas em uma conta de cliente do Azure.

Essas implantações usam RBAC, portanto, você poderá atualizar e gerenciar a implantação na conta do cliente.

### Azure Marketplace

Se você deseja anunciar e vender suas ofertas por meio de um marketplace, como o Azure Marketplace, você pode desenvolver modelos para fornecer tipos distintos de implantações que serão executados em uma conta de cliente do Azure. Essas implantações distintas normalmente podem ser descritas como um tamanho de camiseta (pequeno, médio, grande), tipo de produto/público (community, developer, enterprise) ou tipo de recurso (básico, de alta disponibilidade). Em alguns casos, esses tipos permitirão que você especifique certos atributos de implantação, como tipo de VM ou número de discos.

## Projetos de software livre

Em projetos de software livre, os modelos de Gerenciador de Recursos habilitam uma comunidade a implantar uma solução rapidamente, usando práticas comprovadas. Você pode armazenar modelos em um repositório GitHub, para que a comunidade possa revisá-los ao longo do tempo. Os usuários finais podem então implantar esses modelos em suas próprias assinaturas do Azure.

As seções a seguir identificam as coisas que você precisa considerar antes de projetar sua solução.

## Identificar o que é o exterior e interior de uma máquina virtual

Ao projetar seu modelo, é útil examinar os requisitos em termos de o que está no interior e no exterior das VMs (máquinas virtuais):

- Exterior significa as máquinas virtuais e outros recursos de sua implantação, como a topologia de rede, marcação, faz referência aos certificados/segredos e controle de acesso baseado em função. Todos esses recursos fazem parte do seu modelo.
- Interior significa o software instalado e a configuração do estado geral desejado. Outros mecanismos, como scripts ou extensões de VM, são usados no todo ou em parte. Esses mecanismos podem ser identificados e executados pelo modelo, mas não estão contidos nele.

Exemplos comuns de atividades que você realizaria "no interior da caixa" incluem:

- Instalar ou remover recursos e funções de servidor
- Instalar e configurar o software no nível do cluster ou nó
- Implantar sites em um servidor Web
- Implantar esquemas de banco de dados
- Gerenciar o Registro ou outros tipos de configurações
- Gerenciar arquivos e diretórios
- Iniciar, parar e gerenciar processos e serviços
- Gerenciar contas de usuário e grupos locais
- Instalar e gerenciar pacotes (. msi, .exe, yum, etc.)
- Gerenciar variáveis de ambiente
- Executar scripts nativos (Windows PowerShell, bash, etc.)

### Configuração de estado desejado (DSC)

Pensando sobre o estado interno de suas VMs além da implantação, você desejará certificar-se de que essa implantação não se desvie da configuração que você definiu e marcou no controle do código-fonte. Isso garante que seus desenvolvedores ou equipe de operações não façam manualmente, em um determinado ambiente, alterações ad hoc que não sejam aprovadas, testadas ou registradas no controle do código-fonte. Isso é importante, porque as alterações manuais não estão no controle do código-fonte; elas também não fazem parte da implantação padrão e terão impacto sobre futuras implantações automatizadas do software.

Além de seus funcionários internos, a configuração de estado desejado também é importante segundo uma perspectiva de segurança. Os hackers estão regularmente tentando comprometer e explorar sistemas de software. Quando têm êxito, é comum que instalem arquivos e alterem de outras maneiras o estado de um sistema comprometido. Usando a configuração de estado desejado, você pode identificar os deltas entre o estado desejado e o atual, para então restaurar uma configuração conhecida.

Há extensões de recurso para os mecanismos mais populares para DSC - PowerShell DSC, Chef e Puppet. Cada um deles pode implantar o estado inicial de sua VM e também ser usado para assegurar que o estado desejado seja mantido.

## Escopos de modelo comuns

Em nossa experiência, vimos três escopos principais de modelos de solução surgirem. Esses três escopos – capacidade, funcionalidade e solução de ponta a ponta – são descritos em mais detalhes abaixo.

### Escopo de capacidade

Um escopo de capacidade oferece um conjunto de recursos em uma topologia padrão que é pré-configurada para estar em conformidade com normas e políticas. O exemplo mais comum é implantar um ambiente de desenvolvimento padrão em um cenário de SI (integrador de sistemas) ou TI empresarial.

### Escopo de funcionalidade

Um escopo de funcionalidade concentra-se em como implantar e configurar uma topologia para uma determinada tecnologia. Cenários comuns, incluindo tecnologias como SQL Server, Cassandra, Hadoop, etc.

### Escopo da solução de ponta a ponta

Um escopo de solução de ponta a ponta é direcionado além de uma única funcionalidade e, em vez disso, dedicado a oferecer uma solução de ponta a ponta, composta de várias funcionalidades.

Um escopo de modelo com escopo de solução se manifesta como um conjunto de um ou mais modelos com escopo de funcionalidade com recursos, lógica e estado desejado específicos da solução. Um exemplo de um modelo com escopo de solução é um modelo de solução de pipeline de dados de ponta a ponta, que pode misturar topologia e estado específicos da solução com vários modelos de solução com escopo de funcionalidade, como Kafka, Storm e Hadoop.

## Escolhendo entre configurações de forma livre e configurações conhecidas

Você pode pensar inicialmente que um modelo deve fornecer a máxima flexibilidade aos clientes, mas muitas considerações afetam a escolha entre usar configurações de forma livre ou conhecidas. Esta seção identifica as principais necessidades do cliente e considerações técnicas que deram forma à abordagem compartilhada neste documento.

### Configurações de forma livre

À primeira vista, configurações de forma livre parecem ideais. Elas permitem que você selecione um tipo de VM e forneça um número arbitrário de nós e discos para os nós conectados — e faça isso como parâmetros para um modelo. No entanto, quando você examinar de perto e considerar modelos que implantarão várias máquinas virtuais de tamanhos diferentes, surgem considerações adicionais que tornam essa escolha menos apropriada em vários cenários.

No artigo [Tamanhos de máquinas virtuais e serviços de nuvem do Azure](http://msdn.microsoft.com/library/azure/dn641267.aspx) no site do Azure, são identificados os diferentes tipos de VM e os tamanhos disponíveis, além de cada um dos números de discos duráveis (2, 4, 8, 16 ou 32) que podem ser anexados. Cada disco conectado fornece 500 IOPS e múltiplos desses discos podem ser agrupados (pooling) para se obter um multiplicador desse número de IOPS. Por exemplo, 16 discos podem ser agrupados (pooling) para fornecer 8.000 IOPS. O pooling é realizado com a configuração no sistema operacional, usando espaços de armazenamento do Microsoft Windows ou RAID (redundant array of inexpensive disks) no Linux.

Uma configuração de forma livre permite a seleção de um número de instâncias de VM, um número de diferentes tipos e tamanhos de VM para essas instâncias, um número de discos que pode variar com base no tipo de máquina virtual e um ou mais scripts para configurar o conteúdo da VM.

É comum que uma implantação tenha vários tipos de nós, como nós mestres e de dados; portanto, essa flexibilidade geralmente é fornecida para todos os tipos de nó.

Quando você começar a implantar clusters com qualquer significância, você começa a trabalhar com múltiplos de todos eles. Se você estivesse implantando um cluster Hadoop, por exemplo, com 8 nós mestres e 200 nós de dados e, em seguida, agrupasse em pool 4 discos anexados em cada nó mestre e 16 discos conectados por nó de dados, você teria 208 VMs e 3.232 discos para gerenciar.

Uma conta de armazenamento acelerará as solicitações acima de seu limite identificado de 20.000 transações/s, portanto, você deve examinar o particionamento de conta de armazenamento e usar cálculos para determinar o número apropriado de contas de armazenamento para acomodar essa topologia. Dada a infinidade de combinações às quais a abordagem de forma livre dá suporte, são necessários cálculos dinâmicos para determinar o particionamento apropriado. O idioma do modelo do Gerenciador de Recursos do Azure não oferece funções matemáticas atualmente; portanto, você deve executar esses cálculos em código, gerando um modelo exclusivo embutido em código com os detalhes apropriados.

Em cenários de SI e TI empresarial, alguém deve manter os modelos e dar suporte às topologias implantadas para uma ou mais organizações. Essa sobrecarga adicional - diferentes configurações e modelos para cada cliente - está longe de ser desejável.

Você pode usar esses modelos para implantar ambientes na assinatura do Azure do seu cliente, mas as equipes de TI empresariais e também os CSVs normalmente os implantarão em suas próprias assinaturas, usando uma função de estorno para cobrar seus clientes. Nesses cenários, a meta é implantar capacidade para vários clientes em um pool de assinaturas e manter as implantações densamente populadas nas assinaturas, para minimizar a proliferação de assinatura - ou seja, mais assinaturas para gerenciar. Com tamanhos de implantação realmente dinâmicos, alcançar esse tipo de densidade requer um planejamento cuidadoso e desenvolvimento adicional para o trabalho de scaffolding em nome da organização.

Além disso, você não pode criar assinaturas por meio de uma chamada à API, mas deve fazê-lo manualmente por meio do portal. Na medida em que aumenta o número de assinaturas, qualquer expansão de assinatura resultante requer intervenção humana - ela não pode ser automatizada. Com tanta variabilidade em tamanhos de implantações, você precisaria pré-provisionar um número de assinaturas manualmente para garantir que as assinaturas estivessem disponíveis.

Considerando todos esses fatores, uma configuração de forma verdadeiramente livre é menos atraente do que parece à primeira vista.

### Configurações conhecidas - a abordagem de dimensionamento por tamanho de camiseta

Em de oferecer um modelo que fornece total flexibilidade e inúmeras variações, um padrão comum é, segundo nossa experiência, fornecer a capacidade de selecionar configurações conhecidas - na verdade, tamanhos de camiseta padrão como área restrita, pequeno, médio e grande. Outros exemplos de tamanhos de camiseta são ofertas de produtos, como community edition ou enterprise edition. Em outros casos, podem se tratar de configurações de uma tecnologia específicas para uma determinada carga de trabalho - como mapear/reduzir ou no SQL.

Muitas organizações de TI empresarial, fornecedores de software livre e SIs disponibilizam hoje suas ofertas dessa maneira em ambientes virtualizados (empresas) locais ou como ofertas de SaaS (software como serviço) - CSVs e OSVs.

Essa abordagem fornece boas configurações conhecidas, de tamanhos variados que são pré-configurados para os clientes. Sem configurações conhecidas, os clientes finais devem determinar o tamanho do cluster por conta própria, levar em consideração restrições de recursos de plataforma e fazer cálculos para identificar o particionamento resultante de contas de armazenamento e outros recursos (devido a restrições de recursos e de tamanho do cluster). As configurações conhecidas permitem que os clientes selecionem facilmente o tamanho de camiseta certo - ou seja, uma determinada implantação. É mais fácil dar suporte a um pequeno número de configurações conhecidas; além disso, o uso de um pequeno número delas resulta em uma melhor experiência para o cliente e pode ajudá-lo a oferecer um maior nível de densidade.

Uma abordagem de configuração conhecida voltada para tamanhos de camiseta também pode ter um número variável de nós em um determinado tamanho. Por exemplo, um tamanho de camiseta pequeno pode ter entre 3 e 10 nós. O tamanho de camiseta seria projetado para acomodar até 10 nós e fornecer ao consumidor a capacidade de fazer seleções de forma livre até o tamanho máximo identificado.

Um tamanho de camiseta com base no tipo de carga de trabalho pode ter uma natureza mais voltada à forma livre em termos de número de nós que podem ser implantados, mas terá a configuração do software do nó e o tamanho de nó distintos para a carga de trabalho.

Tamanhos de camiseta baseados em ofertas de produto, como Community ou Enterprise, podem ter diferentes tipos de recurso e número máximo de nós que podem ser implantados, normalmente associados a considerações sobre licenciamento ou a disponibilidade de recursos entre as diferentes ofertas.

Você também pode acomodar clientes com variantes exclusivas, usando os modelos baseados em JSON. Ao lidar com exceções, você pode incorporar o planejamento e considerações adequados para o desenvolvimento, suporte e custeio.

Com base em cenários de consumo de modelo a clientes, requisitos identificados no início deste documento e nossa experiência prática criando vários modelos, identificamos um padrão para decomposição de modelos.

## Modelos de solução no escopo de capacidade e funcionalidade

A decomposição fornece uma abordagem modular para desenvolvimento de modelos, que dá suporte a reutilização, extensibilidade, testes e instrumentação. Esta seção fornece detalhes sobre como uma abordagem hierárquica pode ser aplicada aos modelos com um escopo de Capacidade ou Funcionalidade.

Nessa abordagem, um modelo principal recebe valores de parâmetro de um consumidor de modelo, além de links para vários tipos de modelos e scripts downstream, conforme mostrado abaixo. Os parâmetros, variáveis estáticas e variáveis gerados são usados para fornecer valores de entrada e saída nos modelos vinculados.

![Parâmetros de modelo](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Os parâmetros são passados para um modelo principal e, em seguida, para os modelos vinculados**

Essa seções a seguir se concentram nos tipos de modelos e scripts nos quais um único modelo deve ser decomposto, além de examinar as abordagens para passar informações de estado entre os modelos. Cada modelo e os tipos de script na imagem são descritos juntamente com exemplos. Para obter um exemplo contextual, consulte "Juntando as peças: um exemplo de implementação", mais adiante neste documento.

### Metadados de modelo

Metadados de modelo (o arquivo metadata.json) contém pares chave/valor que descrevem um modelo em JSON, que pode ser lido por seres humanos e sistemas de software.

![Metadados de modelo](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Os metadados do modelo são descritos no arquivo metadata.json**

Agentes de software podem recuperar o arquivo metadata.json e publicar as informações e um link para o modelo em um diretório ou uma página da Web. Os elementos incluem *itemDisplayName*, *description*, *summary*, *githubUsername* e *dateUpdated*.

Um exemplo de arquivo é mostrado abaixo em sua totalidade.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### Modelo principal

O modelo principal (o arquivo azuredeploy.json) é chamado por um usuário final e é o modelo por meio do qual um conjunto de parâmetros definidos pelo usuário são apresentados.

![Modelo principal](./media/best-practices-resource-manager-design-templates/main-template.png)

**O modelo principal recebe parâmetros de um usuário**

A função desse modelo é receber parâmetros de um usuário, usar essas informações para preencher um conjunto de variáveis de objeto complexo e, em seguida, executar o conjunto apropriado de modelos relacionados usando a vinculação de modelos.

Um parâmetro que é fornecido é um tipo de configuração conhecida, também conhecido como o parâmetro de tamanho de camiseta devido a seus valores padronizados, como pequeno, médio ou grande. Na prática, você pode usar esse parâmetro de várias maneiras. Para obter detalhes, consulte "Modelo de recursos de configuração conhecida", posteriormente neste documento.

Alguns recursos são implantados independentemente da configuração conhecida especificada por um parâmetro de usuário. Esses recursos são provisionados usando um único modelo de recurso compartilhado e são compartilhados por outros modelos, de modo que o modelo de recurso compartilhado é executado primeiro.

Alguns recursos são implantados opcionalmente, independentemente da configuração conhecida especificada.

### Modelo de recursos compartilhados

Esse modelo fornece recursos que são comuns a todas as configurações conhecidas. Ele contém a rede virtual, conjuntos de disponibilidade e outros recursos que são necessários, independentemente do modelo de configuração que é implantado.

![Recursos de modelo](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Modelo de recursos compartilhados**

Os nomes de recursos, como o nome de rede virtual, baseiam-se no modelo principal. Você pode especificá-los como uma variável dentro desse modelo ou recebê-los como um parâmetro do usuário, conforme exigido pela sua organização.

### Modelo de recursos opcionais

O modelo de recursos opcionais contém recursos que são implantados por meio de programação com base no valor de um parâmetro ou variável.

![Recursos opcionais](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Modelo de recursos opcionais**

Por exemplo, você pode usar um modelo de recursos opcionais para configurar um jumpbox que permita acesso indireto a um ambiente implantado pela Internet pública. Você usaria um parâmetro ou variável para identificar se o jumpbox deve ou não ser habilitado e a função *concat* para criar o nome de destino para o modelo, como *jumpbox_enabled.json*. A vinculação de modelos usaria a variável resultante para instalar o jumpbox.

É possível vincular o modelo de recursos opcionais de vários locais:

-	Quando aplicável a todas as implantações, crie, do modelo de recursos compartilhados, um link orientado por parâmetros.
-	Quando aplicável para configurações conhecidas seletas - por exemplo, para instalar somente em grandes implantações - crie, do modelo de configuração, um link orientado por parâmetros ou orientado por variáveis.

Se um determinado recurso for opcional, ele pode não estar sendo orientado pelo consumidor de modelos, mas sim pelo provedor de modelos. Por exemplo, talvez seja necessário atender a um requisito de determinado produto ou complemento de produto (comum para CSVs) ou impor políticas (comum para SIs e grupos de TI empresarial). Nesses casos, você pode usar uma variável para identificar se o recurso deve ou não ser implantado.

### Modelo de recursos de configuração conhecida

No modelo principal, um parâmetro pode ser exposto para permitir que o consumidor de modelo especifique uma configuração conhecida desejada para implantar. Em muitos casos, essa configuração usa uma abordagem de tamanho de camiseta com um conjunto de tamanhos de configuração fixa, como área restrita, pequeno, médio e grande.

![Recursos de configuração conhecida](./media/best-practices-resource-manager-design-templates/known-config.png)

**Modelo de recursos de configuração conhecida**

A abordagem de tamanho de camiseta é usada frequentemente, mas os parâmetros podem representar qualquer conjunto de configurações conhecidas. Por exemplo, você pode especificar um conjunto de ambientes para um aplicativo empresarial como Development, Test e Product. Ou você pode usá-lo para um serviço de nuvem representar unidades de escala, versões do produto ou configurações de produto diferentes, como Community, Developer ou Enterprise.

Assim como acontece com o modelo de recurso compartilhado, variáveis são passadas para o modelo de configurações conhecidas de um dos entes citados a seguir:

-	Um usuário final - ou seja, os parâmetros enviados para o modelo principal.
-	Uma organização - ou seja, as variáveis no modelo principal que representam requisitos ou políticas internos.

### Modelo de recursos de membro

Em uma configuração conhecida, um ou mais tipos de nó de membro costumam ser incluídos. Por exemplo, com o Hadoop, você teria nós mestres e nós de dados. Se você estiver instalando o MongoDB, você terá nós de dados e um arbitrador. Se você estiver implantando DataStax, você terá nós de dados, bem como uma máquina virtual com OpsCenter instalado.

![Recursos de membros](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Modelo de recursos de membro**

Cada tipo de nó pode ter diferentes tamanhos de VMs, números de discos conectados, scripts para instalar e configurar os nós, configurações de porta para a(s) VM(s), número de instâncias e outros detalhes. Portanto, cada tipo de nó obtém seu próprio modelo de recurso de membro, que contém os detalhes para implantação e configuração de uma infraestrutura, bem como execução de scripts para implantar e configurar o software na VM.

Para VMs, geralmente dois tipos de scripts são utilizados: os amplamente reutilizáveis e os personalizados.

### Scripts amplamente reutilizáveis

Scripts amplamente reutilizáveis podem ser usados em vários tipos de modelos. Um dos melhores exemplos desses scripts amplamente reutilizáveis define o RAID no Linux para efetuar o pooling de discos e obter um maior número de IOPS. Independentemente do software que está sendo instalado na VM, esse script oferece a reutilização de práticas comprovadas para cenários comuns.

![Scripts reutilizáveis](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Modelos de recursos de membro podem chamar scripts amplamente reutilizáveis**

### Scripts personalizados

Modelos normalmente chamam um ou mais scripts que instalam e configuram software em máquinas virtuais. Um padrão comum é visto com grandes topologias em que várias instâncias de um ou mais tipos de membro são implantadas. Um script de instalação é iniciado para cada VM que pode ser executada em paralelo, seguido por um script de instalação que é chamado depois que todas as máquinas virtuais (ou todas as máquinas virtuais de um determinado tipo de membro) são implantadas.

![Scripts personalizados](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Modelos de recursos de membro podem chamar scripts para uma finalidade específica, como a configuração da VM**

## Exemplo de modelo de solução no escopo de funcionalidade - Redis

Para mostrar como uma implementação pode funcionar, vamos examinar um exemplo prático de como criar um modelo que facilitará a implantação e configuração do Redis em tamanhos de camiseta padrão.

Para a implantação, haverá um conjunto de recursos compartilhados (rede virtual, conta de armazenamento, conjuntos de disponibilidade) e um recurso opcional (jumpbox). Há várias configurações conhecidas representadas como tamanhos de camiseta (pequeno, médio, grande), mas cada uma com um único tipo de nó. Há também dois scripts específicos para a finalidade (instalação, configuração).

### Criando os arquivos de modelo

Você cria um modelo principal chamado azuredeploy.json.

Você cria um modelo de recursos compartilhados chamado shared-resources.json

Você cria um modelo de recurso opcional para habilitar a implantação de um jumpbox denominado jumpbox_enabled.json

O Redis usará apenas um tipo de nó, portanto, você criará um modelo único de recurso de membro chamado node-resources.json.

Com o Redis, você desejará instalar cada nó individualmente e, em seguida, depois que todos os nós forem instalados, você desejará configurar o cluster. Você tem scripts para acomodar ambos os arquivos redis-cluster-install.sh e redis-cluster-setup.sh.

### Vinculando os modelos

Usando vinculação de modelos, o modelo principal vincula-se ao modelo de recursos compartilhados, o que estabelece a rede virtual.

A lógica é adicionada dentro do modelo principal para permitir que os consumidores do modelo especifiquem se um jumpbox deve ou não ser implantado. Um valor *enabled* para o parâmetro *EnableJumpbox* indica que o cliente deseja implantar um jumpbox. Quando esse valor é fornecido, o modelo concatena *_enabled* como um sufixo para um nome de modelo base para a funcionalidade de jumpbox.

O modelo principal aplica o valor de parâmetro *large* como um sufixo para um nome de modelo base para tamanhos de camiseta, então usa esse valor em uma vinculação de modelo para *technology_on_os_large.json*.

A topologia seria semelhante a essa ilustração.

![Modelo de Redis](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Estrutura de modelo para um modelo de Redis**

### Configurando o estado

Para os nós no cluster, há duas etapas para configurar o estado, representadas por Scripts de Finalidade Específica. “redis-cluster-install.sh” executará uma instalação do Redis e “redis-cluster-setup.sh” configurará o cluster.

### Suporte a implantações de tamanho diferente

Nas variáveis, o modelo de tamanho de camiseta especifica o número de nós de cada tipo a implantar para o tamanho especificado (*large*). Em seguida, implanta esse número de instâncias de VMs usando loops de recursos, fornecendo nomes exclusivos para recursos ao acrescentar um nome de nó com um número de sequência numérica de *copyIndex()*. Ele faz isso tanto para VMs de zona temperada quanto de zona quente, conforme definido no modelo de nome de camiseta

## Modelos com escopo de solução de ponta a ponta e decomposição

Um modelo de solução com um escopo de solução de ponta a ponta se concentra em fornecer uma solução desse tipo. Normalmente, isso será uma composição de vários modelos com escopo de funcionalidade com recursos adicionais, lógica e estado.

Conforme realçado na imagem abaixo, o mesmo modelo usado para modelos com escopo de funcionalidade estende-se a modelos com escopo de solução de ponta a ponta.

Um modelo de recursos compartilhados e modelos de recursos opcionais têm a mesma função que as observadas nas abordagens de modelo com escopo de capacidade e de funcionalidade, mas têm o escopo para solução de ponta a ponta.

Já que os modelos com escopo de solução de ponta a ponta também podem normalmente ter tamanhos de roupa, o modelo de recursos de configuração conhecida reflete o que é necessário para uma determinada configuração conhecida da solução.

O modelo de recursos de configuração conhecida se vinculará a um ou mais modelos de solução com escopo de funcionalidade que são relevantes para a solução de ponta a ponta, bem como os modelos de recursos de membro que são necessários para a solução de ponta a ponta.

Como o tamanho de camiseta da solução pode ser diferente do modelo individual com escopo de funcionalidade, variáveis dentro do modelo de recursos de configuração conhecida são usadas para fornecer os valores apropriados para modelos de solução com escopo de funcionalidade downstream para implantar o tamanho de camiseta apropriado.

![Ponta a ponta](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**O modelo usado para modelos de solução com escopo de capacidade ou funcionalidade podem ser prontamente estendidos para escopos de modelo de solução de ponta a ponta**

## Preparando modelos para o Marketplace

A abordagem anterior acomoda prontamente cenários em que as empresas, SIs e CSVs desejam implantar os modelos ou então habilitar seus clientes a implantar esses modelos por conta própria.

Outro cenário desejado é implantar um modelo por meio do marketplace. Essa abordagem de decomposição funcionará para o marketplace também, com algumas pequenas alterações.

Conforme mencionado anteriormente, os modelos podem ser usados para oferecer tipos distintos de implantação para venda no marketplace. Tipos distintos de implantação podem ser tamanhos de camiseta (pequeno, médio, grande), tipo de produto/público (community, developer, enterprise) ou tipo de recurso (básico, de alta disponibilidade).

Conforme mostrado abaixo, a solução de ponta a ponta existente ou modelos com escopo de funcionalidade podem ser prontamente utilizados para listar as diferentes configurações conhecidas no marketplace.

Primeiro, os parâmetros para o modelo principal são modificados para remover o parâmetro de entrada chamado tshirtSize.

Embora os tipos de implantação distintos sejam mapeados para o modelo de recursos de configuração conhecida, eles também precisam das configurações e recursos comuns encontrados no modelo de recursos compartilhados e, potencialmente, daqueles nos modelos de recursos opcionais.

Se desejar publicar seu modelo para o marketplace, você simplesmente estabelece cópias distintas do seu modelo principal, que substitui o parâmetro de entrada tshirtSize anteriormente disponível para uma variável inserida no modelo.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Adaptando um modelo com escopo de solução para o marketplace**

## Próximas etapas

- Para ver exemplos contextuais de como implementar os princípios de design apresentados neste tópico, consulte [Exemplos contextuais de práticas recomendadas para a implementação de modelos](best-practices-resource-manager-examples.md).
- Para obter recomendações de como lidar com segurança no Gerenciador de Recursos do Azure, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md)
- Para saber mais sobre como compartilhar o estado dentro e fora dos modelos, consulte [Compartilhando estado em modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-state.md).

<!---HONumber=July15_HO4-->