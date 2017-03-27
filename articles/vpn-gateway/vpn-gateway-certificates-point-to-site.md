---
title: 'Criar certificados autoassinados para ponto a site: PowerShell: Azure | Microsoft Docs'
description: "Este artigo contém etapas para criar um certificado raiz autoassinado, exportar a chave pública e gerar os certificados do cliente usando o PowerShell no Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cb66edd0c0ff1f0b78232233719dd44329584c78
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell

Conexões ponto a site usam certificados para se autenticar. Quando você configura uma conexão ponto a site, você precisa carregar a chave pública (arquivo .cer) de um certificado raiz no Azure. Este artigo ajuda você a criar um certificado raiz autoassinado, exportar a chave pública e gerar e instalar certificados do cliente.

> [!NOTE]
> Anteriormente, o makecert era o método recomendado para criar certificados raiz autoassinados e gerar certificados do cliente para conexões ponto a site. Agora você pode usar o PowerShell para criar esses certificados. Uma vantagem de usar o PowerShell é a capacidade de criar certificados SHA-2. 
>
>

## <a name="rootcert"></a>Criar um certificado raiz autoassinado

As etapas abaixo o orientarão na criação de um certificado raiz autoassinado usando PowerShell. O Windows 10 é necessário para concluir as etapas a seguir. Os cmdlets e parâmetros que são usados nessas etapas fazem parte do sistema operacional Windows 10, não são parte de uma versão do PowerShell.

1. Em um computador com Windows 10, abra um console do Windows PowerShell com privilégios elevados.
2. Use o exemplo a seguir para criar o certificado raiz autoassinado. O exemplo a seguir cria um certificado raiz autoassinado chamado 'P2SRootCert' que é instalado automaticamente em 'Certificates-Current User\Personal\Certificates'. Você pode exibir o certificado abrindo *certmgr.msc*.

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>Para obter a chave pública

Conexões ponto a site exigem a chave pública (.cer) carregadas no Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado.

1. Para obter um arquivo .cer do certificado, abra **certmgr.msc**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**. Selecione **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base&64; (.CER).** e clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado. Você verá a mensagem **A exportação foi bem-sucedida**. Clique em **OK** para fechar o assistente.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>Para exportar um certificado raiz autoassinado (opcional)
Convém exportar o certificado raiz autoassinado e armazená-lo com segurança. Se precisar, você pode instalá-lo depois em outro computador e gerar mais certificados de cliente, ou exportar outro arquivo .cer.

Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="clientcert"></a>Gerar um certificado do cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.

O Windows 10 é necessário para concluir as etapas a seguir. Os cmdlets e parâmetros que são usados nessas etapas fazem parte do sistema operacional Windows 10, não são parte de uma versão do PowerShell.

### <a name="example-1"></a>Exemplo 1

Este exemplo usa a variável '$cert' declarada da seção anterior. Se você fechou o console do PowerShell após criar o certificado raiz autoassinado ou está criando certificados do cliente adicionais em uma nova sessão de console do PowerShell, use as etapas no exemplo 2.

Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'.  Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>Exemplo 2

Se você estiver criando certificados do cliente adicionais ou não estiver usando a mesma sessão do PowerShell usada para criar o certificado raiz autoassinado, use as seguintes etapas:

1. Identifique o certificado raiz autoassinado instalado no computador. Esse cmdlet retorna uma lista de certificados instalados no seu computador.

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. Localize o nome da entidade da lista retornada e copie a impressão digital localizada ao lado dela para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado raiz autoassinado do qual você deseja gerar um certificado filho. Nesse caso, 'P2SRootCert'.

        Thumbprint                                Subject
        ----------                                -------
        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Declare uma variável para o certificado raiz usando a impressão digital da etapa anterior. Substitua THUMBPRINT pela impressão digital do certificado raiz do qual você deseja gerar um certificado filho.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    Por exemplo, usando a impressão digital para P2SRootCert na etapa anterior, a variável teria esta aparência:

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'. Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>Exportar um certificado do cliente   

Quando você gerar um certificado do cliente, ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado do cliente em outro computador cliente, será necessário exportar o certificado do cliente que você gerou.                              

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Os certificados do cliente que você gerou são, por padrão, localizados em 'Certificates - Current User\Personal\Certificates'. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo** , você pode deixar os padrões selecionados. Em seguida, clique em **Próximo**. 
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.    

## <a name="install"></a>Instalar um certificado do cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados do cliente, instale um certificado do cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Localização do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. Clique em 'Sim' sem medo, pois você gerou o certificado. O certificado foi importado com êxito.

## <a name="next-steps"></a>Próximas etapas
Continue com a configuração de Ponto a Site. 

* Para as etapas do modelo de implantação do **Resource Manager**, veja [Configurar uma conexão Ponto a Site a uma VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).


