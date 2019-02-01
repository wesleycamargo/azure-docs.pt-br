---
title: Guia de segurança do Armazenamento do Azure Data Lake Storage Gen2 | Microsoft Docs
description: Detalha os vários métodos para proteger o Armazenamento do Azure, incluindo, entre outros, RBAC e Criptografia do Serviço de Armazenamento
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/04/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: ca1012e0f1b0b7a9e0d8bd88d94876c20d22dec5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473775"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Guia de segurança do Azure Data Lake Storage Gen2

A versão prévia do Azure Data Lake Storage Gen2, é um conjunto de recursos criados em contas do Armazenamento do Azure. Dessa forma, as referências neste artigo são destinadas a uma conta de Armazenamento do Azure com namespace hierárquico habilitado (recursos do Data Lake Storage Gen2).

- Todos os dados gravados no Armazenamento do Azure são criptografados automaticamente usando a [Criptografia do Serviço de Armazenamento (SSE)](storage-service-encryption.md). Para obter mais informações, consulte [Anunciando Criptografia Padrão para Blobs do Azure, Arquivos, Tabelas e Armazenamento de Filas](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- O Azure AD (Azure Active Directory) e o RBAC (controle de acesso baseado em função) têm suporte para o Armazenamento do Azure em relação a operações de gerenciamento de recursos e operações de dados, da seguinte maneira:
    - Você pode atribuir funções RBAC, no escopo da conta de armazenamento, a entidades de segurança e usar o Azure AD para autorizar operações de gerenciamento de recursos, por exemplo, gerenciamento de chaves.
    - Na versão prévia, a integração do Azure AD tem suporte para operações de dados no Armazenamento do Azure. É possível atribuir funções RBAC com escopo para uma assinatura, grupo de recursos, conta de armazenamento ou um sistema de arquivos individual para uma entidade de segurança ou uma identidade gerenciada para recursos do Azure. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md).
- O acesso delegado aos objetos de dados no Armazenamento do Microsoft Azure pode ser concedido usando [Assinaturas de Acesso Compartilhado](../storage-dotnet-shared-access-signature-part-1.md).

Este artigo apresenta uma visão geral de cada um desses recursos de segurança que podem ser usados com o Armazenamento do Azure. São fornecidos links para artigos que darão detalhes de cada recurso, para que você possa investigar mais cada tópico mais aprofundadamente.

Estes são os tópicos abordados neste artigo:

