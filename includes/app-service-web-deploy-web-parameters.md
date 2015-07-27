Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Descreveremos cada parâmetro no modelo.

### siteName

O nome do aplicativo Web que você deseja criar.

    "siteName":{
      "type":"string"
    }

### hostingPlanName

O nome do plano do Serviço de Aplicativo a usar para hospedar o aplicativo Web.
    
    "hostingPlanName":{
      "type":"string"
    }

### siteLocation

O local a ser usado para criar o aplicativo Web e o plano de hospedagem. Ele deve ser um dos locais do Azure que dão suporte a aplicativos Web.

    "siteLocation":{
      "type":"string"
    }

### sku

A camada de preços do plano de hospedagem.

    "sku":{
      "type":"string",
      "allowedValues":[
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue":"Free"
    }

O modelo define os valores que são permitidos para esse parâmetro (Gratuito, Compartilhado Básico ou Standard) e atribui um valor padrão (Gratuito) se nenhum valor é especificado.

### workerSize

O tamanho da instância do plano de hospedagem (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
O modelo define os valores que são permitidos para esse parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor é especificado. Os valores correspondem a pequeno, médio e grande.

<!---HONumber=July15_HO3-->