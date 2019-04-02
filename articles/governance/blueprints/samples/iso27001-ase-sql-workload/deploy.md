---
title: Exemplo - diagrama de carga de trabalho do ISO 27001 ASE/SQL - as etapas de implantação
description: As etapas do exemplo de especificações técnicas do ISO 27001 App Service ambiente/SQL Database carga de trabalho de implantação.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4ed51ee5f8cbdc50fa65a189d8f468bd7713a74b
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804149"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implantar o exemplo de plano gráfico de carga de trabalho de banco de dados do ISO 27001 App Service ambiente/SQL

Para implantar o exemplo de plano gráfico de carga de trabalho do Azure BluePrint ISO 27001 aplicativo serviço ambiente/banco de dados SQL, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Implantar o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de plano gráfico
> - Criar um novo projeto de exemplo
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implantar o exemplo de plano gráfico de serviços compartilhados do ISO 27001

Antes que este exemplo de especificação técnica pode ser implantado, o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de plano gráfico deve ser implantado para a assinatura de destino. Sem uma implantação bem-sucedida do exemplo de plano gráfico de serviços compartilhados do ISO 27001, este exemplo de plano gráfico estará sem dependências de infraestrutura e falhar durante a implantação.

> [!IMPORTANT]
> Este exemplo de plano gráfico deve ser atribuído na mesma assinatura que o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de plano gráfico.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Primeiro, implemente o exemplo de projeto, criando um novo projeto em seu ambiente usando o exemplo de como um starter.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o **ISO 27001: Carga de trabalho do SQL/ASE** exemplo de plano gráfico em _outras amostras_ e selecione **usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de plano gráfico de carga de trabalho do ISO 27001 ASE/SQL.
   - **Localização da definição**: Use nas reticências e selecione o grupo de gerenciamento para salvar sua cópia do exemplo para.

1. Selecione a guia _Artefatos_ na parte superior da página ou selecione **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de plano gráfico pode ser personalizada ao seu ambiente e as necessidades, mas essa modificação pode movê-lo para fora da norma ISO 27001.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecioná-lo.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** de sua cópia do exemplo de plano gráfico. Essa propriedade será útil se você fizer uma modificação mais tarde. Fornecer **alterar notas** , como "primeira versão publicada do exemplo ISO 27001 blueprint." Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecioná-lo.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é preenchido previamente para você com base no nome do projeto.
       Alterar conforme necessário ou deixar como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolher uma **publicado** versão da sua cópia do exemplo de plano gráfico.

   - Bloquear atribuição

     Selecione o bloqueio de blueprint configuração para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe o padrão _atribuída do sistema_ gerenciados a opção de identidade.

   - Parâmetros de blueprint

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição de plano gráfico para fornecer a consistência.

     - **Nome da organização**: Insira um nome curto para a sua organização. Essa propriedade é usada principalmente para recursos de nomenclatura.
     - **Compartilhado ID de assinatura do serviço**: ID da assinatura em que o [ISO 27001 Shared Services](../iso27001-shared/index.md) amostra de plano gráfico é atribuída.
     - **Prefixo de endereço de sub-rede padrão**: A notação CIDR para a sub-rede da rede virtual padrão.
       Valor padrão é _10.1.0.0/16_.
     - **Local da carga de trabalho**: Determina o local em que os artefatos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Artefatos para implantar esses serviços fornecem uma opção de parâmetro para o local implantar esse artefato para.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de planta é criada e começa a implantação de artefato. Implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de planta.

