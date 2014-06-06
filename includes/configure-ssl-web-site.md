#Habilitar HTTPS para Sites do Azure

Quando alguém visita o seu site usando HTTPS, a comunicação entre o navegador e o site é protegida usando a criptografia Secure Socket Layer (SSL). Esse é o método de proteção de dados enviados pela internet usado com maior frequência e garante aos visitantes que as transações com o seu site são seguras. Este artigo descreve como habilitar HTTPS para Sites do Azure. 

> [WACOM.NOTE] Para habilitar HTTPS para nomes de domínio personalizados, você deve configurar seus sites em modo padrão. Isso pode resultar em custos adicionais se você estiver usando modo gratuito ou compartilhado. Para obter mais informações sobre os preços do modo compartilhado e padrão, consulte [Detalhes de preços][pricing].

<a href="bkmk_azurewebsites"></a><h2>O domínio \*.azurewebsites.net</h2>

Se você não estiver planejando usar um nome de domínio personalizado, e sim o domínio \*.azurewebsites.net atribuído ao seu site pelo Azure (por exemplo, contoso.azurewebsites.net), seu site já estará protegido por um certificado fornecido pela Microsoft. Você pode usar **https://mywebsite.azurewebsites.net** para acessar seu site de maneira segura.

O restante deste documento fornece detalhes sobre a habilitação HTTPS para nomes de domínio personalizados, tais como **contoso.com**, **www.contoso.com** ou **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Nomes de domínio personalizados</h2>

Para habilitar HTTPS para um nome de domínio personalizado, como **contoso.com**, você deve registrar um nome de domínio personalizado em um registro de nomes de domínio. Para obter mais informações sobre como configurar o nome de domínio de Sites do Azure, consulte [Configurando um nome de domínio personalizado para Sites do Azure](/pt-br/develop/net/common-tasks/custom-dns-web-site/). Depois de registrar o nome de domínio personalizado e configurar seu site para responder ao nome personalizado, você deve solicitar um certificado SSL para o domínio. 

Registrar um nome de domínio também permite que você crie subdomínios como **www.contoso.com** ou **mail.contoso.com** Antes de solicitar um certificado SSL, você deve primeiro determinar quais nomes de domínio serão protegidos pelo certificado. Isso determinará o tipo de certificado que você precisa ter. Se você precisar proteger um único nome de domínio como **contoso.com** ou **www.contoso.com**, um certificado básico provavelmente será suficiente. Se você precisar proteger vários nomes de domínio, como **contoso.com**, **www.contoso.com** e **mail.contoso.com**, um certificado curinga ou um certificado com o nome alternativo da entidade (subjectAltName, SAN) será necessário.

> [WACOM.NOTE] A maioria dos navegadores exibirá um aviso se o nome de domínio especificado no certificado não coincidir com o nome de domínio que foi inserido no navegador. Por exemplo, se o certificado listar apenas www.contoso.com, mas login.contoso.com for o nome de domínio usado para acessar o site no Internet Explorer, você receberá um aviso informando que "o certificado de segurança apresentado pelo site foi emitido para um outro endereço."

**Certificados básicos** são certificados em que o CN (nome comum) do certificado é definido para um domínio específico ou subdomínio que os clientes usarão para visitar o site. Por exemplo, **www.contoso.com**. Esses certificados protegem apenas o nome de domínio especificado pelo CN.

**Certificados curinga** são certificados em que o CN do certificado contém um caractere curinga ' \*' no nível do subdomínio. Isso permite que o certificado corresponda a um único nível de subdomínios de um determinado domínio. Por exemplo, um certificado curinga para **\*. contoso.com** seria válido para **www.contoso.com**, **payment.contoso.com** e **login.contoso.com** Ele não seria válido para **test.login.contoso.com**, já que adiciona um nível extra de subdomínio. Ele também não é válido para **contoso.com**, pois esse é o nível do domínio raiz e não um subdomínio.

