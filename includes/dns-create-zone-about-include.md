Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem de seu domínio, você primeiro precisa criar uma zona DNS. Qualquer registro DNS criado para um determinado domínio estará dentro de uma zona DNS do domínio.

Por exemplo, domínio "contoso.com" pode conter uma série de registros DNS, como “mail.contoso.com” (para um servidor de email) e “www.contoso.com” (para um site da Web).

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>Sobre nomes da zona DNS
* O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não deve existir ainda. Caso contrário, a operação falhará.
* O mesmo nome de zona pode ser reutilizado em outro grupo de recursos ou uma assinatura do Azure diferente.
* Quando várias zonas compartilharem o mesmo nome, a cada instância serão atribuídos endereços de servidor de nome diferentes, e somente uma instância pode ser delegada do domínio pai. Confira [Delegar um domínio ao Azure DNS](../articles/dns/dns-domain-delegation.md)para saber mais.


<!--HONumber=Nov16_HO2-->


