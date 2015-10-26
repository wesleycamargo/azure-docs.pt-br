<properties
   pageTitle="Pré-requisitos técnicos para criar um modelo de solução para o Marketplace | Microsoft Azure"
   description="Compreender os requisitos para a criação de um modelo de solução para implantar e vender no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte" />

# Pré-requisitos técnicos para criar um Modelo de Solução para o Azure Marketplace
Leia o processo completamente antes de começar e compreender onde e por que cada etapa é executada. O quanto for possível, você deve preparar informações da sua empresa e outros dados, baixar as ferramentas necessárias e criar componentes técnicos antes de iniciar o processo de criação de oferta. Esses itens devem estar claros com a leitura deste artigo.

## Definição de topologia e avaliação de plataforma
Neste ponto, talvez você já esteja familiarizado com o artefato de computação do Azure e esteja se perguntando exatamente o que pode criar. Imagens de VM, extensões, modelos ARM?

Antes de tentar mapear seu aplicativo para artefatos do Azure, será útil entender as diferentes topologias que fazem sentido para sua solução e que, provavelmente, você também recomendaria a um cliente que considere a implantação de seu aplicativo no local. A maioria das empresas geralmente passa por alguma variação dos quatro seguintes estágios: Avaliação, Prova de Conceito, Pré-Produção e Produção. Normalmente, você trabalha com ele nesses diferentes estágios, avaliando seus requisitos e recomendando o layout e a escala exatos necessários para seus cenários. Embora os clientes sejam diferentes, em geral, esperamos que haja topologias e tamanhos comuns que você recomenda com base no porte da implantação do cliente. Portanto, antes mesmo de começar a desenvolver soluções, considere os diferentes layouts que fazem sentido para o aplicativo e o que poderia atender a boa parte das necessidades do cliente sem exigir grandes personalizações. Novamente, sabemos que sempre haverá clientes que precisam de algo muito exato, mas estamos buscando os casos comuns.

Para cada tipo de implantação (Avaliação, Prova de Conceito, Pré-Produção e Produção), considere o layout e a topologia exatos necessários para o aplicativo. Quais são as diferentes camadas? Quais recursos são necessários para cada camada, e de que tamanho? Quais são os blocos de construção necessários para criar esses recursos? Uma implantação de produção ou até mesmo um ambiente de desenvolvimento/teste podem variar significativamente em termos de tamanho, dependendo do cliente e de suas necessidades. Decida como seria uma topologia pequena, média, grande e extragrande para sua carga de trabalho e considere os requisitos de disponibilidade, escalabilidade e desempenho necessários para ser bem-sucedido.

Após refletir sobre os diferentes layouts de topologia e os tamanhos correspondentes (talvez você tenha concluído a maior parte disso em suas experiências com implantações locais com clientes), é hora de começar a recorrer ao que o Azure tem a oferecer.

Avalie os requisitos de topologia em relação ao que o Gerenciador de Recursos do Azure e a Computação no Gerenciador de Recursos do Azure oferecem. Embora a maioria dos clientes comece com implantações menores, **é altamente recomendável que você avalie os requisitos de desempenho, dimensionamento e capacidade de sua topologia grande na Computação no Gerenciador de Recursos do Azure, mesmo que você não esteja integrando inicialmente uma topologia grande.** Isso é principalmente para garantir que, à medida que os clientes começarem a adotar as soluções menores, eles tenham uma quantidade significativa de espaço para crescer com êxito para implantações de maior escala. Quaisquer limitações de escala, desempenho ou capacidade devem ser mencionadas a seus parceiros da Microsoft para garantir que continuemos a melhorar as áreas necessárias para você, nosso parceiro do Marketplace e, em seguida, por seus clientes.

> [AZURE.TIP]**Recomendado:** avalie e crie um modelo para corresponder ao maior tamanho de implantação esperado de seu aplicativo para oferecer oportunidades de crescimento aos clientes finais.

