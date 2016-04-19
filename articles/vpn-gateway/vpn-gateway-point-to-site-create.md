<properties
   pageTitle="Configurar uma conexão VPN ponto a site para uma Rede Virtual do Azure usando o portal clássico| Microsoft Azure"
   description="Conecte-se com segurança à Rede Virtual do Azure criando uma conexão VPN ponto a site. Instruções para Redes Virtuais que foram criadas usando o modelo de implantação do Gerenciamento de Serviços (clássico)."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2016"
   ms.author="cherylmc"/>

# Configurar uma conexão VPN ponto a site em uma rede virtual usando o portal clássico

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Clássico](vpn-gateway-point-to-site-create.md)

Uma configuração Ponto a Site permite que você crie uma conexão segura com sua rede virtual em um computador cliente, individualmente. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. A conexão Ponto a Site é uma excelente solução quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Para saber mais sobre as conexões Ponto a Site, confira as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre conexões entre locais](vpn-gateway-cross-premises-options.md).)

Este artigo se aplica a conexões de Gateway de VPN Ponto a Site para uma rede virtual criada usando o **modelo clássico de implantação** (Gerenciamento de Serviços) e o portal clássico. Quando tivermos as etapas para o portal do Azure, vamos vincular ao artigo desta página.

**Modelos de implantação e ferramentas para conexões Site a Site**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![Diagrama ponto a site](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")



## Sobre a criação de uma conexão Ponto a Site
 
As etapas a seguir orientarão você durante as etapas para criar uma conexão Ponto a Site segura para uma rede virtual. Embora a configuração de uma conexão Ponto a Site exija várias etapas, ela é uma ótima maneira de obter uma conexão segura do computador com sua rede virtual sem adquirir e configurar um dispositivo VPN.

A configuração de uma conexão Ponto a Site é dividida em quatro seções. A ordem em que você configura cada um deles é importante, portanto, não ignore nem pule as etapas.


- A **Seção 1** o orientará na criação de uma rede virtual e de um gateway de VPN.
- A **Seção 2** ajudará a criar os certificados usados para autenticação e a carregá-los.
- A **Seção 3** o orientará pelas etapas para exportar e instalar os certificados de cliente.
- A **Seção 4** explicará as etapas para configurar seu cliente VPN.

## Seção 1 - Criar uma rede virtual e um gateway de VPN


### Parte 1: criar uma rede virtual.

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com/). Observe que essas etapas usam o portal clássico, não o portal do Azure.

2. No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita.
	- **Nome**: nome da sua rede virtual. Por exemplo, "VNetEast". Esse será o nome você usará ao implantar as VMs e instâncias de PaaS para esta VNet.
	- **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

4. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito.
	- **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual. Se você quiser usar o serviço de resolução de nome padrão do Azure, deixe esta seção em branco.
	- **Configurar VPN Ponto a Site**: selecione a caixa de seleção.

5. Na página **Conectividade ponto a site**, especifique o intervalo de endereços IP do qual os seus clientes VPN receberão um endereço IP quando conectados. Há algumas regras sobre os intervalos de endereços que você pode especificar. É muito importante verificar se o intervalo especificado não coincide com nenhum dos intervalos existentes na rede local.

6. Digite as informações a seguir e, em seguida, clique na seta de avanço.
 - **Espaço de endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR).
 - **Adicionar espaço de endereço**: adicione o espaço de endereço somente se necessário para seu design de rede.

7. Na página **Espaços de Endereço de Rede Virtual**, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual. É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.

8. Insira as informações a seguir e clique na marca de seleção para iniciar a criação da sua rede virtual.
 - **Espaço de endereço**: adicione o intervalo de endereços IP internos que você deseja usar para essa rede virtual, incluindo IP Inicial e Contagem. É importante selecionar um intervalo que não se sobreponha a qualquer um dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.
 - **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas convém criar uma sub-rede separada para as VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
 - **Adicionar sub-rede de gateway**: a sub-rede de gateway é necessária para uma VPN ponto a site. Clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual.

9. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada em **Status** na página de redes no portal clássico do Azure. Depois que sua rede virtual tiver sido criada, você poderá criar o gateway de roteamento dinâmico.

### Parte 2: criar um gateway de roteamento dinâmico

O tipo de gateway deve ser configurado como dinâmico. Os gateways de roteamento estáticos não funcionarão com este recurso.

