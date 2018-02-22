---
title: "Criar e instalar arquivos de configuração de cliente VPN para conexões RADIUS P2S: PowerShell: Azure | Microsoft Docs"
description: "Crie arquivos de configuração de cliente de VPN do Windows, Mac OS X e Linux para conexões que usam autenticação RADIUS."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: ce914d2fd0472855ad7a17bf64ae43a76ceb5743
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar arquivos de configuração de cliente VPN para autenticação RADIUS P2S

Para se conectar a uma rede virtual via ponto a site, você precisa configurar o dispositivo cliente do qual se conectará. É possível criar conexões VPN P2S de dispositivos cliente do Windows, Mac OSX e Linux. Ao usar a autenticação RADIUS, há várias opções de autenticação: autenticação de nome de usuário e senha, autenticação de certificado, bem como outros tipos de autenticação. A configuração do cliente de VPN é diferente para cada tipo de autenticação. Para configurar o cliente de VPN, use arquivos de configuração do cliente que contêm as configurações necessárias. Este artigo ajuda você a criar e instalar a configuração do cliente de VPN para o tipo de autenticação de RADIUS que você deseja usar.

O fluxo de trabalho de configuração para a autenticação de RADIUS P2S é o seguinte:

1. [Configurar o gateway de VPN do Azure para conectividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configurar o servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obter a configuração de cliente de VPN para a opção de autenticação de sua preferência e usá-la para configurar o cliente de VPN**. (Este artigo)
4. [Conclua a configuração de P2S e conecte-se](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se houver alterações na configuração de VPN Ponto a Site depois de gerar o perfil de configuração de cliente VPN, tais como o tipo de protocolo VPN ou o tipo de autenticação, você deve gerar e instalar uma nova configuração de cliente VPN em seus dispositivos do usuário.
>
>

Para usar as seções neste artigo, primeiro decida qual tipo de autenticação você deseja usar: nome de usuário e senha, certificado ou outros tipos de autenticação. Em cada seção, há etapas para Windows, Mac OS X e Linux (etapas limitadas disponíveis no momento).

## <a name="adeap"></a>Autenticação de nome de usuário e senha

Há duas maneiras de configurar a autenticação com nome de usuário e senha. Você pode configurar a autenticação para usar o AD ou autenticar sem usá-lo. Em cada cenário, todos os usuários que se conectam devem ter credenciais de nome de usuário e senha que podem ser autenticadas por meio do RADIUS.

* Quando configura a autenticação com nome de usuário e senha, você só pode criar uma configuração para o protocolo de autenticação EAP-MSCHAPv2 de nome de usuário e senha.
* '-AuthenticationMethod' é 'EapMSChapv2'.

### <a name="usernamefiles"></a> 1. Gerar os arquivos de configuração de cliente VPN

Gere arquivos de configuração de cliente VPN para usar com a autenticação de nome de usuário e senha. Você pode gerar os arquivos de configuração de cliente de VPN usando o seguinte comando:

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Executar o comando retorna um link. Copie e cole o link em um navegador da Web para baixar o arquivo 'VpnClientConfiguration.zip'. Descompacte o arquivo para ver as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86** – Essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* **Genérica** - Essa pasta contém informações gerais, usadas para criar sua própria configuração de cliente VPN. Essa pasta não é necessária para as configurações de autenticação de nome de usuário e senha.
* **Mac** -Se IKEv2 foi configurado quando você criou o gateway de rede virtual, você verá uma pasta chamada 'Mac' que contém um arquivo **mobileconfig**. Esse arquivo é usado para configurar os clientes Mac.

Se você já tiver criado os arquivos de configuração do cliente, poderá recuperá-los usando o cmdlet 'Get-AzureRmVpnClientConfiguration'. No entanto, se você fizer alterações na sua configuração de VPN de P2S, tais como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet 'New-AzureRmVpnClientConfiguration' para criar um novo download de configuração.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Configurar clientes de VPN

É possível configurar os seguintes clientes de VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux usando strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Configuração de cliente VPN do Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais que recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes](vpn-gateway-vpn-faq.md#P2S).

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta. 

#### <a name="admaccli"></a>Configuração de cliente de VPN do Mac (OSX)

1. Selecione o arquivo **VpnClientSetup mobileconfig** e envie-o para cada um dos usuários. Você pode usar o email ou outro método.

2. Localize o arquivo **mobileconfig** no Mac.

  ![localize o arquivo mobilconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. Clique duas vezes no perfil para instalá-lo, clique em **Continuar**. O nome do perfil é o mesmo que o nome da sua rede virtual.

  ![instalar](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. Clique em **Continuar** para confiar no remetente do perfil e continuar com a instalação.

  ![continuar](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. Durante a instalação do perfil, você terá a opção de especificar o nome de usuário e a senha usados para a autenticação de VPN. Não é obrigatório inserir essas informações. Se especificadas, as informações são salvas e usadas automaticamente quando você iniciar uma conexão. Clique em **Instalar** para continuar.

  ![configurações](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. Insira um nome de usuário e senha para os privilégios necessários para instalar o perfil em seu computador. Clique em **OK**.

  ![nome de usuário e senha](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. Uma vez instalado, o perfil fica visível na caixa de diálogo **Perfis**. Essa caixa de diálogo também pode ser aberta posteriormente em **Preferências do sistema**.

  ![preferências do sistema](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. Para acessar a conexão VPN, abra a caixa de diálogo **Rede** em **Preferências do sistema**.

  ![rede](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. A conexão VPN aparece como **IkeV2-VPN**. O nome pode ser alterado, atualizando o arquivo **mobileconfig**.

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. Clique em **Configurações da Autenticação**. Escolha o **Nome de usuário** na lista suspensa e insira suas credenciais. Se você inseriu as credenciais anteriormente, então o **Nome de usuário** é automaticamente selecionado na lista suspensa e o nome de usuário e senha são pré-populados. Clique em **OK** para salvar as configurações. Isso levará você de volta à caixa de diálogo de Rede.

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. Clique em **Aplicar** para salvar os detalhes. Para iniciar a conexão, clique em **Conectar**.

#### <a name="adlinuxcli"></a>Configuração do cliente de VPN Linux usando strongSwan

As instruções a seguir foram criadas usando strongSwan 5.5.1 no Ubuntu 17.0.4. As telas reais podem ser diferentes dependendo de sua versão do Linux e do strongSwan.

1. Abra o **Terminal** para instalar o **strongSwan** e seu Gerenciador de Rede executando o comando no exemplo. Se você receber um erro relacionado a "libcharon-extraplug-ins", substitua-o por "strongswan-plugin-eap-mschapv2".

  ```Terminal
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Clique no ícone do **Gerenciador de rede** (seta para cima/seta para baixo) e selecione **Editar conexões**.

  ![Editar conexão](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Clique no botão **Adicionar** para criar uma nova conexão.

  ![Adicionar conexão](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **IPsec/IKEv2 (strongswan)** no menu suspenso e clique em **Criar**. Você pode renomear sua conexão nesta etapa.

  ![Adicionar ikev2](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o arquivo **VpnSettings.xml** na pasta **Genérico** dos arquivos de configuração do cliente baixado. Localize a marca chamada **VpnServer** e copie o nome, começando com "azuregateway" e terminando com ". cloudapp.net".

  ![Configurações de VPN](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Cole este nome no campo **Endereço** de sua nova conexão de VPN na seção **Gateway**. Em seguida, clique no ícone de pasta no final do campo **Certificado**, navegue até a pasta genérica e selecione o arquivo **VpnServerRoot** localizado nela.
7. Na seção **Cliente** da conexão, escolha **EAP** para **Autenticação** e insira seu nome de usuário/senha. Talvez você precise selecionar o ícone de cadeado à direita para salvar essas informações. Em seguida, clique em **Salvar**.

  ![Editar configurações de conexão](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Clique no ícone do **Gerenciador de rede** (seta para cima/seta para baixo) e passe o mouse sobre **Conexões VPN**. Você verá a conexão VPN que criou. Para iniciar a conexão, selecione-a para se conectar.

  ![Conectar-se ao Radius](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Autenticação de certificado
 
Você pode criar arquivos de configuração de cliente VPN para autenticação de certificado RADIUS que usa o protocolo EAP-TLS. Normalmente, um certificado emitido pelo Enterprise é usado para autenticar um usuário para VPN. Certifique-se de que todos os usuários que se conectam possuem um certificado instalado no dispositivo dos usuários e que o certificado pode ser validado pelo servidor RADIUS.
 
* '-AuthenticationMethod' é 'EapTls'.
* Durante a autenticação do certificado, o cliente valida o servidor RADIUS, validando seu certificado. -RadiusRootCert é o arquivo. cer que contém o certificado raiz que é usado para validar o servidor RADIUS.
* Cada dispositivo cliente de VPN requer um certificado do cliente instalado.
* Às vezes, um dispositivo Windows possui vários certificados de cliente. Durante a autenticação, isso pode resultar em uma caixa de diálogo pop-up que lista todos os certificados. O usuário deve, em seguida, escolha o certificado a ser usado. O certificado correto pode ser filtrado especificando o certificado raiz ao qual o certificado de cliente deve estar encadeado. '-ClientRootCert' é o arquivo. cer que contém o certificado raiz. É um parâmetro opcional. Se o dispositivo do qual você deseja se conectar tiver apenas um certificado de cliente, esse parâmetro não precisa ser especificado.

### <a name="certfiles"></a>1. Gerar os arquivos de configuração de cliente VPN

Gere arquivos de configuração de cliente VPN para usar com a autenticação de certificado. Você pode gerar os arquivos de configuração de cliente de VPN usando o seguinte comando:
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Executar o comando retorna um link. Copie e cole o link em um navegador da Web para baixar o arquivo 'VpnClientConfiguration.zip'. Descompacte o arquivo para ver as seguintes pastas:

* **WindowsAmd64** e **WindowsX86** – Essas pastas contêm os pacotes dos instaladores do Windows de 64 bits e 32 bits, respectivamente. 
* **GenericDevice** – Essa pasta contém informações gerais, usadas para criar sua própria configuração de cliente VPN.

Se você já tiver criado os arquivos de configuração do cliente, poderá recuperá-los usando o cmdlet 'Get-AzureRmVpnClientConfiguration'. No entanto, se você fizer alterações na sua configuração de VPN de P2S, tais como o tipo de protocolo de VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet 'New-AzureRmVpnClientConfiguration' para criar um novo download de configuração.

Para recuperar arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Configurar clientes de VPN

É possível configurar os seguintes clientes de VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (com suporte, nenhuma etapa no artigo ainda)

#### <a name="certwincli"></a>Configuração de cliente VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**. Você também pode salvar o pacote de instalação em outros computadores clientes.
2. Cada cliente requer um certificado do cliente para se autenticar. Instale o certificado do cliente. Para obter informações sobre certificados do cliente, consulte [Certificados do cliente para ponto a site](vpn-gateway-certificates-point-to-site.md). Para instalar um certificado que foi gerado, consulte [Instalar um certificado em clientes Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.

#### <a name="certmaccli"></a>Configuração de cliente de VPN do Mac (OSX)

Deve ser criado um perfil separado para cada dispositivo do Mac que se conecta à rede virtual do Azure. Isso ocorre porque esses dispositivos exigem o certificado de autenticação do usuário a ser especificado no perfil. A pasta **Genérico** possui todas as informações necessárias para criar um perfil.

  * **VpnSettings.xml** contém configurações importantes, como o tipo de túnel e o endereço do servidor.
  * **VpnServerRoot.cer** contém o certificado raiz necessário para validar o gateway de VPN durante a instalação de conexão P2S.
  * **VpnServerRoot.cer** contém o certificado raiz necessário para validar o servidor RADIUS durante a autenticação.

Use as seguintes etapas para configurar o cliente VPN do Mac nativo para autenticação de certificado:

1. Importar os certificados raiz **VpnServerRoot** e **RadiusServerRoot** para o Mac. Isso pode ser feito copiando o arquivo para o seu Mac e clicando duas vezes nele.  
Clique em **Adicionar** para importar.

  *Adicionar VpnServerRoot*

  ![adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  *Adicionar RadiusServerRoot*

  ![adicionar certificado](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente requer um certificado do cliente para se autenticar. Instale o certificado do cliente no dispositivo cliente.
3. Abra a caixa de diálogo **Rede** em **Preferências de rede** e clique em **'+'** para criar um novo perfil de conexão de cliente VPN para uma conexão de P2S para a rede virtual do Azure.

  O valor da **Interface** 'VPN' e o valor do **Tipo de VPN** 'IKEv2'. Especifique um nome para o perfil no campo **Nome do serviço** e, em seguida, clique em **Criar** para criar o perfil de conexão de cliente VPN.

  ![rede](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na pasta **Genérico**, no arquivo **VpnSettings.xml**, copie o valor da marca **VpnServer**. Cole esse valor nos campos **Endereço do servidor** e **ID remoto** do perfil. Deixe o campo **ID Local** em branco.

  ![informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Clique em **Configurações de autenticação** e selecione **Certificado**. 

  ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique em **Selecionar...** para escolher o certificado que você deseja usar para autenticação.

  ![certificado](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolha uma identidade** exibe uma lista de certificados de sua escolha. Selecione o certificado apropriado e, em seguida, clique em **Continuar**.

  ![identidade](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. No campo **ID local**, especifique o nome do certificado (da Etapa 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para salvar as alterações.

  ![aplicar](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na caixa de diálogo **Rede**, clique em **Aplicar** para salvar todas as alterações. Em seguida, clique em **Conectar** para iniciar a conexão de P2S para a rede virtual do Azure.

## <a name="otherauth"></a>Trabalho com outros tipos de autenticação ou protocolos

Para usar um tipo de autenticação diferente (por exemplo, OTP) e não nome de usuário/senha ou certificados ou para usar um protocolo de autenticação diferentes (como PEAP-MSCHAPv2, em vez de EAP-MSCHAPv2), você deve criar seu próprio perfil de configuração de cliente VPN. Para criar o perfil, você precisa de informações como o endereço IP do gateway de rede virtual, tipo de túnel e rotas de túnel dividido. Você pode obter essas informações através das seguintes etapas:

1. Use o cmdlet 'Get-AzureRmVpnClientConfiguration' para gerar a configuração do cliente VPN para EapMSChapv2. Para obter instruções, confira [esta seção](#ccradius) do artigo.

2. Descompacte o arquivo VpnClientConfiguration.zip e procure a pasta GenenericDevice. Ignore as pastas que contendo os instaladores do Windows para arquiteturas de 64 bits e de 32 bits.
 
3. A pasta GenenericDevice contém um arquivo XML chamado VpnSettings. Este arquivo contém todas as informações necessárias.

  * VpnServer - FQDN do gateway de VPN do Azure. Este é o endereço ao qual o cliente se conecta.
  * VpnType - o tipo de túnel que você usa para se conectar.
  * Routes - As rotas que você precisa configurar no seu perfil para que somente o tráfego de saída da rede virtual do Azure seja enviada por meio do túnel de P2S.
  * A pasta GenenericDevice também contém um arquivo .cer chamado 'VpnServerRoot'. Este arquivo contém o certificado raiz necessário para validar o gateway de VPN durante a instalação de conexão de P2S. Instale o certificado em todos os dispositivos que irão se conectar à rede virtual do Azure.

## <a name="next-steps"></a>Próximas etapas

Retornar para o artigo [concluir a configuração de P2S](point-to-site-how-to-radius-ps.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).