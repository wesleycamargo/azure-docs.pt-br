Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Descreveremos cada parâmetro no modelo.

### logicAppName

O nome do aplicativo lógico a criar.

    "logicAppName": {
        "type": "string"
    }

### hostingPlanName

O nome do plano do Serviço de Aplicativo a criar para hospedar o aplicativo lógico.
    
    "hostingPlanName": {
      "type": "string",
      "metadata": {
        "description": "The name of the App Service plan to create for hosting the logic app."
      }
    }

### flowSkuName

A camada de preços do aplicativo lógico.

    "flowSkuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Free",
        "Basic",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The pricing tier for the logic app."
      }
    },


O modelo define os valores que são permitidos para esse parâmetro (Gratuito, Básico, Standard ou Premium) e atribui um valor padrão (Padrão) se nenhum valor é especificado.

### hostingSkuName

O tipo de preço do plano do Serviço de Aplicativo.

    "hostingSkuName": {
      "type": "string",
      "defaultValue": "S1",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "metadata": {
        "description": "Describes plan's pricing tier and instance size."
      }
    },


### hostingSkuCapacity

O tamanho da instância do aplicativo.

    "hostingSkuCapacity": {
      "type": "int",
      "defaultValue": 1,
      "minValue": 1,
      "metadata": {
        "description": "Describes plan's instance count"
      }
    },


O modelo define os valores que são permitidos para esse parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor é especificado. Os valores correspondem a pequeno, médio e grande.

