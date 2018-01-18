---
title: "Personalização das configurações de segurança do sistema operacional na Central de segurança do Azure [visualização] | Microsoft Docs"
description: "Este artigo ensina a personalizar avaliações da central de segurança"
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
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Personalização das configurações de segurança do sistema operacional na Central de segurança do Azure [visualização]

Saiba como personalizar avaliações de configuração de segurança do sistema operacional na Central de segurança do Azure usando este passo a passo.

## <a name="what-are-os-security-configurations"></a>Quais são as configurações de segurança do sistema operacional?

A Central de segurança do Azure monitora as configurações de segurança usando um conjunto de mais de 150 regras recomendadas para proteger o sistema operacional, incluindo regras relacionadas a firewalls, auditoria, políticas de senha e muito mais. Se um computador foi encontrado com uma configuração vulnerável, será gerada uma recomendação de segurança.

A personalização das regras pode ajudar as organizações a controlar quais opções de configuração são as mais adequadas para o seu ambiente. Esse recurso permite aos usuários definir uma política de avaliação personalizada e aplicá-la em todos os computadores aplicáveis na assinatura.

> [!NOTE]
> - No momento, a personalização da Configuração de Segurança do sistema operacional está disponível somente para os seguintes sistemas operacionais: Windows Server 2008, 2008R2, 2012, 2012R2.
- A configuração se aplica a todas as VMs e computadores conectados a todos os espaços de trabalho sob a assinatura selecionada.
- A personalização da configuração de segurança do sistema operacional está disponível apenas na camada padrão da Central de segurança.
>
>

Como personalizar as regras de configuração de segurança do sistema operacional?

Você pode personalizar as regras de configuração de segurança do sistema operacional habilitando e desabilitando uma regra específica, alterando a configuração desejada para uma regra existente e adicionando uma nova regra com base nos tipos de regra com suporte (registro, política de auditoria e política de segurança). Atualmente, a configuração desejada deve ser um valor exato.

As regras novas precisam estar no mesmo formato e estrutura das outras regras existentes do mesmo tipo.

> [!NOTE]
> Para personalizar as configurações de segurança do sistema operacional, você deve ser atribuído à função de proprietário da assinatura, colaborador da assinatura ou o administrador da segurança.
>
>

## <a name="customize-security-configuration"></a>Personalização da configuração de segurança

Para personalizar a configuração de segurança do sistema operacional padrão na Central de segurança:

1.  Abra o painel **Central de Segurança**.

2.  No menu principal da Central de segurança, selecione **Política de segurança**.  **Central de segurança - política de segurança** abrirá.

3.  Selecione a assinatura que você deseja personalizar.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. Em **COMPONENTES DA POLÍTICA**, selecione **Editar configurações de segurança**.