## Blocos de construção de Computação do Azure
Vamos começar identificando quais artefatos de computação estão disponíveis para você e como pensar sobre seu aplicativo levando em conta esses artefatos.

### 1\. Usando máquinas virtuais existentes
Os blocos de construção para qualquer máquina virtual são Imagens de VM. Imagens de VM são o perfil de armazenamento completo de uma máquina virtual, contendo um VHD do sistema operacional e zero ou mais discos de dados. Você pode criar uma Imagem de VM para empacotar o aplicativo de software com os bits do sistema operacional, além de quaisquer dados adicionais necessários. Por exemplo, hoje, no Azure Marketplace, há algumas Imagens de VM para SQL Server, que empacotam o sistema operacional Windows com o aplicativo SQL Server. Embora este documento não se aprofunde nos detalhes de criação e integração de Imagens de VM no Azure Marketplace, é importante entender o que é uma Imagem de VM, já que, provavelmente, ela será o bloco de construção para sua solução de aplicativo. Consulte as etapas em [Criando uma Imagem de VM para o Marketplace](marketplace-publishing-vm-image-creation.md).

### 2\. Extensão de VM
Às vezes, uma imagem não é o que você procura, pois não deseja possuir e administrar o sistema operacional subjacente como um provedor de aplicativos. No Azure Marketplace, atualmente, há algumas empresas de sistema operacional que publicam os sistemas operacionais Windows, Linux e Unix regularmente. Você pode encontrar imagens para Windows Server, Ubuntu, com base no CentOS, CoreOS, Oracle Linux etc. Em vez de publicar sua própria Imagem de VM, empacotando o sistema operacional e o aplicativo, você pode considerar a opção de escrever um Manipulador de Extensões de VM ou aproveitar um já existente. Uma extensão de VM é um mecanismo de software para simplificar a configuração e o gerenciamento da VM, que aproveita o agente convidado (um processo seguro e leve) em uma VM do Azure. Por exemplo, a Extensão de Script Personalizado utiliza um script personalizado, como o nome sugere, e o executa na VM. Isso permite qualquer tipo de configuração que um script genérico permitiria.

Em um cenário de imagem tradicional, você deve instalar todos os bits na imagem desde o início e fazer as configurações necessárias antes ou, possivelmente, depois da inicialização da VM (com uma tarefa de inicialização). Essa imagem pode se tornar muito personalizada e não necessariamente seria fácil de reutilizar. Em alguns casos, isso faz mais sentido. Em outros, principalmente com aplicativos leve ou configurações mais complicadas que precisam ser feitas depois que a VM é provisionada, o modelo de extensão pode ajudar. Às vezes, os clientes implantam uma combinação de extensões e imagens de VM para ter ambas as soluções pré-instaladas, mas também habilitam a configuração complexa pós-inicialização.

Imagens e extensões são muito úteis para configurar uma única VM, como você bem sabe. No entanto, uma VM não é suficiente para oferecer aos clientes alta disponibilidade na nuvem e ser dimensionada para atender às necessidades do tráfego crescente. Portanto, para qualquer carga de trabalho de produção, é altamente recomendável planejar uma solução de alta disponibilidade com várias VMs usando conjuntos de disponibilidade e balanceamento de carga.

>[AZURE.TIP]**Recomendado:** todas as cargas de trabalho de produção no Marketplace devem ser implantadas usando várias Máquinas Virtuais que servem o tráfego em um Conjunto de Disponibilidade (com três domínios de falha) e com um balanceador de carga.

