<properties
   pageTitle="Visão Geral do Gerenciador de Recursos do Azure"
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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Visão Geral do Gerenciador de Recursos do Azure

Aplicativos normalmente são compostos por vários componentes, como um aplicativo da Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. Tais componentes não são vistos como entidades separadas, em vez disso, eles são mostrados como partes relacionadas e interdependentes de uma única entidade. Você deseja implantar, gerenciar e monitorá-los como um grupo. O Gerenciador de Recursos do Azure permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo.

O Gerenciador de Recursos do Azure nativamente integra o controle de acesso à plataforma de gerenciamento para que você possa especificar as ações que um usuário da sua organização pode utilizar em um grupo de recursos.

Veja uma demonstração desta visão geral.

[AZURE.VIDEO azure-resource-manager-overview]

> [AZURE.NOTE]Este tópico descreve os recursos, grupos e modelos usando o portal de visualização para demonstrar os conceitos. No entanto, você pode também criar, gerenciar e excluir recursos do Azure usando a [CLI do Azure para Mac, Linux e Windows](virtual-machines/xplat-cli-azure-resource-manager.md), bem como o [PowerShell](powershell-azure-resource-manager.md).

## Grupos de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que são agrupados logicamente. Você pode decidir como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

Existem alguns fatores importantes a considerar ao definir seu grupo de recursos:

1. Todos os recursos no grupo devem compartilhar o mesmo ciclo de vida. Você vai implantar, atualizar e excluí-los juntos. Se um recurso, como um servidor de banco de dados, precisar existir em um ciclo de implantação diferente, ele deve ser de outro grupo de recursos.
2. Cada recurso só pode existir em um grupo de recursos.
3. Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento.
4. Um grupo de recursos pode conter recursos que residem em regiões diferentes.
5. Um grupo de recursos pode ser usado para definir o escopo de controle de acesso para ações administrativas.

No Portal de Visualização do Azure, todos os novos recursos são criados em um grupo de recursos. Mesmo se você criar um único recurso, como um site da Web, deverá decidir se deseja adicionar esse recurso a um grupo existente ou criar um novo grupo para ele.

A imagem a seguir mostra um grupo de recursos com Application Insights, servidor de banco de dados, banco de dados, plano de serviço de aplicativo e site da Web.

![resumo do grupo de recursos](./media/resource-group-overview/resourcegroupsummary2.png)

Um grupo de recursos também pode ser vinculado a um recurso em outro grupo de recursos. Um recurso é considerado vinculado quando existe uma dependência de implantação entre os recursos em diferentes grupos de recursos. Por exemplo, se um aplicativo Web em um grupo de recursos se conecta ao banco de dados em outro grupo de recursos, esses recursos são vinculados. Você também pode definir explicitamente os links entre os recursos em outro grupo de recursos.

Para obter mais informações sobre a vinculação de recursos, consulte [Vinculação de recursos no Gerenciador de Recursos do Azure](resource-group-link-resources.md)

Se você precisar mover um recurso para um novo grupo de recursos, consulte [Mover recursos para um novo grupo de recursos ou uma assinatura](resource-group-move-resources.md).

No portal de visualização, é possível exibir custos, monitorar eventos e gerenciar alertas com facilidade. A imagem a seguir mostra a cobrança consolidada para um grupo.

![cobrança](./media/resource-group-overview/billing.png)

## Implantação de Modelo

Com o Gerenciador de Recursos do Azure, você pode criar um modelo simples (no formato JSON) que define a implantação e a configuração do seu aplicativo. Este modelo é conhecido como um modelo do Azure e fornece uma forma declarativa de definir a implantação. Usando um modelo, você pode implantar seu aplicativo em todo seu ciclo de vida repetidamente e com a confiança que seus recursos são implantados em um estado consistente.

Dentro do modelo, você pode definir a infraestrutura do seu aplicativo, como configurar essa infraestrutura e como publicar seu código do aplicativo a esta infraestrutura. Você não precisa se preocupar sobre a ordem de implantação porque o Gerenciador de Recursos do Azure analisa as dependências para garantir que os recursos sejam criados na ordem correta.

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um novo recurso ao seu aplicativo e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo especificar a criação de um novo recurso, mas esse recurso já existe, o Gerenciador de Recursos do Azure executa uma atualização em vez de criar um novo ativo. O Gerenciador de Recursos do Azure atualiza o ativo existente para o mesmo estado de um novo.

Você pode especificar parâmetros em seu modelo para conferir personalização e flexibilidade à implantação. Por exemplo, você pode passar valores de parâmetro para personalizar a implantação para seu ambiente de teste. Especificando os parâmetros, você pode usar o mesmo modelo para a implantação em todos os ambientes do seu aplicativo.

