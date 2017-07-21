---
title: "Solucionar problemas de conexão de Ponto a Site do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas de conexão de Ponto a Site."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c9d9b099bf71c5b1e51a11e3b62779bbc8767fd6
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Solução de problemas: problemas de conexão de Ponto a Site do Azure

Este artigo lista os problemas comuns de conexão de Ponto a Site que podem ocorrer. Também discute as possíveis causas e soluções para esses problemas.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erro de cliente VPN: não foi possível encontrar um certificado

### <a name="symptom"></a>Sintoma

Quando você tenta se conectar à rede virtual do Microsoft Azure usando o cliente VPN, a seguinte mensagem de erro é exibida:

**Não foi possível encontrar um certificado que possa ser usado com este protocolo EAP. (Erro 798)**

### <a name="cause"></a>Causa

Esse problema ocorre se o certificado do cliente está ausente em **Certificates - Current User\Personal\Certificates** .

### <a name="solution"></a>Solução

Verifique se o certificado do cliente está instalado no seguinte local do repositório de certificados (Certmgr.msc):
 
**Certificates - Current User\Personal\Certificates**

Para saber mais sobre como instalar o certificado do cliente, confira [Gerar e exportar certificados para conexões Ponto a Site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando você importar o certificado do cliente, não selecione a opção **Habilitar a proteção de chave privada forte**.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erro de cliente VPN: a mensagem recebida foi inesperada ou formatada incorretamente

### <a name="symptom"></a>Sintoma

Quando você tenta se conectar à rede virtual usando o cliente VPN, a seguinte mensagem de erro é exibida:

**Erro de cliente VPN: a mensagem recebida foi inesperada ou formatada incorretamente. (Erro 0x80090326)**

### <a name="cause"></a>Causa

Esse problema ocorre se a chave pública do certificado raiz não é carregada no gateway de VPN do Microsoft Azure ou se ela está corrompida ou expirada.

### <a name="solution"></a>Solução

Para resolver esse problema, verifique o status do certificado raiz no portal do Azure para ver se ele foi revogado. Se não foi revogado, tente excluir o certificado raiz e carregá-lo novamente. Para saber mais, confira [Criar certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erro de cliente VPN: uma cadeia de certificados foi processada, mas fechada 

### <a name="symptom"></a>Sintoma 

Quando você tenta se conectar à rede virtual do Azure usando o cliente VPN, a seguinte mensagem de erro é exibida:

**Uma cadeia de certificados foi processada, mas terminava em um certificado raiz que não é confiável pelo provedor de confiabilidade**

### <a name="solution"></a>Solução

1. Verifique se os certificados abaixo estão no local correto:

    | Certificado | Local padrão |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. Se os certificados estiverem no local, tente excluir os certificados e reinstalá-los. O certificado  **azuregateway -*GUID*. cloudapp.net** pode ser encontrado no pacote de configuração de cliente VPN que você baixou do portal do Azure. Você pode usar arquivadores para extrair os arquivos do pacote.

##  <a name="error-file-download-error-target-uri-is-not-specified"></a>Erro: "erro no download do arquivo O URI de destino não foi especificado"

### <a name="symptom"></a>Sintoma

Você vê a seguinte mensagem de erro:

**erro no download do arquivo O URI de destino não foi especificado**

### <a name="cause"></a>Causa 

Esse problema ocorre devido ao tipo de gateway incorreto. 

### <a name="solution"></a>Solução

O tipo de gateway de VPN deve ser **VPN** e o tipo de VPN deve ser **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed-8007026f"></a>Erro de cliente VPN: falha de script VPN Azure personalizado (8007026f)

### <a name="symptom"></a>Sintoma

Quando você tenta se conectar à rede virtual do Azure usando o cliente VPN, a seguinte mensagem de erro é exibida:

**Falha de script personalizado (para atualizar a tabela de roteamento) (8007026f).**

### <a name="cause"></a>Causa

Esse problema pode ocorrer se você está tentando abrir a conexão VPN Ponto a Site usando um atalho.

### <a name="solution"></a>Solução 

Abra o pacote VPN diretamente em vez de abri-lo pelo atalho.

## <a name="cannot-install-the-vpn-client"></a>Não é possível instalar o cliente VPN

### <a name="cause"></a>Causa 

Um certificado adicional é necessário para confiar no gateway de VPN da sua rede virtual. O certificado está incluído no pacote de configuração de cliente VPN que é gerado no portal do Azure.

### <a name="solution"></a>Solução

Extraia o pacote de configuração de cliente VPN. Você encontrará um arquivo .cer. Instale o certificado nas **Autoridades de Certificação Raiz Confiáveis** da **Conta do computador**:

1. Abra mmc.exe
2. Adicione o snap-in **Certificados**.
3. Selecione a conta **Computador** do computador local
4. Clique com botão direito do mouse no nó **Autoridades de Certificação Raiz Confiáveis** >  **Toda a tarefa** > **Importar**e navegue até o arquivo .cer que você extraiu do pacote de configuração do cliente VPN.
5. Reinicie o computador. 
6. Tente instalar o cliente VPN.

## <a name="azure-portal-error-failed-to-save-vpn-gateway-data-is-invalid"></a>Erro do portal do Azure: falha ao salvar gateway de VPN-dados inválidos

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro:

**Falha ao salvar o gateway de rede virtual &lt;nome do gateway&gt;. Dados de erro do certificado &lt;ID do certificado&gt; é Inválido.**

### <a name="cause"></a>Causa 

Esse problema pode ocorrer se a chave pública do certificado raiz que você carregou contém caracteres inválidos, como espaço.

### <a name="solution"></a>Solução

Verifique se os dados no certificado não contêm caracteres inválidos, como quebras de linha (retorno de carro). O valor inteiro deve estar em uma linha longa. O texto abaixo é um exemplo de certificado:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-vpn-gateway-resource-name-is-invalid"></a>Erro do portal do Azure: falha ao salvar gateway de VPN-Nome do recurso inválido

### <a name="symptom"></a>Sintoma

Quando você tenta salvar as alterações do gateway de VPN no portal do Azure, recebe a seguinte mensagem de erro: 

**Falha ao salvar o gateway de rede virtual &lt;nome do gateway&gt;. Nome do recurso de erro &lt;nome do certificado que você está tentando carregar&gt; inválido**.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do certificado contém caracteres inválidos, como um espaço. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erro de portal do Azure: erro de download do arquivo de pacote VPN 503

### <a name="symptom"></a>Sintoma

Ao tentar baixar o pacote de configuração de cliente VPN, você recebe a seguinte mensagem de erro:

**Falha ao baixar o arquivo. Detalhes do erro: erro 503. O servidor está ocupado.**
 
### <a name="solution"></a>Solução

Esse erro pode ser causado por um problema de rede temporário. Tente baixar o pacote VPN novamente após alguns minutos.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Atualização de Gateway de VPN do Azure; nenhum cliente P2S consegue se conectar

### <a name="cause"></a>Causa

Se o certificado passou de 50% de seu tempo de vida, o certificado é substituído.

### <a name="solution"></a>Solução

Para resolver esse problema, crie e redistribua os novos certificados para os clientes VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Muitos clientes VPN conectados ao mesmo tempo

Para cada gateway de VPN, o número máximo de conexões permitidas é 128.  Você pode ver o número total de clientes conectados no portal do Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-route-table"></a>O VPN Ponto a Site adiciona uma rota para 10.0.0.0/8 à Tabela de Rotas incorretamente

### <a name="symptom"></a>Sintoma

Ao discar a conexão VPN no cliente de ponto a site, espera-se que o cliente VPN adicione uma rota para a Rede Virtual do Azure e o serviço Iphelper adicione uma rota para a sub-rede de clientes VPN. 

No entanto, se o intervalo de cliente VPN pertence a uma sub-rede menor de 10.0.0.0/8, como 10.0.12.0/24, em vez de uma rota para 10.0.12.0/24, uma rota "incorreta" para 10.0.0.0/8 é adicionada com maior prioridade. 

Isso interrompe a conectividade com outras redes locais que podem pertencer a outra sub-rede dentro do intervalo 10.0.0.0/8, como 10.50.0.0/24, que não têm uma rota específica definida. 

### <a name="cause"></a>Causa

Esse comportamento ocorre por padrão para clientes do Windows. Quando ele usa o protocolo PPP IPCP, o cliente obtém o endereço IP para a interface de túnel do servidor (gateway de VPN, neste caso). No entanto, devido a uma limitação no protocolo, o cliente não tem a máscara de sub-rede. Como não há nenhuma outra forma de obtê-la, o cliente tenta adivinhar a máscara de sub-rede com base na classe do endereço IP da interface do túnel. 

Portanto, uma rota é adicionada com base no seguinte mapeamento estático: 

Se o endereço pertence à classe A--> aplicar /8

Se o endereço pertence à classe B--> aplicar

Se o endereço pertence à classe C--> aplicar /24

##  <a name="vpn-client-cannot-access-network-file-shares"></a>O cliente VPN não pode acessar compartilhamentos de arquivos de rede

### <a name="symptom"></a>Sintoma

O cliente de VPN foi conectado à rede do Azure. No entanto, o cliente não pode acessar compartilhamentos de rede.

### <a name="cause"></a>Causa

O protocolo SMB é usado para acesso de compartilhamento de arquivos. A falha ocorre quando as credenciais de sessão são adicionadas pelo cliente VPN quando a conexão é iniciada. Depois que a conexão é estabelecida, o cliente é forçado a usar as credenciais de cache para a autenticação Kerberos. Isso inicia consultas Key Distribution Center (um controlador de domínio) para obter um token. Como os clientes se conectam da Internet, eles não podem alcançar o controlador de domínio. Portanto, os clientes não podem realizar failover de Kerberos para NTLM. 

A única vez que o cliente será solicitado a digitar uma credencial é quando ele tem um certificado válido (com SAN = UPN) emitido pelo domínio ao qual o cliente está associado e está fisicamente conectado à rede de domínio. Nesse caso, o cliente tenta usar o certificado e busca o controlador de domínio. Em seguida, o KDC retorna um erro "KDC_ERR_C_PRINCIPAL_UNKNOWN".  Isso força o cliente a fazer failover para NTLM. 

### <a name="solution"></a>Solução

Para contornar o problema, desabilite o cache de credenciais de domínio da seguinte subchave do registro: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-vpn-client"></a>Não é possível localizar a conexão VPN Ponto a Site no Windows após a reinstalação do cliente VPN

### <a name="symptom"></a>Sintoma

Você remove a conexão VPN Ponto a Site e reinstala o cliente VPN. Nessa situação, a conexão VPN não foi configurada com êxito. Você não vê a conexão VPN nas configurações **Conexões de rede** do Windows.

### <a name="solution"></a>Solução

Para resolver o problema, exclua os arquivos de configuração de cliente VPN antigos de **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections** e execute novamente o instalador do cliente VPN.
