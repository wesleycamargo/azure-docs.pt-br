## Endereço IP público
Um recurso de endereço IP público pode fornecer tanto um endereço IP público dinâmico quanto um reservado. Embora você possa criar um endereço IP público como um objeto autônomo, você precisa associá-la a outro objeto para usar o endereço em si. Você pode associar um endereço IP público a um balanceador de carga, Application Gateway ou uma NIC para fornecer acesso à Internet a esses recursos.

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**publicIPAllocationMethod**|Define se o endereço IP é *estático* ou *dinâmico*.|estático, dinâmico|
|**idleTimeoutInMinutes**|Define o tempo limite ocioso.|qualquer valor entre 4 e 30|
|**ipAddress**|Endereço IP atribuído a um objeto. Essa é uma propriedade somente leitura.|104\.42.233.77|

### Configurações DNS
Endereços IP públicos têm um objeto filho chamado **dnsSettings** que contém as seguintes propriedades:

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**domainNameLabel**|Host nomeado usado para resolução de nomes.|www, ftp, vm1|
|**reverseFqdn**|Nome de domínio totalmente qualificado que resolve o endereço IP e está registrado no DNS como um registro PTR.|www.contoso.com.|

Endereço IP de público exemplo, no formato JSON:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->