* [Segurança do plano de gerenciamento](#management-plane-security) – proteção da conta de armazenamento

  O plano de gerenciamento consiste em recursos usados para gerenciar a conta de armazenamento. Esta seção trata do Modelo de implantação do Azure Resource Manager e de como usar o RBAC (Controle de Acesso Baseado em Função) para controlar o acesso às contas de armazenamento. Também trata de como gerenciar as chaves da conta de armazenamento e como regenerá-las.
* [Segurança do plano de dados](#data-plane-security) – proteção do acesso aos seus dados

  Nesta seção, analisaremos o acesso aos objetos de dados reais na conta de Armazenamento, ou seja, arquivos e diretórios, usando assinaturas de acesso compartilhado e Políticas de Acesso Armazenadas. Vamos abordar a SAS de nível de serviço e de nível de conta. Também veremos como limitar o acesso a um endereço IP específico (ou a um intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma Assinatura de Acesso Compartilhado sem esperar que ela expire.
* [Criptografia em trânsito](#encryption-in-transit)

Esta seção aborda como proteger dados ao transferi-los para dentro ou para fora de uma conta de armazenamento com Data Lake Storage Gen2 habilitado. Abordaremos o uso recomendado de HTTPS.

## <a name="management-plane-security"></a>Segurança do plano de gerenciamento

O plano de gerenciamento consiste em operações que afetam a conta de armazenamento em si. Por exemplo, você pode criar ou excluir uma conta de armazenamento, obter uma lista de contas de armazenamento em uma assinatura, recuperar as chaves da conta de armazenamento ou regenerar as chaves da conta de armazenamento.

Este guia concentra-se no modelo de implantação do Resource Manager, que é o meio de criar contas de armazenamento com os recursos do Data Lake Storage Gen2. Com as contas de armazenamento do Gerenciador de Recursos, em vez de fornecer acesso à toda a assinatura, você pode controlar o acesso em um nível mais limitado no plano de gerenciamento usando o RBAC (Controle de Acesso Baseado em Função).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função)

Vamos falar sobre o que é o RBAC e como você pode usá-lo. Cada assinatura do Azure tem um Azure Active Directory. Os usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure que usam o modelo de implantação Resource Manager. Esse tipo de segurança é chamado de Controle de Acesso Baseado em função (RBAC). Para gerenciar esse acesso, use **Controle de acesso (IAM)** no portal do Azure.

Com o modelo Resource Manager, você coloca a conta de armazenamento em um grupo de recursos e controla o acesso ao plano de gerenciamento dessa conta de armazenamento específica usando o Azure Active Directory. Por exemplo, é possível permitir que usuários específicos acessem as chaves da conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar suas chaves.

#### <a name="granting-access"></a>Concessão de acesso

O acesso é concedido atribuindo-se a função RBAC apropriada a usuários, grupos e aplicativos no escopo correto. Para conceder acesso à assinatura inteira, você atribui uma função no nível de assinatura. É possível conceder acesso a todos os recursos em um grupo de recursos concedendo permissões ao próprio grupo de recursos. Você também pode atribuir funções específicas a recursos específicos, como contas de armazenamento.

Veja os principais pontos que você precisa saber sobre como usar o RBAC para acessar as operações de gerenciamento de uma conta de armazenamento do Azure:

* Para um usuário que tenha permissão para acessar os objetos de dados na conta de armazenamento, você poderá conceder a permissão para leitura de chaves da conta de armazenamento e esse usuário poderá usar essas chaves para acessar os arquivos e diretórios.
* As funções podem ser atribuídas a uma conta de usuário específica, a um grupo de usuários ou a um aplicativo específico.
* Cada função tem uma lista de Ações e de Não Ações. Por exemplo, a função Colaborador de Máquina Virtual tem uma Ação de "listKeys" que permite que as chaves da conta de armazenamento sejam lidas. O Colaborador tem "Não Ações", tais como atualizar o acesso de usuários no Active Directory.
* As funções de armazenamento incluem (entre outras):

  * Proprietário – ele pode gerenciar tudo, inclusive o acesso.
  * Colaborador – ele pode fazer tudo que o proprietário pode fazer, menos atribuir acesso. Alguém com essa função pode exibir e regenerar as chaves da conta de armazenamento. Com as chaves da conta de armazenamento, ele pode acessar os objetos de dados.
  * Leitor – ele pode exibir informações sobre a conta de armazenamento, exceto os segredos. Por exemplo, se você atribuir a alguém uma função com permissões de leitor na conta de armazenamento, ele poderá exibir as propriedades da conta de armazenamento, mas não poderá fazer alterações nas propriedades nem exibir as chaves da conta de armazenamento.
  * Colaborador da Conta de Armazenamento – ele pode gerenciar a conta de armazenamento; ele pode ler os grupos de recursos e os recursos da assinatura, bem como criar e gerenciar implantações de grupo de recursos da assinatura. Eles também podem acessar as chaves da conta de armazenamento que, por sua vez, significa que eles podem acessar o plano de dados.
  * Administrador de Acesso do Usuário – ele pode gerenciar o acesso do usuário à conta de armazenamento. Por exemplo, ele pode conceder acesso de Leitor a um usuário específico.
  * Colaborador de Máquina Virtual – ele pode gerenciar máquinas virtuais, mas não a conta de armazenamento à qual ele está conectado. Essa função pode listar as chaves da conta de armazenamento, o que significa que o usuário a quem você atribui essa função pode atualizar o plano de dados.

    Para que um usuário crie uma máquina virtual, ele deve poder criar o arquivo VHD correspondente em uma conta de armazenamento. Para fazer isso, ele precisa poder recuperar a chave da conta de armazenamento e passá-la à API que está criando a VM. Portanto, ele deve ter essa permissão para que possa listar as chaves da conta de armazenamento.
* A capacidade de definir funções personalizadas é um recurso que permite compor um conjunto de ações usando uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.
* O usuário deve ser configurado no Azure Active Directory antes de você atribuir uma função a ele.
* Usando o PowerShell ou a CLI do Azure, você pode criar um relatório de quem concedeu/revogou que tipo de acesso a/de quem e em que escopo.

#### <a name="resources"></a>Recursos

* [Controle de acesso baseado em função do Active Directory do Azure](../../role-based-access-control/role-assignments-portal.md)

  Esse artigo explica o Controle de Acesso Baseado em Função do Azure Active Directory.
* [RBAC: Funções internas](../../role-based-access-control/built-in-roles.md)

  Esse artigo detalha todas as funções disponíveis internas no RBAC.
* [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Gerenciando as chaves da conta de armazenamento

As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades em uma tabela, mensagens na fila e arquivos em um compartilhamento de arquivos do Azure. O controle do acesso às chaves da conta de armazenamento controla o acesso ao plano de dados dessa conta de armazenamento.

Cada conta de armazenamento tem duas chaves, conhecidas como "Chave 1" e "Chave 2" no [Portal do Azure](http://portal.azure.com/) e nos cmdlets do PowerShell. Elas podem ser regeneradas manualmente usando um dos vários métodos, incluindo, entre outros, o [Portal do Azure](https://portal.azure.com/), o PowerShell, a CLI do Azure ou, de modo programático, a Biblioteca de Cliente de Armazenamento .NET ou a API REST dos Serviços de Armazenamento do Microsoft Azure.

Há vários motivos para regenerar suas chaves da conta de armazenamento.

* Você pode regenerá-las regularmente por motivos de segurança.
* Você poderia regenerar as chaves da conta de armazenamento se alguém conseguisse invadir um aplicativo e acessasse a chave que estava codificada ou salva em um arquivo de configuração, dando a ele acesso completo à sua conta de armazenamento.
* Outro caso para nova geração de chave é se sua equipe estivesse usando um aplicativo Gerenciador de Armazenamento que mantivesse a chave da conta de armazenamento e um dos membros da equipe saísse. O aplicativo continuaria funcionando, dando a ele acesso à sua conta de armazenamento depois que ele fosse embora. Na verdade, esse é o principal motivo para a criação das Assinaturas de Acesso Compartilhado de nível de conta – você pode usar uma SAS de nível de conta em vez de armazenar as chaves de acesso em um arquivo de configuração.

#### <a name="key-regeneration-plan"></a>Plano de nova geração de chave

Você não desejará apenas regenerar a chave que está usando sem nenhum planejamento. Se fizer isso, você poderá cortar todo o acesso a essa conta de armazenamento, o que pode causar uma grande interrupção. É por isso que há duas chaves. Você deve regenerar uma chave de cada vez.

Antes de regenerar suas chaves, certifique-se de que tenha uma lista de todos os seus aplicativos que dependem da conta de armazenamento, bem como de todos os outros serviços que está usando no Azure. Por exemplo, se você estiver usando os Serviços de Mídia do Azure que dependem da sua conta de armazenamento, será preciso ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar a chave. Se estiver usando quaisquer aplicativos, como um Explorador de Armazenamento, você precisará fornecer as novas chaves para esses aplicativos também. Se você tiver VMs cujos arquivos VHD são armazenados na conta de armazenamento, eles não serão afetados pela regeneração das chaves da conta de armazenamento.

É possível regenerar suas chaves no Portal do Azure. Depois que as chaves forem regeneradas, pode levar até 10 minutos até serem sincronizadas entre os Serviços de Armazenamento.

Quando você estiver pronto, aqui está o processo geral que detalha como você deve alterar sua chave. Nesse caso, a suposição é a de que você esteja usando no momento a Chave 1 e que vai mudar tudo para usar a Chave 2 no lugar.

1. Regenere a Chave 2 para garantir que ela seja protegida. Você pode fazer isso no Portal do Azure.
2. Em todos os aplicativos em que a chave de armazenamento é armazenada, altere a chave de armazenamento para usar o novo valor da Chave 2. Teste e publique o aplicativo.
3. Depois que todos os aplicativos e serviços estiverem funcionando perfeitamente, regenere a Chave 1. Isso garante que qualquer pessoa a quem você não concedeu expressamente a nova chave não terá mais acesso à conta de armazenamento.

Se, no momento, você estiver usando a Chave 2, será possível usar o mesmo processo, mas inverta os nomes das chaves.

É possível fazer a migração em dois dias, alterando cada aplicativo para usar a nova chave e publicando-o. Depois de fazer isso com todos, você deverá voltar e regenerar a chave antiga, pois ela não funciona mais.

Outra opção é colocar a chave de conta de armazenamento em um [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/) como um segredo e fazer com que os aplicativos recuperam a chave de lá. Assim, quando você regenerar a chave e atualizar o Cofre de Chaves do Azure, os aplicativos não precisarão ser reimplantados porque eles selecionarão a nova chave no Cofre de Chaves do Azure automaticamente. Você pode fazer com que o aplicativo leia a chave sempre que precisar, ou armazená-la em cache na memória e, se ela falhar, recuperá-la novamente do Azure Key Vault.

Usar o Cofre de Chaves do Azure também acrescenta outro nível de segurança para suas chaves de armazenamento. Ao usar esse método, você nunca terá a chave de armazenamento codificada em um arquivo de configuração, o que remove a possibilidade de alguém obter acesso às chaves sem permissão específica.

Outra vantagem de usar o Cofre de Chaves do Azure é que você também pode controlar o acesso às suas chaves usando o Azure Active Directory Isso significa que é possível conceder acesso a um punhado de aplicativos que precisam recuperar as chaves no Cofre de Chaves do Azure e saber que outros aplicativos não poderão acessar as chaves sem que você conceda especificamente permissão a eles.

> [!NOTE]
> A Microsoft recomenda usar apenas uma das chaves em todos os aplicativos ao mesmo tempo. Se você usar a Chave 1 em alguns lugares e a Chave 2 em outros, nãos será possível alternar as chaves sem que algum aplicativo perca o acesso.

#### <a name="resources"></a>Recursos

* [Gerenciar configurações de conta de armazenamento no portal do Azure](storage-account-manage.md)
* [Azure Storage Resource Provider REST API Reference (Referência à API REST do provedor de recursos de armazenamento do Azure)](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Segurança do plano de dados
Segurança do Plano de Dados refere-se aos métodos usados para proteger os objetos de dados armazenados no Armazenamento do Azure. Já vimos métodos para criptografar os dados e a segurança durante a transferência de dados. Mas e quanto a controlar o acesso aos objetos?

Você tem três opções para autorizar o acesso a objetos de dados no Armazenamento do Azure, incluindo:

- Usando o Azure AD para autorizar o acesso a sistemas de arquivos e filas (versão prévia). O Azure AD oferece vantagens em relação a outras abordagens para autorização, incluindo a eliminação da necessidade de armazenar segredos em seu código. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md). 
- Usar as chaves da conta de armazenamento para autorizar o acesso via Chave Compartilhada. A autorização via Chave Compartilhada requer o armazenamento das chaves da conta de armazenamento em seu aplicativo e, portanto, a Microsoft recomenda usar o Azure AD sempre que possível. Para aplicativos de produção, ou para autorizar o acesso a arquivos e tabelas do Azure, continue usando a Chave Compartilhada enquanto a integração do Azure AD estiver em versão prévia.
- Usar Assinaturas de Acesso Compartilhado para conceder permissões controladas a objetos de dados específicos por um determinado período de tempo.

Além de limitar o acesso por meio da autorização, você também pode usar [Firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Essa abordagem permite que você negue o acesso ao tráfego de Internet pública e conceda acesso apenas às Redes Virtuais do Azure ou intervalos de endereços IP da Internet pública específicos.

### <a name="storage-account-keys"></a>Chaves da conta de armazenamento

As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento.

Por exemplo, é possível ler arquivos. Muitas dessas ações podem ser executadas no Portal do Azure ou usando um dos muitos aplicativos do Gerenciador de Armazenamento. Você também pode gravar código para usar a API REST para executar essas operações.

Conforme abordado na seção [Segurança do Plano de Gerenciamento](#management-plane-security), o acesso às chaves de armazenamento de uma conta de armazenamento usando o modelo do Azure Resource Manager pode ser controlado por meio de RBAC (Controle de Acesso Baseado em Função).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar acesso a objetos em sua conta usando Assinaturas de Acesso Compartilhado e Políticas de Acesso Armazenado

Uma Assinatura de Acesso Compartilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que permite delegar acesso aos objetos de armazenamento e especificar restrições, como as permissões e o intervalo de data/hora de acesso.

É possível conceder acesso a arquivos ou diretórios.

Você pode fornecer um token SAS a um cliente que poderá utilizá-lo para carregar imagens em um sistema de arquivos no Armazenamento de Blobs, e conceder uma permissão de aplicativo Web para ler essas fotos. Em ambos os casos, há uma separação de interesses – cada aplicativo pode receber apenas o acesso que eles precisam para realizar as respectivas tarefas. Isso é possível graças às Assinaturas de Acesso Compartilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que usar as Assinaturas de Acesso Compartilhado

Por que você ia querer usar uma SAS em vez de apenas fornecer sua chave da conta de armazenamento, o que é muito mais fácil? Fornecer a chave da conta de armazenamento é como compartilhar as chaves do seu reino de armazenamento. Isto é, ela concede acesso a tudo. Um indivíduo poderia usar as chaves e carregar toda a biblioteca de músicas dele na sua conta de armazenamento. Ele também poderia substituir seus arquivos por versões infectadas por vírus ou até mesmo roubar seus dados. Dar acesso ilimitado à sua conta de armazenamento é algo que não se pode fazer de modo leviano.

Com as Assinaturas de Acesso Compartilhado, você pode dar a um cliente apenas as permissões necessárias por um período limitado. Por exemplo, se alguém estiver fazendo upload de um arquivo para a própria conta, você poderá conceder acesso de gravação por tempo suficiente para carregar o arquivo (dependendo do tamanho do arquivo, evidentemente). E se você mudar de ideia, poderá revogar esse acesso.

Além disso, você pode especificar que as solicitações feitas usando uma SAS sejam restritas a um determinado endereço IP ou intervalo de endereços IP fora do Azure. Você também pode exigir que as solicitações sejam feitas usando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se você deseja permitir apenas o tráfego HTTPS, será possível definir o protocolo necessário para apenas HTTPS, e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma Assinatura de Acesso Compartilhado

Uma Assinatura de Acesso Compartilhado é um conjunto de parâmetros de consulta acrescentado à URL que aponta para o recurso

que fornece informações sobre o acesso permitido e por quanto tempo o acesso é permitido. Veja um exemplo; este URI fornece acesso de leitura a um blob por cinco minutos. Os parâmetros de consulta de SAS devem ser codificados por URL, como %3A para dois-pontos (:) ou %20 para um espaço.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Como a Assinatura de Acesso Compartilhado é autorizada pelo Serviço de Armazenamento do Azure

Quando o serviço de armazenamento recebe a solicitação, ele usa os parâmetros de consulta de entrada e cria uma assinatura usando o mesmo método que o programa de chamada. Ele então compara as duas assinaturas. Se elas coincidirem, o serviço de armazenamento poderá verificar a versão do serviço de armazenamento para garantir que ele seja válido, verificar se a data e a hora atuais estão dentro da janela especificada, ter certeza de que o acesso solicitado corresponde à solicitação feita, etc.

Por exemplo, se a URL acima estivesse apontando para um arquivo em vez de para um blob, essa solicitação falharia, pois ela especifica que a Assinatura de Acesso Compartilhado é para um blob. Se o comando REST que está sendo chamado fosse para atualizar um blob, ele falharia, pois a Assinatura de Acesso Compartilhado especifica que somente o acesso de leitura é permitido.

#### <a name="types-of-shared-access-signatures"></a>Tipos de Assinatura de Acesso Compartilhado

* Uma SAS de nível de serviço pode ser usada para acessar recursos específicos em uma conta de armazenamento. Alguns exemplos disso são recuperar uma lista de arquivos em um sistema de arquivos ou baixar um arquivo.
* Uma SAS de nível de conta pode ser usada para acessar tudo aquilo para o que ela pode ser usada. Além disso, pode fornecer opções para recursos que não são permitidos com uma SAS de nível de serviço como a capacidade de criar sistemas de arquivos.

#### <a name="creating-a-sas-uri"></a>Criar um URI de SAS

1. Você pode criar um URI sob demanda, definindo todos os parâmetros de consulta todas as vezes.

   Essa abordagem é flexível, mas se você tiver um conjunto lógico de parâmetros que sempre são semelhantes, usar uma Política de Acesso Armazenado é uma opção mais adequada.
2. É possível criar uma Política de Acesso Armazenada para um sistema de arquivos inteiro, compartilhamento de arquivos, tabela ou fila. Desse modo, você pode usar isso como a base para os URIs SAS que cria. As permissões com base em Políticas de Acesso Armazenado podem ser facilmente revogadas. É possível ter até cinco políticas definidas em cada sistema de arquivos, fila, tabela ou compartilhamento de arquivos.

   Por exemplo, se muitas pessoas lessem os blobs em um sistema de arquivos específico, você poderia criar uma Política de Acesso Armazenada que informasse "conceder acesso de leitura" e quaisquer outras configurações que serão sempre as mesmas. Assim, você poderia criar um URI de SAS usando as configurações da Política de Acesso Armazenado e especificando a data/hora de expiração. A vantagem disso é que você não precisa especificar todos os parâmetros de consulta todas as vezes.

#### <a name="revocation"></a>Revogação

Suponha que a SAS foi comprometida ou que você queira alterá-la devido a requisitos de conformidade normativa ou segurança corporativa. Como você revoga o acesso a um recurso usando essa SAS? Depende de como você criou o URI da SAS.

Se estiver usando URIs ad hoc, você terá três opções. Você pode emitir tokens SAS com políticas de expiração curta e aguardar a SAS expirar. Você pode renomear ou excluir o recurso (supondo que o escopo do token fosse para um único objeto). Você pode alterar as chaves da conta de armazenamento. Essa última opção pode ter um impacto significativo dependendo de quantos serviços estão usando essa conta de armazenamento, o que provavelmente não é algo que você queira fazer sem planejamento.

Se estiver usando uma SAS derivada de uma Política de Acesso Armazenado, você poderá remover o acesso revogando a Política de Acesso Armazenado. Basta alterá-la para que ela expire ou removê-la completamente. Isso entra em vigor imediatamente e invalida cada SAS criada usando essa Política de Acesso Armazenado. Atualizar ou remover a Política de Acesso Armazenada poderá afetar as pessoas que acessam esse sistema de arquivos, compartilhamento de arquivos, tabelas ou filas específicas via SAS, mas se os clientes estiverem gravados, solicitarão uma nova SAS quando a antiga tornar-se inválida e funcionará sem problemas.

Como usar uma SAS derivada de uma Política de Acesso Armazenado dá a possibilidade de revogar essa SAS imediatamente, a prática recomendada é, sempre que possível, usar Políticas de Acesso Armazenado.

#### <a name="resources"></a>Recursos

Obtenha informações mais detalhadas sobre como usar as Assinatura de Acesso Compartilhado e as Políticas de Acesso Armazenado, com exemplos, nos artigos a seguir:

* Estes são os artigos de referência.

  * [Service SAS (SAS de serviço)](https://msdn.microsoft.com/library/dn140256.aspx)

    Esse artigo fornece exemplos de como usar uma SAS de nível de serviço com blobs, mensagens da fila, intervalos de tabelas e arquivos.
  * [Constructing a service SAS (Criação de uma SAS de serviço)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Constructing an account SAS (Criação de uma SAS de conta)](https://msdn.microsoft.com/library/mt584140.aspx)
* Estes são os tutoriais de como usar a biblioteca de cliente .NET para criar Assinatura de Acesso Compartilhado e Políticas de Acesso Armazenado.

  * [Uso de SAS (Assinaturas de Acesso Compartilhado)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Assinatura de Acesso Compartilhado, Parte 2: Criar e usar uma SAS com o Serviço Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Esse artigo inclui uma explicação do modelo SAS, exemplos de Assinatura de Acesso Compartilhado e recomendações para uso da prática recomendada de SAS. Também é abordada a revogação da permissão concedida.

* Autenticação

  * [Autenticação para os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Tutorial de introdução às Assinaturas de Acesso Compartilhado

  * [SAS Getting Started Tutorial (Tutorial de introdução à SAS)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Criptografia em trânsito

### <a name="transport-level-encryption--using-https"></a>Criptografia no nível de transporte – usando HTTPS

Outra etapa que você deve executar para garantir a segurança dos dados do Armazenamento do Azure é criptografá-los entre o cliente e o Armazenamento do Microsoft Azure. A primeira recomendação é sempre usar o protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , que garante a comunicação segura na Internet pública.

Para ter um canal de comunicação seguro, você sempre deve usar o HTTPS ao chamar as APIs REST ou acessar objetos no armazenamento. Além disso, as **Assinaturas de Acesso Compartilhado**, que podem ser usadas para delegar acesso a objetos do Armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado ao usar as Assinaturas de Acesso Compartilhado, garantindo que qualquer pessoa que envie links com tokens SAS usará o protocolo adequado.

É possível impor o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a opção [Transferência segura necessária](../storage-require-secure-transfer.md) na conta de armazenamento. As conexões que usam HTTP serão recusadas depois que essa opção for habilitada.

## <a name="encryption-at-rest"></a>Criptografia em repouso

### <a name="storage-service-encryption-sse"></a>SSE (Criptografia do Serviço de Armazenamento)

A SSE é habilitada para todas as contas de armazenamento e não pode ser desabilitada. Ela criptografa automaticamente seus dados ao gravá-los no Armazenamento do Azure. Quando você faz a leitura de dados do Armazenamento do Azure, eles são descriptografados pelo armazenamento do Azure antes de serem retornados. A SSE permite proteger os dados sem precisar modificar código nem adicionar código a nenhum aplicativo.

Você pode usar chaves gerenciadas pela Microsoft ou suas próprias chaves personalizadas. A Microsoft gera as chaves gerenciadas e administra o armazenamento seguro delas, bem como sua rotatividade regular, conforme definido pela política interna da Microsoft. Para obter mais informações sobre como usar chaves personalizadas, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).
