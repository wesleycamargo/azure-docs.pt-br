---
title: Visão geral do Gerenciador de Recursos do Azure | Microsoft Docs
description: Descreve como usar o Gerenciador de Recursos do Azure para implantação, gerenciamento e controle de acesso dos recursos do Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: tomfitz

---
# Visão geral do Azure Resource Manager
A infraestrutura do seu aplicativo geralmente é composta de vários componentes; talvez uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou aplicativo Web, banco de dados, servidor de banco de dados e serviços de terceiros. Tais componentes não são vistos como entidades separadas, em vez disso, eles são mostrados como partes relacionadas e interdependentes de uma única entidade. Você deseja implantar, gerenciar e monitorá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos da sua solução como um grupo. Você pode implantar, atualizar ou excluir todos os recursos da sua solução em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

## Terminologia
Se você for um novo usuário do Azure Resource Manager, há alguns termos com os quais talvez você não esteja familiarizado.

* **recurso** -um item gerenciável que está disponível por meio do Azure. Alguns recursos comuns são uma máquina virtual, conta de armazenamento, aplicativo Web, banco de dados e rede virtual, mas há muito mais.
* **grupo de recursos** - Um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Confira [Grupos de recursos](#resource-groups).
* **provedor de recursos** - Um provedor de recursos é um serviço que fornece os recursos que você pode implantar e gerenciar por meio do Gerenciador de Recursos. Cada provedor de recursos oferece operações para trabalhar com os recursos implantados. Alguns provedores de recursos comuns são Microsoft.Compute que fornece o recurso de máquina virtual, Microsoft.Storage que fornece o recurso de conta de armazenamento e Microsoft.Web que fornece recursos relacionados aos aplicativos Web. Confira [Provedores de recursos](#resource-providers).
* **Modelo do Resource Manager** - Um arquivo JSON (JavaScript Object Notation) que define um ou mais recursos para implantação em um grupo de recursos. Ele também define as dependências entre os recursos implantados. O modelo pode ser usado para implantar os recursos de forma consiste e repetida. Confira [Implantação de modelo](#template-deployment).
* **sintaxe declarativa** - sintaxe que permite a declaração "Isso é o que pretendo criar" sem precisar escrever a sequência de comandos de programação para criá-la. O modelo do Resource Manager é um exemplo de sintaxe declarativa. No arquivo, você define as propriedades da infraestrutura a ser implantada no Azure.

## Os benefícios de usar o Gerenciador de Recursos
O Gerenciador de Recursos fornece vários benefícios:

* Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo em vez de tratá-los individualmente.
* Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.
* Você pode gerenciar sua infraestrutura por meio de modelos declarativos em vez de scripts.
* Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.
* Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.
* Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.
* Você pode esclarecer a cobrança da sua organização exibindo os custos para um grupo de recursos que compartilha a mesma marcação.

O Gerenciador de Recursos fornece uma nova maneira de implantar e gerenciar suas soluções. Se você usou o modelo de implantação anterior e quiser saber mais sobre as alterações, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md).

## Diretrizes
As sugestões a seguir ajudarão você a aproveitar ao máximo o Gerenciador de Recursos quando estiver trabalhando com suas soluções.

1. Defina e implante a sua infraestrutura através de sintaxe declarativa em modelos do Gerenciador de Recursos, e não de comandos imperativos.
2. Defina todas as etapas de implantação e configuração no modelo. Você não deve ter nenhuma etapa manual para configurar sua solução.
3. Execute comandos imperativos para gerenciar os recursos, como iniciar ou interromper um aplicativo ou máquina.
4. Organize recursos com o mesmo ciclo de vida em um grupo de recursos. Use marcas para as demais organizações de recursos.

Para obter mais recomendações, confira [Práticas recomendadas para criar modelos do Azure Resource Manager](resource-manager-template-best-practices.md).

## Grupos de recursos
Existem alguns fatores importantes a considerar ao definir seu grupo de recursos:

1. Todos os recursos no grupo devem compartilhar o mesmo ciclo de vida. Você os implanta, atualiza e exclui juntos. Se um recurso, como um servidor de banco de dados, precisar existir em um ciclo de implantação diferente, ele deve ser de outro grupo de recursos.
2. Cada recurso só pode existir em um grupo de recursos.
3. Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento.
4. Você pode mover um recurso de um grupo de recursos para outro grupo. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).
5. Um grupo de recursos pode conter recursos que residem em regiões diferentes.
6. Um grupo de recursos pode ser usado para definir o escopo de controle de acesso para ações administrativas.
7. Um recurso pode interagir com recursos em outros grupos de recursos. Essa interação é comum quando dois recursos estão relacionados, mas não compartilham o mesmo ciclo de vida (por exemplo, aplicativos Web que se conectam a um banco de dados).

Ao criar um grupo de recursos, você precisará fornecer um local para ele. Você pode estar se perguntando: "Por que um grupo de recursos precisa de um local? E, se os recursos podem ter locais diferentes do grupo de recursos, por que o local do grupo de recursos importa?" O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

## Provedores de recursos
Cada provedor de recursos oferece um conjunto de recursos e operações para trabalhar com a área técnica. Por exemplo, se você quer armazenar chaves e segredos, trabalhe com o provedor de recursos **Microsoft.KeyVault**. Este provedor de recursos oferece um tipo de recurso chamado **cofres** para criar o cofre da chave e um tipo de recurso chamado **cofres/segredos** para criar um segredo no cofre de chaves. Ele também fornece operações por meio de [Operações de API REST do Cofre de Chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx). Você pode chamar a API REST diretamente ou pode usar [cmdlets PowerShell do Cofre de Chaves](https://msdn.microsoft.com/library/dn868052.aspx) e a [CLI do Azure do Cofre de Chaves](key-vault/key-vault-manage-with-cli.md) para gerenciar o cofre de chaves. Também é possível usar várias linguagens de programação para trabalhar com a maioria dos recursos. Para saber mais, confira [SDKs e exemplos](#sdks-and-samples).

Para implantar e gerenciar sua infraestrutura, você precisará saber detalhes sobre o provedor de recursos. Você precisará saber os tipos de recursos, os números de versão das operações de API REST, as operações com suporte e o esquema para usar na criação de recursos. Para saber mais sobre os provedores de recursos compatíveis, consulte [Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos](resource-manager-supported-services.md).

## Implantação de modelo
Com o Gerenciador de Recursos, você pode criar um modelo (no formato JSON) que define a implantação e a configuração do seu aplicativo. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos são implantados em um estado consistente. O Azure Resource Manager analisa as dependências para garantir que os recursos sejam criados na ordem correta. Para saber mais, confira [Definindo as dependências nos modelos do Gerenciador de Recursos do Azure](resource-group-define-dependencies.md).

Quando você cria uma solução no Portal, ela inclui automaticamente um modelo de implantação. Você não precisa criar seu modelo do zero, pois é possível iniciar com o modelo da sua solução e personalizá-lo para atender às suas necessidades específicas. Você pode recuperar um modelo de um grupo de recursos existente exportando o estado atual do grupo de recursos ou exibindo o modelo usado para determinada implantação. A exibição do modelo exportado é uma maneira útil de saber mais sobre a sintaxe do modelo. Para saber mais sobre como trabalhar com modelos exportados, confira [Exportar um modelo do Azure Resource Manager de recursos existentes](resource-manager-export-template.md).

Você não precisa definir toda a sua infraestrutura em um único modelo. Muitas vezes, faz sentido dividir seus requisitos de implantação em um conjunto de modelos com destinação e fins específicos. Você pode reutilizar esses modelos facilmente para soluções diferentes. Para implantar uma solução específica, você deve criar um modelo mestre que vincule todos os modelos necessários. Para saber mais, confira [Usando modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md).

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um recurso à sua solução e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo especificar a criação de um recurso, mas esse recurso já existir, o Azure Resource Manager executará uma atualização em vez de criar um novo ativo. O Azure Resource Manager atualiza o ativo existente para o mesmo estado de um novo. Ou então, você pode especificar que o Gerenciador de Recursos deve excluir os recursos que não estão especificados no modelo. Para entender as diferentes opções na implantação, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

Você pode especificar parâmetros em seu modelo para conferir personalização e flexibilidade à implantação. Por exemplo, você pode passar valores de parâmetro para personalizar a implantação para seu ambiente de teste. Especificando os parâmetros, você pode usar o mesmo modelo para implantar a solução em ambientes diferentes.

O Gerenciador de Recursos do Azure fornece extensões para cenários que precisam de operações adicionais, como a instalação de um software específico que não está incluído na configuração. Se você já estiver usando um serviço de gerenciamento de configuração, como DSC, Chef ou Puppet, poderá continuar trabalhando com esse serviço usando as extensões.

Finalmente, o modelo se torna parte do código-fonte do seu aplicativo. Você pode adicioná-lo ao repositório de código-fonte e atualizá-lo conforme a evolução de seu aplicativo. Você pode editar o modelo com o Visual Studio.

Para obter mais informações sobre como definir o modelo, consulte [Criação de modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

Para obter instruções detalhadas sobre como criar um modelo, confira [Passo a passo do modelo do Resource Manager](resource-manager-template-walkthrough.md).

Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).

## Marcas
O Gerenciador de Recursos fornece um recurso de marcação que permite classificar os recursos de acordo com suas necessidades de gerenciamento ou de cobrança. Use as marcas quando você tiver um conjunto complexo de grupos de recursos e recursos e precisar visualizar os ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento. Sem marcas, os usuários de sua organização podem criar vários recursos que podem ser difíceis de identificar e gerenciar mais tarde. Por exemplo, você pode querer excluir todos os recursos de um projeto específico. Se esses recursos não estão marcados para o projeto, você precisa encontrá-los manualmente. A marcação pode ser uma maneira importante de reduzir custos desnecessários em sua assinatura.

Recursos não precisam residir no mesmo grupo de recursos para compartilhar uma marca. Você pode criar sua própria taxonomia de marca para garantir que todos os usuários na sua organização usem marcas comuns em vez de aplicarem marcas ligeiramente diferentes inadvertidamente (como por exemplo, "Dept" em vez de "Departamento").

Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md). Você pode criar uma [política personalizada](#manage-resources-with-customized-policies) que requer a adição de marcas a recursos durante a implantação.

## Controle de acesso
O Gerenciador de Recursos permite controlar quem tem acesso a ações específicas da sua organização. Ele integra nativamente OAuth e RBAC (controle de acesso baseado em função) à plataforma de gerenciamento e aplica esse controle de acesso a todos os serviços em seu grupo de recursos. Você pode adicionar os usuários à plataforma predefinida e funções específicas ao recurso, bem como aplicar essas funções a uma assinatura, grupo de recursos ou recurso para limitar o acesso. Por exemplo, você pode aproveitar a função predefinida chamada SQL DB Contributor que permite aos usuários gerenciar bancos de dados, mas não os servidores de banco de dados ou políticas de segurança. Adicione usuários da sua organização que precisam desse tipo de acesso à função SQL DB Contributor e aplique a função à assinatura, o grupo de recursos ou recurso.

O Gerenciador de Recursos registra automaticamente as ações do usuário para auditoria. Para saber mais sobre como trabalhar com os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).

Para obter mais informações sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure](active-directory/role-based-access-control-configure.md). O tópico [RBAC: funções internas](active-directory/role-based-access-built-in-roles.md) contém uma lista de funções internas e ações permitidas. As funções internas incluem funções gerais de Proprietário, Leitor e Colaborador; bem como funções específicas do serviço, como Colaborador da Máquina Virtual, Colaborador de Rede Virtual e o Gerenciador de Segurança do SQL (para citar apenas algumas das funções disponíveis).

