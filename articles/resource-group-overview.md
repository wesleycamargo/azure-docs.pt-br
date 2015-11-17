<properties
   pageTitle="Visão geral do Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Descreve como usar o Gerenciador de Recursos do Azure para implantação, gerenciamento e controle de acesso dos recursos do Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/09/2015"
   ms.author="tomfitz"/>

# Visão geral do Gerenciador de Recursos do Azure

A infraestrutura do seu aplicativo geralmente é composta de vários componentes; talvez uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou aplicativo Web, banco de dados, servidor de banco de dados e serviços de terceiros. Tais componentes não são vistos como entidades separadas, em vez disso, eles são mostrados como partes relacionadas e interdependentes de uma única entidade. Você deseja implantar, gerenciar e monitorá-los como um grupo. O Gerenciador de Recursos do Azure permite trabalhar com os recursos da sua solução como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para a sua solução em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

## Os benefícios de usar o Gerenciador de Recursos

O Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo, em vez de tratá-los individualmente.
- Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.
- Você pode usar modelos declarativos para definir sua implantação.
- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.
- Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.
- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.
- Você pode esclarecer a cobrança para a sua organização exibindo os custos acumulados para todo o grupo ou para um grupo de recursos compartilhando a mesma marca.  

O Gerenciador de Recursos fornece uma nova maneira de implantar e gerenciar suas soluções. Se você usou o modelo de implantação anterior e quiser saber mais sobre as alterações, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md).

## Diretrizes

As sugestões a seguir ajudarão você a aproveitar ao máximo o Gerenciador de Recursos quando estiver trabalhando com suas soluções.

1. Defina e implante a sua infraestrutura através de sintaxe declarativa em modelos do Gerenciador de Recursos, e não de comandos imperativos.
2. Defina todas as etapas de implantação e configuração no modelo. Você não deve ter nenhuma etapa manual para configurar sua solução.
3. Execute comandos imperativos para gerenciar os recursos, como iniciar ou interromper um aplicativo ou máquina.
4. Organize recursos com o mesmo ciclo de vida em um grupo de recursos. Use marcas para as demais organizações de recursos.

## Grupos de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que são agrupados logicamente. Você pode decidir como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

Existem alguns fatores importantes a considerar ao definir seu grupo de recursos:

1. Todos os recursos no grupo devem compartilhar o mesmo ciclo de vida. Você vai implantar, atualizar e excluí-los juntos. Se um recurso, como um servidor de banco de dados, precisar existir em um ciclo de implantação diferente, ele deve ser de outro grupo de recursos.
2. Cada recurso só pode existir em um grupo de recursos.
3. Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento.
4. Você pode mover um recurso de um grupo de recursos para outro grupo. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).
4. Um grupo de recursos pode conter recursos que residem em regiões diferentes.
5. Um grupo de recursos pode ser usado para definir o escopo de controle de acesso para ações administrativas.
6. Um recurso pode ser vinculado a um recurso em outro grupo de recursos quando os dois recursos tiverem que interagir entre si, mas eles não compartilham o mesmo ciclo de vida (por exemplo, vários aplicativos conectando-se a um banco de dados). Para obter mais informações, confira [Associando recursos no Gerenciador de Recursos do Azure](resource-group-link-resources.md).

## Implantação de modelo

Com o Gerenciador de Recursos, você pode criar um modelo simples (no formato JSON) que define a implantação e a configuração do seu aplicativo. Esse modelo é conhecido como um modelo do Gerenciador de Recursos e fornece uma forma declarativa de definir a implantação. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos são implantados em um estado consistente.

Dentro do modelo, você pode definir a infraestrutura do seu aplicativo, como configurar essa infraestrutura e como publicar seu código do aplicativo a esta infraestrutura. Você não precisa se preocupar sobre a ordem de implantação porque o Gerenciador de Recursos do Azure analisa as dependências para garantir que os recursos sejam criados na ordem correta. Para saber mais, confira [Definindo as dependências nos modelos do Gerenciador de Recursos do Azure](resource-group-define-dependencies.md).

Você não precisa definir toda a sua infraestrutura em um único modelo. Muitas vezes, faz sentido dividir seus requisitos de implantação em um conjunto de modelos com destinação e fins específicos. Você pode facilmente reutilizar esses modelos para soluções diferentes. Para implantar uma solução específica, você deve criar um modelo mestre que vincule todos os modelos necessários. Para saber mais, confira [Usando modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md).

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um novo recurso ao seu aplicativo e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo especificar a criação de um novo recurso, mas esse recurso já existe, o Gerenciador de Recursos do Azure executa uma atualização em vez de criar um novo ativo. O Gerenciador de Recursos do Azure atualiza o ativo existente para o mesmo estado de um novo.

Você pode especificar parâmetros em seu modelo para conferir personalização e flexibilidade à implantação. Por exemplo, você pode passar valores de parâmetro para personalizar a implantação para seu ambiente de teste. Especificando os parâmetros, você pode usar o mesmo modelo para a implantação em todos os ambientes do seu aplicativo.

O Gerenciador de Recursos do Azure fornece extensões para cenários que precisam de operações adicionais, como a instalação de um software específico que não está incluído na configuração. Se você já estiver usando um serviço de gerenciamento de configuração, como DSC, Chef ou Puppet, poderá continuar trabalhando com esse serviço usando as extensões.