Para dar suporte a essas numerosas topologias de implantação às quais seu aplicativo pode dar suporte e que pode recomendar, o Azure Marketplace oferece o Gerenciador de Recursos do Azure (ARM, de forma abreviada) e modelos do ARM. O Gerenciador de Recursos do Azure implanta e gerencia o ciclo de vida de uma coleção de recursos por meio de linguagem de modelo declarativa com base em modelos. Esse modelo é simplesmente um arquivo JSON parametrizado que expressa o conjunto de recursos e sua relação, a serem usados para implantações. Cada recurso é colocado em um grupo de recursos, que é simplesmente um contêiner para recursos. O Gerenciador de Recursos também oferece um conjunto de recursos adicionais muito valiosos aos clientes finais, incluindo auditoria de operação centralizada, marcação de recursos para a cobrança ao cliente, Controle de Acesso com Base em Recursos para segurança granular e suporte a três domínios de falha de computação para oferecer a maior disponibilidade. Além disso, todas as suas operações são idempotentes.

> [AZURE.IMPORTANT]**Obrigatório:** considerando os benefícios para os clientes finais, todo o conteúdo do Marketplace deve ser implantado usando modelos do Gerenciador de Recursos do Azure.

Com os modelos do ARM e do ARM, você pode expressar implantações mais complexas de recursos do Azure, como Máquinas Virtuais, Contas de Armazenamento e Redes Virtuais, que se baseiam no conteúdo do Marketplace, como Imagens de VM e Extensões de VM.

## Desenvolvimento de blocos de construção
Depois que você determinar as topologias de aplicativo e aquelas que você desenvolverá e integrará ao Marketplace em seu primeiro estágio, é hora de identificar e desenvolver os blocos de construção de VM. Conforme discutido anteriormente, você precisará decidir se deseja empacotar o conteúdo da VM como sua própria Imagem de VM publicada ou utilizar uma Imagem de VM já existente no Azure Marketplace, configurando-a com um manipulador de extensões publicado ou com seu próprio manipulador. Não há uma única solução correta; depende muito de quanto controle você deseja ter em relação ao sistema operacional subjacente e dos requisitos sobre como você instala, configura e gerencia o aplicativo. Você pode encontrar muitas informações sobre imagens e extensões no MSDN e nos blogs do Azure. Uma lista de artigos potencialmente úteis pode ser encontrada abaixo na seção **Consulte também**.

Observe que, para qualquer manipulador de imagens ou extensões usado, primeiro ele deve ser publicado por meio do Marketplace.

## Desenvolvendo modelos do ARM
Se você é iniciante no Gerenciador de Recursos do Azure e nos Modelos do ARM, pode valer a pena examinar parte da documentação sobre o Gerenciador de Recursos do Azure e a Computação no Gerenciador de Recursos do Azure. As diretrizes e os exemplos fornecidos abaixo pressupõem um nível básico de compreensão da linguagem de Modelos do ARM e de como usar a Computação com ARM. Esta seção aborda principalmente as práticas recomendadas e os requisitos para ajudá-lo a desenvolver bons modelos, especificamente para o Azure Marketplace. Eles levam em conta os requisitos que permitem uma experiência unificada, fácil de usar e relevante para os clientes do Portal de Ibiza e programaticamente.

Sua solução provavelmente será expressa com um conjunto de modelos. Esses modelos são partes lógicas de sua topologia. Há muitos motivos para dividir a expressão da topologia em vários arquivos, como facilidade de desenvolvimento e capacidade de gerenciamento, capacidade de reutilização de determinados subconjuntos, testes modulares e expansão de soluções de modelo. Depois de trabalhar com vários parceiros, uma divisão proposta pela equipe do Azure é dividir a topologia em modelos para recursos comuns (como VNets, contas de armazenamento etc.), chamados de modelo de Recurso Compartilhado, zero ou mais modelos para recursos opcionais, chamados de modelo de Recurso Opcional, e um ou mais modelos para os recursos específicos da topologia, denominados modelos de Recurso de Configuração Conhecido.

Uma solução completa de modelo no Marketplace consistirá nos seguintes itens:

