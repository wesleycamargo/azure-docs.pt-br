---
title: "Proteger o domínio personalizado de seu aplicativo com HTTPS | Microsoft Docs"
description: "Saiba como proteger o nome de domínio personalizado para seu aplicativo no Serviço de Aplicativo do Azure configurando uma associação do certificado SSL. Você também aprenderá como obter um certificado SSL de várias ferramentas."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 613d7932-73aa-4318-867c-1ce1416224dc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: f7a2066f43219e8748b5c5356ff6c81535b7842a
ms.lasthandoff: 03/17/2017


---
# <a name="secure-your-apps-custom-domain-with-https"></a>Proteger o domínio personalizado de seu aplicativo com HTTPS
> [!div class="op_single_selector"]
> * [Comprar certificado SSL no Azure](web-sites-purchase-ssl-web-site.md)
> * [Usar certificado SSL de outro lugar](web-sites-configure-ssl-certificate.md)
> 
> 

Este artigo mostra como habilitar HTTPS para um aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) usando um nome de domínio personalizado. O artigo aborda a autenticação somente de servidor. Se você precisar de informações sobre autenticação mútua (incluindo autenticação de cliente), consulte [Como configurar a autenticação mútua TLS para o Serviço de Aplicativo](app-service-web-configure-tls-mutual-auth.md).

Para proteger com HTTPS um aplicativo com nome de domínio personalizado, adicione um certificado para esse nome de domínio. Por padrão, o Azure protege o domínio curinga **\*. azurewebsites.net** com um único certificado SSL, de modo que seus clientes possam acessar o aplicativo em **https://*&lt;appname>*.azurewebsites.net**. Mas se você quiser usar um domínio personalizado, como**contoso.com**, **www.contoso.com**e**\*.contoso.com**, o certificado padrão não poderá protegê-lo. Além disso, assim como todos os [certificados curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), o certificado padrão não é tão seguro quanto usar um domínio personalizado e um certificado para esse domínio personalizado.   

