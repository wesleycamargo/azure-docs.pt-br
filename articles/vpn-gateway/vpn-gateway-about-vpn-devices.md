<properties 
   pageTitle="Sobre dispositivos VPN para conexões site a site de Rede Virtual do Azure | Microsoft Azure"
   description="Saiba mais sobre dispositivos VPN e os parâmetros de IPsec para conexões de Gateway de VPN site a site de Rede Virtual do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="cherylmc" />

# Sobre dispositivos VPN para conexões de rede virtual site a site

Um dispositivo VPN é necessário para configurar uma conexão VPN site a site. Conexões site a site podem ser usadas para criar uma solução de nuvem híbrida ou sempre que desejar uma conexão segura entre sua rede local e sua rede virtual. Este artigo aborda os dispositivos VPN compatíveis e parâmetros de configuração.

É importante saber que, além de um dispositivo VPN compatível, as conexões site a site também exigem um endereço IP IPv4 voltado para o público. Além disso, será útil selecionar o tipo de gateway que dará suporte à sua solução. Nem todos os dispositivos VPN dão suporte a todos os tipos de gateway. Consulte [Gateways VPN](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway que você precisa para criar sua solução.



## Dispositivos VPN

Validamos um conjunto de dispositivos de VPN site a site (S2S) padrão em parceria com fornecedores de dispositivos. Para obter uma lista dos dispositivos VPN comprovadamente compatíveis com a Rede Virtual, consulte a seção [Dispositivos VPN compatíveis](#compatible-vpn-devices) abaixo. Todos os dispositivos das famílias de dispositivos contidos nessa lista devem funcionar com gateways de VPN do Azure. Para ajudar a configurar seu dispositivo VPN, consulte o exemplo de configuração do dispositivo ou o link que corresponde à família de dispositivos apropriada.

As especificações de gateway de VPN de alto desempenho e gateway de VPN de roteamento dinâmico são os mesmos, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados que são compatíveis com os gateways de VPN de roteamento dinâmico do Azure também serão compatíveis com o novo gateway de VPN de alto desempenho do Azure.


### Dispositivos VPN compatíveis

Trabalhamos com fornecedores de dispositivos VPN para qualificar em conjunto famílias específicas de dispositivos VPN. A seção a seguir fornece uma lista de todas as famílias de dispositivos que funcionam com nosso gateway de VPN. Todos os dispositivos que são membros das famílias de dispositivos listados funcionam comprovadamente, salvo se exceções forem mencionadas. Se o dispositivo não aparecer na lista, consulte [Dispositivos não estão presentes na lista compatível](#devices-not-on-the-compatible-list).



Para suporte ao dispositivo VPN, entre em contato com o fabricante do dispositivo.



| **Fornecedor** | **Família do dispositivo** | **Versão mínima do sistema operacional** | **Roteamento Estático** | **Roteamento Dinâmico** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | Série AR de roteadores VPN | 2\.9.2 | Em breve | Não compatível |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| Não compatível |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Não compatível |
| Brocade | Vyatta 5400 vRouter | Roteador virtual 6.6R3 GA | [Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Não compatível |
| Ponto de Verificação | Gateway de segurança | R75.40, R75.40VS | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Exemplos do Cisco ASA](https://msdn.microsoft.com/library/azure/dn133793.aspx) | Não compatível |
| Cisco | ASR | IOS 15.1 (estático), IOS 15.2 (dinâmico) | [Exemplos do Cisco ASR](https://msdn.microsoft.com/library/azure/dn133802.aspx) | [Exemplos do Cisco ASR](https://msdn.microsoft.com/library/azure/dn133802.aspx) |
| Cisco | ISR | IOS 15.0 (estático), IOS 15.1 (dinâmico) | [Exemplos do Cisco ISR](https://msdn.microsoft.com/library/azure/dn133800.aspx) | [Exemplos do Cisco ISR](https://msdn.microsoft.com/library/azure/dn133800.aspx) |
| Citrix | Dispositivo CloudBridge MPX ou VPX virtual | N/D | [Instruções de integração](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Não compatível |
| Dell SonicWALL | Séries TZ, NSA, SuperMassive e NSA classe E | SonicOS 5.8.x, SonicOS 5.9.x e SonicOS 6.x | [Instruções de configuração](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | Não compatível |
| F5 | Série BIG-IP | N/D | [Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Não compatível |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Instruções de configuração](http://docs.fortinet.com/fortigate/admin-guides) | [Instruções de configuração](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | Série SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Não compatível |
| Juniper | SRX | JunOS 10.2 (estático) e JunOS 11.4 (dinâmico) | [Exemplos do Juniper SRX](https://msdn.microsoft.com/library/azure/dn133794.aspx) | [Exemplos do Juniper SRX](https://msdn.microsoft.com/library/azure/dn133794.aspx) |
| Juniper | Série J | JunOS 10.4r9 (estático), JunOS 11.4 (dinâmico) | [Exemplos da série J Juniper](https://msdn.microsoft.com/library/azure/dn133799.aspx) | [Exemplos da série J Juniper](https://msdn.microsoft.com/library/azure/dn133799.aspx) |
| Juniper | ISG | ScreenOS 6.3 (estático e dinâmico) | [Exemplos do Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) | [Exemplos do Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) |
| Juniper | SSG | ScreenOS 6.2 (estático e dinâmico) | [Exemplos do Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) | [Exemplos do Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) |
| Microsoft | Serviço de Roteamento e Acesso Remoto | Windows Server 2012 | Não compatível | [Exemplos de RRAS (Serviço de Roteamento e Acesso Remoto)](https://msdn.microsoft.com/library/azure/dn133801.aspx) |
| Openswan | Openswan | 2\.6.32 | (Em breve) | Não compatível |
| Redes Palo Alto | Todos os dispositivos com PAN-OS 5.0 ou superior em execução | PAN-OS 5x ou superior | [Redes Palo Alto](https://support.paloaltonetworks.com/) | Não compatível |
| Watchguard | Todos | Fireware XTM v11.x | [Instruções de configuração](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Não compatível |


### Dispositivos não presentes na lista compatível


Se você não vir seu dispositivo na lista de dispositivos VPN compatíveis conhecidos se quiser usar o dispositivo para a conexão VPN, verifique se ele atende aos requisitos mínimos descritos na tabela de [Requisitos do gateway](vpn-gateway-about-vpngateways.md#gateway-requirements). Os dispositivos que atendem aos requisitos mínimos também devem funcionar bem com a Rede Virtual. Entre em contato com o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.


## Edição de exemplos de configuração de dispositivo

Depois de baixar o exemplo de configuração de dispositivo VPN fornecido, você precisará substituir alguns dos valores para refletir as configurações do seu ambiente.

**Para editar o exemplo:**

1. Abra o exemplo usando o Bloco de Notas. 
1. Pesquise e substitua todas as cadeias de caracteres de <*texto*> por valores condizentes com seu ambiente. Certifique-se de incluir < and >. Quando um nome é especificado, o nome que você selecionar deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo** | **Alterar para** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | O nome que você escolheu para este objeto. Exemplo: myOnPremisesNetwork |
| &lt;RP\_AzureNetwork&gt; | O nome que você escolheu para este objeto. Exemplo: myAzureNetwork |
| &lt;RP\_AccessList&gt; | O nome que você escolheu para este objeto. Exemplo: myAzureAccessList |
| &lt;RP\_IPSecTransformSet&gt; | O nome que você escolheu para este objeto. Exemplo: myIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | O nome que você escolheu para este objeto. Exemplo: myIPSecCryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | Especifique o intervalo. Exemplo: 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Especificar máscara de sub-rede. Exemplo: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Especifique o intervalo local. Exemplo: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Especifique a máscara de sub-rede local. Exemplo: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | Estas informações são específicas à sua rede virtual e estão localizadas no Portal de Gerenciamento como **endereço IP do Gateway**. |
| &lt;SP\_PresharedKey&gt; | Essas informações são específicas da sua rede virtual e estão localizadas no Portal de Gerenciamento como Gerenciar Chave. |



## Parâmetros IPsec

### Fase 1 da configuração IKE

| **Propriedade** | **Gateway de VPN de roteamento estático** | **Gateway de VPN de roteamento dinâmico e gateway padrão ou VPN HighPerformance** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versão IKE | IKEv1 | IKEv2 |
| Grupo Diffie-Hellman | Grupo 2 (1024 bits) | Grupo 2 (1024 bits) |
| Método de autenticação | Chave Pré-Compartilhada | Chave Pré-Compartilhada |
| Algoritmos de criptografia | AES256 AES128 3DES | AES256 3DES |
| Algoritmo de hash | SHA1(SHA128) | SHA1(SHA128) |
| Tempo de vida (tempo) da SA (associação de segurança) da fase 1 | 28\.800 segundos | 28\.800 segundos |


### Fase 2 da configuração IKE

| **Propriedade** | **Gateway de VPN de roteamento estático** | **Gateway de VPN de roteamento dinâmico e gateway padrão ou VPN HighPerformance** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versão IKE | IKEv1 | IKEv2 |
| Algoritmo de hash | SHA1(SHA128) | SHA1(SHA128) |
| Tempo de vida (Tempo) da SA (Associação de Segurança) da fase 2 | 3.600 segundos | - | | Tempo de vida (Taxa de transferência) da SA (Associação de Segurança) da fase 2 | 102.400.000 KB | - | | Ofertas de criptografia e autenticação IPsec SA (em ordem de preferência) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D | Consulte ofertas de SA (Associação de Segurança) IPsec do gateway de roteamento dinâmico | | PFS (Perfect Forward Secrecy) | Não | Sim (DH Group1) | | Detecção de Par Inativo | Sem suporte | Com suporte |

### Ofertas de SA (Associação de Segurança) de IPsec de gateway de roteamento dinâmico

A tabela a seguir lista as ofertas de criptografia e autenticação IPsec SA. Ofertas estão listadas na ordem de preferência que a oferta é apresentada ou aceita.

| **Ofertas de criptografia e autenticação IPsec SA** | **Gateway do Azure como iniciador** | Gateway do Azure como respondente |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 com ESP AES\_128 com HMAC nulo |
| 5 | AH SHA1 com ESP AES\_256 com HMAC nulo | AH SHA1 com ESP 3\_DES com HMAC nulo |
| 6 | AH SHA1 com ESP AES\_128 com HMAC nulo | AH MD5 com ESP 3\_DES com HMAC nulo, sem tempos de vida propostos |
| 7 | AH SHA1 com ESP 3\_DES com HMAC nulo | AH SHA1 com ESP 3\_DES SHA1, sem tempos de vida |
| 8 | AH MD5 com ESP 3\_DES com HMAC nulo, sem tempos de vida propostos | AH MD5 com ESP 3\_DES MD5, sem tempos de vida |
| 9 | AH SHA1 com ESP 3\_DES SHA1, sem tempos de vida | ESP DES MD5 |
| 10 | AH MD5 com ESP 3\_DES MD5, sem tempos de vida | ESP DES SHA1, sem tempos de vida |
| 11 | ESP DES MD5 | AH SHA1 com ESP DES HMAC nulo, sem tempos de vida propostos |
| 12 | ESP DES SHA1, sem tempos de vida | AH MD5 com ESP DES HMAC nulo, sem tempos de vida propostos |
| 13 | AH SHA1 com ESP DES HMAC nulo, sem tempos de vida propostos | AH SHA1 com ESP DES SHA1, sem tempos de vida |
| 14 | AH MD5 com ESP DES HMAC nulo, sem tempos de vida propostos | AH MD5 com ESP DES MD5, sem tempos de vida |
| 15 | AH SHA1 com ESP DES SHA1, sem tempos de vida | ESP SHA, sem tempos de vida |
| 16 | AH MD5 com ESP DES MD5, sem tempos de vida | ESP MD5, sem tempos de vida |
| 17 | - | AH SHA, sem tempos de vida | | 18 | - | AH MD5, sem tempos de vida |




- Você pode especificar criptografia IPsec ESP NULL com gateway de VPN com roteamento dinâmico e de alto desempenho, destinado a conexões VNet a VNet nas redes do Azure. 

- Para a conectividade entre locais através da Internet, use as configurações padrão de gateway de VPN do Azure com criptografia e algoritmos de hash listados nas tabelas acima para garantir a segurança da comunicação crítica.

## Próximas etapas


Para saber mais sobre gateways de VPN, consulte [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md).

Para configurar uma VPN site a site, consulte [Criar uma rede virtual com uma conexão VPN site a site](vpn-gateway-site-to-site-create.md).

<!---HONumber=Oct15_HO3-->