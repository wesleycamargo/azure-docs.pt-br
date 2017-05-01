## <a name="quick-steps"></a>Etapas rápidas
No artigo, presumimos que você se conectou à sua assinatura no portal e criou uma máquina virtual com as imagens disponíveis, usando o modelo de implantação do Gerenciador de Recursos. Siga estas etapas assim que a máquina virtual começar a ser executada.

1. Selecione sua máquina virtual no portal. O nome DNS está em branco. Clique em **Endereço IP público**:
   
   ![Clique no recurso IP Público no portal](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. Insira o rótulo de nome DNS desejado e clique em **Salvar**.
   
   ![Inserir um rótulo de nome DNS para seu recurso IP público](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   O recurso IP Público agora mostra essa nova etiqueta DNS em sua folha.

3. Feche as folhas de IP Público e volte até a folha de visão geral da VM no portal. Após alguns segundos, o portal deve atualizar suas configurações. Verifique se o nome DNS/FQDN aparece ao lado do endereço IP do recurso **Endereço IP Público**.
   
   ![Confirme que o novo rótulo DNS foi definido](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

