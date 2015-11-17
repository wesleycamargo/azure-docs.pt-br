<properties
   pageTitle="Configurar uma conexão VPN ponto a site para uma Rede Virtual do Azure| Microsoft Azure"
   description="Conecte-se com segurança à Rede Virtual do Azure criando uma conexão VPN ponto a site. Instruções para Redes Virtuais que foram criadas usando o modelo de implantação do Gerenciamento de Serviços (clássico)."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="cherylmc"/>

# Configurar uma conexão VPN ponto a site para uma Rede Virtual


Este artigo se aplica a conexões ponto a site para redes virtuais criadas usando o modelo clássico de implantação (Gerenciamento de Serviços).

**Neste momento, não há suporte para conexões ponto a site para uma rede virtual criada usando o modelo de implantação do Gerenciador de Recursos do Azure.** Esta página será atualizada quando este recurso tiver suporte para o modelo de implantação do Gerenciador de Recursos.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

O procedimento a seguir orientará você durante as etapas para criar uma conexão ponto a site segura para uma rede virtual. Embora a configuração de uma conexão ponto a site exija várias etapas, ela é uma ótima maneira de obter uma conexão segura do computador com sua rede virtual sem adquirir e configurar um dispositivo VPN. Há três partes principais para configurar uma VPN ponto a site: a rede virtual e o gateway de VPN, os certificados usados para autenticação e o cliente VPN que é usado para conectar você à sua rede virtual. A ordem em que você configura cada um deles é importante, portanto, não ignore nem pule as etapas.


1. Criar uma rede virtual e um gateway de VPN
2. Gerar e carregar certificados
3. Configurar o seu cliente VPN

## 1\. Criar uma rede virtual e um gateway de VPN


Uma conexão ponto a site requer uma rede virtual com um gateway de roteamento dinâmico. As etapas a seguir orientarão você pelo seguinte:

Etapa 1 - Criar uma rede virtual

Etapa 2 - Criar um gateway de roteamento dinâmico

### Etapa 1 - Criar uma rede virtual

1. Faça logon no **Portal do Azure** (não é o Portal de Visualização).
1. No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
1. Na página **Detalhes da Rede Virtual**, insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita.
	- **Nome**: nome da sua rede virtual. Por exemplo, "VNetEast". Esse será o nome você usará ao implantar as VMs e instâncias de PaaS para esta VNet.
	- **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.
1. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito.
	- **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual. Se você quiser usar o serviço de resolução de nome padrão do Azure, deixe esta seção em branco.
	- **Configurar VPN Ponto a Site**: selecione a caixa de seleção.
1. Na página **Conectividade ponto a site**, especifique o intervalo de endereços IP do qual os seus clientes VPN receberão um endereço IP quando conectados. Há algumas regras sobre os intervalos de endereços que você pode especificar. É muito importante verificar se o intervalo especificado não coincide com nenhum dos intervalos existentes na rede local.
1. Digite as informações a seguir e, em seguida, clique na seta de avanço.
 - **Espaço de endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR).
 - **Adicionar espaço de endereço**: adicione somente se necessário para seu design de rede.
1. Na página **Espaços de Endereço de Rede Virtual**, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual. É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.
1. Insira as informações a seguir e clique na marca de seleção para iniciar a criação da sua rede virtual.
 - **Espaço de endereço**: adicione o intervalo de endereços IP internos que você deseja usar para essa rede virtual, incluindo IP Inicial e Contagem. É importante selecionar um intervalo que não se sobreponha a qualquer um dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.
 - **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas convém criar uma sub-rede separada para as VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
 - **Adicionar sub-rede de gateway**: a sub-rede de gateway é necessária para uma VPN ponto a site. Clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual.
1. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada sob **Status** na página de redes no portal do Azure. Depois que sua rede virtual tiver sido criada, você poderá criar o gateway de roteamento dinâmico.

### Etapa 2 - Criar um gateway de roteamento dinâmico

O tipo de gateway deve ser configurado como dinâmico. Os gateways de roteamento estáticos não funcionarão com este recurso.

1. No portal do Azure, na página **Redes**, clique na rede virtual que você acabou de criar e navegue até a página **Painel**.
1. Clique em **Criar Gateway**, localizado na parte inferior da página **Painel**. Será exibida uma mensagem perguntando se **Você deseja criar um gateway para a rede virtual "sua\_rede"**. Clique em **Sim** para começar a criar o gateway. Pode levar cerca de 15 minutos para que o gateway seja criado.

