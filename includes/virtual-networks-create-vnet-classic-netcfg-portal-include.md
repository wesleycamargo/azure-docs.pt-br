## Como criar uma rede virtual usando um arquivo de configuração de rede no portal do Azure

O Azure usa um arquivo xml para definir todas as redes virtuais disponíveis para uma assinatura. Você pode baixar esse arquivo e editá-lo para modificar ou excluir redes virtuais existentes e criar novas. Neste documento, você aprenderá a baixar esse arquivo, conhecido como arquivo de configuração de rede (ou netcgf) e editá-lo para criar uma nova rede virtual. Confira [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o arquivo de configuração de rede.

Para criar uma rede virtual usando um arquivo netcfg por meio do portal do Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://manage.windowsazure.com e, se necessário, entre com sua conta do Azure.
2. Role para baixo na lista de serviços e clique em **REDES**, conforme mostrado abaixo.

	![Redes virtuais do Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Na parte inferior da página, clique no botão **EXPORTAR**, conforme mostrado abaixo.

	![Botão Exportar](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Na página **Exportar configuração de rede**, selecione a assinatura da qual deseja exportar a configuração da rede virtual e clique no botão da marca de seleção no canto inferior esquerdo da página.
5. Siga as instruções do navegador para salvar o arquivo **NetworkConfig.xml**. Não se esqueça de anotar onde está salvando o arquivo.
6. Abra o arquivo que salvou na etapa 5 acima usando qualquer aplicativo de edição de texto ou XML e procure o elemento **<VirtualNetworkSites>**. Se você já tiver redes criadas, cada rede será exibida como seu próprio elemento **<VirtualNetworkSite>**.
7. Para criar a rede virtual descrita nesse cenário, adicione o seguinte XML logo abaixo do elemento **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  Salve o arquivo de configuração de rede.
9.  No portal do Azure, no canto inferior esquerdo da página, clique em **NOVO**, em **SERVIÇOS DE REDE**, em **REDE VIRTUAL** e em **IMPORTAR CONFIGURAÇÃO**, conforme mostrado na figura abaixo.

	![Importar configuração](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Na página **Importar o arquivo de configuração de rede**, clique em **PROCURAR ARQUIVO...**, navegue até a pasta em que salvou o arquivo na etapa 8 acima, selecione o arquivo e clique em **Abrir**. A página da Web deve se parecer com a figura abaixo. No canto inferior direito da página, clique no botão de seta para passar para a próxima etapa.

	![Página Importar arquivo de configuração de rede](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Na página **Construindo a rede**, observe a entrada da sua nova rede virtual, conforme mostrado na figura abaixo.

	![Página Construindo a rede](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Clique no botão de marca de seleção no canto inferior direito da página para criar a rede virtual. Depois de alguns segundos, sua rede virtual será mostrada na lista de redes virtuais disponíveis, conforme mostrado na figura abaixo.

	![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=August15_HO9-->