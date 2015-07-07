Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Descreveremos cada parâmetro no modelo.

### logicAppName

O nome do aplicativo lógico a criar.

    "logicAppName": {
        "type": "string"
    }

### svcPlanName

O nome do plano do Serviço de Aplicativo a criar para hospedar o aplicativo lógico.
    
    "svcPlanName": {
        "type": "string"
    }

### sku

A camada de preços do aplicativo lógico.

    "sku": {
        "type": "string",
        "defaultValue": "Standard",
        "allowedValues": [
            "Free",
            "Basic",
            "Standard",
            "Premium"
        ]
    }

O modelo define os valores que são permitidos para esse parâmetro (Gratuito, Básico, Standard ou Premium) e atribui um valor padrão (Padrão) se nenhum valor é especificado.

### svcPlanSize

O tamanho da instância do aplicativo.

    "svcPlanSize": {
        "defaultValue": "0",
        "type": "string",
        "allowedValues": [
            "0",
            "1",
            "2"
        ]
    }

O modelo define os valores que são permitidos para esse parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor é especificado. Os valores correspondem a pequeno, médio e grande.

<!---HONumber=62-->