---
title: Criar artefatos personalizados para sua VM do DevTest Labs | Microsoft Docs
description: "Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e86af029422340bdfa38ba233f0ed61f6f1d8ca2
ms.lasthandoff: 03/17/2017


---
# <a name="create-custom-artifacts-for-your-devtest-labs-vm"></a>Criar artefatos personalizados para suas VM dos Laboratórios de Desenvolvimento/Teste.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Visão geral
**Artefatos** são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Um artefato é composto por um arquivo de definição de artefato e outros arquivos de script que são armazenados em uma pasta em um repositório git. Arquivos de definição de artefato são formados por JSON e expressões que você pode usar para especificar o que você deseja instalar em uma VM. Por exemplo, você pode definir o nome do artefato, comando a ser executado e parâmetros disponibilizados quando o comando é executado. Você pode consultar por nome outros arquivos de script dentro do arquivo de definição de artefato.

## <a name="artifact-definition-file-format"></a>Formato do arquivo de definição de artefato
O exemplo a seguir mostra as seções que compõem a estrutura básica de um arquivo de definição.

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nome do elemento | Obrigatório? | Descrição |
| --- | --- | --- |
| $schema |Não |Local do arquivo de esquema JSON que ajuda a testar a validade do arquivo de definição. |
| título |Sim |Nome do artefato exibido no laboratório. |
| Descrição |Sim |Descrição do artefato exibido no laboratório. |
| iconUri |Não |URI do ícone exibido no laboratório. |
| targetOsType |Sim |Sistema operacional da VM na qual o artefato será instalado. As opções com suporte são: Windows e Linux. |
| parameters |Não |Valores fornecidos quando o comando de instalação do artefato é executado em um computador. Isso ajuda a personalizar o artefato. |
| runCommand |Sim |Comando de instalação do artefato executado em uma VM. |

### <a name="artifact-parameters"></a>Parâmetros do artefato
Na seção de parâmetros do arquivo de definição, especifique os valores que um usuário pode inserir ao instalar um artefato. Você pode consultar esses valores no comando de instalação do artefato.

Defina os parâmetros com a seguinte estrutura:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nome do elemento | Obrigatório? | Descrição |
| --- | --- | --- |
| type |Sim |Tipo do valor do parâmetro. Consulte a seguir a lista de tipos permitidos: |
| displayName |Sim |Nome do parâmetro exibido para um usuário no laboratório. | |
| Descrição |Sim |Descrição do parâmetro exibido no laboratório. |

Os tipos permitidos são:

* string - qualquer cadeia de caracteres JSON válida
* int - qualquer inteiro JSON válido
* bool - qualquer booliano JSON válido
* array - qualquer matriz JSON válida

## <a name="artifact-expressions-and-functions"></a>Expressões e funções de artefatos
Você pode usar expressões e funções para construir o comando de instalação do artefato.
As expressões são colocadas entre colchetes ([ e ]) e avaliadas quando o artefato é instalado. As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre retornam outro valor JSON. Se precisar usar uma cadeia de caracteres literal que comece com um colchete [, você deverá usar dois colchetes [[.
Normalmente, você usa expressões com funções para construir um valor. Assim como no JavaScript, as chamadas de função são formatadas como functionName(arg1,arg2,arg3)

A lista a seguir mostra funções comuns.

* Parameters(ParameterName): Retorna um valor de parâmetro fornecido quando o comando do artefato é executado.
* concat(arg1,arg2,arg3, …..) -     Combina diversos valores da cadeia de caracteres. Essa função pode conter qualquer número de argumentos.

O exemplo a seguir mostra como usar expressões e funções para construir um valor.

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Criar um artefato personalizado
Crie seu artefato personalizado executando as etapas abaixo:

1. Instalar um editor de JSON: você precisará de um editor de JSON para trabalhar com arquivos de definição de artefato. Recomendamos o uso do [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e OS X.
2. Obter um exemplo de artifactfile.json: verifique os artefatos criados pela equipe dos Laboratórios de Desenvolvimento/Teste do Azure em nosso [repositório GitHub](https://github.com/Azure/azure-devtestlab) , no qual criamos uma vasta biblioteca de artefatos que ajudarão você a criar seus próprios artefatos. Baixe um arquivo de definição de artefato e faça as alterações nele para criar seus próprios artefatos.
3. Usar o IntelliSense: aproveite o IntelliSense para ver os elementos válidos que podem ser usados para construir um arquivo de definição de artefato. Você também pode ver as opções diferentes de valores para um elemento. Por exemplo, o IntelliSense mostra a você as duas opções, Windows ou Linux, ao editar o elemento **targetOsType** .
4. Armazenar o artefato em um repositório git
   
   1. Crie um diretório separado para cada artefato, em que o nome do diretório é igual ao nome do artefato.
   2. Armazene o arquivo de definição de artefato (artifactfile.json) no diretório que você criou.
   3. Armazene os scripts referenciados pelo comando de instalação de artefato.
      
      Veja um exemplo de como uma pasta de artefatos pode ser:
      
      ![Exemplo de repositório git de artefatos](./media/devtest-lab-artifact-author/git-repo.png)
5. Adicione o repositório de artefatos ao laboratório: consulte o artigo [Adicionar um repositório de artefato Git a um laboratório](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Como solucionar problemas de falha de Artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Ingressar uma VM ao domínio de AD existente usando o modelo do ARM no Laboratório de Teste de Desenvolvimento do Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório de artefatos Git a um laboratório](devtest-lab-add-artifact-repo.md).


