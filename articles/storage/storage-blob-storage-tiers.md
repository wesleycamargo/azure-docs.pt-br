<properties
    pageTitle="Armazenamento estático do Azure para Blobs | Microsoft Azure"
    description="As camadas de armazenamento para armazenamento de Blobs oferecem armazenamento econômico de dados de objetos com base em padrões de acesso. O armazenamento estático do Azure é otimizado para dados acessados com menos frequência."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Armazenamento de Blobs do Azure: camadas de armazenamento dinâmica e estática

## Visão geral

Agora, o armazenamento do Azure oferece duas camadas de armazenamento para o Armazenamento de Blobs (armazenamento de objetos), para que você possa armazenar seus dados de uma maneira mais econômica dependendo de como você os utiliza. A **camada de armazenamento dinâmica** do Azure é otimizada para armazenar dados acessados com frequência. A **camada de armazenamento estática** do Azure é otimizada para armazenar dados acessados com menos frequência e de longa duração. Os dados na camada de armazenamento estático podem tolerar uma disponibilidade um pouco menor, mas ainda exigem alta durabilidade e tempo de acesso e taxa de transferência semelhantes aos de dados ativos. Para os dados estáticos, um SLA com disponibilidade um pouco menor e custos mais altos de acesso são compensações aceitáveis, em troca de custos de armazenamento muito menores.

Hoje, os dados armazenados na nuvem estão aumentando em um ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado. Os dados armazenados na nuvem podem ser muito diferentes com relação ao modo como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com muita frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente, ou nunca, são acessados após serem armazenados.

Cada um dos cenários de acesso a dados descritos acima se beneficia de uma camada diferenciada de armazenamento, otimizada para um padrão de acesso específico. Agora, com a introdução das camadas de acesso de armazenamento ativo e estático, o Armazenamento de Blobs do Azure atende a essa necessidade por camadas de armazenamento diferenciado com modelos de preços separados.

## Contas de armazenamento de Blobs

**Contas de armazenamento de Blobs** são contas de armazenamento especializadas para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Azure. Com contas de armazenamento de Blobs, você pode escolher entre as camadas de acesso de armazenamento estático e dinâmico para armazenar os dados estáticos acessados com menos frequência a um custo de armazenamento menor e armazenar os dados dinâmicos acessados com mais frequência a um custo de acesso menor. As contas de armazenamento de Blobs são semelhantes a contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo.

> [AZURE.NOTE] As contas de armazenamento de blobs oferecem suporte apenas aos blobs de bloco e aos blobs de acréscimo, e não aos blobs de página.

As contas de armazenamento de Blobs expõem o atributo de **Camada de Acesso**, que permite especificar a camada de armazenamento como **Dinâmica** ou **Estática**, dependendo dos dados armazenados na conta. Se houver uma alteração no padrão de uso dos dados, você também poderá alternar entre as camadas de acesso a qualquer momento.

> [AZURE.NOTE] A alteração da camada de acesso pode resultar em cobranças adicionais. Confira a seção [Preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) a seguir para obter mais detalhes.

Os cenários de uso de exemplo para a camada de armazenamento dinâmico incluem:

- Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
- Dados preparados para processamento e eventual migração para a camada de armazenamento estático.

Os cenários de uso de exemplo para a camada de acesso de armazenamento estático incluem:

- Conjuntos de dados de backup, arquivamento e recuperação de desastre.
- Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
- Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)
- Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*Por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
- Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagens de câmeras de segurança, raios X/ressonâncias magnéticas antigos para organizações de serviços de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

Confira [Sobre contas de armazenamento do Azure](storage-create-storage-account.md) para saber mais sobre contas de armazenamento.

Para aplicativos que exigem apenas armazenamento de blobs de bloco ou acréscimo, é recomendável usar contas de armazenamento de Blobs para aproveitar o modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que talvez isso não seja possível em determinadas circunstâncias em que o uso de contas de armazenamento de finalidade geral seria a melhor solução, como:

- Você precisa usar tabelas, filas ou arquivos e deseja que seus blobs sejam armazenados na mesma conta de armazenamento. Observe que armazená-los na mesma conta não oferece vantagens técnicas, exceto ter as mesmas chaves compartilhadas.
- Você ainda precisa usar o modelo de implantação Clássico. As contas de armazenamento de Blobs só estão disponíveis por meio do modelo de implantação do Azure Resource Manager.
- Você precisa usar blobs de página. As contas de armazenamento de blobs não dão suporte a blobs de página. Geralmente, é recomendável usar blobs de bloco, a menos que você tenha uma necessidade específica para blobs de página.
- Você usa uma versão da [API REST dos Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) que é anterior a 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4.x e não pode atualizar o aplicativo.

> [AZURE.NOTE] Atualmente, as contas de armazenamento de Blobs têm suporte na maioria das regiões do Azure, e outras regiões serão adicionadas. Você pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services).

## Comparação entre as camadas de acesso

