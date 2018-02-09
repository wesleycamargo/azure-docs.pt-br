---
title: "Personalizar as configurações de segurança do sistema operacional na Central de Segurança do Azure (versão prévia) | Microsoft Docs"
description: "Este artigo demonstra como personalizar as avaliações da Central de Segurança"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2018
ms.author: terrylan
ms.openlocfilehash: f12441a960db9f1c45bca2a5b95f3669923c7e3d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Personalizar as configurações de segurança do sistema operacional na Central de Segurança do Azure (versão prévia)

Este passo a passo demonstra como personalizar as avaliações de configuração de segurança do sistema operacional na Central de Segurança do Azure.

## <a name="what-are-os-security-configurations"></a>O que são configurações de segurança do sistema operacional?

A Central de Segurança do Azure monitora as configurações de segurança aplicando um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o sistema operacional, incluindo regras relacionadas a firewalls, auditoria, políticas de senha e muito mais. Se uma configuração vulnerável é encontrada em um computador, a Central de Segurança gera uma recomendação de segurança.

Com a personalização das regras, as organizações podem controlar quais opções de configuração são mais adequadas para o ambiente. Defina uma política de avaliação personalizada e, em seguida, aplique-a a todos os computadores aplicáveis na assinatura.

> [!NOTE]
> - No momento, a personalização da configuração de segurança do sistema operacional está disponível somente para os sistemas operacionais Windows Server versões 2008, 2008 R2, 2012 e 2012 R2.
> - A configuração se aplica a todas as VMs e computadores conectados a todos os espaços de trabalho na assinatura selecionada.
> - A personalização da configuração de segurança do sistema operacional está disponível apenas na camada standard da Central de Segurança.
>
>

Você pode personalizar as regras de configuração de segurança do sistema operacional habilitando e desabilitando uma regra específica, alterando a configuração desejada para uma regra existente ou adicionando uma nova regra com base nos tipos de regra com suporte (registro, política de auditoria e política de segurança). Atualmente, a configuração desejada deve ser um valor exato.

As novas regras precisam estar no mesmo formato e estrutura das outras regras existentes do mesmo tipo.

> [!NOTE]
> Para personalizar as configurações de segurança do sistema operacional, você precisa receber a função de *Proprietário da Assinatura*, *Colaborador da Assinatura* ou *Administrador da Segurança*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Personalizar a configuração de segurança padrão do sistema operacional

Para personalizar a configuração padrão de segurança do sistema operacional na Central de Segurança, faça o seguinte:

1.  Abra o painel **Central de Segurança**.

