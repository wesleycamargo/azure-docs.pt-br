---
title: Exemplo – serviços compartilhados do ISO 27001 blueprint - as etapas de implantação
description: As etapas do exemplo de plano gráfico de serviços compartilhados do ISO 27001 de implantação.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7fea9b0d00d92c63bb4c8bbdadada9e8eb6a05fe
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202496"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Implantar o exemplo de plano gráfico de serviços compartilhados do Azure BluePrint ISO 27001

Para implantar o exemplo de plano gráfico de serviços compartilhados do Azure BluePrint ISO 27001, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um novo projeto de exemplo
> - Marcar sua cópia da amostra como **publicado**
> - Atribuir a sua cópia do plano gráfico para uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Primeiro, implemente o exemplo de projeto, criando um novo projeto em seu ambiente usando o exemplo de como um starter.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Do **guia de Introdução** página à esquerda, selecione o **Create** botão sob _criar um plano gráfico_.

1. Encontre o **ISO 27001: Serviços compartilhados** exemplo de plano gráfico sob _outras amostras_ e selecione **usar este exemplo**.

1. Insira o _Noções básicas de_ do exemplo de plano gráfico:

   - **Nome do plano gráfico**: Forneça um nome para a sua cópia do exemplo de plano gráfico de serviços compartilhados do ISO 27001.
   - **Local da definição**: Use nas reticências e selecione o grupo de gerenciamento para salvar sua cópia do exemplo para.

1. Selecione o _artefatos_ guia na parte superior da página ou **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de plano gráfico. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar rascunho** quando tiver terminado, examinando o exemplo de plano gráfico.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada em seu ambiente. Ele é criado no **rascunho** modo e deve ser **publicado** antes de ser atribuída e implantada. A cópia do exemplo de plano gráfico pode ser personalizada ao seu ambiente e as necessidades, mas essa modificação pode movê-lo para fora da norma ISO 27001.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Selecione o **Blueprint definições** página à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecioná-lo.

1. Selecione **publicar blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** de sua cópia do exemplo de plano gráfico. Essa propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **alterar notas** , como "primeira versão publicada do exemplo ISO 27001 blueprint." Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo de plano gráfico foi atualizado com êxito **publicado**, ele pode ser atribuído a uma assinatura dentro do grupo de gerenciamento que ele foi salvo. Esta etapa é onde os parâmetros são fornecidos para tornar cada implantação da cópia do exemplo de plano gráfico exclusivo.

1. Selecione **todos os serviços** e pesquise e selecione **diretiva** no painel esquerdo. Sobre o **diretiva** página, selecione **plantas**.

1. Selecione o **Blueprint definições** página à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecioná-lo.

1. Selecione **Assign blueprint** na parte superior da página de definição de plano gráfico.

1. Forneça os valores de parâmetro para a atribuição de planta:

   - Noções Básicas

     - **Assinaturas**: Selecione um ou mais assinaturas que estão no grupo de gerenciamento que você salvou sua cópia do exemplo de plano gráfico para. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada um usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é preenchido previamente para você com base no nome do projeto.
       Alterar conforme necessário ou deixar como está.
     - **Localização**: Selecione uma região para a identidade gerenciada seja criado. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de plano gráfico**: Escolher uma **publicado** versão da sua cópia do exemplo de plano gráfico.

   - Bloquear atribuição

     Selecione o bloqueio de blueprint configuração para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe o padrão _atribuída do sistema_ gerenciados a opção de identidade.

   - Parâmetros de plano gráfico

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição de plano gráfico para fornecer a consistência.

     - **Nome da organização**: Insira um nome curto para a sua organização. Essa propriedade é usada principalmente para recursos de nomenclatura.
     - **Prefixo de endereço de subrede serviços compartilhados**: Forneça o valor de notação de CIDR de rede os recursos implantados juntos.
     - **Serviços local compartilhado**: Determina o local em que os artefatos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Artefatos para implantar esses serviços fornecem uma opção de parâmetro para o local implantar esse artefato para.
     - **Local permitidos (política: Especificações técnicas de iniciativa para ISO 27001)**: Valor que indica os locais permitidos para grupos de recursos e recursos.
     - **Espaço de trabalho de análise de logs para agentes de VM (política: Especificações técnicas de iniciativa para ISO 27001)**: Especifica a ID do recurso de um espaço de trabalho. Esse parâmetro usa um `concat` função para construir a ID do recurso.

   - Parâmetros de artefato

     Os parâmetros definidos nesta seção se aplicam para o artefato sob a qual ele está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois elas são definidas durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição de planta é criada e começa a implantação de artefato. Implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de planta.