Um certificado curinga é o que a Microsoft fornece para o nome de domínio \*. azurewebsites.net criado automaticamente para seu site.

**subjectAltName** é uma extensão de certificado que permite a associação de vários valores ou nomes alternativos de entidade a um certificado. Para certificados SSL, isso permite que você adicione outros nomes DNS que também serão válidos com o certificado. Por exemplo, um certificado usando subjectAltName pode ter um CN de **contoso.com**, mas também pode ter nomes alternativos como **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com** e até mesmo **fabrikam.com** Esse tipo de certificado será válido para todos os nomes de domínio especificados no nome comum e no subjectAltName.

É possível que um certificado ofereça suporte a caracteres curinga e subjectAltName.

<a href="bkmk_getcert"></a><h2>Obter um certificado</h2>

Certificados SSL usados com os Sites do Azure devem ser assinados por uma autoridade de certificação (CA), um terceiro confiável que emite certificados para essa finalidade. Se ainda não tiver um, precisará obter junto a uma empresa que venda certificados SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

* O certificado deve conter uma chave privada.

* O certificado deve ser criado para troca de chaves, exportável para um arquivo de troca de informações pessoais (.pfx).

* O nome da entidade do certificado deve corresponder ao domínio usado para acessar site. Se for necessário atender a vários domínios com esse certificado, você precisará usar um valor curinga ou especificar valores subjectAltName conforme discutido anteriormente.

	* Para obter informações sobre a configuração de um nome de domínio personalizado para Sites do Azure, consulte [Configurando um nome de domínio personalizado para Sites do Azure (a página pode estar em inglês)][customdomain].
	
	> [WACOM.NOTE] Não tente obter ou gerar um certificado para o domínio azurewebsites.net.

* O certificado deve usar, no mínimo, uma criptografia de 2.048 bits.

> [WACOM.NOTE] Certificados emitidos por servidores de autoridade de certificação privadas não são suportados pelos Sites do Azure.

Para obter um certificado SSL de uma autoridade de certificação, é necessário gerar uma Solicitação de Assinatura de Certificado (CSR), que é enviada para a autoridade de certificação. A autoridade de certificação enviará um certificado que é usado para complementar o CSR. Duas maneiras de gerar um CSR são usando o certmgr.exe ou aplicativos [OpenSSL][openssl]. Certmgr.exe só está disponível para Windows, enquanto que o OpenSSL está disponível para a maioria das plataformas. A seguir estão as etapas para usar esses recursos.

Talvez você também precise obter **certificados intermediários** (também conhecidos como certificados de cadeia), caso eles sejam usados pela autoridade de certificação. O uso de certificados intermediários é considerado mais seguro do que 'certificados sem cadeia, por isso é comum uma autoridade de certificação usá-los. Certificados intermediários geralmente são fornecidos como um download separado do site das autoridades de certificação. As etapas neste artigo mostram como garantir que todos os certificados intermediários sejam mesclados com o certificado carregado para os Sites do Azure. 

