


## Marcando uma máquina virtual por meio de modelos

Primeiramente, vamos observar uma marcação por meio de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) coloca marcas nos seguintes recursos: Computação (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). (Esse exemplo de modelo destina-se a uma VM do Windows, mas pode ser adaptada para VMs do Linux.)

Clique no botão **Implantar no Azure** no [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Você será direcionado para o [Portal do Azure](https://portal.azure.com/), onde poderá implantar esse modelo.

![Implantação simples com marcas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Esse modelo inclui as seguintes marcas: *Departamento*, *Aplicativo* e *Criado por*. Você pode adicionar/editar essas marcas diretamente no modelo se desejar diferentes nomes de marca.

![Marcas do Azure em um modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ser visto, as marcas são pares de chave/valor definidos, separados por dois-pontos (:). As marcas devem ser definidas neste formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Salve o arquivo de modelo quando terminar de editá-lo com as marcas de sua escolha.

Em seguida, na seção **Editar Parâmetros**, você pode preencher os valores de suas marcas.

![Editar marcas no Portal do Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **Criar** para implantar esse modelo com seus valores de marca.


## Marcando por meio do portal

Depois de criar seus recursos com marcas, você pode exibir, adicionar e excluir as marcas no portal.

Selecione o ícone de marcas para exibir suas marcas:

![Ícone de marcas no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova marca por meio do portal definindo seu próprio par de chave/valor e salve-a.

![Adicionar nova marca no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Agora, a nova marca deve aparecer na lista de marcas do seu recurso.

![Nova marca salva no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)


## Exibindo suas marcas nos detalhes de uso

As marcas colocadas nos recursos de Computação, Rede e Armazenamento por meio do Gerenciador de Recursos do Azure serão preenchidas nos detalhes de uso no [portal de cobrança](https://account.windowsazure.com/).

Clique em **Baixar detalhes de uso** para exibir os detalhes de uso da assinatura.

![Detalhes de uso no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-usage-details.png)

Selecione o demonstrativo e os detalhes de uso da **Versão 2**:

![Detalhes de uso da versão de visualização 2 no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-version2-usage-details.png)

Nos detalhes de uso, você pode ver todas as marcas na coluna **Marcas**:

![Coluna Marcas no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-column.png)

Ao analisar essas marcas juntamente com o uso, as organizações poderão adquirir novas percepções sobre os respectivos dados de consumo.

<!---HONumber=AcomDC_0330_2016-->