A seguinte tabela realça a comparação entre as duas camadas de acesso:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250"> <col width="250"> <col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Camada de acesso de armazenamento dinâmico</center></strong></td>
    <td><strong><center>Camada de acesso de armazenamento estático</center></strong>
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
    <td><strong><center>Escalabilidade e metas de desempenho<center></strong></td>
    <td colspan="2"><center>Igual ao das contas de armazenamento de finalidade geral</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] As contas de armazenamento de Blobs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de finalidade geral. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para saber mais.

## Preços e cobrança

As contas de armazenamento de Blobs usam um novo modelo de preços para o armazenamento de blobs com base na camada de acesso. Ao se usar uma conta de armazenamento de Blobs, as seguintes considerações de cobranças são aplicáveis:

- **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia de acordo com a camada de acesso. O custo por gigabyte é menor para a camada de acesso de armazenamento estático do que para a camada de acesso de armazenamento dinâmico.
- **Custos de acesso a dados**: para dados na camada de acesso de armazenamento estático, será cobrado um encargo de acesso a dados por gigabyte para leituras e gravações.
- **Custos de transação**: há um encargo por transação para ambas as camadas. No entanto, o custo por transação para a camada de acesso de armazenamento estático é maior do que para a camada de acesso de armazenamento dinâmico.
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

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até a conta de armazenamento.

2. Clique em **Todas as configurações** e em **Configuração** para exibir e/ou alterar a configuração da conta.

