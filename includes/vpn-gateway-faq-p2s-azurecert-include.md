[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso usar minha própria CA de raiz de PKI interna para a conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quais ferramentas posso usar para criar certificados?

Você pode usar sua solução de Enterprise PKI (sua PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Há instruções para configurações e parâmetros de certificado?

* **Solução PKI interna/Enterprise PKI** : ver as etapas para [Gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** consulte o artigo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) para ver as etapas.

* **MakeCert**: consulte o artigo [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) para ver as etapas.

* **OpenSSL:** 

    * Ao exportar certificados, verifique se converteu o certificado raiz em Base64.

    * Para o certificado do cliente:

      * Ao criar a chave privada, especifique o período como 4096.
      * Ao criar o certificado, para o parâmetro *-extensions*, especifique *usr_cert*.