Você pode bloquear explicitamente recursos essenciais para impedir que os usuários possam excluí-los ou modificá-los. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

Para ver as práticas recomendadas, confira [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md)

## Gerenciar recursos com políticas personalizadas
O Gerenciador de Recursos permite que você crie políticas personalizadas para gerenciar seus recursos. Os tipos de políticas que você cria podem incluir diversos cenários. Você pode impor uma convenção de nomenclatura para recursos, limitar os tipos e instâncias de recursos que podem ser implantados ou limitar quais regiões podem hospedar um tipo de recurso. Você pode exigir um valor de marcação nos recursos para organizar a cobrança por departamentos. Você pode criar políticas para ajudar a reduzir os custos e manter a consistência em sua assinatura. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

## Camada de gerenciamento consistente
O Gerenciador de Recursos fornece operações compatíveis por meio do Azure PowerShell, da CLI do Azure para Mac, Linux e Windows, do Portal do Azure ou da API REST. Você pode usar a interface que funciona melhor para você e alternar rapidamente entre as interfaces sem problemas.

Para obter informações sobre o PowerShell, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos](powershell-azure-resource-manager.md) e [Cmdlets do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obter informações sobre a CLI do Azure, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).

Para obter informações sobre a API REST, consulte [Referência da API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). Para exibir as operações REST de seus recursos implantados, confira [Uso do Azure Resource Explorer para exibir e modificar recursos](resource-manager-resource-explorer.md).

