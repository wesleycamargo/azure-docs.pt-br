---
title: Testar módulos do Terraform no Azure usando o Terratest
description: Saiba como usar o Terratest para testar seus módulos do Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, conta de armazenamento, azure, terratest, teste de unidade, teste de integração
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638697"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Testar módulos do Terraform no Azure usando o Terratest

Os módulos do Terraform são usados para criar componentes que podem ser testados, reutilizados e combinados. Eles incorporam encapsulamento no mundo de "infraestrutura como código".

Assim como acontece com outros componentes de software, a garantia de qualidade desempenha um papel importante em módulos do Terraform. Infelizmente, há pouca documentação disponível explicando como criar testes de unidade e testes de integração em módulos do Terraform. Este tutorial apresenta uma infraestrutura de teste e melhores práticas que adotamos durante a compilação de nossos [módulos do Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Depois de considerar todas as infraestruturas de testes mais populares, optamos por usar o [Terratest](https://github.com/gruntwork-io/terratest). O Terratest é implementado como uma biblioteca Go. Ele fornece um conjunto de padrões e funções auxiliares para tarefas de teste de infraestrutura comuns, como fazer solicitações HTTP e SSH para uma máquina virtual específica. Algumas das principais vantagens do Terratest são:

- **Fornecer auxiliares práticos para verificar a infraestrutura.** Esse recurso é útil quando você deseja verificar sua infraestrutura real no ambiente real.
- **A estrutura de pastas é organizada claramente.** Os casos de teste serão organizados claramente e seguirão a [estrutura de pasta do módulo do Terraform padrão](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Todos os casos de teste são escritos em linguagem Go.** Como a maioria dos desenvolvedores de Terraform já são desenvolvedores de Go, o uso do Terratest elimina a necessidade de aprender outra linguagem de programação. Além disso, as únicas dependências necessárias para executar casos de teste no Terratest são Go e Terraform.
- **Essa infraestrutura é altamente extensível.** Não é difícil de estender funções adicionais usando o Terratest como base, por exemplo, recursos específicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este guia prático independe da plataforma; ele pode ser executado no Windows, no Linux ou no MacOS. Antes de continuar, instale o seguinte software:

- **A linguagem de programação Go**: os casos de teste do Terraform escritos em [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) é uma ferramenta de gerenciamento de dependências para a linguagem Go.
- A **CLI do Azure**: a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) é uma ferramenta de linha de comando para gerenciar recursos do Azure. (O Terraform dá suporte à autenticação no Azure por meio de uma entidade de serviço ou [por meio da CLI do Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: usaremos o [executável do mage](https://github.com/magefile/mage/releases) para saber como simplificar a execução dos casos de Terratest. 

## <a name="create-a-static-webpage-module"></a>Criar um módulo de página da Web estático

Neste tutorial, você criará um módulo do Terraform que provisionará uma página da Web estática carregando um único arquivo HTML para o blob de armazenamento do Azure. Esse módulo permitirá que os usuários de todo o mundo acessem a página da Web por meio de uma URL retornada por ele.

> [!NOTE]
> Todos os arquivos descritos nesta seção devem ser criados em seu [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Primeiro, crie uma nova pasta chamada `staticwebpage` na pasta `src` de seu GoPath. A estrutura de pastas geral deste tutorial está descrita abaixo. (Os arquivos marcados com um asterisco `(*)` são o principal foco desta seção.)

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

O módulo de página da Web estática aceita três entradas, que são declaradas em `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Como mencionado anteriormente, este módulo também produzirá uma URL declarada em `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Isso nos leva à lógica principal deste módulo. No total, ele provisionará quatro recursos:
- Um grupo de recursos cujo nome é a entrada `website_name` acrescida de `-staging-rg`.
- Uma conta de armazenamento cujo nome é a entrada `website_name` acrescida de `data001`. Mas, para cumprir as limitações de nome da conta de armazenamento, o módulo removerá todos os caracteres especiais e colocará todo o nome em minúsculas.
- Um contêiner chamado `wwwroot` criado na conta de armazenamento acima.
- Um único arquivo HTML lido na entrada `html_path` e carregado em `wwwroot/index.html`.

A lógica de módulo de página da Web estática é implementada em `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Teste de unidade

Tradicionalmente, o Terratest é uma ferramenta projetada para testes de integração, o que significa que ele provisionará recursos reais em um ambiente real. Às vezes, esses trabalhos podem se tornar particularmente grandes, especialmente quando você tem muitos recursos a serem provisionados. A lógica de conversão de nomes de conta de armazenamento descrita na seção anterior é um bom exemplo: não precisamos realmente provisionar todos os recursos; queremos apenas ter certeza de que a lógica de conversão de nomes está correta.

Graças à flexibilidade do Terratest, isso é feito facilmente com o uso de testes de unidade. Testes de unidade são casos de teste locais em execução (embora o acesso à internet ainda seja necessário) apenas com os comandos `terraform init` e `terraform plan`; os casos de teste de unidade analisarão a saída de `terraform plan` e procurarão os valores de atributo de comparação.

O restante desta seção descreve como usar o Terratest a fim de implementar um teste de unidade para ter certeza de que a lógica de conversão de nomes de conta de armazenamento está correta. Estamos interessados somente nos arquivos marcados com um asterisco `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Primeiro, um arquivo HTML vazio `./test/fixtures/storage-account-name/empty.html` é apenas um espaço reservado.

O arquivo `./test/fixtures/storage-account-name/main.tf` é o esqueleto do caso de teste. Ele aceita uma entrada `website_name`, que também é a entrada dos testes de unidade. Sua lógica é exibida aqui:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Por fim, o componente principal é a implementação dos testes de unidade: `./test/storage_account_name_unit_test.go`

Se você for um desenvolvedor Go, notará que ele corresponde à assinatura de uma função de teste do Go clássica ao aceitar um argumento do tipo `*testing.T`.

No corpo do teste de unidade, temos um total de cinco casos definidos na variável `testCases` (chave como entrada e valor como saída esperada). Para cada caso de teste de unidade, primeiro executaremos `terraform init` buscando a pasta de acessório de teste (`./test/fixtures/storage-account-name/`). 

Depois disso, um comando `terraform plan` com a entrada do caso de teste específico (dê uma olhada na definição `website_name` em `tfOptions`) salvará o resultado em `./test/fixtures/storage-account-name/terraform.tfplan` (que não está listado na estrutura de pastas geral).

Em seguida, esse arquivo de resultado será analisado como uma estrutura de código legível usando o analisador de plano oficial do Terraform.

Vamos procurar agora os atributos que nos interessam (nesse caso, o `name` da `azurerm_storage_account`) e compará-los com a saída esperada.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Para executar os testes de unidade, você precisará concluir as etapas a seguir na linha de comando.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Você verá o resultado do teste de Go tradicional depois de aproximadamente um minuto.

### <a name="integration-test"></a>Teste de integração

Ao contrário dos testes de unidade, os testes de integração precisam provisionar recursos para um ambiente real de ponta a ponta. O Terratest é bom para isso. Já que a melhor prática do módulo do Terraform também recomenda a pasta `examples` que contém alguns exemplos de ponta a ponta, por que não testar esses exemplos apenas como testes de integração? Nesta seção, vamos nos concentrar em três arquivos, cada um marcado com um asterisco `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Primeiro, vamos começar com os exemplos. Uma nova pasta de exemplo chamada `hello-world/` é criada na pasta `./examples/`. Aqui, fornecemos uma página HTML simples para carregamento `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

O exemplo do Terraform `./examples/hello-world/main.tf` é semelhante ao mostrado no teste de unidade, com apenas uma grande diferença: ele também imprime a URL do HTML carregado chamado `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

O Terratest e a função de teste Go clássica aparecem novamente no arquivo de teste de integração `./test/hello_world_example_test.go`.

Ao contrário dos testes de unidade, os testes de integração criarão os recursos reais no Azure e, por isso, você precisa ter cuidado para evitar conflitos de nome. (Preste atenção principalmente em alguns nomes globalmente exclusivos, como o nome da conta de armazenamento.) Portanto, a primeira etapa da lógica do teste é gerar um `websiteName` aleatório usando a função `UniqueId()` fornecida pelo TerraTest. Essa função gerará um nome aleatório que contém letras minúsculas, letras maiúsculas ou números. `tfOptions` direciona todos os comandos do Terraform para a pasta `./examples/hello-world/` e também faz com que `website_name` seja definido como o `websiteName` aleatório.

Em seguida, `terraform init`, `terraform apply` e `terraform output` são executados individualmente. Usamos outra função auxiliar `HttpGetWithCustomValidation()` fornecida pelo Terratest para fazer com que esse HTML seja carregado para a URL `homepage` de saída retornada por `terraform output`, comparando o código de status HTTP Get com `200` e procurando algumas palavras-chave no conteúdo HTML. Por fim, `terraform destroy` tem a “promessa” de ser executado aproveitando o recurso `defer` da linguagem Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Para executar os testes de integração, você precisará concluir as etapas a seguir na linha de comando.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Você verá o resultado do teste de Go tradicional depois de aproximadamente dois minutos. É claro, você também pode executar ambas as unidades de testes, além dos testes de integração, executando:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Como pode ver, os testes de integração demoram muito mais do que os testes de unidade (dois minutos para um caso de integração e um minuto para cinco casos de unidade). Porém, ainda cabe a você decidir quando usar testes de unidade e quando aproveitar testes de integração. Normalmente, preferimos usar testes de unidade para lógica complexa usando funções Terraform HCL e testes de integração a partir da perspectiva de ponta a ponta de um usuário.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Usar mage para simplificar casos do Terratest em execução 

Como foi visto, a execução de casos de teste no shell não é uma tarefa fácil porque você precisa navegar até diretórios diferentes e executar comandos diferentes. É por esse motivo que apresentamos o sistema de compilação em nosso projeto. Nesta seção, usaremos um mage do sistema de compilação do Go para fazer o trabalho.

A única coisa exigida pelo mage é um `magefile.go` no diretório raiz de seu projeto (marcados com `(+)` na figura a seguir).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Aqui está um exemplo de `./magefile.go`. Neste script de compilação, escrito em Go, implementamos cinco etapas de compilação:
- `Clean`: essa etapa removerá todos os arquivos gerados ou temporários durante as execuções de teste.
- `Format`: essa etapa executará `terraform fmt` e `go fmt` para formatar sua base de código.
- `Unit`: essa etapa será executada em todos os testes de unidade (usando a convenção de nome de função `TestUT_*`) na pasta `./test/`.
- `Integration`: semelhante a `Unit`, mas em vez de testes de unidade, ele executa testes de integração (`TestIT_*`).
- `Full`: essa etapa executa `Clean`, `Format`, `Unit', and `Integration', em sequência.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Semelhantes às etapas em execução anteriores, você pode usar os seguintes comandos para executar um conjunto completo de testes:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Fique à vontade para substituir a última linha de comando por etapas de mage, por exemplo, `mage unit` ou `mage clean`. Agora, você pode pensar que ainda há muitas linhas de comando aqui e que é uma boa ideia inserir comandos `dep` e `az login` no magefile. Mas não mostraremos o código aqui. Uma etapa adicional ao usar mage é que as etapas podem ser compartilhadas usando o sistema de pacotes do Go. Assim, os magefiles em todos os seus módulos podem ser simplificados, bastando fazer referência a uma implementação comum e declarando dependências (`mg.Deps()`).

> [!NOTE]
> **Opção: definir variáveis de ambiente de entidade de serviço para executar testes de aceitação**
> 
> Em vez de executar `az login` antes dos testes, você pode realizar a autenticação do Azure definindo as variáveis de ambiente da entidade de serviço. O Terraform publica [uma lista de nomes de variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Somente as quatro primeiras dessas variáveis de ambiente são obrigatórias.) O Terraform também publica uma instrução detalhada explicando como [obter o valor dessas variáveis de ambiente.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Terratest, confira [sua página do GitHub](https://github.com/gruntwork-io/terratest). Você pode encontrar algumas informações úteis sobre o mage na [sua página do GitHub](https://github.com/magefile/mage) e na [home page](https://magefile.org/).
