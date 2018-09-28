---
title: Como configurar clientes do OpenVPN para o Gateway de VPN do Azure | Microsoft Docs
description: Etapas para configurar clientes do OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977835"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Configurar clientes do OpenVPN para o Gateway de VPN do Azure (Versão prévia)

Este artigo ajuda você a configurar clientes do OpenVPN.

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

Verifique se você concluiu as etapas para configurar o OpenVPN para seu gateway de VPN. Para obter detalhes, confira [Configurar o OpenVPN para Gateway de VPN do Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Clientes do Windows

1. Faça o download e instale o cliente OpenVPN do [site oficial do OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito a partir da guia Configuração ponto a site no portal do Azure, ou "New-AzureRmVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Depois, abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no Bloco de notas.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Para saber mais sobre como extrair a chave privada, consulte [exportar a chave](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7. Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
8. Clique com botão direito no ícone OpenVPN na bandeja do sistema e clique em conectar.

## <a name="mac"></a>Clientes Mac

1. Faça o download e instale um cliente de OpenVPN, como [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Faça o download do perfil VPN para o gateway. Isso pode ser feito a partir da guia Configuração ponto a site no portal do Azure, ou usando "New-AzureRmVpnClientConfiguration" no PowerShell.
3. Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Confira [Exportar a chave pública](vpn-gateway-certificates-point-to-site.md#cer) para obter informações sobre como exportar um certificado e obter a chave pública codificada.
5. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Para saber mais sobre como extrair a chave privada, confira [Exportar sua chave privada](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/).
6. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7. Clique duas vezes no arquivo de perfil para criar o perfil no tunnelblik.
8. Inicie o Tunnelblik na pasta de aplicativos.
9. Clique no ícone do Tunneblik na bandeja do sistema e escolha conectar.

## <a name="linux"></a>Clientes Linux

1. Abra uma nova sessão de Terminal. Você pode abrir uma nova sessão pressionando simultaneamente "Ctrl + Alt + t"
2. Insira o comando a seguir para instalar os componentes necessários:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Faça o download do perfil VPN para o gateway. Isso pode ser feito a partir da guia Configuração ponto a site no portal do Azure, ou usando "New-AzureRmVpnClientConfiguration" no PowerShell.
4. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Para saber mais sobre como extrair a chave privada, confira [Exportar sua chave privada](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/).
5. Para se conectar usando a linha de comando, digite o seguinte:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Para se conectar usando o GUI, acesse as configurações do sistema.
6. Clique em **+** para adicionar uma nova conexão VPN.
7. Em **Adicionar VPN**, escolha **Importar do arquivo...**
8. Navegue até o arquivo de perfil e clique duas vezes ou escolha **Abrir**
9. Clique em **Adicionar** na janela **Adicionar VPN**.
  
  ![Importar do arquivo](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. Você pode se conectar **LIGANDO** a VPN na página **Configurações de Rede**, ou no ícone de rede na bandeja do sistema.

## <a name="next-steps"></a>Próximas etapas

Se você quiser que os clientes de VPN possam acessar recursos em outra rede virtual (produção), siga as instruções no artigo [Vnet-para-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para configurar uma conexão de rede virtual para rede virtual. Não se esqueça de habilitar o BGP nos gateways e conexões, caso contrário, o tráfego não fluirá.
