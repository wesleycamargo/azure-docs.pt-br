<properties
	pageTitle="Armazenamento de Blobs do Azure: camadas estáticas e dinâmicas | Microsoft Azure"
	description="Uma visão geral das camadas de Armazenamento de Blobs do Azure para o armazenamento de dados econômico de dados de objetos com base nos padrões de acesso."
	services="storage"
	documentationCenter=""
	authors="sribhat-msft"
	manager="vamshik"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/24/2016"
	ms.author="sribhat"/>


# Armazenamento de Blobs do Azure: camadas estáticas e dinâmicas

## Visão geral

O armazenamento de Blobs do Azure é uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Com grande capacidade de armazenamento e escalabilidade, o armazenamento de Blobs armazena de forma simples e econômica petabytes de dados e bilhões de objetos por cliente. Para saber mais, confira [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md).

Hoje, os dados armazenados na nuvem estão aumentando em ritmo exponencial, e um aspecto importante do gerenciamento do custo para suas necessidades crescentes de armazenamento é organizar os dados em camadas com base em atributos como frequência de acesso, período de retenção etc. Com base nos padrões de uso e nos comentários de clientes, vimos que os dados armazenados na nuvem podem ser muito diferentes em termos de como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são acessados e modificados ativamente em todo o seu tempo de vida, alguns são acessados com muita frequência no início de seu tempo de vida, com o acesso sendo drasticamente reduzido com o passar do tempo, enquanto outros inicialmente permanecem ociosos na nuvem e são raramente ou nunca acessados após serem armazenados. Em geral, classificamos **dados dinâmicos** como dados que são acessados com muita frequência e precisam ser altamente disponíveis e duráveis. Por outro lado, **dados estáticos** são raramente acessados e têm vida longa. Os dados estáticos podem tolerar uma disponibilidade um pouco menor, mas ainda exigem alta durabilidade e tempo de acesso e taxa de transferência semelhantes aos de dados ativos. Para os dados estáticos, um SLA com disponibilidade um pouco menor e maiores custos de acesso são compensações aceitáveis, em troca de custos de armazenamento muito menores.

Cada um dos cenários de acesso a dados descritos acima se beneficia de uma camada diferenciada de armazenamento que é otimizada para um padrão de acesso específico. O armazenamento de Blobs do Azure agora aborda a necessidade de camadas de armazenamento diferenciadas para dados com padrões de acesso e modelos de preços diferentes.

## Contas de armazenamento de Blobs

**Contas de armazenamento de Blobs** são contas de armazenamento especializadas para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Azure. Com contas de armazenamento de Blobs, você pode escolher entre as camadas de acesso Estáticas e Dinâmicas para armazenar os dados estáticos acessados com menos frequência a um custo de armazenamento menor e armazenar os dados dinâmicos acessados com mais frequência a um custo de acesso menor. As contas de armazenamento de Blobs são semelhantes a contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo.

As contas de armazenamento de Blobs expõem o atributo de **Camada de Acesso**, que permite especificar a camada de acesso como **Dinâmica** ou **Estática**, dependendo dos dados armazenados na conta. Se houver uma alteração no padrão de uso dos dados, você também poderá alternar entre as camadas de acesso a qualquer momento.

> [AZURE.NOTE] A alteração da camada de acesso pode resultar em cobranças adicionais. Confira a seção [Preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) a seguir para saber mais.

Os cenários de uso de exemplo para a camada de acesso dinâmica incluem:

- Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
- Dados preparados para processamento e eventual migração para a camada estática.

Os cenários de uso de exemplo para a camada de acesso estática incluem:

- Conjuntos de dados de backup, arquivamento e recuperação de desastre.
- Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
- Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)
- Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*Por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
- Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagens de câmeras de segurança, raios X/ressonâncias magnéticas antigos para organizações de serviços de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

Confira [Sobre contas de armazenamento do Azure](storage-create-storage-account.md) para saber mais sobre contas de armazenamento.