> [WACOM.NOTE] Ao seguir uma série de etapas, você será solicitado a inserir um **nome comum** Se você obter um certificado curinga para uso em vários domínios (www.contoso.com, sales.contoso.com), esse valor deverá ser \*.nomedodomínio (por exemplo, \*.contoso.com). Se você obter certificado para um único nome de domínio, o valor deverá ser o valor exato que os usuários digitarão no navegador para visitar seu site. Por exemplo, www.contoso.com.
>
> Se você precisar oferecer suporte tanto para um nome curinga como \*.contoso.com quanto para um nome de domínio raiz como contoso.com, é necessário usar um certificado curinga de nome alternativo da entidade (SAN). Para ver exemplos de criação de uma solicitação de certificado que usa as extensões SubjectAltName, consulte [Certificado SubjectAltName](#bkmk_subjectaltname).
> 
> Para obter mais informações sobre como configurar o nome de domínio de Sites do Azure, consulte <a href="/pt-br/develop/net/common-tasks/custom-dns-web-site/">Configurando um nome de domínio personalizado para Sites do Azure (a página pode estar em inglês)</a>.

###Obtenha um certificado usando o certreq.exe (somente Windows)

O certreq.exe é um utilitário do Windows para criar solicitações de certificado. Ele faz parte da instalação básica do Windows desde o Windows XP/Windows Server 2000, por isso deve estar disponível nos sistemas Windows recentes. Siga as etapas para obter um certificado SSL usando o certreq.exe.

Se você deseja criar um certificado autoassinado para fins de teste, consulte a seção [Certificados autoassinados](#bkmk_selfsigned) deste documento.

Se você desejar usar o Gerenciador do IIS para criar uma solicitação de certificado, consulte a seção [Obter um certificado usando o Gerenciador do IIS](#bkmk_iismgr).

1. Abra o **Bloco de notas** e crie um novo documento com o seguinte. Substitua **mysite.com** na linha de assunto pelo nome de domínio personalizado de seu site. Por exemplo, assunto = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Para obter mais informações sobre as opções especificadas acima, bem como outras opções disponíveis, consulte a [documentação de referência do CertReq](http://technet.microsoft.com/library/cc725793.aspx).

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

6. Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado nas próximas etapas. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

	Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

7. Para exportar o certificado do repositório do certificados, execute o **certmgr.msc** na **tela Iniciar** ou no **Menu Iniciar**. Quando o **Gerenciador de certificados** aparecer, expanda a pasta **Pessoal** e selecione **Certificados**. No campo **Emitido Para**, pesquise a entrada com o nome de domínio personalizado referente ao certificado solicitado. No campo **Emitido Por** deve aparecer a Autoridade de Certificação usada para esse certificado.

	![inserir a imagem do gerenciador de certificado aqui][certmgr]

9. Clique com o botão direito no certificado e selecione **Todas as Tarefas** e depois **Exportar**. No **Assistente de exportação de certificados**, clique em **Avançar** e, em seguida, selecione **Sim, exportar a chave particular**. Clique em **Avançar**.

	![Exporte a chave particular][certwiz1]

10. Selecione **Troca de Informações Pessoais - PKCS #12**, **Incluir Todos os Certificados na Cadeia de Certificados** e **Exportar Todas as Propriedades Estendidas**. Clique em **Avançar**.

	![incluir todos os certificados e propriedades estendidas][certwiz2]

11. Selecione **Senha**, insira e confirme a senha. Clique em **Avançar**.

	![especifique uma senha][certwiz3]

12. Determine um caminho e o nome do arquivo que conterá o certificado exportado. O nome do arquivo deve ter uma extensão **.pfx**. Clique em **Avançar** para concluir o processo.

	![forneça um caminho do arquivo][certwiz4]

Agora você pode carregar o arquivo PFX exportado no Site do Azure.

###Obtenha um certificado usando OpenSSL

1. Gere uma chave particular e uma Solicitação de Assinatura de Certificado (CSR) usando o seguinte de uma sessão de linha de comando, bash ou terminal:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Quando solicitado, digite as informações adequadas. Por exemplo:

 		Nome do país (código de 2 letras) 
        Estado ou província (nome completo) []: Washington
        Nome da localidade (por exemplo, cidade) []: Redmond
        Nome da organização (por exemplo, a empresa) []: Microsoft
        Nome da unidade organizacional (por exemplo, seção) []: Azure
        Nome comum (por exemplo, SEU nome) []: www.microsoft.com
        Endereço de e-mail []:

		Insira os seguintes atributos 'extras' a serem enviados com a solicitação de certificado

       	Verificar senha []: 

	Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

3. Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

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

5. Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter o **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelos Sites do Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	<div class="dev-callout"> 
	<b>Observação</b>
	<p>Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão que é fornecida como um arquivo PEM (extensão de arquivo .pem)</p>
	<p>O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermediários contidos no arquivo <b>intermediate-cets.pem</b> file:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Depois de executar este comando, você deve ter um arquivo **myserver.pfx** apropriado para uso nos Sites do Azure.

<a href="bkmk_standardmode"></a><h2>Configure o modo padrão</h2>

A habilitação HTTPS para um domínio personalizado só está disponível para o modo padrão nos Sites do Azure. Use as etapas a seguir para alternar para o modo padrão.

> [WACOM.NOTE] Antes alternar um site do modo gratuito para o modo padrão, remova os limites de despesas em vigor na assinatura do site, caso contrário seu site pode ficar indisponível se o limite for alcançado antes do fim do período de cobrança. Para obter mais informações sobre os preços do modo compartilhado e padrão, consulte [Detalhes de preços][pricing].

1. No navegador, abra o [Portal de Gerenciamento][portal].

2. Na guia **Sites**, clique no nome do seu site.

	![selecionando um site][website]

3. Clique na guia **ESCALA**.

	![Na guia Escala][scale]

4. Na seção **Geral**, defina o modo de site clicando em **PADRÃO**.

	![modo padrão selecionado][standard]

5. Clique em **Salvar**. Quando solicitado, clique em **Sim**.

	> [WACOM.NOTE] Se você receber um erro "Falha na configuração da escala para o site '&lt;site name&gt;'", use o botão Detalhes para obter mais informações. Você pode receber um erro "Não servidores de instância padrão disponíveis para atender a esta solicitação". Se você receber esse erro, entre em contato com o [suporte do Azure](http://www.windowsazure.com/pt-br/support/options/)

<a href="bkmk_configuressl"></a><h2>Configurar SSL</h2>

Antes de executar as etapas nesta seção, você deve ter associado um nome de domínio personalizado aos Sites do Azure. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para Sites do Azure][customdomain].

1. No navegador, abra o [Portal de Gerenciamento do Azure][portal].

2. Na guia **Sites**, clique no nome de seu site e, em seguida, selecione a guia **CONFIGURAR**.

	![a guia configurar][configure]

3. Na seção **Certificados**, clique em **Carregar um Certificado**

	![carregar um certificado][uploadcert]

4. Usando a caixa de diálogo **Carregar um certificado**, selecione o arquivo de certificado .pfx criado anteriormente usando o Gerenciador do IIS ou OpenSSL. Digite a senha que foi usada para proteger o arquivo .pfx, se houver. Por fim, clique em **Verificação** para carregar o certificado.

	![caixa de diálogo carregar certificado][uploadcertdlg]

5. Na seção **Associações SSL** da guia **CONFIGURAR**, use os menus suspensos para selecionar o nome de domínio a ser protegido com o SSL e o certificado a ser usado. Você também pode selecionar se deseja usar [Indicação de nome do servidor][sni] (SNI) ou SSL baseado em IP.

	![associações SSL][sslbindings]
	
	* O SSL baseado em IP associa um certificado com um nome de domínio mapeando um endereço IP público dedicado do servidor ao nome de domínio. Isso exige que cada nome de domínio (contoso.com, fabricam.com etc.), associado ao seu serviço tenha um endereço IP dedicado. Esse é o método tradicional de associar certificados SSL a um servidor web.

	* SSL baseado em SNI é uma extensão para SSL e [Transport Layer Security][tls] (TLS) que permite que vários domínios compartilhem o mesmo endereço IP, com certificados de segurança separados para cada domínio. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) oferecem suporte ao SNI. No entanto, navegadores mais antigos podem não oferecer esse suporte. Para obter mais informações sobre SNI, consulte o artigo [Indicação de nome do servidor][sni] no Wikipédia.

6. Clique em **Salvar** para salvar as alterações e habilitar SSL.

> [WACOM.NOTE] Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado por meio de um registro A, você deverá executar as seguintes etapas adicionais:
>
> 1. Depois de configurar uma associação de SSL baseado em IP, um endereço IP dedicado é atribuído ao seu site. Você encontrará esse endereço IP na página **Painel** do site, na seção **Visão Rápida**. Ele será listado como **Endereço IP Virtual**:
>    
>     ![Endereço IP Virtual](./media/configure-ssl-web-site/staticip.png)
>    
>     Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI, ou não estiver configurado para usar SSL, nenhum endereço será listado para essa entrada.
>
> 2. Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior.


Nesse momento, você poderá visitar o seu site usando HTTPS para verificar se o certificado foi configurado corretamente.

<a href="bkmk_subjectaltname"></a><h2>Certificados SubjectAltName (opcional)</h2>

OpenSSL pode ser usado para criar uma solicitação de certificado que usa a extensão SubjectAltName para oferecer suporte a vários nomes de domínio com um único certificado. No entanto, ele requer um arquivo de configuração. As etapas a seguir abordam a criação de um arquivo de configuração e, depois, o uso dele para solicitar um certificado.

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

	Não é preciso alterar o campo commonName_default, pois você deverá inserir seu nome comum em uma das etapas seguintes.

2. Salve o arquivo __sancert.cnf__.

1. Gere uma chave particular e a Solicitação de Assinatura de Certificado usando o arquivo de configuração sancert.cnf. Em uma sessão de terminal ou bash, use o seguinte comando:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Quando solicitado, digite as informações adequadas. Por exemplo:

 		Nome do país (código de 2 letras) []: US
        Nome do estado ou província (nome completo) []: Washington
        Nome da localidade (por exemplo, cidade) []: Redmond
        Nome da unidade organizacional (por exemplo, seção) []: Azure
        Seu nome comum (por exemplo, nome de domínio) []: www.microsoft.com
 

	Quando o processo for concluído, você deverá ter dois arquivos: **myserver.key** e **server.csr**. O **server.csr** contém a Solicitação de Assinatura de Certificado.

3. Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

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

5. Na sessão de linha de comando, Bash ou terminal, use o seguinte comando para converter o **myserver.key** e **myserver.crt** em **myserver.pfx**, que é o formato exigido pelos Sites do Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	<div class="dev-callout"> 
	<b>Observação</b>
	<p>Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão que é fornecida como um arquivo PEM (extensão de arquivo .pem)</p>
	<p>O comando a seguir demonstra como criar um arquivo.pfx que inclui certificados intermediários contidos no arquivo <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Depois de executar este comando, você deve ter um arquivo **myserver.pfx** apropriado para uso nos Sites do Azure.

##<a name="bkmk_iismgr"></a>Obtenha um certificado usando o Gerenciador do IIS (opcional)

Se estiver familiarizado com o Gerenciador do IIS, você pode usá-lo para gerar um certificado que pode ser usado com os Sites do Azure.

1. Gere uma Solicitação de Assinatura de Certificado (CSR) com o Gerenciador do IIS para enviar à Autoridade de Certificação. Para obter mais informações sobre como gerar um CSR, consulte [Solicitar um certificado de servidor para a Internet (IIS 7)][iiscsr].

2. Envie seu CSR a uma Autoridade de Certificação para obter um certificado SSL. Para obter uma lista das Autoridades de Certificação, consulte [Programa de certificação raiz SSL do Windows e Windows Phone 8 (membros CAs) (em inglês)][cas] no Microsoft TechNet Wiki.

3. Conclua o CSR com o certificado fornecido pelo fornecedor de Autoridade de Certificação. Para obter mais informações sobre a conclusão do CSR, consulte [Instalar um certificado de servidor para a Internet (IIS 7)][installcertiis].

4. Se sua CA usar certificados intermediários, você deve instalar esses certificados antes de exportar o certificado na próxima etapa. Geralmente esses certificados são fornecidos como um download separado da autoridade de certificação e são fornecidos em vários formatos para tipos de servidor diferentes. Selecione a versão fornecida para o Microsoft IIS.

	Depois que você tiver baixado o certificado, clique com botão direito no explorer e selecione **Instalar certificado**. Use os valores padrão no **o Assistente de importação de certificados**e continue selecionando **Próximo** até que a importação seja concluída.

4. Exportar o certificado do Gerenciador do IIS Para obter mais informações sobre como exportar o certificado, consulte [Exportar um certificado do servidor (IIS 7)][exportcertiis]. O arquivo exportado será usado nas etapas posteriores quando for carregado no Azure para uso nos Sites do Azure.

	<div class="dev-callout"> 
	<b>Observação</b>
	<p>Durante o processo de exportação, certifique-se de selecionar a opção <strong>Sim, exportar a chave particular</strong>. Isso incluirá a chave particular no certificado exportado.</p>
	</div>

	<div class="dev-callout"> 
	<b>Observação</b>
	<p>Durante o processo de exportação, certifique-se de selecionar a opção <strong>incluir todos os certificados no caminho de certificação</strong> e <strong>Exportar todas as propriedades estendidas</strong>. Isso incluirá todos os certificados intermediários no certificado exportado.</p>
	</div>

<a href="bkmk_selfsigned"></a><h2>Certificados autoassinados (opcional)</h2>

Em alguns casos você poderá obter um certificado para fins de teste e, com isso, adiar a compra de um certificado confiável até a entrada em produção. Os certificados autoassinados podem preencher essa lacuna. Um certificado autoassinado é um certificado que você cria e assina como se fosse uma autoridade de certificação. Enquanto este certificado pode ser usado para proteger um site, a maioria dos navegadores retornará erros ao visitar o site, já que o certificado não foi assinado por uma autoridade de certificação confiável. Alguns navegadores podem até mesmo recusar a exibição do site.

Embora haja várias maneiras de criar um certificado autoassinado, este artigo fornece apenas informações sobre o uso de **makecert** e **OpenSSL**.

###Criar um certificado autoassinado usando makecert

Você pode criar um certificado de teste em um sistema do Windows com Visual Studio instalado executando as seguintes etapas:

1. Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Prompt de comando do desenvolvedor**. Por fim, clique com o botão direito no **Prompt de comando do desenvolvedor** e selecione **Executar como Administrador**.

	Se você receber uma caixa de diálogo Controle de Conta de Usuário, selecione **Sim** para continuar.

2. No Prompt de comando do desenvolvedor, use o seguinte comando para criar um novo certificado autoassinado. Você deve substituir o **serverdnsname** pelo DNS de seu site.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Este comando criará um certificado que é válido entre as datas de 01/01/2013 e 01/01/2014 e armazenará o local no repositório de certificados CurrentUser.

3. Do **Menu Iniciar** ou **tela Iniciar**, pesquise por **Windows PowerShell** e inicie este aplicativo.

4. No prompt do Windows PowerShell, use os seguintes comandos para exportar o certificado criado anteriormente:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Isso armazena a senha especificada como uma cadeia segura em $mypwd, depois localiza o certificado usando o nome DNS especificado pelo parâmetro **dnsname** e exporta para o arquivo especificado pelo parâmetro **filepath**. A cadeia de segura que contém a senha é usada para proteger o arquivo exportado.

###Criar um certificado autoassinado usando OpenSSL

1. Criar um novo documento chamado **serverauth.cnf**, usando o seguinte conteúdo deste arquivo:

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
        commonName			= Common Name (eg, your website's domain name)
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

	Isso especifica as definições de configuração necessárias para produzir um certificado SSL que pode ser usado pelos Sites do Azure.

2. Gere um novo certificado autoassinado usando uma das opções de uma sessão de linha de comando, bash ou terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Isso cria um novo certificado usando as configurações especificadas no arquivo **serverauth.cnf**.

3. Para exportar o certificado para um arquivo .PFX que pode ser carregado em Sites do Azure, use o seguinte comando:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando solicitado, digite uma senha para proteger o arquivo.pfx.

	O **myserver.pfx** produzido por este comando pode ser usado para proteger seu Site do Azure para fins de teste.

[customdomain]: /pt-br/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/pt-br/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/pt-br/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/pt-br/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/pt-br/pricing/details/
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