> [!WARNING]
> O serviço de especificações técnicas do Azure e os exemplos de especificações técnicas internas estão **sem custos,**. Recursos do Azure são [com preço por produto](https://azure.microsoft.com/en-us/pricing/). Use o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de plano gráfico.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista do plano gráfico em parâmetros de artefato:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|[Versão prévia]: Implantar o agente do Log Analytics para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que tem suporte para o sistema operacional Linux para adicionar ao escopo|(Opcional) Valor padrão é _["none"]_.|
|[Versão prévia]: Implantar o Agente do Log Analytics para VMs do Linux|Atribuição de política|Opcional: Lista de imagens VM que tem suporte para o sistema operacional Linux para adicionar ao escopo|(Opcional) Valor padrão é _["none"]_.|
|[Versão prévia]: Implantar o agente do Log Analytics para conjuntos de dimensionamento VM do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|(Opcional) Valor padrão é _["none"]_.|
|[Versão prévia]: Implantar o Agente do Log Analytics para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|(Opcional) Valor padrão é _["none"]_.|
|Tipos de recurso permitidos|Atribuição de política|Tipos de recurso permitidos|Lista de tipos de recursos permitidos para ser implantado. Essa lista é composta de todos os tipos de recursos implantados em serviços compartilhados.|
|SKUs de conta de armazenamento permitidos|Atribuição de política|Permitido SKUs de armazenamento|Lista de diagnóstico registra permitidos de SKUs de conta de armazenamento. Valor padrão é _["Standard_LRS"]_.|
|SKUs de máquina virtual permitidos|Atribuição de política|Lista de SKUs de máquina virtual pode ser implantado. Default value is _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa de blueprint para ISO 27001|Atribuição de política|Tipos de recursos para auditar os logs de diagnóstico|Lista de tipos de recurso de auditoria se a configuração de log de diagnóstico não está habilitada. Os valores aceitáveis podem ser encontrados em [esquemas de logs de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Grupo de recursos do log Analytics|Grupo de recursos|Nome|**Bloqueado** – concatena as **nome da organização** com `-sharedsvsc-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do log Analytics|Grupo de recursos|Localizaçãoização|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviço|Define a camada do espaço de trabalho do Log Analytics. Valor padrão é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Localizaçãoização|Região usada para criar o espaço de trabalho do Log Analytics. Valor padrão é _Oeste dos EUA 2_.|
|Grupo de recursos de rede|Grupo de recursos|Nome|**Bloqueado** – concatena as **nome da organização** com `-sharedsvcs-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Localizaçãoização|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Firewall do Azure|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o endereço IP privado a [firewall do Azure](../../../../firewall/overview.md). Esse valor também é usado como a tabela de rotas padrão na sub-rede de serviços compartilhados. Deve ser parte da notação CIDR definida no **prefixo de endereço de sub-rede de Firewall do Azure**. Valor padrão é _10.0.4.4_.|
|Modelo do Firewall do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de Rede Virtual|A notação CIDR para a rede virtual. Valor padrão é _10.0.0.0/16_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Habilitar a proteção contra DDoS da Rede Virtual|Configura a proteção contra DDoS para a rede virtual. Valor padrão é _verdadeira_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede de serviços de compartilhado|A notação CIDR para a sub-rede de serviços compartilhados. Valor padrão é _10.0.0.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede DMZ|A notação CIDR para a sub-rede de rede de Perímetro. Valor padrão é _10.0.1.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço de sub-rede de Gateway de Aplicativo|A notação CIDR para a sub-rede de gateway de aplicativo. Valor padrão é _10.0.2.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede de Gateway de Rede Virtual|A notação CIDR da sub-rede do gateway de rede virtual. Valor padrão é _10.0.3.0/24_.|
|Modelo de Rede Virtual e a Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede do Firewall do Azure|A notação CIDR para o [firewall do Azure](../../../../firewall/overview.md) sub-rede. Deve incluir o **endereço IP privado do Azure firewall** parâmetro.|
|Grupo de recursos do Cofre de Chaves|Grupo de recursos|Nome|**Bloqueado** – concatena as **nome da organização** com `-sharedsvcs-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de Chaves|Grupo de recursos|Localizaçãoização|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário de administrador do Jumpbox|Nome de usuário para o jumpbox. Deve corresponder ao mesmo valor de propriedade em **Jumpbox modelo**. Valor padrão é _usuário de administrador jb_.|
|Modelo do Key Vault|Modelo do Resource Manager|Chave SSH ou senha do administrador do Jumpbox|Chave ou senha para a conta na jumpbox. Deve corresponder ao mesmo valor de propriedade em **Jumpbox modelo**. Nenhum padrão de valor e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de usuário do administrador de domínio|O nome de usuário usado para acessar a VM do Active Directory e adicionar outras VMs a um domínio. Deve corresponder ao **usuário administrador de domínio** no valor da propriedade **modelo do Active Directory Domain Services**. Valor padrão é _usuário de administrador de domínio_.|
|Modelo do Key Vault|Modelo do Resource Manager|Senha do administrador de domínio|Senha do usuário do administrador de domínio. Nenhum padrão de valor e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|ID de objeto do AAD|O identificador de objeto do AAD da conta que exige acesso à instância do Key Vault. Nenhum padrão de valor e não pode ser deixado em branco. Para localizar esse valor do portal do Azure, pesquise e selecione "Usuários" em _Services_. Use o _nome_ caixa para filtrar o nome da conta e selecione essa conta. No _perfil de usuário_ , selecione o ícone de "Clique para copiar" ao lado de _ID de objeto_.  |
|Modelo do Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. Valor padrão é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU do Cofre de chaves é criado. Valor padrão é _Premium_.|
|Grupo de recursos do Jumpbox|Grupo de recursos|Nome|**Bloqueado** – concatena as **nome da organização** com `-sharedsvcs-jb-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Jumpbox|Grupo de recursos|Localizaçãoização|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo de Jumpbox|Modelo do Resource Manager|Nome de usuário de administrador do Jumpbox|O nome de usuário usado para acessar a VM jumpbox. Deve corresponder ao mesmo valor de propriedade em **modelo do Cofre de chaves**. Valor padrão é _usuário de administrador jb_.|
|Modelo de Jumpbox|Modelo do Resource Manager|Senha do administrador do Jumpbox (ID do recurso Cofre de chave)|A ID de recurso do Cofre de chaves. Use "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` com sua ID de assinatura e `{orgName}` com o  **Nome da organização** especificações técnicas de parâmetro.|
|Modelo de Jumpbox|Modelo do Resource Manager|Senha do administrador do Jumpbox (nome de segredo do Key Vault)|Nome de usuário do administrador do jumpbox. Deve corresponder ao valor em **modelo de Key Vault** propriedade **nome de usuário de administrador de Jumpbox**.|
|Modelo de Jumpbox|Modelo do Resource Manager|Sistema Operacional do Jumpbox|Determina o sistema operacional da VM jumpbox. Valor padrão é _Windows_.|
|Grupo de recursos do Active Directory Domain Services|Grupo de recursos|Nome|**Bloqueado** – concatena as **nome da organização** com `-sharedsvcs-adds-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Active Directory Domain Services|Grupo de recursos|Localizaçãoização|**Bloqueado** -usa o parâmetro de especificações técnicas.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Nome de usuário do administrador de domínio|Nome de usuário para o jumpbox do ADDS. Deve corresponder ao mesmo valor de propriedade em **modelo do Cofre de chaves**. Valor padrão é _adiciona-admin-user_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha de administrador de domínio (ID do recurso Cofre de chave)|A ID de recurso do Cofre de chaves. Use "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` com sua ID de assinatura e `{orgName}` com o  **Nome da organização** especificações técnicas de parâmetro.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha de administrador de domínio (nome de segredo do Key Vault)|Nome de usuário do administrador do domínio. Deve corresponder ao valor em **modelo de Key Vault** propriedade **nome de usuário de administrador de domínio**.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pelo exemplo. Valor padrão é _contoso.com_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Usuário administrador de domínio|Nome de usuário para a conta do administrador do AD e ingresso de dispositivos para o domínio do AD. Deve corresponder ao **nome de usuário de administrador do AD** no valor da propriedade **modelo do Cofre de chaves**. Valor padrão é _usuário de administrador de domínio_.|
|Modelo do Active Directory Domain Services|Modelo do Resource Manager|Senha do administrador de domínio|Defina os detalhes do Cofre de chaves para armazenar a senha. Nenhum padrão de valor e não pode ser deixado em branco.|

## <a name="next-steps"></a>Próximas etapas

Agora que você tiver revisado as etapas para implantar o exemplo de plano gráfico de serviços compartilhados do ISO 27001, visite os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Diagrama de ISO 27001 Shared Services - visão geral](./index.md)
> [diagrama de serviços compartilhados do ISO 27001 - mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).