1. No portal clássico do Azure, na página **Redes**, clique na rede virtual que você acabou de criar e navegue até a página **Painel**.

2. Clique em **Criar Gateway**, localizado na parte inferior da página **Painel**. Será exibida uma mensagem perguntando se **Você deseja criar um gateway para a rede virtual "sua\_rede"**. Clique em **Sim** para começar a criar o gateway. Pode levar cerca de 15 minutos para que o gateway seja criado.

## Seção 2 - Gerar e carregar certificados

Os certificados são usados para autenticar clientes VPN para VPNs ponto a site. Você pode usar certificados gerados por uma solução de certificado corporativo, bem como certificados autoassinados. É possível carregar até 20 certificados raiz no Azure.

Se você quiser usar um certificado autoassinado, as etapas a seguir o guiarão pelo processo. Se você estiver planejando usar uma solução de certificado corporativo, as etapas em cada seção serão diferentes, mas você ainda precisará realizar as seguintes etapas:

### Parte 1: identificar ou gerar um certificado raiz

Se você não estiver usando uma solução de certificado corporativo, será preciso gerar um certificado raiz autoassinado. As etapas nesta seção foram escritas para o Windows 8. Para obter as etapas para o Windows 10, você pode conferir [Trabalhando com certificados raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md).

Uma maneira de criar um certificado X.509 é usando a Ferramenta de Criação de Certificado (makecert.exe). Para usar a ferramenta makecert, baixe e instale o [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), que é gratuito.

1. Navegue até a pasta Ferramentas do Visual Studio e inicie o prompt de comando como administrador.

2. O comando no exemplo a seguir cria e instala um certificado raiz no repositório de certificados Pessoais de seu computador e também cria um arquivo *.cer* correspondente, que você posteriormente carregará no portal clássico do Azure.

3. Vá para o diretório no qual deseja colocar o arquivo .cer e execute o comando a seguir, em que *RootCertificateName* é o nome que você deseja usar para o certificado. Se você executar o exemplo a seguir sem alterações, o resultado será um certificado raiz e o arquivo *RootCertificateName.cer* correspondente.

>[AZURE.NOTE] Como você criou um certificado raiz do qual serão gerados certificados de cliente, você pode querer exportar o certificado juntamente com a sua chave privada e salvá-lo em um local seguro onde possa ser recuperado.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Parte 2: carregar o arquivo .cer do certificado raiz no portal clássico do Azure

Você precisará carregar o arquivo. cer correspondente para cada certificado raiz no Azure. É possível carregar até 20 certificados.

1. Quando você gerou um certificado raiz no procedimento anterior, criou também um arquivo *.cer*. Agora você carregará esse arquivo no portal clássico do Azure. Observe que o arquivo.cer não contém a chave privada do certificado raiz. É possível carregar até 20 certificados raiz.

2. No portal clássico do Azure, na página **Certificados** de sua rede virtual, clique em **Carregar um certificado raiz**.

3. Na página **Carregar certificado**, procure o certificado raiz .cer e clique na marca de seleção.

### Parte 3: gerar um certificado de cliente

As etapas a seguir visam gerar um certificado do cliente a partir do certificado raiz autoassinado. As etapas nesta seção foram escritas para o Windows 8. Para obter as etapas para o Windows 10, você pode conferir [Trabalhando com certificados raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md). Se você estiver usando uma solução de certificado corporativo, siga as diretrizes para a solução que está usando.

1. No mesmo computador que você usou para criar o certificado raiz autoassinado, abra uma janela de Prompt de comando do Visual Studio como administrador.

2. Altere o diretório para o local onde você deseja salvar arquivo de certificado de cliente. *RootCertificateName* refere-se ao certificado raiz autoassinado que você gerou. Se você executar o exemplo a seguir (alterando o RootCertificateName para o nome do seu certificado raiz), o resultado será um certificado de cliente chamado "ClientCertificateName" no seu repositório de certificados Pessoais.

3. Digite o seguinte comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos os certificados são armazenados no repositório de certificados Pessoal do computador. Marque *certmgr* para verificar. Você pode gerar quantos certificados de cliente forem necessários com base neste procedimento. É recomendável que você crie certificados de cliente exclusivos para cada computador que você deseje conectar à rede virtual.