1. **Um modelo principal (mainTemplate.json)** – esse é o ponto de entrada para a implantação geral expressa no arquivo de modelo JSON do ARM. É o primeiro arquivo que é avaliado para a solução de modelo e aquele que é usado para reunir (vincular) todos os outros modelos. Esse arquivo também deve conter toda a entrada desejada (parâmetros) do usuário final.
2. **Zero ou mais modelos vinculados** – o restante dos modelos vinculados do modelo principal para expressar a topologia de solução completa.
3. **Zero ou mais arquivos de script** – quaisquer arquivos de script relevantes que são esperados como entrada para extensões a serem usadas para configurar uma máquina virtual.
4. **Um arquivo de definição para criar a IU (uiDefinition.json createUiDefinition.json)** – esse é o arquivo que mapeia os parâmetros no modelo do ARM para elementos na experiência do usuário. Esse arquivo permite que os parceiros tenham controle da interface do usuário renderizada para os clientes finais no Ibiza. Todas as entradas que o usuário final deve fornecer serão expressas nesse arquivo. Cada entrada permite controlar todo o aspecto da interface do usuário associado a cada entrada. Por exemplo, widget da interface do usuário, padrões, regras de validação, mensagens de erro etc. Ao começar a desenvolver cada um desses arquivos para expressar sua topologia completa, considere os seguintes critérios para garantir o melhor modelo para sua oferta do Marketplace.

## Recomendações e requisitos adicionais

### Conjunto mínimo padronizado de entrada do usuário
Os parâmetros permitem que você expresse valores que devem ser fornecidos pelo usuário no momento da implantação no modelo. A entrada do usuário pode variar de aplicativo para aplicativo e até mesmo de camada para camada da topologia. A lista de parâmetros precisa fornecer um bom equilíbrio entre solicitar menos perguntas necessárias e fornecer ao usuário os recursos certos para controlar sua implantação. Embora não haja requisitos quanto à lista completa de entrada do usuário, queremos garantir que todas as soluções de modelo do Marketplace tenham um conjunto de entrada comum que muitos clientes desejam controlar. Verifique se você definiu parâmetros para os seguintes valores:

1. **Local** – a região do Azure em que os recursos são implantados
2.	**Nome de Rede Virtual** – para implantações que criam uma nova Rede Virtual, o nome a ser usado para criar esse recurso. Para implantações que usam uma Rede Virtual existente, o nome da VNet na qual implantar.
3.	**Conta de Armazenamento** – para implantações que criam uma nova conta de armazenamento, o nome da conta de armazenamento a ser criada. Para implantações que usam uma Rede Virtual existente, o nome da conta de armazenamento a ser usada.
4.	**Nome DNS** – o nome de domínio da máquina virtual ou do balanceador de carga ao qual um usuário final pode se conectar.
5.	**Nome de usuário** – o nome de usuário para a(s) máquina(s) virtual(is) e, potencialmente, o(s) aplicativo(s). Mais de um nome de usuário pode ser solicitado ao usuário final, mas pelo menos um deve ser solicitado.
6.	**Senha** – a senha para a(s) máquina(s) virtual(is) e, potencialmente, o(s) aplicativo(s). Mais de uma senha pode ser solicitada ao usuário final para VMs ou aplicativos diferentes, mas pelo menos uma deve ser solicitada.
7.	**Chave Pública SSH (apenas para VMs Linux)** – chave SSH para a(s) máquina(s) virtual(is). Mais de uma chave pode ser solicitada ao usuário final para VMs diferentes, mas pelo menos uma deve ser solicitada.

## Próximas etapas
Agora que examinou os pré-requisitos e concluiu as tarefas necessárias, você pode prosseguir com a criação de sua oferta de Modelo de Solução, conforme detalhado no [Guia para criar um Modelo de Solução](marketplace-publishing-solution-template-creation.md)

## Consulte também
- [Introdução: como publicar uma oferta para o Azure Marketplace](marketplace-publishing-getting-started.md)
- [Práticas recomendadas para criar um modelo de solução](marketplace-publishing-solution-template-best-practices.md)

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->