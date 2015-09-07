## Baixar e entender o modelo ARM

No github, você pode baixar o modelo ARM existente para criar uma rede virtual e duas sub-redes; faça as alterações que desejar e reutilize-o. Para isso, siga as etapas abaixo.

1. Navegue para https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets.
2. Clique em **azuredeploy.json** e em **RAW**.
3. Salve o arquivo em uma pasta local do computador.
4. Se você estiver familiarizado com modelos ARM, pule para a etapa 7.
5. Abra o arquivo que você acabou de salvar e examine o conteúdo em **parameters** na linha 5. Os parâmetros do modelo ARM fornecem um espaço reservado para valores que podem ser preenchidos durante a implantação.

	| Parâmetro | Descrição |
	|---|---|
	| **local** | Região do Azure em que a rede virtual será criada |
	| **vnetName** | Nome para a nova rede virtual |
	| **addressPrefix** | Espaço de endereço para a rede virtual, no formato CIDR |
	| **subnet1Name** | Nome da primeira rede virtual |
	| **subnet1Prefix** | Bloco CIDR da primeira sub-rede |
	| **subnet2Name** | Nome da segunda rede virtual |
	| **subnet2Prefix** | Bloco CIDR da segunda sub-rede |

	>[AZURE.IMPORTANT]Os modelos ARM mantidos no github pode mudar ao longo do tempo. Certifique-se de verificar o modelo antes de usá-lo.
	
6. Verificar o conteúdo em **recursos** e observe o seguinte:

	- **type**. Tipo de recurso que está sendo criado pelo modelo. Nesse caso, **Microsoft.Network/virtualNetworks**, que representa uma rede virtual.
	- **name**. Nome do recurso. Observe o uso de **[parameters('vnetName')]**, que significa que o nome será fornecido como entrada pelo usuário ou por um arquivo de parâmetro durante a implantação.
	- **properties**. Lista de propriedades do recurso. Esse modelo usa as propriedades de sub-rede e espaço de endereço durante a criação da rede virtual.

7. Navegue de volta para https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets.
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Salve o arquivo em uma pasta local do computador.
10. Abra o arquivo que acabou de salvar e edite os valores dos parâmetros. Use os valores abaixo para implantar a rede virtual descrita em nosso cenário.

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. Salve o arquivo.
  

<!---HONumber=August15_HO9-->