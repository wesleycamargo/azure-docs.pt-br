
Este artigo mostra como configurar o HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure. Ele não aborda a autenticação de certificado de cliente. Para saber mais sobre isso, consulte [Como configurar a autenticação mútua TLS para aplicativos Web](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md).

Por padrão, o Azure já habilita o HTTPS para seu aplicativo com um certificado curinga para o domínio * azurewebsites.net. Se não planeja configurar um domínio personalizado, você pode aproveitar o certificado HTTPS padrão. No entanto, como com [todos os domínios curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), ele não é tão seguro quanto usar um domínio personalizado com seu próprio certificado.

O restante deste documento fornece detalhes sobre como habilitar HTTPS para domínios personalizados, como **contoso.com**, **www.contoso.com** ou ***.contoso.com**

<a name="bkmk_domainname"></a>
## Habilitar SSL para seu domínio personalizado

Para habilitar HTTPS para um domínio personalizado, como **contoso.com**, você deve primeiro [configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](../articles/app-service-web/web-sites-custom-domain-name.md). Em seguida, faça o seguinte:

1. [Obter um Certificado SSL](#bkmk_getcert)
2. [Configurar o tipo de preço Standard ou Premium](#bkmk_standardmode)
2. [Configurar o SSL em seu aplicativo](#bkmk_configuressl)
3. [Reforçar o SSL em seu aplicativo](#bkmk_enforce) (opcional)

Se precisar de mais ajuda em qualquer momento neste artigo, você pode entrar em contato com os especialistas do Azure nos [fóruns do Azure no MSDN e do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

<a name="bkmk_getcert"></a>
## 1\. Obter um Certificado SSL

Antes de solicitar um certificado SSL, você deve primeiro determinar quais nomes de domínio serão protegidos pelo certificado. Isso determinará o tipo de certificado que você precisa ter. Se você só precisa garantir um único nome de domínio, como **contoso.com** ou **www.contoso.com**, um certificado básico é suficiente. Se você precisa proteger vários nomes de domínio, como **contoso.com**, **www.contoso.com** e **mail.contoso.com**, você poderá obter um [certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate) ou um certificado com [Nome alternativo da entidade](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

Certificados SSL usados com o Serviço de Aplicativo devem ser assinados por uma AC ([Autoridade de Certificação](http://en.wikipedia.org/wiki/Certificate_authority)). Se você ainda não tiver um, precisará obter junto a uma empresa que emita certificados SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome da entidade do certificado deve corresponder ao domínio usado para acessar o aplicativo. Se for necessário atender a vários domínios com esse certificado, você precisará usar um valor curinga ou especificar valores subjectAltName conforme discutido anteriormente.
* O certificado deve usar, no mínimo, uma criptografia de 2.048 bits.
* O Serviço de Aplicativo do Azure não dá suporte a certificados emitidos por servidores privados de AC.

Para obter um certificado SSL para uso com o Serviço de Aplicativo do Azure, envie uma CSR (Solicitação de Assinatura de Certificado) para uma autoridade de certificação e, em seguida, gere um arquivo .pfx por meio do certificado que você receber de volta. Você pode fazer isso usando a ferramenta de sua escolha. A seguir estão algumas das maneiras comuns para obter um certificado:

- [Obter um certificado usando Certreq.exe](#bkmk_certreq)
- [Obter um certificado usando o Gerenciador do IIS](#bkmk_iismgr)
- [Obter um certificado usando OpenSSL](#bkmk_openssl)
- [Obter um certificado SubjectAltName usando OpenSSL](#bkmk_subjectaltname)
- [Gerar um certificado auto-assinado (somente para teste)](#bkmk_selfsigned)

> [AZURE.NOTE] **Ao seguir as etapas, será solicitado que você insira um Nome Comum**, como `www.contoso.com`. Para certificados curinga, esse valor deve ser *.domainname (por exemplo, *.contoso.com). Se você precisar dar suporte para um nome curinga como *.contoso.com e um nome de domínio raiz como contoso.com, é necessário usar um certificado curinga subjectAltName.
>
> Há suporte aos Certificados ECC (Criptografia de Curva Elíptica) com o Serviço de Aplicativo do Azure; no entanto, eles são relativamente novos, e é necessário planejar as etapas exatas para a criação da CSR junto à sua AC.

Talvez você também precise obter **[certificados intermediários](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (também conhecidos como certificados de cadeia), caso eles sejam usados por sua AC. O uso de certificados intermediários é considerado mais seguro do que “certificados sem cadeia”, por isso é comum que uma AC os use. Certificados intermediários geralmente são fornecidos como um download separado do site das ACs. As etapas neste artigo mostram como garantir que todos os certificados intermediários sejam mesclados com o certificado carregado em seus aplicativos.

<a name="bkmk_certreq"></a>
### Obter um certificado usando Certreq.exe (somente Windows)

O certreq.exe é um utilitário do Windows para criar solicitações de certificado. Ele faz parte da instalação básica do Windows desde o Windows XP/Windows Server 2000, por isso deve estar disponível nos sistemas Windows recentes. Siga as etapas para obter um certificado SSL usando o certreq.exe.

1. Abra o **Bloco de notas** e crie um novo documento com o seguinte. Substitua **mysite.com** na linha do Assunto pelo nome de domínio personalizado de seu aplicativo. Por exemplo, Assunto = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Para saber mais sobre as opções especificadas acima, bem como outras opções disponíveis, consulte a [documentação de referência do Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Salve o arquivo de texto como **myrequest.txt**.

3. Na **tela Iniciar** ou no **Menu Iniciar**, execute o **cmd.exe**.

4. No prompt de comando, use o seguinte comando para criar o arquivo de solicitação de certificado:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Especifique o caminho para o arquivo **myrequest.txt** criado na etapa 1 e o caminho a ser usado para o arquivo **myrequest.csr**.

5. Envie o **myrequest.csr** a uma Autoridade de Certificação para obter um certificado SSL. Pode ser necessário carregar o arquivo ou abri-lo no bloco de notas e colar o conteúdo diretamente no formulário da web.

	Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

6. Depois que a Autoridade de Certificação fornecer um arquivo de certificado (.CER), você deverá salvá-lo no computador usado para gerar a solicitação e usar o seguinte comando para aceitar a solicitação e concluir o processo de geração do certificado.

		certreq -accept -user mycert.cer

	Nesse caso, o certificado **mycert.cer** recebido da Autoridade de Certificação será usado para concluir a assinatura do certificado. Nenhum arquivo será criado. Em vez disso, o certificado será armazenado no repositório de certificados do Windows.

6. Se sua AC usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

	Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

7. Para exportar o certificado do repositório de certificados, execute **certmgr.msc** na **tela Iniciar** ou no **Menu Iniciar**. Quando o **Gerenciador de certificados** aparecer, expanda a pasta **Pessoal** e selecione **Certificados**. No campo **Emitido Para**, pesquise a entrada com o nome de domínio personalizado referente ao certificado solicitado. No campo **Emitido Por** deve aparecer a Autoridade de Certificação usada para esse certificado.

	![inserir a imagem do gerenciador de certificado aqui][certmgr]

9. Clique com o botão direito no certificado e selecione **Todas as Tarefas** e depois **Exportar**. No **Assistente de exportação de certificados**, clique em **Avançar** e, em seguida, selecione **Sim, exportar a chave particular**. Clique em **Próximo**.

	![Exporte a chave particular][certwiz1]

10. Selecione **Troca de Informações Pessoais - PKCS #12**, **Incluir Todos os Certificados na Cadeia de Certificados** e **Exportar Todas as Propriedades Estendidas**. Clique em **Próximo**.

	![incluir todos os certificados e propriedades estendidas][certwiz2]

11. Selecione **Senha**, insira e confirme a senha. Clique em **Próximo**.

	![especifique uma senha][certwiz3]

12. Determine um caminho e o nome do arquivo que conterá o certificado exportado. O nome do arquivo deve ter uma extensão **.pfx**. Clique em **Avançar** para concluir o processo.

	![forneça um caminho do arquivo][certwiz4]

Agora você pode carregar o arquivo PFX exportado para seu aplicativo no Serviço de Aplicativo do Azure.

<a name="bkmk_openssl"></a>
### Obter um certificado usando OpenSSL

1. Gere uma chave particular e uma Solicitação de Assinatura de Certificado (CSR) usando o seguinte de uma sessão de linha de comando, bash ou terminal:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

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

	Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

3. Envie sua CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

4. Depois de obter um certificado de uma autoridade de certificação, salve-o em um arquivo chamado **myserver.crt**. Se a autoridade de certificação tiver fornecido o certificado em formato de texto, cole o texto do certificado no arquivo **myserver.crt**. O conteúdo do arquivo deve ser semelhante ao seguinte quando visualizado em um editor de texto:

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

	Salve o arquivo.

5. Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter o **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelo Serviço de Aplicativo do Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	> [AZURE.NOTE] Se sua AC usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão que é fornecida como um arquivo PEM (extensão de arquivo .pem).
	>
	> O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermediários contidos no arquivo **intermediate-cets.pem**:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Depois de executar este comando, você deve ter um arquivo **myserver.pfx** adequada para uso no Serviço de Aplicativo do Azure.

<a name="bkmk_iismgr"></a>
### Obtenha um certificado usando o Gerenciador do IIS

Se você estiver familiarizado com o Gerenciador do IIS, é possível usá-lo para gerar um certificado que pode ser usado com o Serviço de Aplicativo do Azure.

1. Gere uma CSR com o Gerenciador do IIS para enviar à Autoridade de Certificação. Para obter mais informações sobre como gerar um CSR, consulte [Solicitar um certificado de servidor para a Internet (IIS 7)][iiscsr].

2. Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

3. Conclua o CSR com o certificado fornecido pelo fornecedor de Autoridade de Certificação. Para obter mais informações sobre a conclusão do CSR, consulte [Instalar um certificado de servidor para a Internet (IIS 7)][installcertiis].

4. Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

	Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

4. Exportar o certificado do Gerenciador do IIS Para obter mais informações sobre como exportar o certificado, consulte [Exportar um certificado do servidor (IIS 7)][exportcertiis]. O arquivo exportado será usado nas etapas posteriores para ser carregado no Azure, para uso com seu aplicativo.

	> [AZURE.NOTE] Durante o processo de exportação, verifique se você selecionou a opção <strong>Sim, exportar a chave privada</strong>. Isso incluirá a chave privada no certificado exportado.

	> [AZURE.NOTE] Durante o processo de exportação, verifique se você selecionou as opções **Incluir todos os certificados no caminho de certificação** e **Exportar todas as propriedades estendidas**. Isso incluirá todos os certificados intermediários no certificado exportado.

<a name="bkmk_subjectaltname"></a>
### Obtenha um certificado SubjectAltName usando OpenSSL

O OpenSSL pode ser usado para criar uma solicitação de certificado que usa a extensão SubjectAltName para oferecer suporte a vários nomes de domínio com um único certificado. No entanto, ele requer um arquivo de configuração. As etapas a seguir abordam a criação de um arquivo de configuração e, depois, o uso dele para solicitar um certificado.

1. Crie um novo arquivo chamado __sancert.cnf__ e use o seguinte como o conteúdo do arquivo:

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

	Observe a linha que começa com 'subjectAltName'. Substitua os nomes de domínio listados pelos nomes de domínio que você deseja oferecer suporte além do nome comum. Por exemplo:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Não é preciso alterar o campo commonName\_default, pois será solicitado que você insira seu nome comum em uma das etapas seguintes.

2. Salve o arquivo __sancert.cnf__.

1. Gere uma chave particular e a Solicitação de Assinatura de Certificado usando o arquivo de configuração sancert.cnf. Em uma sessão de terminal ou bash, use o seguinte comando:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Quando solicitado, digite as informações adequadas. Por exemplo:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com


	Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

3. Envie sua CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

4. Depois de obter um certificado de uma autoridade de certificação, salve-o em um arquivo chamado **myserver.crt**. Se a autoridade de certificação tiver fornecido o certificado em formato de texto, cole o texto do certificado no arquivo **myserver.crt**. O conteúdo do arquivo deve ser semelhante ao seguinte quando visualizado em um editor de texto:

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

	Salve o arquivo.

5. Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter o **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelo Serviço de Aplicativo do Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	> [AZURE.NOTE] Se sua AC usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão que é fornecida como um arquivo PEM (extensão de arquivo .pem).
	>
	> O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermediários contidos no arquivo **intermediate-cets.pem**:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Depois de executar este comando, você deve ter um arquivo **myserver.pfx** adequada para uso no Serviço de Aplicativo do Azure.

<a name="bkmk_selfsigned"></a>
### Gerar um certificado autoassinado (somente para teste)

Em alguns casos, você poderá obter um certificado para fins de teste e, com isso, adiar a compra de uma AC confiável até a entrada em produção. Os certificados autoassinados podem preencher essa lacuna. Um certificado autoassinado é um certificado que você cria e assina como se fosse uma autoridade de certificação. Embora este certificado possa ser usado para proteger um aplicativo, a maioria dos navegadores retornará erros ao visitar o aplicativo, já que o certificado não foi assinado por uma AC confiável. Alguns navegadores podem até mesmo não permitir a exibição do aplicativo.

- [Gerar um certificado autoassinado usando makecert](#bkmk_ssmakecert)
- [Gerar um certificado autoassinado usando OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### Gerar um certificado autoassinado usando makecert ####

Você pode criar um certificado de teste em um sistema do Windows com Visual Studio instalado executando as seguintes etapas:

1. Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Prompt de comando do desenvolvedor**. Por fim, clique com o botão direito no **Prompt de comando do desenvolvedor** e selecione **Executar como Administrador**.

	Se você receber uma caixa de diálogo Controle de Conta de Usuário, selecione **Sim** para continuar.

2. No Prompt de comando do desenvolvedor, use o seguinte comando para criar um novo certificado autoassinado. Você deve substituir o **serverdnsname** pelo DNS de seu aplicativo.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Este comando criará um certificado que é válido entre as datas de 01/01/2013 e 01/01/2014 e armazenará o local no repositório de certificados CurrentUser.

3. Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Windows PowerShell** e inicie este aplicativo.

4. No prompt do Windows PowerShell, use os seguintes comandos para exportar o certificado criado anteriormente:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Isso armazena a senha especificada como uma cadeia de caracteres segura em $mypwd e localiza o certificado usando o nome DNS especificado pelo parâmetro **dnsname**, e exporta para o arquivo especificado pelo parâmetro **filepath**. A cadeia de segura que contém a senha é usada para proteger o arquivo exportado.

<a name="bkmk_ssopenssl"></a>
####Gerar um certificado autoassinado usando OpenSSL ####

1. Crie um novo documento chamado **serverauth.cnf** usando o seguinte como o conteúdo deste arquivo:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Isso especifica as definições de configuração necessárias para produzir um certificado SSL que possa ser usado pelo Serviço de Aplicativo do Azure.

2. Gere um novo certificado autoassinado usando uma das opções de uma sessão de linha de comando, bash ou terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Isso cria um novo certificado usando as definições de configuração especificadas no arquivo **serverauth.cnf**.

3. Para exportar o certificado para um arquivo .PFX que pode ser carregado em um aplicativo no Serviço de Aplicativo do Azure, use o seguinte comando:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	O **myserver.pfx** produzido por este comando pode ser usado para proteger seu aplicativo para fins de teste.

<a name="bkmk_standardmode"></a>
## 2\. Configurar o tipo de preço Standard ou Premium

A habilitação do HTTPS para um domínio personalizado só está disponível para os tipos de preço **Standard** e **Premium** no Serviço de Aplicativo do Azure. Use as etapas a seguir para mudar o plano do Serviço de Aplicativo para a camada **Standard**.

> [AZURE.NOTE] Antes de mudar um aplicativo da camada **Free** para a camada **Standard**, você deve remover os limites de gastos em vigot para sua assinatura. Caso contrário, seu aplicativo poderá ficar indisponível se você atingir os limites antes do término do período de cobrança. Para obter mais informações sobre as camadas compartilhada e **Standard**, consulte os [Detalhes de preços][pricing].

1.	Em seu navegador, abra o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
	
2.	Clique na opção **Procurar** no lado esquerdo da página.

3.	Clique na lâmina **Aplicativos Web**.

4.	Clique no nome do seu aplicativo.

5.	Na página **Informações Gerais**, clique em **Configurações**.

6.	Clique em **Escala**
	
	![Na guia Escala][scale]

7.	Na seção **Escala**, defina o modo do plano de Serviço de Aplicativo clicando em **Selecionar**.

	> [AZURE.NOTE] Se você receber um erro "Configurando a escala para o aplicativo Web '&lt;nome do aplicativo&gt;' com falha", você pode usar o botão Detalhes para saber mais. Você pode receber um erro "Não servidores de instância padrão disponíveis para atender a esta solicitação". Se você receber esse erro, contate o [suporte do Azure](/support/options/).

<a name="bkmk_configuressl"></a>
## 3\. Configurar o SSL em seu aplicativo

Antes de executar as etapas nesta seção, você precisa ter associado um nome de domínio personalizado ao seu aplicativo. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para um aplicativo Web][customdomain].

1.	No seu navegador, abra o [Portal de Gerenciamento do Azure](https://portal.azure.com).

2.	Clique na opção **Procurar** no lado esquerdo da página.

3.	Clique na lâmina **Aplicativos Web**.

4.	Clique no nome do seu aplicativo.

5.	Na página **Informações Gerais**, clique em **Configurações**.

6.	Clique em **Domínios personalizados e SSL**.

	![A guia configuração][configure]

7.	Na seção **Certificados**, clique em **Carregar**.

8.	Usando a caixa de diálogo **Carregar um certificado**, selecione o arquivo de certificado .pfx criado anteriormente usando o Gerenciador do IIS ou OpenSSL. Digite a senha que foi usada para proteger o arquivo .pfx, se houver. Por fim, clique em **Salvar** para carregar o certificado.

	![carregamento de SSL][uploadcert]

9. Na seção **Associações SSL** da guia **Configurações de SLL**, use os menus suspensos para selecionar o nome de domínio a ser protegido com o SSL e o certificado a usar. Você também pode selecionar se deseja usar [Indicação de nome do servidor][sni] (SNI) ou SSL baseado em IP.

	![associações SSL][sslbindings]

	* O SSL baseado em IP associa um certificado a um nome de domínio mapeando um endereço IP público dedicado do servidor ao nome de domínio. Isso exige que cada nome de domínio (contoso.com, fabricam.com etc.), associado ao seu serviço tenha um endereço IP dedicado. Esse é o método tradicional de associar certificados SSL a um servidor web.

	* O SSL baseado em SNI é uma extensão para SSL e [protocolo TLS][tls] que permite que vários domínios compartilhem o mesmo endereço IP, com certificados de segurança separados para cada domínio. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) oferecem suporte ao SNI. No entanto, navegadores mais antigos podem não oferecer esse suporte. Para obter mais informações sobre SNI, consulte o artigo [Indicação de nome do servidor][sni] no Wikipédia.

10. Clique em **Salvar** para salvar as alterações e habilitar SSL.

> [AZURE.NOTE] Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá executar as seguintes etapas adicionais:
>
> 1. Depois de ter configurado uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Você encontrará esse endereço IP na página **Painel** do aplicativo, na seção **Visualização Rápida**. Ele será listado como **Endereço IP Virtual**:
>    
>     ![Endereço IP virtual](./media/configure-ssl-web-site/staticip.png)
>    
>     Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI, ou não estiver configurado para usar SSL, nenhum endereço será listado para essa entrada.
>
> 2. Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior.


Nesse momento, você poderá visitar o seu aplicativo usando `HTTPS://` em vez de `HTTP://` para verificar se o certificado foi configurado corretamente.

<a name="bkmk_enforce"></a>
## 4\. Impor HTTPS no seu aplicativo

O Serviço de Aplicativo do Azure *não* impõe HTTPS. Os visitantes ainda podem acessar seu aplicativo usando HTTP, o que pode comprometer a segurança desse aplicativo. Se você deseja impor HTTPS para seu aplicativo, você pode usar o módulo **Reescrita de URL**. O Módulo de Reescrita de URL está incluído no Serviço de Aplicativo do Azure e permite definir regras aplicadas a solicitações recebidas antes das solicitações serem repassadas ao aplicativo. **Ele pode ser usado em aplicativos escritos em qualquer linguagem de programação com suporte pelo Azure.**

> [AZURE.NOTE] Os aplicativos MVC do .NET devem usar o filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) em vez da Reescrita de URL. Para obter mais informações sobre como usar RequireHttps, consulte [Implantar um aplicativo MVC 5 do ASP.NET em um aplicativo Web](../articles/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
>
> Para obter informações sobre o redirecionamento programático de solicitações usando outras linguagens de programação, consulte a documentação dessas tecnologias.

As regras de Reescrita de URL são definidas em um arquivo **web.config** armazenado na raiz do aplicativo. O exemplo a seguir contém uma regra de Reescrita de URL que força todo o tráfego de entrada a usar o protocolo HTTPS.

<a name="example"></a>**Web.Config de exemplo da Reescrita de URL**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Essa regra funciona retornando um código de status de protocolo HTTP 301 (redirecionamento permanente) quando o usuário solicita uma página usando o protocolo HTTP. O 301 redireciona a solicitação para a mesma URL solicitada pelo visitante, mas substitui a parte do protocolo HTTP da solicitação com o protocolo HTTPS. Por exemplo, HTTP://contoso.com seria redirecionado para HTTPS://contoso.com.

> [AZURE.NOTE] Se seu aplicativo é escrito em **Node.js**, **PHP**, **Django Python** ou **Java**, ele provavelmente não inclui um arquivo web.config. Porém, **Node.js**, **Python Django** e **Java** todos usam, efetivamente, um web.config quando hospedados no Serviço de Aplicativo do Azure. Como o Azure cria o arquivo automaticamente durante a implantação, você jamais vê esse arquivo. Se você incluir um como parte do aplicativo, ele substituirá o gerado automaticamente pelo Azure.

###.NET

Para aplicativos .NET, modifique o arquivo web.config para seu aplicativo e adicione a seção **&lt;rewrite>** no [exemplo](#example) à seção **&lt;system.WebServer>**.

Se o seu arquivo web.config já inclui uma seção **&lt;rewrite>**, adicione o **&lt;rule>** no [exemplo](#example) como a primeira entrada na seção **&lt;rules>**.

###PHP

Para aplicativos PHP, basta salvar o [exemplo](#example) como um arquivo web.config na raiz do aplicativo e reimplantar o aplicativo no Site do Azure.

###Node.js, Python Django e Java

Um arquivo web.config é criado automaticamente para aplicativos Node.js, Python Django e Java, caso ainda não forneçam um, mas ele só existe no servidor ao ser criado durante a implantação. O arquivo gerado automaticamente contém configurações que informam ao Azure como hospedar o aplicativo.

Para recuperar e modificar o arquivo gerado automaticamente pelo aplicativo, use as etapas a seguir.

1. Baixe o arquivo usando o protocolo FTP (consulte [Carregando/baixando arquivos via protocolo FTP e coletando logs de diagnóstico](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Adicione-o à raiz do aplicativo.

3. Adicione as regras de reescrita usando as informações a seguir.

	* **Node.js e Python Django**

		O arquivo web.config gerado para os aplicativos Node.js e Python Django já terá uma seção **<rewrite>**, contendo entradas **<rule>** necessárias para o funcionamento apropriado do aplicativo. Para forçar o aplicativo a usar HTTPS, adicione **&lt;rule>** do exemplo como a primeira entrada na seção **&lt;rules>**. Isso irá impor o protocolo HTTPS, deixando o restante das regras intactas.

	* **Java**

		Como o arquivo web.config para aplicativos Java que usam o Apache Tomcat não contêm uma seção **&lt;rewrite>**, você deve adicionar a seção **&lt;rewrite>** do exemplo à seção **&lt;system.webServer>**.

4. Reimplantar o projeto (inclusive o web.config atualizado) no Azure

Depois que você implantar um web.config com uma regra de reescrita para forçar o protocolo HTTPS, ele deverá entrar em vigor imediatamente e redirecionar todas as solicitações para o protocolo HTTPS.

Para obter mais informações sobre o Módulo de Reescrita de URL do IIS, consulte a documentação [Reescrita de URL](http://www.iis.net/downloads/microsoft/url-rewrite).

## Mais Recursos ##
- [Central de confiabilidade do Microsoft Azure](/support/trust-center/security/)
- [Opções de configuração desbloqueadas nos Sites do Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Habilitar registro em log de diagnóstico](../articles/app-service-web/web-sites-enable-diagnostic-log.md)
- [Configurar aplicativos Web no Serviço de Aplicativo do Azure](../articles/app-service-web/web-sites-configure.md)
- [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

<!---HONumber=AcomDC_0204_2016-->