3. Especifique a camada de acesso desejada: **Dinâmica** ou **Estática**.

    > [AZURE.NOTE] A alteração da camada de acesso pode resultar em cobranças adicionais. Confira a seção [Preços e cobrança](storage-blob-storage-tiers.md#pricing-and-billing) a seguir para obter mais detalhes.

## Migração para contas de armazenamento de Blobs

O objetivo desta seção é ajudar os usuários a fazer uma transição sem problemas para contas de armazenamento de Blobs. Uma conta de Armazenamento de Blobs é especializada no armazenamento exclusivo de blobs de bloco e de acréscimo. As contas de armazenamento de finalidade geral existentes, que permitem o armazenamento de tabelas, filas, arquivos e discos não podem ser convertidas em contas de Armazenamento de Blobs. Para usar as camadas de armazenamento, será necessário criar novas contas de Armazenamento de Blobs e migrar os dados existentes para as contas recém-criadas.

### Planejamento da migração de dados existentes

Se você está movendo os dados para uma conta de armazenamento de Blobs, provavelmente convém aproveitar a camada de acesso de armazenamento estático para economizar custos de armazenamento para dados usados com menos frequência. A primeira etapa no planejamento da migração de dados em uma conta de armazenamento de Blobs na camada de acesso de armazenamento estático é avaliar seu padrão de uso existente para determinar se você se beneficiará da migração para uma conta de armazenamento de Blobs. Em geral, você precisa saber:

- O padrão de consumo de armazenamento: quanto dados são armazenados e como isso é alterado mensalmente?
- Os padrões de acesso de armazenamento: quantos dados são lidos e gravados na conta (incluindo novos dados)? Quantas e quais transações são usadas para o acesso aos dados?

Para monitorar as contas de armazenamento existentes e coletar esses dados, confira [Habilitação de métricas do Armazenamento do Azure e exibição de dados de métricas](storage-enable-and-view-metrics.md). Agora, com esses dados, você pode usar a [Calculadora de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

### Migração de dados existentes

Você pode usar os métodos a seguir para migrar os dados existentes em contas de armazenamento de Blobs de um dispositivo de armazenamento local, de um provedor de armazenamento de nuvem de terceiros ou de suas contas de armazenamento de finalidade geral existentes no Azure:

#### AzCopy

O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. Você pode usar o AzCopy para copiar dados para sua conta de armazenamento de Blobs de suas contas de armazenamento de finalidade geral existentes ou carregar dados do dispositivo de armazenamento local para sua conta de armazenamento de Blobs.

Para saber mais, confira [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

#### Biblioteca de movimentação de dados

A biblioteca de movimentação de dados do Armazenamento do Azure para .NET baseia-se na estrutura de movimentação de dados de núcleo que habilita o AzCopy. A biblioteca foi projetada para operações de transferência de dados de alto desempenho, confiáveis e fáceis, de forma semelhante ao AzCopy. Isso permite que você aproveite todos os benefícios dos recursos fornecidos pelo AzCopy em seu aplicativo de forma nativa, sem a necessidade de lidar com a execução e o monitoramento de instâncias externas do AzCopy.

Para saber mais, confira [Biblioteca de movimentação de dados do Armazenamento do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

#### API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar os dados para uma conta de armazenamento de Blobs usando uma das bibliotecas de cliente do Azure ou a API REST dos serviços de armazenamento do Azure. O Armazenamento do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas, como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Para saber mais, confira [Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] Blobs criptografados usando metadados relacionados à criptografia de armazenamento no lado do cliente armazenados com o blob. É absolutamente essencial que qualquer mecanismo de cópia assegure que os metadados de blob, e especialmente os metadados relacionados à criptografia, sejam preservados. Se você copiar os blobs sem esses metadados, o conteúdo do blob não poderá ser recuperado novamente. Para obter mais detalhes sobre os metadados relacionados à criptografia, confira [Criptografia do lado de cliente do Armazenamento do Azure](storage-client-side-encryption.md).

## Perguntas frequentes

1. **As contas de armazenamento existentes ainda estão disponíveis?**

    Sim, as contas de armazenamento existentes ainda estão disponíveis, e seus preços ou funcionalidade não foram alterados. Elas não têm a capacidade de escolher uma camada de acesso e não terão recursos de camadas no futuro.

2. **Por que e quando devo começar a usar contas de armazenamento de Blobs?**

    As contas de armazenamento de Blobs são especializadas em armazenamento de blobs e nos permitem introduzir novos recursos centrados em blobs. Futuramente, as contas de armazenamento de Blobs serão a maneira recomendada para armazenar blobs, à medida que recursos futuros, como o armazenamento hierárquico e em camadas, forem introduzidos com base nesse tipo de conta. No entanto, você pode decidir quando deseja migrar com base nas necessidades de negócios.

3. **Posso converter minha conta de armazenamento existente em uma conta de armazenamento de Blobs?**

    Não. Uma conta de armazenamento de Blobs é um tipo diferente de conta de armazenamento. Você precisará criar uma nova e migrar os dados, conforme explicado acima.

4. **Posso armazenar objetos em ambas as camadas de acesso na mesma conta?**

    O atributo de camada de acesso é definido no nível de conta e se aplica a todos os objetos na conta. Não é possível definir a camada de acesso no nível do objeto.

5. **Posso alterar o nível de acesso em minha conta de armazenamento de Blobs?**

    Sim. Você poderá alterar a camada de acesso na conta de armazenamento. A alteração da camada de acesso no nível de conta se aplicará a todos os objetos armazenados na conta. A alteração da camada de acesso de dinâmica para estática não incorrerá em encargos, mas a alteração de estática para dinâmica incorrerá em um custo por GB para a leitura de todos os dados na conta.

6. **Com que frequência posso alterar a camada de acesso em minha conta de armazenamento de Blobs?**

    Embora não imponhamos uma limitação à frequência com que a camada de acesso pode ser alterada, lembre-se de que a alteração da camada de acesso de estática para dinâmica incorrerá em encargos significativos. Não recomendamos alterar a camada de acesso com frequência.

7. **Os blobs na camada de acesso de armazenamento estático se comportarão de forma diferente daqueles na camada de acesso de armazenamento dinâmico?**

    Os Blobs na camada de acesso de armazenamento dinâmico têm a mesmo latência que os blobs nas contas de armazenamento de uso geral. Os Blobs na camada de acesso de armazenamento estático têm uma latência parecida (em milissegundos) que os blobs nas contas de armazenamento de uso geral.

    Os Blobs na camada de acesso de armazenamento estático terão um SLA (acordo de nível de serviço) com disponibilidade ligeiramente menor do que os blobs armazenados na camada de acesso de armazenamento dinâmico. Para obter mais detalhes, consulte [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Posso armazenar os blobs de página e discos de máquinas virtuais em contas de armazenamento de Blob?**

    As contas de armazenamento de blobs oferecem suporte apenas aos blobs de bloco e aos blobs de acréscimo, e não aos blobs de página. Os discos de máquina virtual do Azure têm o suporte de blobs de página e, como resultado, as contas de armazenamento de blobs não podem ser usadas para armazenar os discos da máquina virtual. No entanto, é possível armazenar backups dos discos de máquina virtual como blobs de bloco em uma conta de Armazenamento de Blobs.

9. **Precisarei alterar meus aplicativos existentes para usar contas de armazenamento de Blobs?**

    Contas de Armazenamento de Blobs são 100% consistentes com API com contas de armazenamento de uso geral para blobs de bloco e de acréscimo. Contanto que seu aplicativo esteja usando blobs de bloco ou blobs de acréscimo, e você esteja usando a versão 2014-02-14 da [API REST dos Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx), ou mais recente, o aplicativo deverá funcionar. Se estiver usando uma versão mais antiga do protocolo, você precisará atualizar o aplicativo para usar a nova versão, para que funcione perfeitamente com os dois tipos de contas de armazenamento. Em geral, é sempre recomendável usar a última versão, independentemente do tipo de conta de armazenamento que você usa.

10. **Haverá uma alteração na experiência do usuário?**

    As contas de Armazenamento de Blobs são bastante parecidas com as contas de armazenamento de finalidade geral para armazenamento de blobs de bloco e de acréscimo, e oferecem suporte a todos os principais recursos do Armazenamento do Azure, incluindo alta durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Além dos recursos e restrições específicos de contas de armazenamento de Blobs e suas camadas de acesso, explicados acima, todo o resto permanece o mesmo.

## Próximas etapas

### Avaliar as contas de armazenamento de Blobs

[Verificar a disponibilidade das contas de armazenamento de blobs por região](https://azure.microsoft.com/regions/#services)

[Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](storage-enable-and-view-metrics.md)

[Verificar preços do armazenamento de blobs por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)

### Começar a usar as contas de Armazenamento de Blob

[Introdução ao Armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md)

[Movendo dados para dentro e para fora do Armazenamento do Azure](storage-moving-data.md)

[Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0511_2016-->