## 2\. Gerar e carregar certificados

Os certificados são usados para autenticar clientes VPN para VPNs ponto a site. Anteriormente, era necessário gerar seu próprio certificado autoassinado. Agora você agora pode usar certificados gerados usando uma solução corporativa. É possível carregar até 20 certificados raiz no Azure.

Se você quiser usar um certificado autoassinado, as etapas a seguir o guiarão pelo processo. Se você estiver planejando usar uma solução de certificado corporativo, as etapas em cada seção serão diferentes, mas você ainda precisará fazer o seguinte:


Etapa 1 - Identificar ou gerar um certificado raiz

Etapa 2 - Carregar o arquivo .cer do certificado raiz no Azure

Etapa 3 - Gerar um certificado do cliente

Etapa 4 - Exportar e instalar o certificado do cliente


### Etapa 1 - Identificar ou gerar um certificado raiz

Se você não estiver usando uma solução de certificado corporativo, será preciso gerar um certificado raiz autoassinado. As etapas abaixo funcionam no Windows 8. Estamos em processo de atualização com novas etapas para o Windows 10.

1. Uma maneira de criar um certificado X.509 é usando a Ferramenta de Criação de Certificado (makecert.exe). Para usar a ferramenta makecert, baixe e instale o [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), que é gratuito.
2. Navegue até a pasta Ferramentas do Visual Studio e inicie o prompt de comando como administrador.
3. O comando no exemplo a seguir criará e instalará um certificado raiz no repositório de certificados Pessoais do seu computador e também criará um arquivo *.cer* correspondente, que você posteriormente carregará no portal do Azure.
4. Vá para o diretório no qual deseja colocar o arquivo .cer e execute o comando a seguir, em que *RootCertificateName* é o nome que você deseja usar para o certificado. Se você executar o exemplo a seguir sem alterações, o resultado será um certificado raiz e o arquivo *RootCertificateName.cer* correspondente.

>[AZURE.NOTE]Como você criou um certificado raiz do qual serão gerados certificados de cliente, você pode querer exportar o certificado juntamente com a sua chave privada e salvá-lo em um local seguro onde possa ser recuperado.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Etapa 2 - Carregar o arquivo .cer do certificado raiz no portal do Azure

Você precisará carregar o arquivo. cer correspondente para cada certificado raiz no Azure. É possível carregar até 20 certificados.

1. Quando você gerou um certificado raiz no procedimento anterior, criou também um arquivo *.cer*. Agora você vai carregar esse arquivo no Portal do Azure. Observe que o arquivo.cer não contém a chave privada do certificado raiz. É possível carregar até 20 certificados raiz.
1. No portal do Azure, na página **Certificados** da sua rede virtual, clique em **Carregar um certificado raiz**.
1. Na página **Carregar certificado**, procure o certificado raiz .cer e clique na marca de seleção.

### Etapa 3 - Gerar um certificado do cliente

As etapas a seguir visam gerar um certificado do cliente a partir do certificado raiz autoassinado. Se você estiver usando uma solução de certificado corporativo, siga as diretrizes para a solução que está usando.

1. No mesmo computador que você usou para criar o certificado raiz autoassinado, abra uma janela de Prompt de comando do Visual Studio como administrador.
2. Altere o diretório para o local onde você deseja salvar arquivo de certificado de cliente. *RootCertificateName* refere-se ao certificado raiz autoassinado que você gerou. Se você executar o exemplo a seguir (alterando o RootCertificateName para o nome do seu certificado raiz), o resultado será um certificado de cliente chamado "ClientCertificateName" no seu repositório de certificados Pessoais.
3. Digite o seguinte comando:

    makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos os certificados são armazenados no repositório de certificados Pessoal do computador. Marque *certmgr* para verificar. Você pode gerar quantos certificados de cliente forem necessários com base neste procedimento. É recomendável que você crie certificados de cliente exclusivos para cada computador que você deseje conectar à rede virtual.

### Etapa 4 - Exportar e instalar o certificado do cliente

A instalação de um certificado do cliente em cada computador que você queira conectar à rede virtual é uma etapa obrigatória. As etapas a seguir guiarão você pela instalação manual do certificado do cliente.