Quando você cria uma solução do Marketplace, ela inclui automaticamente um modelo de implantação. Você não precisa criar seu modelo do zero, pois é possível iniciar com o modelo da sua solução e personalizá-lo para atender às suas necessidades específicas.

Finalmente, o modelo se torna parte do código-fonte do seu aplicativo. Você pode adicioná-lo ao repositório de código-fonte e atualizá-lo conforme a evolução de seu aplicativo. Você pode editar o modelo com o Visual Studio.

Para obter mais informações sobre como definir o modelo, consulte [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).

Para esquemas de modelo, confira [Esquemas do Gerenciador de Recursos do Azure](https://github.com/Azure/azure-resource-manager-schemas).

Para saber mais sobre como usar um modelo de implantação, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

Para obter orientações sobre como estruturar seus modelos, confira [Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md).

Para obter orientação sobre como implantar sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments-preview-portal.md).

## Marcas

O Gerenciador de Recursos fornece um recurso de marcação que permite classificar os recursos de acordo com suas necessidades de gerenciamento ou de cobrança. Pode ser útil usar marcas quando você tiver um conjunto complexo de grupos de recursos e recursos e se precisar visualizar os ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento.

Recursos não precisam residir no mesmo grupo de recursos para compartilhar uma marca. Você pode criar sua própria taxonomia de marca para garantir que todos os usuários na sua organização usem marcas comuns em vez de aplicarem marcas ligeiramente diferentes inadvertidamente (como por exemplo, "Dept" em vez de "Departamento").

Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md).

## Controle de acesso

O Gerenciador de Recursos permite controlar quem tem acesso a ações específicas da sua organização. Ele integra nativamente OAuth e RBAC (controle de acesso baseado em função) à plataforma de gerenciamento e aplica esse controle de acesso a todos os serviços em seu grupo de recursos. Você pode adicionar os usuários à plataforma predefinida e funções específicas ao recurso, bem como aplicar essas funções a uma assinatura, grupo de recursos ou recurso para limitar o acesso. Por exemplo, você pode aproveitar a função predefinida chamada SQL DB Contributor que permite aos usuários gerenciar bancos de dados, mas não os servidores de banco de dados ou políticas de segurança. Adicione usuários da sua organização que precisam desse tipo de acesso à função SQL DB Contributor e aplique a função à assinatura, o grupo de recursos ou recurso.

O Gerenciador de Recursos registra automaticamente as ações do usuário para auditoria. Para saber mais sobre como trabalhar com os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).

Para obter mais informações sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função no portal de visualização do Microsoft Azure](role-based-access-control-configure.md). Este tópico contém uma lista de funções internas e ações permitidas. As funções internas incluem funções gerais de Proprietário, Leitor e Colaborador; bem como funções específicas do serviço, como Colaborador da Máquina Virtual, Colaborador de Rede Virtual e o Gerenciador de Segurança do SQL (para citar apenas algumas das funções disponíveis).

Para obter exemplos de atribuição de funções, consulte [Gerenciando o acesso a recursos](resource-group-rbac.md).

Você pode bloquear explicitamente recursos essenciais para impedir que os usuários possam excluí-los ou modificá-los. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

Para ver as práticas recomendadas, confira [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md)

## Gerenciar recursos com políticas personalizadas

O Gerenciador de Recursos permite que você crie políticas personalizadas para gerenciar seus recursos. Os tipos de políticas que você criar poderão incluir cenários tão diferentes como impor uma convenção de nomenclatura a recursos, limitar quais regiões poderão hospedar um tipo de recurso ou exigir um valor de marca em recursos para organizar a cobrança por departamentos. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

## Camada de gerenciamento consistente

O Gerenciador de Recursos do Azure fornece operações completamente compatíveis por meio do Azure PowerShell, do CLI do Azure para Mac, do Linux e do Windows, do portal de visualização do Azure ou da API REST. Você pode usar a interface que funciona melhor para você e alternar rapidamente entre as interfaces sem problemas. O portal exibe até mesmo notificação para ações executadas fora do portal.

Para obter informações sobre o PowerShell, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos](./powershell-azure-resource-manager.md) e [Cmdlets do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obter informações sobre a CLI do Azure, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./virtual-machines/xplat-cli-azure-resource-manager.md).

Para obter informações sobre a API REST, consulte [Referência da API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Para saber mais sobre o uso do portal de visualização, confira [Usando o Portal de Visualização do Azure para gerenciar os recursos do Azure](azure-portal/resource-group-portal.md).

O Gerenciador de Recursos do Azure oferece suporte a compartilhamento de recursos entre origens (CORS). Com o CORS, você pode chamar a API REST do Gerenciador de Recursos ou uma API REST do serviço do Azure de um aplicativo Web que resida em um domínio diferente. Sem suporte do CORS, o navegador da Web impediria que um aplicativo em um domínio acessasse recursos em outro domínio. O Gerenciador de Recursos habilita o CORS para todas as solicitações com credenciais de autenticação válidas.

## Próximas etapas

- Para saber mais sobre a criação de modelos, consulte [Criando modelos](./resource-group-authoring-templates.md).
- Para implantar o modelo criado, consulte [Implantando modelos](resource-group-template-deploy.md)
- Para entender as funções que você pode usar em um modelo, consulte [Funções de modelo](./resource-group-template-functions.md)
- Para obter orientação sobre como criar seus modelos, consulte [Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md)

Veja uma demonstração em vídeo desta visão geral:

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=Nov15_HO3-->