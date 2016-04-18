


Quando você cria uma máquina virtual no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do **Gerenciador de Recursos**, um recurso de IP público para a máquina virtual é criado automaticamente. Você pode usar esse endereço IP para acessar remotamente a máquina virtual. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, por padrão, é extremamente fácil criar um depois que a máquina virtual é criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

No artigo, presumimos que você se conectou à sua assinatura no portal e criou uma máquina virtual com as imagens disponíveis, usando o **Gerenciador de Recursos**. Siga estas etapas assim que a máquina virtual começar a ser executada.

1.  Exiba as configurações da máquina virtual no portal e clique no endereço IP Público.

    ![localizar recurso ip](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  Observe que o nome DNS para o IP público está em branco. Clique em **Todas as Configurações** da folha IP Público.

    ![ip de configurações](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  Abra a guia **Configuração** nas Configurações de IP Público. Insira o rótulo do nome DNS desejado e **Salve** essa configuração.

    ![inserir rótulo do nome dns](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    O recurso IP Público agora mostrará essa nova etiqueta DNS em sua folha.

4.  Feche as folhas de IP Público e volte até a folha da máquina virtual no portal. Verifique se o nome DNS/FQDN aparece ao lado do endereço IP para o recurso IP Público.

    ![O FQDN é criado](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)


    Agora, você pode se conectar remotamente à máquina virtual usando esse nome DNS. Por exemplo, use o `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com` ao se conectar a uma máquina virtual do Linux que tem o nome de domínio totalmente qualificado de `testdnslabel.centralus.cloudapp.azure.com` e o nome de usuário de `adminuser`.

<!---HONumber=AcomDC_0406_2016-->