2.  No painel esquerdo, selecione **Política de segurança**.  
    A janela **Central de Segurança – política de segurança** será aberta.

    ![Lista Política de Segurança](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Selecione a assinatura que você deseja personalizar.

4. Em **Componentes da Política**, selecione **Editar configurações de segurança**.  
    A janela **Editar configurações de segurança** será aberta.

    ![A janela “Editar configurações de segurança”](media/security-center-customize-os-security-config/blade.png)

5. No painel direito, siga as etapas para baixar, editar e carregar o arquivo modificado.

   > [!NOTE]
   > Por padrão, o arquivo de configuração a ser baixado está em formato *json*. Para obter instruções sobre como modificar esse arquivo, acesse [Personalizar o arquivo de configuração](#customize-the-configuration-file).
   >

   Depois de salvar o arquivo com êxito, a configuração é aplicada a todas as VMs e computadores conectados a todos os espaços de trabalho na assinatura. O processo geralmente leva alguns minutos, mas pode demorar mais, dependendo do tamanho da infraestrutura.

6. Para confirmar a alteração, selecione **Salvar**. Caso contrário, a política não será armazenada.

    ![O botão Salvar](media/security-center-customize-os-security-config/save-successfully.png)

A qualquer momento, você pode redefinir a configuração de política atual para seu estado padrão. Para fazer isso, na janela **Editar regras de configuração de segurança do sistema operacional**, selecione **Redefinir**. Confirme esta opção selecionando **Sim** na janela pop-up de confirmação.

![A janela Confirmação da redefinição](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalização do arquivo de configuração

No arquivo de personalização, cada versão do sistema operacional compatível tem um conjunto de regras. Cada conjunto de regras tem seu próprio nome e uma ID exclusiva, conforme mostrado no seguinte exemplo:

![O nome e a ID do conjunto de regras](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Este arquivo de exemplo foi editado no Visual Studio, mas você também poderá usar o Bloco de notas se tiver o plug-in Visualizador do JSON instalado.
>
>

Quando você edita o arquivo de personalização, pode modificar uma regra ou todas elas. Cada conjunto de regras inclui uma seção *rules* que é separada em três categorias: Registro, Política de Auditoria e Política de Segurança, conforme mostrado aqui:

![Três categorias de conjunto de regras](media/security-center-customize-os-security-config/rules-section.png)

Cada categoria tem seu próprio conjunto de atributos. Você pode alterar os seguintes atributos:

- **expectedValue**: esse tipo de dados de campo do atributo deve corresponder aos valores com suporte por *tipo de regra*, por exemplo:

  - **baselineRegistryRules**: o valor deve corresponder ao [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) definido na regra.

  - **baselineAuditPolicyRules**: use um dos seguintes valores de cadeia de caracteres:

    - *Êxito e Falha*

    - *Êxito*

  - **baselineSecurityPolicyRules**: use um dos seguintes valores de cadeia de caracteres:

    - *Ninguém*

    - Lista de grupos de usuários permitidos, por exemplo: *Administradores*,  *Operadores de Backup*

-   **state**: a cadeia de caracteres pode conter as opções *Disabled* ou *Enabled*. Para esta versão prévia privada, a cadeia de caracteres diferencia maiúsculas de minúsculas.

Esses são os únicos campos que podem ser configurados. Se você violar o tamanho ou o formato do arquivo, não poderá salvar a alteração. A seguinte mensagem de erro ocorre quando o arquivo não pode ser processado:

![Mensagem de erro de configuração de segurança](media/security-center-customize-os-security-config/invalid-json.png)

Para obter uma lista de outros erros potenciais, consulte [Códigos de erro](#error-codes).

As três seções a seguir contêm exemplos das regras anteriores. Os atributos *expectedValue* e *state* podem ser alterados.

**baselineRegistryRules**
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Algumas regras estão duplicadas para os diferentes tipos de sistema operacional. As regras duplicadas têm o mesmo atributo *originalId*.

## <a name="create-custom-rules"></a>Criar regras personalizadas

Você também pode criar novas regras. Antes de criar uma nova regra, tenha em mente as seguintes restrições:

-   Versão do esquema, *baselineId* e *baselineName* não podem ser alterados.

-   Não é possível remover o conjunto de regras.

-   Não é possível adicionar o conjunto de regras.

-   O número máximo de regras permitidas (incluindo as regras padrão) é 1.000.

Novas regras personalizadas são marcadas com uma nova fonte personalizada (!= "Microsoft"). O campo *ruleId* pode ser nulo ou vazio. Se estiver vazio, a Microsoft gera um. Se não estiver vazio, ele precisará ter um GUID válido exclusivo entre todas as regras (padrão e personalizadas). Examine as seguintes restrições para os principais campos:

-   **originalId**: pode ser nulo ou vazio. Se *originalId* não for vazio, ele deve ser um GUID válido.

-   **cceId**: pode ser nulo ou vazio. Se *cceId* não for vazio, ele deve ser exclusivo.

-   **ruleType**: (selecione uma opção) Registry, AuditPolicy ou SecurityPolicy.

-   **Severidade**: (selecione uma opção) Unknown, Critical, Warning ou Informational.

-   **analyzeOperation**: deve ser *Equals*.

-   **auditPolicyId**: deve ser um GUID válido.

-   **regValueType**: (selecione uma opção) Int, Long, String ou MultipleString.

> [!NOTE]
> Hive deve ser *LocalMachine*.
>
>

Exemplo de uma nova regra personalizada:

**Registro**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Política de segurança**:
```
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Política de auditoria**:
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Falhas de carregamento de arquivo

Se o arquivo de configuração enviado for inválido devido a erros em valores ou na formatação, um erro de falha será exibido. Baixe um relatório .csv detalhado de erros para corrigir os erros antes de reenviar um arquivo de configuração corrigido.

![Mensagem de erro “Falha ao salvar a ação”](media/security-center-customize-os-security-config/invalid-configuration.png)

Exemplo de um arquivo de erro:

![Exemplo de arquivo de erro](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Códigos do Erro

Todos os erros potenciais são listados na seguinte tabela:

| **Erro**                                | **Descrição**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | A propriedade *schemaVersion* foi considerada inválida ou vazia. O valor deve ser definido como *{0}*.                                                         |
| BaselineInvalidStringError               | A propriedade *{0}* não pode conter *\\n*.                                                                                                         |
| BaselineNullRuleError                    | A lista de regras de configuração de linha de base contém uma regra com o valor *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | A CCE-ID *{0}* não é exclusiva.                                                                                                                  |
| BaselineRuleEmptyProperty                | A propriedade *{0}* está ausente ou é inválida.                                                                                                       |
| BaselineRuleIdNotInDefault               | A regra tem uma propriedade de origem *Microsoft*, mas não foi encontrada no conjunto de regras padrão da Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | A ID da regra não é exclusiva.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A propriedade *{0}* foi considerada inválida. O valor não é um GUID válido.                                                                             |
| BaselineRuleInvalidHive                  | O Hive deve ser LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | O nome da regra não é exclusivo.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A regra não foi encontrada na configuração nova. A regra não pode ser excluída.                                                                     |
| BaselineRuleNotFoundError                | A regra não foi encontrada no conjunto de regras de linha de base padrão.                                                                                        |
| BaselineRuleNotInPlace                   | A regra corresponde a uma regra-padrão com o tipo {0} e está listada na lista de {1}.                                                                       |
| BaselineRulePropertyTooLong              | A propriedade *{0}* é muito longa. Comprimento máximo permitido: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | O valor esperado *{0}* não corresponde ao tipo de valor de registro definido.                                                              |
| BaselineRulesetAdded                     | O conjunto de regras com a ID *{0}* não foi encontrado na configuração padrão. Não é possível adicionar o conjunto de regras.                                               |
| BaselineRulesetIdMustBeUnique            | O conjunto de regras de linha de base *{0}* fornecido deve ser exclusivo.                                                                                           |
| BaselineRulesetNotFound                  | O conjunto de regras com a ID *{0}* e o nome *{1}* não foi encontrado na configuração especificada. Não é possível excluir o conjunto de regras.                                |
| BaselineRuleSourceNotMatch               | A regra com a ID *{0}* já está definida.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | O tipo de regra padrão é *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | O tipo real da regra é *{0}*, mas a propriedade *ruleType* é *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Somente as propriedades *expectedValue* e *state* podem ser alteradas.                                                                       |
| BaselineTooManyRules                     | O número máximo de regras personalizadas permitidas é {0} regras. A configuração fornecida contém regras {1}, regras padrão {2} e regras personalizadas {3}. |
| ErrorNoConfigurationStatus               | Nenhuma configuração de status encontrada. Informe o status da configuração desejada: *Default* ou *Custom*.                                    |
| ErrorNonEmptyRulesetOnDefault            | O estado da configuração é definido como padrão. A lista *BaselineRulesets* deve ser nula ou vazia.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | O status da configuração fornecida é *Custom*, mas a propriedade *baselineRulesets* é nula ou vazia.                                             |
| ErrorParsingBaselineConfig               | A configuração fornecida é inválida. Um ou mais dos valores definidos contêm um valor nulo ou um tipo inválido.                                  |
| ErrorParsingIsDefaultProperty            | O valor do *configurationStatus* fornecido *{0}* é inválido. O valor pode ser apenas *Default* ou *Custom*.                                         |
| InCompatibleViewVersion                  | A versão da exibição *{0}* *não* é compatível com este tipo de espaço de trabalho.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A configuração de linha de base especificada foi encontrada com um ou mais tipos de erros de validação.                                                          |
| ViewConversionError                      | A exibição é uma versão mais antiga compatível com o espaço de trabalho. Falha na conversão de exibição: {0}.                                                                 |

Se você não tiver permissões suficientes, poderá receber um erro de falha geral, conforme mostrado aqui:

![Mensagem de erro “Falha ao salvar a ação”](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Próximas etapas
Este artigo explicou como personalizar as avaliações de configuração de segurança do sistema operacional na Central de Segurança. Para saber mais sobre as regras de configuração e correções, confira:

- [Regras de linhas de base e identificadores de configuração comum da Central de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A Central de Segurança usa a CCE (Common Configuration Enumeration) para atribuir identificadores exclusivos a regras de configuração. Para obter mais informações, consulte [CCE](https://nvd.nist.gov/config/cce/index).
- Para resolver vulnerabilidades quando a configuração do sistema operacional não corresponde às regras de configuração de segurança recomendadas, consulte [Corrigir configurações de segurança](security-center-remediate-os-vulnerabilities.md).