Para aplicativos que exigem apenas armazenamento de blobs de bloco ou acréscimo, é recomendável usar contas de armazenamento de Blobs para aproveitar o modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que talvez isso não seja possível em determinadas circunstâncias em que o uso de contas de armazenamento de finalidade geral seria a melhor solução, como:

- Você precisa usar tabelas, filas ou arquivos e deseja que seus blobs sejam armazenados na mesma conta de armazenamento. Observe que armazená-los na mesma conta não oferece vantagens técnicas, exceto ter as mesmas chaves compartilhadas.
- Você ainda precisa usar o modelo de implantação Clássico. As contas de armazenamento de Blobs só estão disponíveis por meio do modelo de implantação do Azure Resource Manager.
- Você precisa usar blobs de página. Geralmente, é recomendável usar blobs de bloco, a menos que você tenha uma necessidade específica para blobs de página.
- Você usa uma versão da [API REST dos Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) que é anterior a 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4.x e não pode atualizar o aplicativo.

> [AZURE.NOTE] Atualmente, as contas de armazenamento de Blobs têm suporte na maioria das regiões do Azure, e outras regiões serão adicionadas. Você pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services).

## Comparação entre as camadas de acesso

A seguinte tabela realça a comparação entre as duas camadas de acesso:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250"> <col width="250"> <col width="250">
<tbody>
<tr>
	<td><strong><center></center></strong></td>
	<td><strong><center>Camada de acesso dinâmica</center></strong></td>
	<td><strong><center>Camada de acesso estática</center></strong>&lt;/td
</tr>
<tr>
    <td><strong><center>Disponibilidade</center></strong></td>
	<td><center>99,9%</center></td>
	<td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidade<br>(Leituras de RA-GRS)</center></strong></td>
	<td><center>99,99%</center></td>
	<td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Encargos de uso</center></strong></td>
	<td><center>Custos de armazenamento maiores<br>Custos de acesso e de transações menores</center></td>
	<td><center>Custos de armazenamento menores<br>Custos de acesso e de transações maiores</center></td>
</tr>
<tr>
	<td><strong><center>Tamanho mínimo de objeto<center></strong></td>
	<td colspan="2"><center>N/D</center></td>
</tr>
<tr>
	<td><strong><center>Duração mínima de armazenamento<center></strong></td>
	<td colspan="2"><center>N/D</center></td>
</tr>
<tr>
	<td><strong><center>Latência<br>(Tempo até o primeiro byte)<center></strong></td>
	<td colspan="2"><center>milissegundos</center></td>
</tr>
<tr>
	<td><strong><center>Desempenho e escalabilidade<center></strong></td>
	<td colspan="2"><center>Igual ao das contas de armazenamento de finalidade geral</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] As contas de armazenamento de Blobs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de finalidade geral. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para saber mais.

## Preços e cobrança

As contas de armazenamento de Blobs usam um novo modelo de preços para o armazenamento de blobs com base na camada de acesso. Ao se usar uma conta de armazenamento de Blobs, as seguintes considerações de cobranças são aplicáveis:

- **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia de acordo com a camada de acesso. O custo por gigabyte é menor para a camada de acesso estática do que para a camada de acesso dinâmica.
- **Custos de acesso a dados**: para dados na camada de acesso estática, será cobrado um encargo de acesso a dados por gigabyte para leituras e gravações.
- **Custos de transação**: há um encargo por transação para ambas as camadas. No entanto, o custo por transação para a camada de acesso estática é maior do que para a camada de acesso dinâmica.
- **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.
- **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.
- **Alteração da camada de acesso**: a alteração da camada de acesso de estática para dinâmica acarretará um encargo igual à leitura de todos os dados existentes na conta de armazenamento para todas as transições. Por outro lado, a alteração da camada de acesso de dinâmica para estática será gratuita.

> [AZURE.NOTE] Para permitir que os usuários experimentem as novas camadas de armazenamento e validem a funcionalidade após o lançamento, o encargo para alterar a camada de acesso de estática para dinâmica não será cobrado até 30 de junho de 2016. A partir de 1º de julho de 2016, o encargo será aplicado a todas as transições de estática para dinâmica. Para saber mais sobre o modelo de preços para contas de armazenamento de Blobs, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## Início rápido