> [!WARNING]
> O serviço de especificações técnicas do Azure e os exemplos de especificações técnicas internas estão **sem custos,**. Recursos do Azure são [com preço por produto](https://azure.microsoft.com/pricing/). Use o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de plano gráfico.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista do plano gráfico em parâmetros de artefato:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|DESCRIÇÃO|
|-|-|-|-|
|Grupo de recursos do log Analytics|Grupo de recursos|NOME|**Bloqueado** – concatena as **nome da organização** com `-workload-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do log Analytics|Grupo de recursos|Local padrão|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviço|Define a camada do espaço de trabalho do Log Analytics. Valor padrão é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Local padrão|Região usada para criar o espaço de trabalho do Log Analytics. Valor padrão é _Oeste dos EUA 2_.|
|Grupo de recursos de rede|Grupo de recursos|NOME|**Bloqueado** – concatena as **nome da organização** com `-workload-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Local padrão|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o endereço IP privado a [firewall do Azure](../../../../firewall/overview.md). Deve ser parte da notação CIDR definida no _ISO 27001: Serviços compartilhados_ parâmetro artefato **prefixo de endereço de sub-rede de Firewall do Azure**. Valor padrão é _10.0.4.4_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|ID da Assinatura de serviços compartilhados|Valor usado para habilitar o emparelhamento VNET entre uma carga de trabalho e serviços compartilhados.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de Rede Virtual|A notação CIDR para a rede virtual. Valor padrão é _10.1.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede padrão|A notação CIDR para a sub-rede da rede virtual padrão. Valor padrão é _10.1.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Endereço IP do ADDS|Endereço IP da primeira VM ADICIONA. Esse valor é usado como VNET DNS personalizado.|
|Grupo de recursos do Cofre de Chaves|Grupo de recursos|NOME|**Bloqueado** – concatena as **nome da organização** com `-workload-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de Chaves|Grupo de recursos|Local padrão|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Key Vault|Modelo do Resource Manager|ID de objeto do AAD|O identificador de objeto do AAD da conta que exige acesso à instância do Key Vault. Nenhum padrão de valor e não pode ser deixado em branco. Para localizar esse valor do portal do Azure, pesquise e selecione "Usuários" em _Services_. Use o _nome_ caixa para filtrar o nome da conta e selecione essa conta. No _perfil de usuário_ , selecione o ícone de "Clique para copiar" ao lado de _ID de objeto_.|
|Modelo do Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU do Cofre de chaves é criado. Valor padrão é _Premium_.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário de administrador do Servidor SQL do Azure|O nome de usuário usado para acessar o servidor SQL do Azure. Deve corresponder ao mesmo valor de propriedade em **modelo de banco de dados SQL**. Valor padrão é _usuário de administrador de sql_.|
|Grupo de recursos de banco de dados SQL do Azure|Grupo de recursos|NOME|**Bloqueado** – concatena as **nome da organização** com `-workload-azsql-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de banco de dados SQL do Azure|Grupo de recursos|Local padrão|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|Nome de usuário de administrador do Servidor SQL do Azure|Nome de usuário para o servidor SQL do Azure. Deve corresponder ao mesmo valor de propriedade em **modelo do Cofre de chaves**. Valor padrão é _usuário de administrador de sql_.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|Senha do administrador do SQL Server do Azure (chave de Cofre de ID de recurso)|A ID de recurso do Cofre de chaves. Use "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e substitua `{subscriptionId}` com sua ID de assinatura e `{orgName}` com o  **Nome da organização** especificações técnicas de parâmetro.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|Senha do administrador do Azure do SQL Server (nome de segredo do Key Vault)|Nome de usuário do SQL do administrador do servidor. Deve corresponder ao valor em **modelo de Key Vault** propriedade **nome de usuário de administrador de servidor SQL do Azure**.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|ID de objeto de administrador do AAD|ID de objeto do AAD do usuário que será atribuído como um administrador do Active Directory. Nenhum padrão de valor e não pode ser deixado em branco. Para localizar esse valor do portal do Azure, pesquise e selecione "Usuários" em _Services_. Use o _nome_ caixa para filtrar o nome da conta e selecione essa conta. No _perfil de usuário_ , selecione o ícone de "Clique para copiar" ao lado de _ID de objeto_.|
|Modelo do Banco de Dados SQL do Azure|Modelo do Resource Manager|Logon de administrador do AAD|Atualmente, as contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administrador. Somente os usuários e grupos de segurança em sua organização podem ser definidos como administrador. Nenhum padrão de valor e não pode ser deixado em branco. Para localizar esse valor do portal do Azure, pesquise e selecione "Usuários" em _Services_. Use o _nome_ caixa para filtrar o nome da conta e selecione essa conta. Sobre o _perfil de usuário_ página, copie o _nome de usuário_.|
|Grupo de recursos do ambiente do serviço de aplicativo|Grupo de recursos|NOME|**Bloqueado** – concatena as **nome da organização** com `-workload-ase-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do ambiente do serviço de aplicativo|Grupo de recursos|Local padrão|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pelo exemplo. Valor padrão é _contoso.com_.|
|Modelo do Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Localização do ASE|Localização do ambiente do serviço de aplicativo. Valor padrão é _Oeste dos EUA 2_.|
|Modelo do Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Retenção de log do Gateway de Aplicativo em dias|Retenção de dados em dias. Valor padrão é _365_.|

## <a name="next-steps"></a>Próximas etapas

Agora que você tiver revisado as etapas para implantar o exemplo de plano gráfico de carga de trabalho de banco de dados do ISO 27001 App Service ambiente/SQL, visite os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Diagrama de carga de trabalho do ISO 27001 App Service ambiente/SQL Database - visão geral](./index.md)
> [diagrama de carga de trabalho de banco de dados do ISO 27001 App Service ambiente/SQL - mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).