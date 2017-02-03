
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Perguntas frequentes - Hospedar a zona ARPA no DNS do Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso hospedar zonas ARPA para meus blocos IP atribuídos pelo ISP no DNS do Azure?

Sim. A hospedagem das zonas ARPA para seus próprios intervalos de IP no DNS do Azure tem suporte total.

Basta [criar a zona no DNS do Azure](../articles/dns/dns-getstarted-create-dnszone.md) e trabalhar com seu ISP para [delegar a zona](../articles/dns/dns-domain-delegation.md).  Em seguida, você pode gerenciar os registros PTR para cada pesquisa inversa da mesma forma que outros tipos de registro.

Você também pode [importar uma zona de pesquisa inversa existente usando a CLI do Azure](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Qual é o custo de hospedar minha zona ARPA?

A hospedagem da zona ARPA para o bloco IP atribuído pelo ISP no DNS do Azure é cobrado usando [taxas padrão do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso hospedar zonas ARPA para endereços IPv4 e IPv6 no DNS do Azure?

Sim.


<!--HONumber=Nov16_HO3-->