## Seção 3 - Exportar e instalar o certificado de cliente

A instalação de um certificado do cliente em cada computador que você queira conectar à rede virtual é uma etapa obrigatória. As etapas a seguir guiarão você pela instalação manual do certificado do cliente.

1. Um certificado de cliente deve ser instalado em cada computador que você deseje conectar à rede virtual. Isso significa que você provavelmente criará vários certificados de cliente e depois precisará exportá-los. Para exportar os certificados de cliente, use *certmgr. msc*. Clique com o botão direito do mouse no certificado de cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar**.
2. Exporte o *certificado de cliente* com a chave privada. Esse será um arquivo *.pfx*. Certifique-se de registrar ou se lembrar da senha (chave) que você definiu para esse certificado.
3. Copie o arquivo *.pfx* para o computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Digite a senha quando solicitado. Não modifique o local de instalação.

## Seção 4 - Configurar o seu cliente VPN

Para se conectar à rede virtual, também será necessário configurar o cliente VPN. O cliente requer um certificado de cliente e a configuração adequada do cliente VPN para se conectar. Para configurar seu cliente VPN, execute as etapas a seguir, na ordem.

### Parte 1: criar o pacote de configuração de cliente VPN.

1. No portal clássico do Azure, na página **Painel** de sua rede virtual, navegue até o menu de visão rápida no canto direito e clique no pacote VPN que pertence ao cliente que você deseja conectar à sua rede virtual.

2. 
Há suporte para os seguintes sistemas operacionais clientes:
 - Windows 7 (32 bits e 64 bits)
 - Windows Server 2008 R2 (somente 64 bits)
 - Windows 8 (32 bits e 64 bits)
 - Windows 8.1 (32 bits e 64 bits)
 - Windows Server 2012 (somente 64 bits)
 - Windows Server 2012 R2 (somente 64 bits)
 - Windows 10

3. Selecione o pacote de download que corresponde ao sistema operacional cliente no qual ele será instalado:
 - Para clientes de 32 bits, selecione **Baixar o Pacote de Cliente VPN de 32 bits**.
 - Para clientes de 64 bits, selecione **Baixar o Pacote de Cliente VPN de 64 bits**.

4. Pode levar alguns minutos para criar o pacote de cliente. Depois que o pacote tiver sido concluído, você poderá baixar o arquivo. O arquivo *.exe* baixado pode ser armazenado com segurança no seu computador local.

5. Depois de gerar e baixar o pacote de cliente VPN no portal clássico do Azure, você pode instalar o pacote do cliente no computador cliente por meio do qual deseja se conectar à sua rede virtual. Se você planeja instalar o pacote do cliente VPN em vários computadores cliente, certifique-se de que cada um deles também tenha um certificado de cliente instalado. O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais.

### Parte 2: instalar o pacote de configuração de VPN no cliente e iniciar a conexão

1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual e clique duas vezes no arquivo .exe. Depois que o pacote tiver sido instalado, você pode iniciar a conexão VPN. Observe que o pacote de configuração não está assinado pela Microsoft. Convém assinar o pacote usando o serviço de assinatura de sua organização ou assiná-lo você mesmo usando [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Não há problemas em usar o pacote sem assinatura. No entanto, se o pacote não estiver assinado, será exibido um aviso quando você instalar o pacote.
2. No computador cliente, navegue até Conexões VPN e localize a conexão VPN que você acabou de criar. Ele será nomeado com o mesmo nome da sua rede virtual. Clique em **Conectar**.
3. Será exibida uma mensagem pop-up, que é usada para criar um certificado autoassinado para o ponto de extremidade do Gateway. Clique em **Continuar** para usar privilégios elevados.
4. Na página de status da **Conexão**, clique em **Conectar** para iniciar a conexão.
5. Se for exibida uma tela de **Selecionar certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
6. Agora você está conectado à sua rede virtual e tem acesso total a qualquer serviço e máquina virtual hospedados em sua rede virtual.

### Parte 3: verificar a conexão VPN

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do intervalo de endereços de conectividade ponto a site que você especificou quando criou a sua VNet. Os resultados devem ser algo semelhante a isto:

Exemplo:



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled

## Próximas etapas

Você pode adicionar máquinas virtuais à sua rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se quiser saber mais sobre Redes Virtuais, consulte a página [Documentação da Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=AcomDC_0413_2016-->