6.  **Editar configurações de segurança** abrirá. Siga as etapas realçadas na tela para baixar, editar e carregar o arquivo modificado.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > Por padrão, o arquivo de configuração a ser baixado está em formato *json*. Para instruções de como modificar esse arquivo, acesse [Personalizar o arquivo de configuração](#customize-the-configuration-file).
  >

7. Depois de salvar com êxito o arquivo, a configuração é aplicada a todas as VMs e computadores conectados a todos os espaços de trabalho sob a assinatura selecionada. Esse processo pode demorar alguns minutos, dependendo do tamanho da infraestrutura. Selecione **Salvar** para confirmar a alteração. Caso contrário, a política não é armazenada.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

A qualquer momento, você pode redefinir a configuração de política atual para o estado de política-padrão selecionando a opção **Redefinir** em **Editar as regras de configuração de segurança do sistema operacional**. Ao escolher essa opção, você receberá o seguinte alerta pop-up. Clique em **Sim** para confirmar.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalização do arquivo de configuração

No arquivo de personalização de cada versão do sistema operacional com suporte há um conjunto de regras. Cada conjunto de regras tem seu próprio nome e uma ID exclusiva, conforme mostrado no exemplo a seguir:

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Este arquivo foi editado usando o Visual Studio, mas você também pode usar o bloco de notas, contanto que o plug-in JSON Viewer esteja instalado.
>
>

Ao editar esse arquivo, você pode modificar uma ou todas as regras. Cada conjunto de regras inclui uma seção de *regras* que contém as regras, divididas em 3 categorias: registro, política de auditoria e política de segurança, conforme mostrado abaixo:

![](media/security-center-customize-os-security-config/rules-section.png)

Cada categoria tem seu próprio conjunto de atributos. Para as regras existentes, você pode fazer alterações em um dos seguintes:

- expectedValue: o tipo de dados do atributo desse campo deve corresponder aos valores com suporte por cada tipo de regra, por exemplo:

  - baselineRegistryRules: o valor deve corresponder a [regValueType] (https://msdn.microsoft.com/library/windows/desktop/ms724884(v=vs.85) definidos na regra.

  - baselineAuditPolicyRules: o valor deve ser uma das seguintes cadeias de caracteres:

    - Êxito e Falha

    - Sucesso

  - baselineSecurityPolicyRules: o valor deve ser uma das seguintes cadeias de caracteres:

    - "Ninguém"

    - Lista de grupos de usuários permitidos, por exemplo: "Administradores, Operadores de Backup"

-   state: cadeia de caracteres que pode conter as opções "Disabled" ou "Enabled". Para esta versão prévia privada, a cadeia de caracteres diferencia maiúsculas de minúsculas.

Esses são os únicos campos que podem ser configurados. Se você violar o tamanho ou o formato do arquivo, não poderá salvar a alteração. A seguinte mensagem de erro ocorre quando o arquivo não pode ser processado:

![](media/security-center-customize-os-security-config/invalid-json.png)

Acesse [Códigos de erro](#error-codes) para ver a lista de erros em potencial.

Abaixo há exemplos de como essas regras e os atributos (em negrito) podem ser alterados:

**Seção de regras:** baselineRegistryRules
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
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**Seção de regras:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**Seções de regras:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

Há algumas regras que estão duplicadas para os diferentes tipos de sistema operacional. As regras duplicadas têm o mesmo "originalId".

## <a name="adding-a-new-custom-rule"></a>Como adicionar uma nova regra personalizada

Você também pode criar uma nova regra. Antes de criar uma nova regra, tenha em mente as seguintes restrições:

-   Versão do esquema, *baselineId* e *baselineName* não podem ser alterados.

-   Não é possível remover o conjunto de regras.

-   Não é possível adicionar o conjunto de regras.

-   O número máximo de regras permitido (incluindo as regras-padrão) é de 1000 regras.

Novas regras personalizadas são marcadas com uma nova fonte personalizada (!= "Microsoft"). O campo *ruleId* pode ser nulo ou vazio. Se estiver vazio, a Microsoft gera um. Se não estiver vazio, ele deve ter um GUID exclusivo válido entre todas as regras (padrão e personalizada). Analise as restrições abaixo sobre os campos principais:

-   *originalId* - pode ser nulo ou vazio. Se *originalId* não for vazio, ele deve ser um GUID válido.

-   *cceId* - pode ser nulo ou vazio. Se *cceId* não for vazio, ele deve ser exclusivo.

-   *ruleType* - um dos: Registry, AuditPolicy ou SecurityPolicy.

-   *Severity* - um dos: Unknown, Critical, Warning ou Informational.

-   *analyzeOperation* - deve ser Equals.

-   *auditPolicyId* - deve ser um GUID válido.

-   *regValueType* -deve ser um destes: Int, Long, String ou MultipleString.

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
**Política de auditoria:**
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

Se o arquivo de configuração enviado for inválido devido a erros em valores ou formatação, será exibido um erro. Você pode baixar um relatório de erros detalhados em formato csv para corrigir os erros antes de reenviar um arquivo de configuração corrigido.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Exemplo de arquivo de erros:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Códigos do Erro

A lista a seguir tem todos os erros em potencial:

| **Erro**                                | **Descrição**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | A propriedade "schemaVersion" foi encontrada inválida ou vazia. Esse valor deve ser definido como "{0}".                                                         |
| BaselineInvalidStringError               | A propriedade "{0}" não pode conter "\\n".                                                                                                         |
| BaselineNullRuleError                    | A lista de regras de configuração de linha de base contém uma regra com o valor "null".                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID "{0}" não é exclusiva.                                                                                                                  |
| BaselineRuleEmptyProperty                | Propriedade: "{0}" está ausente ou inválida.                                                                                                       |
| BaselineRuleIdNotInDefault               | A regra tem uma propriedade de origem "Microsoft", mas não foi encontrada no conjunto de regras-padrão da Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | A ID da regra não é exclusiva.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A propriedade "{0}" foi considerada inválida. O valor não é um Guid válido.                                                                             |
| BaselineRuleInvalidHive                  | Hive deve ser LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | O nome da regra não é exclusivo.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A regra não foi encontrada na configuração nova. A regra não pode ser excluída.                                                                     |
| BaselineRuleNotFoundError                | A regra não foi encontrada no conjunto de regras-padrão da linha de base.                                                                                        |
| BaselineRuleNotInPlace                   | A regra corresponde a uma regra-padrão com o tipo {0} e está listada na lista de {1}.                                                                       |
| BaselineRulePropertyTooLong              | A propriedade: "{0}" é muito longa. Comprimento máximo permitido: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | O valor esperado "{0}" não corresponde ao tipo de valor de registro que está definido.                                                              |
| BaselineRulesetAdded                     | O conjunto de regras com a ID "{0}" não foi encontrado na configuração-padrão. O conjunto de regras não pode ser adicionado.                                               |
| BaselineRulesetIdMustBeUnique            | O conjunto de regras de linha de base "{0}" fornecido deve ser exclusivo.                                                                                           |
| BaselineRulesetNotFound                  | O conjunto de regras com a ID "{0}" e nome "{1}" não foi encontrado na configuração especificada. O conjunto de regras não pode ser excluído.                                |
| BaselineRuleSourceNotMatch               | A regra com a ID "{0}" já está definida.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | O tipo da regra-padrão é "{0}".                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | O tipo real para a regra é: {0}, mas a propriedade ruleType é: {1}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Somente as propriedades "expectedValue" e "state" podem ser alteradas.                                                                       |
| BaselineTooManyRules                     | O número máximo permitido de regras personalizadas é {0}. A configuração fornecida contém {1} regras. ({2} regras-padrão + {3} regras personalizadas. |
| ErrorNoConfigurationStatus               | Nenhuma configuração de status encontrada. Informe o status da configuração desejada - "Default" ou "Custom".                                    |
| ErrorNonEmptyRulesetOnDefault            | O estado da configuração é definido como default. A lista de BaselineRulesets deve ser nula ou vazia.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | O status da configuração fornecido é "Custom", mas a propriedade "baselineRulesets" é nula ou vazia.                                             |
| ErrorParsingBaselineConfig               | A configuração fornecida é inválida. Um ou mais valores definidos possuem um valor nulo ou inválido.                                  |
| ErrorParsingIsDefaultProperty            | O valor fornecido em "configurationStatus" "{0}" é inválido. O valor pode ser apenas "Default" ou "Custom".                                         |
| InCompatibleViewVersion                  | Versão exibida: {0} NÃO tem suporte neste tipo de espaço de trabalho.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A configuração de linha de base especificada foi encontrada com um ou mais tipos de erros de validação.                                                          |
| ViewConversionError                      | A versão da exibição é mais antiga do que o espaço de trabalho suporta. Falha na conversão de exibição: {0}                                                                 |

Se você não tiver permissões suficientes, pode receber um erro generalizado:

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como personalizar avaliações de configuração de segurança do sistema operacional na Central de Segurança. Para saber mais sobre as regras de configuração e correções, confira:

- [Regras de linhas de base e identificadores de configuração comum da Central de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A Central de Segurança usa a Common Configuration Enumeration (CCE) para atribuir identificadores exclusivos para as regras de configuração. Visite o site da [CCE](https://nvd.nist.gov/config/cce/index) para obter mais informações.
- [Corrija as configurações de segurança](security-center-remediate-os-vulnerabilities.md) mostra como resolver vulnerabilidades quando sua configuração de sistema operacional não coincide com as regras de configuração de segurança recomendadas.