Para saber mais sobre como usar o portal, confira [Implantar recursos com modelos do Resource Manager e o Portal do Azure](resource-group-template-deploy-portal.md).

O Gerenciador de Recursos do Azure oferece suporte a compartilhamento de recursos entre origens (CORS). Com o CORS, você pode chamar a API REST do Gerenciador de Recursos ou uma API REST do serviço do Azure de um aplicativo Web que resida em um domínio diferente. Sem suporte do CORS, o navegador da Web impediria que um aplicativo em um domínio acessasse recursos em outro domínio. O Gerenciador de Recursos habilita o CORS para todas as solicitações com credenciais de autenticação válidas.

## SDKs
Os SDKs do Azure estão disponíveis em várias linguagens e plataformas. Cada uma dessas implementações da linguagem está disponível por meio do gerenciador de pacotes do ecossistema e do GitHub.

O código em cada um desses SDKs é gerado a partir de especificações da API RESTful do Azure. Essas especificações têm o código-fonte aberto e baseiam-se na especificação Swagger 2.0. O código do SDK é gerado por um projeto de fonte-aberta denominado AutoRest. O AutoRest transforma essas especificações da API RESTful em bibliotecas de cliente em várias linguagens. Se você quiser melhorar algum aspecto do código gerado nos SDKs, todo o conjunto de ferramentas para criar os SDKs é aberto, está disponível gratuitamente e baseado em um formato de especificação API amplamente adotado.