1. Um certificado de cliente deve ser instalado em cada computador que você deseje conectar à rede virtual. Isso significa que você provavelmente criará vários certificados de cliente e depois precisará exportá-los. Para exportar os certificados de cliente, use *certmgr. msc*. Clique com o botão direito do mouse no certificado de cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar**.
2. Exporte o *certificado de cliente* com a chave privada. Esse será um arquivo *.pfx*. Certifique-se de registrar ou se lembrar da senha (chave) que você definiu para esse certificado.
3. Copie o arquivo *.pfx* para o computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Digite a senha quando solicitado. Não modifique o local de instalação.

## 3\. Configurar o seu cliente VPN

Para se conectar à rede virtual, também será necessário configurar o cliente VPN. O cliente requer um certificado de cliente e a configuração adequada do cliente VPN para se conectar.

Para configurar o cliente VPN, faça o seguinte, na ordem:

Etapa 1 - Criar o pacote de configuração de cliente VPN.

Etapa 2 - Instalar o pacote de configuração de VPN no cliente e iniciar a conexão.

Etapa 3 - Verificar a conexão.

### Etapa 1 - Criar o pacote de configuração de cliente VPN

1. No portal do Azure, na página **Painel** da sua rede virtual, navegue até o menu de visualização rápida no canto direito e clique no pacote VPN que pertence ao cliente que você deseja conectar à sua rede virtual.
2. 
Há suporte para os seguintes sistemas operacionais clientes:
 - Windows 7 (32 bits e 64 bits)
 - Windows Server 2008 R2 (somente 64 bits)
 - Windows 8 (32 bits e 64 bits)
 - Windows 8.1 (32 bits e 64 bits)
 - Windows Server 2012 (somente 64 bits)
 - Windows Server 2012 R2 (somente 64 bits)
 - Windows 10 (em validação no momento)

1. Selecione o pacote de download que corresponde ao sistema operacional cliente no qual ele será instalado:
 - Para clientes de 32 bits, selecione **Baixar o Pacote de Cliente VPN de 32 bits**.
 - Para clientes de 64 bits, selecione **Baixar o Pacote de Cliente VPN de 64 bits**.
1. Pode levar alguns minutos para criar o pacote de cliente. Depois que o pacote tiver sido concluído, você poderá baixar o arquivo. O arquivo *.exe* baixado pode ser armazenado com segurança no seu computador local.
1. Depois de gerar e baixar o pacote de cliente VPN no Portal do Azure, você pode instalar o pacote do cliente no computador cliente por meio do qual deseja se conectar à sua rede virtual. Se você planeja instalar o pacote do cliente VPN em vários computadores cliente, certifique-se de que cada um deles também tenha um certificado de cliente instalado. O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais.

### Etapa 2 - Instalar o pacote de configuração de VPN no cliente e iniciar a conexão

1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual e clique duas vezes no arquivo .exe. Depois que o pacote tiver sido instalado, você pode iniciar a conexão VPN. Observe que o pacote de configuração não está assinado pela Microsoft. Convém assinar o pacote usando o serviço de assinatura de sua organização ou assiná-lo você mesmo usando a ferramenta [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Não há problemas em usar o pacote sem assinatura. No entanto, se o pacote não estiver assinado, será exibido um aviso quando você instalar o pacote.
2. No computador cliente, navegue até Conexões VPN e localize a conexão VPN que você acabou de criar. Ele será nomeado com o mesmo nome da sua rede virtual. Clique em **Conectar**.
3. Será exibida uma mensagem pop-up, que é usada para criar um certificado autoassinado para o ponto de extremidade do Gateway. Clique em **Continuar** para usar privilégios elevados.
4. Na página de status da **Conexão**, clique em **Conectar** para iniciar a conexão.
5. Se for exibida uma tela de **Selecionar certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e, em seguida, clique em **OK**.
6. Agora você está conectado à sua rede virtual e tem acesso total a qualquer serviço e máquina virtual hospedados em sua rede virtual.

### Etapa 3 - Verificar a conexão VPN

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

Você pode saber mais sobre a conectividade entre locais da rede virtual neste artigo: [Sobre a conectividade segura entre locais da rede virtual](vpn-gateway-cross-premises-options.md).

Se você quiser configurar uma conexão VPN site a site, confira [Configurar uma rede virtual com uma conexão de gateway de VPN site a site](vpn-gateway-site-to-site-create.md).

Você pode adicionar máquinas virtuais à sua rede virtual. Confira [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Para saber mais sobre Redes Virtuais, consulte a página [Documentação da Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=Nov15_HO3-->