Você pode executar todas as etapas necessárias para a implantação e configuração com o modelo e nenhuma etapa manual restante deverá ser necessária. O Gerenciador de Recursos do Azure fornece extensões para cenários que precisam de operações adicionais como a instalação de um software específico que não está incluído na configuração. Se você já estiver usando um serviço de gerenciamento de configuração, como DSC, Chef ou Puppet, poderá continuar trabalhando com esse serviço usando as extensões.

Quando você cria uma solução do Marketplace, ela inclui automaticamente um modelo de implantação. Você não precisa criar seu modelo do zero, pois é possível iniciar com o modelo da sua solução e personalizá-lo para atender às suas necessidades específicas.

Finalmente, o modelo se torna parte do código-fonte do seu aplicativo. Você pode adicioná-lo ao repositório de código-fonte e atualizá-lo conforme a evolução de seu aplicativo. Você pode editar o modelo com o Visual Studio.

Para obter mais informações sobre como definir o modelo, consulte [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).

Para esquemas de modelo, consulte [Esquemas do Gerenciador de Recursos do Azure](https://github.com/Azure/azure-resource-manager-schemas).

Para obter informações sobre como usar um modelo para implantação, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](azure-portal/resource-group-template-deploy.md) e [Implantar um aplicativo complexo de modo previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

Para obter orientações sobre como estruturar seus modelos, consulte [Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md).

## Marcas

O Gerenciador de Recursos do Azure fornece um recurso de marcação que permite classificar os recursos de acordo com suas necessidades de gerenciamento ou de cobrança. Pode ser útil usar marcas quando você tiver um conjunto complexo de grupos de recursos e recursos e se precisar visualizar os ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento.

No portal de visualização, você pode começar a trabalhar com marcas clicando no ícone de marca.

![marcas](./media/resource-group-overview/tags.png)

Recursos não precisam residir no mesmo grupo de recursos para compartilhar uma marca. Você pode criar sua própria taxonomia de marca para garantir que todos os usuários na sua organização usem marcas comuns em vez de aplicarem marcas ligeiramente diferentes inadvertidamente (como por exemplo, "Dept" em vez de "Departamento").

Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md).

## Controle de Acesso

O Gerenciador de Recursos do Azure permite controlar quem tem acesso a ações específicas da sua organização. Ele integra nativamente OAuth e RBAC (controle de acesso baseado em função) à plataforma de gerenciamento e aplica esse controle de acesso a todos os serviços em seu grupo de recursos. Você pode adicionar os usuários à plataforma predefinida e funções específicas ao recurso, bem como aplicar essas funções a uma assinatura, grupo de recursos ou recurso para limitar o acesso. Por exemplo, você pode aproveitar a função predefinida chamada SQL DB Contributor que permite aos usuários gerenciar bancos de dados, mas não os servidores de banco de dados ou políticas de segurança. Adicione usuários da sua organização que precisam desse tipo de acesso à função SQL DB Contributor e aplique a função à assinatura, o grupo de recursos ou recurso.

No portal de visualização, você pode definir o controle de acesso clicando no botão de acesso.

![controle de acesso de usuários](./media/resource-group-overview/access.png)

O Gerenciador de Recursos do Azure registra automaticamente as ações do usuário para auditoria.

Você pode bloquear explicitamente recursos essenciais para impedir que os usuários possam excluí-los ou modificá-los. Para obter mais informações, consulte [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

Para obter mais informações sobre o controle de acesso baseado em função, consulte [Controle de acesso baseado em função no portal de visualização do Microsoft Azure](./role-based-access-control-configure.md).

Para obter exemplos de configuração de políticas de acesso, consulte [Gerenciar e auditar o acesso a recursos](azure-portal/resource-group-rbac.md).

Para ver as práticas recomendadas, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md)

## Camada de Gerenciamento Consistente

O Gerenciador de Recursos do Azure fornece operações completamente compatíveis por meio do PowerShell do Azure, CLI do Azure para Mac, Linux e Windows, o Portal do Azure ou a API REST. Você pode usar a interface que funciona melhor para você e alternar rapidamente entre as interfaces sem problemas. O portal exibe até mesmo notificação para ações executadas fora do portal.

Para obter informações sobre o PowerShell, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos](./powershell-azure-resource-manager.md) e [Cmdlets do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Para obter informações sobre a CLI do Azure, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./virtual-machines/xplat-cli-azure-resource-manager.md).

Para obter informações sobre a API REST, consulte [Referência da API REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Para obter informações sobre o uso do portal de visualização, consulte [Usando o Portal de Visualização do Azure para gerenciar os recursos do Azure](azure-portal/resource-group-portal.md).

## Próximas etapas

- Para saber mais sobre a criação de modelos, consulte [Criando modelos](./resource-group-authoring-templates.md)
- Para implantar o modelo criado, consulte [Implantando modelos](azure-portal/resource-group-template-deploy.md)
- Para entender as funções que você pode usar em um modelo, consulte [Funções de modelo](./resource-group-template-functions.md)
- Para obter orientação sobre como criar seus modelos, consulte [Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md)

<!---HONumber=August15_HO6-->