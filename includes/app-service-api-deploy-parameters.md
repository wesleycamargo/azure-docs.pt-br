Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Descreveremos cada parâmetro no modelo.

### gatewayName

O nome do gateway. O aplicativo de API é registrado para este gateway.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

O nome do aplicativo de API a criar. O nome deve conter entre 8 caracteres e 50 caracteres.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

O segredo para o aplicativo de API. Esse valor deve ser uma cadeia de caracteres codificada em base64.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

O local para o novo aplicativo de API.

    "location": {
      "type": "string"
    }

<!---HONumber=July15_HO3-->