> [!NOTE]
> Obtenha ajuda dos especialistas do Azure a qualquer momento nos [fóruns do Azure](https://azure.microsoft.com/support/forums/). Para obter um suporte mais personalizado, vá até [Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.
> 
> 

<a name="bkmk_domainname"></a>

## <a name="what-you-need"></a>O que você precisa
Para proteger o seu nome de domínio personalizado com HTTPS, associe um certificado SSL personalizado a esse domínio personalizado no Azure. Antes de associar um certificado personalizado, você precisa fazer o seguinte:

* **Configurar o domínio personalizado** - o Serviço de Aplicativo só permite adicionar um certificado para um nome de domínio que já está configurado no seu aplicativo. Para obter instruções, consulte [Mapear um nome de domínio personalizado para um aplicativo do Azure](web-sites-custom-domain-name.md). 
* **Escalar verticalmente até a camada Básica ou superior** – planos do Serviço de Aplicativo em camadas de preços inferiores não dão suporte a certificados SSL personalizados. Para obter instruções, consulte [Escalar verticalmente um aplicativo no Azure](web-sites-scale.md). 
* **Obter um certificado SSL** – se ainda não tiver, você precisará obter um de uma [AC](http://en.wikipedia.org/wiki/Certificate_authority) (autoridade de certificação) confiável. O certificado deve atender todos os requisitos a seguir:
  
  * ser assinado por uma AC confiável (sem o uso de servidores de AC).
  * conter uma chave privada.
  * ter sido criado para troca de chaves e exportado para um arquivo .PFX.
  * usar uma criptografia mínima de 2048 bits.
  * o nome da entidade corresponder ao domínio personalizado que ele precisa proteger. Para proteger vários domínios com um certificado, você precisa usar um nome curinga (por exemplo, **\*.contoso.com**) ou especificar valores de subjectAltName.
  * Ele é mesclado com todos os **[certificados intermediários](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** usados pela sua autoridade de certificação. Caso contrário, você poderá se deparar com problemas de interoperabilidade irreproduzíveis em alguns clientes.
    
    > [!NOTE]
    > A maneira mais fácil de obter um certificado SSL que atenda todos os requisitos é [comprar um certificado diretamente no portal do Azure](web-sites-purchase-ssl-web-site.md). Este artigo mostra como fazer isso manualmente e, depois, associá-lo ao seu domínio personalizado no Serviço de Aplicativo.
    > 
    > **Certificados ECC (Criptografia de Curva Elíptica)** podem funcionar com o Serviço de Aplicativo, mas fora do escopo deste artigo. Trabalhe com sua AC seguindo exatamente estas etapas para criar certificados ECC.
    > 
    > 

<a name="bkmk_getcert"></a>

## <a name="step-1-get-an-ssl-certificate"></a>Etapa 1. Obter um certificado SSL
Como as CAs fornecem diferentes tipos de certificado SSL com níveis de preços diferentes, comece decidindo o tipo de certificado SSL que comprará. Para proteger um único nome de domínio (**www.contoso.com**), basta um certificado básico. Para proteger vários nomes de domínio (**contoso.com** *e* **www.contoso.com** 
*e* **mail.contoso.com**), você precisará de um [certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate) ou de um certificado com [Nome Alternativo da Entidade](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Depois de decidir qual certificado SSL comprar, você envia uma CSR (Solicitação de Assinatura de Certificado) a uma AC. Após receber o certificado solicitado da AC, você gera um arquivo .pfx do certificado. É possível executar essas etapas usando a ferramenta de sua preferência. Estas são instruções para as ferramentas comuns:

* [Etapas do Certreq.exe](#bkmk_certreq) – o utilitário do Windows para criar solicitações de certificado. 
  Ele faz parte do Windows desde o Windows XP/Windows Server 2000.
* [Etapas do IIS Manager](#bkmk_iismgr) – a ferramenta preferencial se você já estiver familiarizado com ela.
* [Etapas do OpenSSL](#bkmk_openssl) – uma [ferramenta de software livre entre plataformas](https://www.openssl.org). Utilize-a para ajudá-lo a obter um certificado SSL de qualquer plataforma.
* [Etapas de subjectAltName usando OpenSSL](#bkmk_subjectaltname) – etapas para obter certificados `subjectAltName`.

Se quiser testar a configuração no Serviço de Aplicativo antes de comprar um certificado, você poderá gerar um [certificado autoassinado](https://en.wikipedia.org/wiki/Self-signed_certificate). Este tutorial descreve duas maneiras de gerá-lo:

* [Certificado autoassinado, etapas do Certreq.exe](#bkmk_sscertreq)
* [Certificado autoassinado, etapas do OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>

### <a name="get-a-certificate-using-certreqexe"></a>Obtenha um certificado usando Certreq.exe
1. Crie um arquivo (por exemplo, **myrequest.txt**), copie nele o seguinte texto e salve-o em um diretório de trabalho. 
   Substitua o espaço reservado `<your-domain>` pelo nome de domínio personalizado de seu aplicativo.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = FALSE
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    Para obter mais informações sobre as opções na CSR, bem como outras opções disponíveis, consulte a [Documentação de referência do Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. Em um prompt de comando, `CD` no seu diretório de trabalho e execute o seguinte comando para criar o CSR:
   
        certreq -new myrequest.txt myrequest.csr
   
    **myrequest.csr** foi criado no seu diretório de trabalho atual.
3. Envie **myrequest.csr** para uma AC para obter um certificado SSL. Carregue o arquivo ou copie seu conteúdo de um editor de texto para um formulário da Web.
   
    Para obter uma lista de ACs em que a Microsoft confia, consulte [Programa de Certificado Raiz Confiável da Microsoft: participantes][cas].
4. Após a AC ter respondido com um arquivo de certificado (.CER), salve-o no seu diretório de trabalho. Em seguida, execute o comando a seguir para concluir o CSR pendente.
   
        certreq -accept -user <certificate-name>.cer
   
    Esse comando armazena o certificado concluído no repositório de certificados do Windows.
5. Se a sua AC usar certificados intermediários, instale-os antes de continuar. Geralmente, eles são fornecidos como um download separado pela AC, em vários formatos para diferentes tipos de servidor Web. Selecione a versão relativa ao IIS da Microsoft.
   
    Depois que você tiver baixado os certificados, clique com o botão direito do mouse em cada um deles no Windows Explorer e selecione  **Instalar certificado**. Use os valores padrão no o **Assistente de importação de certificados** e continue selecionando **Próximo** até que a importação seja concluída.
6. Para exportar o certificado SSL do repositório de certificados, pressione `Win`+`R` e execute **certmgr.msc** para iniciar o Gerenciador de Certificados. 
   Selecione **Pessoal** > **Certificados**. Na coluna **Emitido para**, você deverá ver uma entrada com seu nome de domínio personalizado e a AC usada para gerar o certificado na coluna **Emitido por**.
   
    ![inserir a imagem do gerenciador de certificado aqui][certmgr]
7. Clique com o botão direito do mouse no certificado e selecione **Todas as Tarefas** > **Exportar**. No **Assistente para Exportação de Certificados**, clique em **Avançar**, selecione **Sim, exportar a chave privada** e clique em **Avançar** novamente.
   
    ![Exporte a chave particular][certwiz1]
8. Selecione **Troca de Informações Pessoais – PKCS nº 12**, **Incluir todos os certificados no caminho do certificado, se possível** e **Exportar todas as propriedades estendidas**. Em seguida, clique em **Avançar**.
   
   ![incluir todos os certificados e propriedades estendidas][certwiz2]
9. Selecione **Senha**, insira e confirme a senha. Clique em **Próximo**.
   
   ![especifique uma senha][certwiz3]
10. Forneça um caminho e nome de arquivo para o certificado exportado, com a extensão **.pfx**. Clique em **Avançar** para concluir.
    
    ![forneça um caminho do arquivo][certwiz4]

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>

### <a name="get-a-certificate-using-the-iis-manager"></a>Obtenha um certificado usando o Gerenciador do IIS
1. Gere uma CSR com o IIS Manager para enviar à AC. Para obter mais informações sobre como gerar um CSR, consulte [Solicitar um Certificado do Servidor para a Internet (IIS 7)][iiscsr].
2. Envie sua CSR a uma AC para obter um certificado SSL. Para obter uma lista de ACs em que a Microsoft confia, consulte [Programa de Certificado Raiz Confiável da Microsoft: participantes][cas].
3. Conclua a CSR com o certificado que a AC lhe enviar. Para obter mais informações sobre a conclusão do CSR, consulte [Instalar um Certificado do Servidor para a Internet (IIS 7)][installcertiis].
4. Se a sua AC usar certificados intermediários, instale-os antes de continuar. Geralmente, eles são fornecidos como um download separado pela AC, em vários formatos para diferentes tipos de servidor Web. Selecione a versão relativa ao IIS da Microsoft.
   
    Depois que você tiver baixado os certificados, clique com o botão direito do mouse em cada um deles no Windows Explorer e selecione **Instalar certificado**. 
    Use os valores padrão no o **Assistente de importação de certificados** e continue selecionando **Próximo** até que a importação seja concluída.
5. Exporte o certificado SSL do IIS Manager. Para obter mais informações sobre como exportar o certificado, consulte [Exportar um certificado do servidor (IIS 7)][exportcertiis]. 
   
   > [!IMPORTANT]
   > No **Assistente para Exportação de Certificados**, selecione **Sim, exportar a chave privada**  
   > 
   > ![Exporte a chave particular][certwiz1]  
   > 
   > e selecione **Troca de Informações Pessoais – PKCS nº 12**, **Incluir todos os certificados no caminho do certificado, se possível** e **Exportar todas as propriedades estendidas**.
   > 
   > ![incluir todos os certificados e propriedades estendidas][certwiz2]
   > 
   > 

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_openssl"></a>

### <a name="get-a-certificate-using-openssl"></a>Obter um certificado usando OpenSSL
1. Em um terminal de linha de comando, `CD` em um diretório de trabalho, gere uma chave privada e uma CSR executando o seguinte comando:
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048
2. Quando solicitado, digite as informações adequadas. Por exemplo:
   
         Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:
   
        Please enter the following 'extra' attributes to be sent with your certificate request
   
           A challenge password []:
   
    Quando terminar, você deverá ter dois arquivos no diretório de trabalho: **myserver.key** e **server.csr**. 
    O **server.csr** contém o CSR e você precisará do **myserver.key** mais tarde.
3. Envie sua CSR a uma AC para obter um certificado SSL. Para obter uma lista de ACs em que a Microsoft confia, consulte [Programa de Certificado Raiz Confiável da Microsoft: participantes][cas].
4. Após a AC lhe enviar o certificado solicitado, salve-o em um arquivo chamado **myserver.crt** no diretório de trabalho. Caso a AC o forneça em formato de texto, basta copiar o conteúdo em **myserver.crt** em um editor de texto e salvá-lo. Seu arquivo deverá ter a seguinte aparência:
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
5. No terminal de linha de comando, execute o seguinte comando para exportar o **myserver.pfx** de **myserver.key** e **myserver.crt**:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando solicitado, defina uma senha para proteger o arquivo.pfx.
   
   > [!NOTE]
   > Se a sua AC usar certificados intermediários, inclua-os com o parâmetro `-certfile`. Geralmente, eles são fornecidos como um download separado pela AC, em vários formatos para diferentes tipos de servidor Web. Selecione a versão com a extensão `.pem` .
   > 
   > O comando `openssl -export` deve ser semelhante ao exemplo a seguir, que cria um arquivo .pfx que inclui os certificados intermediários do arquivo **intermediate-cets.pem** :
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>

### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Obtenha um certificado SubjectAltName usando OpenSSL
1. Crie um arquivo chamado **sancert.cnf**, copie nele o seguinte texto e salve-o em um diretório de trabalho:
   
        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----
   
    Na linha que começa com `subjectAltName`, substitua o valor por todos os nomes de domínio que você deseja proteger (além de  `commonName`). Por exemplo:
   
        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
   
    Você não precisa alterar nenhum outro campo, incluindo `commonName`. Você será solicitado a especificá-los nas próximas etapas.
2. Em um terminal de linha de comando, `CD` no seu diretório de trabalho e execute o seguinte comando:
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf
3. Quando solicitado, digite as informações adequadas. Por exemplo:
   
         Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
   
    Quando terminar, você deverá ter dois arquivos no diretório de trabalho: **myserver.key** e **server.csr**. 
    O **server.csr** contém o CSR e você precisará do **myserver.key** mais tarde.
4. Envie sua CSR a uma AC para obter um certificado SSL. Para obter uma lista de ACs em que a Microsoft confia, consulte [Programa de Certificado Raiz Confiável da Microsoft: participantes][cas].
5. Após a AC lhe enviar o certificado solicitado, salve-o em um arquivo chamado **myserver.crt**. Caso a AC o forneça em formato de texto, basta copiar o conteúdo em **myserver.crt** em um editor de texto e salvá-lo. O arquivo deverá ter a seguinte aparência:
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
6. No terminal de linha de comando, execute o seguinte comando para exportar o **myserver.pfx** de **myserver.key** e **myserver.crt**:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando solicitado, defina uma senha para proteger o arquivo.pfx.
   
   > [!NOTE]
   > Se a sua AC usar certificados intermediários, inclua-os com o parâmetro `-certfile`. Geralmente, eles são fornecidos como um download separado pela AC, em vários formatos para diferentes tipos de servidor Web. Selecione a versão com a extensão `.pem` .
   > 
   > O comando `openssl -export` deve ser semelhante ao exemplo a seguir, que cria um arquivo .pfx que inclui os certificados intermediários do arquivo **intermediate-cets.pem** :
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>

### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Gerar um certificado autoassinado usando Certreq.exe
> [!IMPORTANT]
> Certificados autoassinados são somente para fins de teste. A maioria dos navegadores retorna erros ao visitar um site protegido por um certificado autoassinado. Alguns navegadores podem até mesmo se recusar a navegar no site. 
> 
> 

1. Crie um arquivo de texto (por exemplo, **mycert.txt**), copie nele o seguinte texto e salve-o em um diretório de trabalho. 
   Substitua o espaço reservado `<your-domain>` pelo nome de domínio personalizado de seu aplicativo.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    O parâmetro importante é `RequestType = Cert`, que especifica um certificado autoassinado. 
    Para obter mais informações sobre as opções na CSR, bem como outras opções disponíveis, consulte a [Documentação de referência do Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. No prompt de comando, `CD` no seu diretório de trabalho e execute o seguinte comando:
   
        certreq -new mycert.txt mycert.crt
   
    O novo certificado autoassinado está instalado no repositório de certificados.
3. Para exportar o certificado do repositório de certificados, pressione `Win`+`R` e execute **certmgr.msc** para iniciar o Gerenciador de Certificados. 
   Selecione **Pessoal** > **Certificados**. Na coluna **Emitido para**, você deverá ver uma entrada com seu nome de domínio personalizado e a AC usada para gerar o certificado na coluna **Emitido por**.
   
    ![inserir a imagem do gerenciador de certificado aqui][certmgr]
4. Clique com o botão direito do mouse no certificado e selecione **Todas as Tarefas** > **Exportar**. No **Assistente para Exportação de Certificados**, clique em **Avançar**, selecione **Sim, exportar a chave privada** e clique em **Avançar** novamente.
   
    ![Exporte a chave particular][certwiz1]
5. Selecione **Troca de Informações Pessoais – PKCS nº 12**, **Incluir todos os certificados no caminho do certificado, se possível** e **Exportar todas as propriedades estendidas**. Em seguida, clique em **Avançar**.
   
   ![incluir todos os certificados e propriedades estendidas][certwiz2]
6. Selecione **Senha**, insira e confirme a senha. Clique em **Próximo**.
   
   ![especifique uma senha][certwiz3]
7. Forneça um caminho e nome de arquivo para o certificado exportado, com a extensão **.pfx**. Clique em **Avançar** para concluir.
   
   ![forneça um caminho do arquivo][certwiz4]

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>

### <a name="generate-a-self-signed-certificate-using-openssl"></a>Gerar um certificado autoassinado usando OpenSSL
> [!IMPORTANT]
> Certificados autoassinados são somente para fins de teste. A maioria dos navegadores retorna erros ao visitar um site protegido por um certificado autoassinado. Alguns navegadores podem até mesmo se recusar a navegar no site. 
> 
> 

1. Crie um arquivo de texto chamado **serverauth. cnf**, copie o seguinte conteúdo nele e salve-o em um diretório de trabalho:
   
        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca
   
        [ req_distinguished_name ]
        countryName            = Country Name (2 letter code)
        countryName_min            = 2
        countryName_max            = 2
        stateOrProvinceName        = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName        = Organization Name (eg, company)
        organizationalUnitName        = Organizational Unit Name (eg, section)
        commonName            = Common Name (eg, your app's domain name)
        commonName_max            = 64
        emailAddress            = Email Address
        emailAddress_max        = 40
   
        [ req_attributes ]
        challengePassword        = A challenge password
        challengePassword_min        = 4
        challengePassword_max        = 20
   
        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth
2. Em um terminal de linha de comando, `CD` no seu diretório de trabalho e execute o seguinte comando:
   
        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
   
    Esse comando cria dois arquivos: **myserver.crt** (o certificado autoassinado) e **myserver.key** (a chave privada), com base nas configurações em **serverauth.cnf**.
3. Exporte o certificado para um arquivo .pfx executando o seguinte comando:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando solicitado, defina uma senha para proteger o arquivo.pfx.

Agora, você está pronto para carregar o arquivo PFX exportado no Serviço de Aplicativo. Consulte a [Etapa 2. Carregar e associar o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>

## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Etapa 2. Carregar e associar o certificado SSL personalizado
Antes de prosseguir, consulte a seção [Do que você precisa](#bkmk_domainname) e verifique se:

* você tem um domínio personalizado que é mapeado para o aplicativo do Azure,
* o aplicativo está sendo executado na camada **Básica** ou superior, e
* você tem um certificado SSL para o domínio personalizado de uma AC.

1. No seu navegador, abra o **[Portal do Azure.](https://portal.azure.com/)**
2. Clique na opção **Serviço de Aplicativo** no lado esquerdo da página.
3. Clique no nome do aplicativo ao qual você deseja atribuir a esse certificado. 
4. Em **Configurações**, clique em **Certificados SSL**
5. Clique em **Carregar Certificado**
6. Selecione o arquivo .pfx exportado na [Etapa 1](#bkmk_getcert) e especifique a senha que você criou anteriormente. 
   Clique em **Carregar** para carregar o certificado. Agora, você deverá ver seu certificado carregado na folha **Certificado SSL** .
7. Na seção **associações ssl**, clique em **Adicionar associações**
8. Na folha **Adicionar Associação SSL** , use os menus suspensos para selecionar o nome de domínio a ser protegido com SSL e o certificado a ser usado. Você também pode selecionar se deseja usar **[SNI (Indicação de Nome de Servidor)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseado em IP.
   
    ![inserir imagem de Associações SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)
   
    > [!NOTE] 
    > O **SSL baseado em IP** associa um certificado a um nome de domínio mapeando um endereço IP público dedicado do servidor para o nome de domínio. Isso exige que cada nome de domínio (contoso.com, fabricam.com etc.), associado ao seu serviço tenha um endereço IP dedicado. Esse é o método tradicional de associar certificados SSL a um servidor web.  
    >
    > O **SSL baseado em SNI** é uma extensão do SSL e do **[protocolo TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security)** que permite que vários domínios compartilhem o mesmo endereço IP, com certificados de segurança separados para cada domínio. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte ao SNI. No entanto, navegadores mais antigos podem não dar esse suporte. Para obter mais informações sobre o SNI, consulte o artigo **[Indicação de Nome de Servidor](http://en.wikipedia.org/wiki/Server_Name_Indication)** no Wikipédia.
    > 

9. Clique em **Adicionar Associação** para salvar as alterações e habilitar o SSL.

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Etapa 3. Alterar o mapeamento do nome de domínio (somente para SSL baseado em IP)
Se você usar somente associações **SSL SNI** , ignore esta seção. Várias associações **SSL SNI** podem funcionar juntas no endereço IP compartilhado existente atribuído ao seu aplicativo. No entanto, se você criar uma associação de **SSL baseado em IP**, o Serviço de Aplicativo criará um endereço IP dedicado para a associação, porque o **SSL baseado em IP** requer um. Somente um endereço IP dedicado pode ser criado, portanto, apenas uma associação de **SSL baseado em IP** pode ser adicionada.

Devido a esse endereço IP dedicado, você precisará configurar mais o aplicativo se:

* você [tiver usado um registro A para mapear seu domínio personalizado](web-sites-custom-domain-name.md#a) para o aplicativo do Azure e tiver adicionado uma associação de **SSL baseado em IP** . Nesse cenário, você precisará remapear o registro A existente para apontar para o endereço IP dedicado seguindo estas etapas:
  
  1. Depois de ter configurado uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Encontre esse endereço IP na página **Domínio personalizado** nas configurações do aplicativo, logo acima da seção **Nomes de host**. Ele estará listado como **Endereço IP Externo**
     
      ![Endereço IP virtual](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)
  2. [Remapeie o registro A de seu nome de domínio personalizado para esse novo endereço IP](web-sites-custom-domain-name.md#a).
* Você já tem uma ou mais associações **SSL SNI** em seu aplicativo e acabou de adicionar uma associação **SSL baseada em IP**. 
  Após a associação ser concluída, seu nome de domínio *&lt;appname>*.azurewebsites.net apontará para o novo endereço IP. 
  Portanto, qualquer [mapeamento CNAME do domínio personalizado](web-sites-custom-domain-name.md#cname) para *&lt;appname>*.azurewebsites.net, incluindo aqueles que o **SSL SNI** protege, também receberá o tráfego no novo endereço, que é criado somente para o **SSL baseado em IP**. Nesse cenário, você precisa enviar o tráfego **SSL SNI** para o endereço IP compartilhado original executando as seguintes etapas:
  
  1. Identificar todos os [mapeamentos CNAME de domínios personalizados](web-sites-custom-domain-name.md#cname) para seu aplicativo que tenham uma associação **SSL SNI** .
  2. Remapear cada registro CNAME para **sni.**&lt;appname>.azurewebsites.net em vez de &lt;appname>.azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Etapa 4. Testar o HTTPS para seu domínio personalizado
Agora, tudo o que resta fazer é certificar-se de que o HTTPS funcione com seu domínio personalizado. Em vários navegadores, navegue até `https://<your.custom.domain>` para ver se ele leva até seu aplicativo.

* Caso seu aplicativo retorne erros de validação de certificado, você provavelmente está usando um certificado autoassinado.
* Se não for o caso, talvez você tenha deixado certificados intermediários quando exportou o certificado .pfx. Volte para [Do que você precisa](#bkmk_domainname) para verificar se sua CSR atende todos os requisitos do Serviço de Aplicativo.

<a name="bkmk_enforce"></a>

## <a name="enforce-https-on-your-app"></a>Impor HTTPS no seu aplicativo
Se ainda quiser permitir acesso HTTP ao seu aplicativo, ignore esta etapa. O Serviço de Aplicativo *não* impõe o HTTPS, de modo que os visitantes ainda podem acessar seu aplicativo usando HTTP. Se quiser impor HTTPS para seu aplicativo, você poderá definir uma regra de reescrita no arquivo `web.config` de seu aplicativo. Todo aplicativo do Serviço de Aplicativo tem esse arquivo, independentemente da estrutura de linguagem do seu aplicativo.

> [!NOTE]
> Há um redirecionamento de solicitações específico a um idioma. ASP.NET MVC pode usar o filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) em vez da regra de reescrita em `web.config` (consulte [Implantar um aplicativo ASP.NET MVC 5 seguro em um aplicativo Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).
> 
> 

Siga estas etapas:

1. Navegue até o console de depuração Kudu de seu aplicativo. O endereço é `https://<appname>.scm.azurewebsites.net/DebugConsole`.
2. No console de depuração, use o CD para `D:\home\site\wwwroot`.
3. Abra `web.config` clicando no botão de lápis.
   
    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)
   
    Se você implantar seu aplicativo com o Visual Studio ou Git, o Serviço de Aplicativo gerará automaticamente o `web.config` adequado para seu aplicativo .NET, PHP, Node.js ou Python na raiz do aplicativo. 
    Se `web.config` não existir, execute `touch web.config` no prompt de comando baseado na Web para criá-lo. Ou você pode criá-lo em seu projeto local e reimplantar o código.
4. Se você precisou criar um `web.config`, copie o código a seguir nele e salve-o. Caso tenha aberto um web.config existente, basta copiar toda a marcação `<rule>` no elemento `configuration/system.webServer/rewrite/rules` do seu `web.config`.
   
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>
   
    Essa regra retorna um HTTP 301 (redirecionamento permanente) para o protocolo HTTPS sempre que o usuário solicitar uma página usando HTTP. Ele redireciona de http://contoso.com para https://contoso.com.
   
   > [!IMPORTANT]
   > Se já houver outras marcações `<rule>` em seu `web.config`, coloque a marcação copiada `<rule>` antes das outras marcações `<rule>`.
   > 
   > 
5. Salve o arquivo no console de depuração Kudu. Ele deve entrar em vigor imediatamente e redirecionar todas as solicitações para HTTPS.

Para obter mais informações sobre o Módulo de Reescrita de URL do IIS, consulte a documentação [Reescrita de URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="more-resources"></a>Mais Recursos
* [Central de confiabilidade do Microsoft Azure](/support/trust-center/security/)
* [Opções de configuração desbloqueadas nos Sites do Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Habilitar registro em log de diagnóstico](web-sites-enable-diagnostic-log.md)
* [Configurar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-configure.md)
* [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png



