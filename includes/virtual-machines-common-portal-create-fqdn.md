## Etapas rápidas
No artigo, presumimos que você se conectou à sua assinatura no portal e criou uma máquina virtual com as imagens disponíveis, usando o modelo de implantação do Gerenciador de Recursos. Siga estas etapas assim que a máquina virtual começar a ser executada.

1. Exiba as configurações da máquina virtual no portal e clique no endereço IP Público.
   
   ![localizar recurso ip](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)
2. Observe que o nome DNS para o IP público está em branco. Clique em **Configuração** na folha do IP Público.
   
   ![ip de configurações](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)
3. Insira o rótulo do nome DNS desejado e **Salve** essa configuração.
   
   ![inserir rótulo do nome dns](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   O recurso IP Público agora mostra essa nova etiqueta DNS em sua folha.
4. Feche as folhas de IP Público e volte até a folha da máquina virtual no portal. Verifique se o nome DNS/FQDN aparece ao lado do endereço IP para o recurso IP Público.
   
   ![O FQDN é criado](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->