Aqui estão nossos repositórios do SDK de software livre. Comentários, problemas e solicitações pull são bem-vindos.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [!NOTE]
> Se o SDK não fornecer a funcionalidade necessária, você também poderá chamar a [API REST do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) diretamente.
> 
> 

## Exemplos
### .NET
* [Gerenciar recursos e grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
* [Implantar uma VM habilitada para SSH com um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java
* [Gerenciar recursos do Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
* [Gerenciar grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
* [Implantar uma VM habilitada para SSH com um modelo](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js
* [Gerenciar recursos e grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
* [Implantar uma VM habilitada para SSH com um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python
* [Gerenciar recursos e grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
* [Implantar uma VM habilitada para SSH com um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby
* [Gerenciar recursos e grupos de recursos do Azure](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
* [Implantar uma VM habilitada para SSH com um modelo](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Além desses exemplos, você pode pesquisar os exemplos da galeria.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Próximas etapas
* Para obter uma introdução simples do trabalho com modelos, confira [Exportar um modelo Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).
* Para obter uma explicação mais completa da criação de um modelo, veja [Passo a Passo do Modelo do Resource Manager](resource-manager-template-walkthrough.md).
* Para entender as funções que você pode usar em um modelo, confira [Funções de modelo](resource-group-template-functions.md)
* Para saber mais sobre como usar o Visual Studio com o Resource Manager, veja [Criar e implantar grupos de recursos do Azure com o Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
* Para saber mais sobre como usar o VS Code com o Resource Manager, confira [Trabalhando com Modelos do Azure Resource Manager no Visual Studio Code](resource-manager-vs-code.md).

Veja uma demonstração em vídeo desta visão geral:

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]


<!---HONumber=AcomDC_0921_2016-->