Nesta seção, demonstraremos os seguintes cenários usando o Portal do Azure:

- Como criar uma conta de armazenamento de Blobs.
- Como gerenciar uma conta de armazenamento de Blobs.

### Usando o Portal do Azure

#### Criar uma conta de armazenamento de Blobs usando o Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** -> **Dados + Armazenamento** -> **Conta de armazenamento**.

3. Insira um nome para a conta de armazenamento.

4. Selecione **Resource Manager** como o modelo de implantação.

5. Selecione **Armazenamento de Blobs** como o tipo de conta de armazenamento.

6. Selecione a camada de acesso: **Dinâmica** ou **Estática**. O padrão é **Dinâmica**.

7. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS** ou **RA-GRS**. O padrão é **RA-GRS**. Para obter mais detalhes sobre as opções de replicação do Armazenamento do Azure, confira [Replicação do Armazenamento do Azure](storage-redundancy.md).

8. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

9. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para saber mais sobre os grupos de recursos, confira [Uso do Portal do Azure para gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

10. Selecione a região geográfica para sua conta de armazenamento.

11. Clique em **Criar** para criar a conta de armazenamento.

#### Alterar a camada de acesso em uma conta de armazenamento de Blobs usando o Portal do Azure

1.	Entre no [Portal do Azure](https://portal.azure.com) e navegue até a conta de armazenamento.

2.	Clique em **Todas as configurações** e em **Configuração** para exibir e/ou alterar a configuração da conta.

3.	Especifique a camada de acesso desejada: **Dinâmica** ou **Estática**.

## Migração para contas de armazenamento de Blobs

O objetivo desta seção é ajudar os usuários a fazer uma transição sem problemas para contas de armazenamento de Blobs. Uma conta de armazenamento de Blobs é especializada no armazenamento somente de blobs. As contas de armazenamento de finalidade geral existentes que também permitem armazenar tabelas, filas e arquivos não podem ser convertidas em contas de armazenamento de Blobs. Isso significa que, para usar os recursos oferecidos por uma conta de armazenamento de Blobs, você precisará criar novas contas de armazenamento de Blobs com a camada de acesso apropriada e migrar os dados existentes para as contas recém-criadas.

### Planejamento da migração de dados existentes

Se você está movendo os dados para uma conta de armazenamento de Blobs, provavelmente convém aproveitar a camada de acesso estática para economizar custos de armazenamento para dados usados com menos frequência. A primeira etapa no planejamento da migração de dados em uma conta de armazenamento de Blobs na camada de acesso estática é avaliar seu padrão de uso existente para determinar se você se beneficiará da migração para uma conta de armazenamento de Blobs. Em geral, você precisa saber:

- O padrão de consumo de armazenamento: quanto dados são armazenados e como isso é alterado mensalmente?
- Os padrões de acesso de armazenamento: quantos dados são lidos e gravados na conta (incluindo novos dados)? Quantas e quais transações são usadas para o acesso aos dados?

Para monitorar as contas de armazenamento existentes e coletar esses dados, confira [Habilitação de métricas do Armazenamento do Azure e exibição de dados de métricas](storage-enable-and-view-metrics.md). Agora, com esses dados, você pode usar a [Calculadora de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

### Migração de dados existentes

Você pode usar os métodos a seguir para migrar os dados existentes em contas de armazenamento de Blobs de uma solução de armazenamento local, de um provedor de armazenamento de nuvem de terceiros ou de suas contas de armazenamento de finalidade geral existentes no Azure:

#### AzCopy

O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. Você pode usar o AzCopy para copiar dados para sua conta de armazenamento de Blobs de suas contas de armazenamento de finalidade geral existentes ou carregar dados do sistema de armazenamento local para sua conta de armazenamento de Blobs.

Para saber mais, confira [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

#### Biblioteca de movimentação de dados

A biblioteca de movimentação de dados do Armazenamento do Azure para .NET baseia-se na estrutura de movimentação de dados de núcleo que habilita o AzCopy. A biblioteca foi projetada para operações de transferência de dados de alto desempenho, confiáveis e fáceis, de forma semelhante ao AzCopy. Isso permite que você aproveite todos os benefícios dos recursos fornecidos pelo AzCopy em seu aplicativo de forma nativa, sem a necessidade de lidar com a execução e o monitoramento de instâncias externas do AzCopy.

Para saber mais, confira [Biblioteca de movimentação de dados do Armazenamento do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

#### API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar os dados para uma conta de armazenamento de Blobs usando uma das bibliotecas de cliente do Azure ou a API REST dos serviços de armazenamento do Azure. O Armazenamento do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas, como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Para saber mais, confira [Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md).

## Perguntas frequentes

1.	**As contas de armazenamento existentes ainda estão disponíveis?**

    Sim, as contas de armazenamento existentes ainda estão disponíveis, e seus preços ou funcionalidade não foram alterados. Elas não têm a capacidade de escolher uma camada de acesso e não terão recursos de camadas no futuro.

2.	**Por que e quando devo começar a usar contas de armazenamento de Blobs?**

    As contas de armazenamento de Blobs são especializadas em armazenamento de blobs e nos permitem introduzir novos recursos centrados em blobs. Futuramente, as contas de armazenamento de Blobs serão a maneira recomendada para armazenar blobs, à medida que recursos futuros, como o armazenamento hierárquico e em camadas, forem introduzidos com base nesse tipo de conta. No entanto, você pode decidir quando deseja migrar com base nas necessidades de negócios.

3.	**Posso converter minha conta de armazenamento existente em uma conta de armazenamento de Blobs?**

    Não. Uma conta de armazenamento de Blobs é um tipo diferente de conta de armazenamento. Você precisará criar uma nova e migrar os dados, conforme explicado acima.

4.	**Posso armazenar objetos em ambas as camadas de acesso na mesma conta?**

    Não no momento. O atributo de camada de acesso é definido no nível de conta e se aplica a todos os objetos na conta.

5.	**Posso alterar o nível de acesso em minha conta de armazenamento de Blobs?**

    Sim. Você poderá alterar a camada de acesso na conta de armazenamento. A alteração da camada de acesso no nível de conta se aplicará a todos os objetos armazenados na conta. A alteração da camada de acesso de dinâmica para estática não incorrerá em encargos, mas a alteração de estática para dinâmica incorrerá em um custo por GB para a leitura de todos os dados na conta.

6.	**Com que frequência posso alterar a camada de acesso em minha conta de armazenamento de Blobs?**

    Embora não imponhamos uma limitação à frequência com que a camada de acesso pode ser alterada, lembre-se de que a alteração da camada de acesso de estática para dinâmica incorrerá em encargos significativos. Não recomendamos alterar a camada de acesso com frequência.

7.	**Precisarei alterar meus aplicativos existentes para usar contas de armazenamento de Blobs?**

    As contas de armazenamento de Blobs têm API 100% consistente com as contas de armazenamento de finalidade geral, contanto que você use a última versão (2014-02-14) da [API REST de Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx). Se estiver usando uma versão mais antiga do protocolo, você precisará atualizar o aplicativo para usar a nova versão, para que funcione perfeitamente com os dois tipos de contas de armazenamento. Em geral, é sempre recomendável usar a última versão, independentemente do tipo de conta de armazenamento que você usa.

8.	**Haverá uma alteração na experiência do usuário?**

    Uma conta de armazenamento de Blobs armazena somente blobs, mas, em outros aspectos, é muito semelhante a uma conta de armazenamento de finalidade geral e herda todos os principais recursos do Armazenamento do Azure, incluindo alta durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Além dos recursos e restrições específicos de contas de armazenamento de Blobs e suas camadas de acesso, explicados acima, todo o resto permanece o mesmo.

<!---HONumber=AcomDC_0427_2016-->