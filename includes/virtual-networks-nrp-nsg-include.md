## NSG (grupo de segurança de rede)
Um recurso NSG habilita a criação de limite de segurança para cargas de trabalho, por meio de regras de permissão e recusa. Essas regras podem ser aplicadas no nível do NIC (nível de instância VM) ou no nível de sub-rede (grupo de VMs).

As propriedades principais de um recurso NSG incluem:

- **Regra de segurança** - um NSG um pode ter várias regras de segurança definidas. Cada regra pode permitir ou negar diferentes tipos de tráfego.

### Regra de segurança
Uma regra de segurança é um recurso de filho de um NSG.

As propriedades principais de uma regra de segurança incluem:

- **Protocolo** – protocolo de rede ao qual essa regra se aplica.
- **Intervalo de portas de origem** - porta de origem ou o intervalo de portas, de 0 a 65535. Um caractere curinga pode ser usado para corresponder a todas as portas. 
- **Intervalo de portas de destino** - porta de destino ou intervalo de portas, de 0 a 65535. Um caractere curinga pode ser usado para corresponder a todas as portas.
- **Prefixo de endereço de origem** – intervalo de endereços IP de origem. 
- **Prefixo de endereço de destino** – intervalo de endereços IP de destino.
- **Acesso** – *Permitir* ou *Bloquear* tráfego.
- **Prioridade** – um valor entre 100 e 4096. O número de prioridade deve ser exclusivo para cada regra na coleção de regras de segurança. Quanto menor o número da prioridade, maior será a prioridade da regra.
- **Direção** – especifica se a regra será aplicada ao tráfego na direção de *entrada* ou *saída*. 

<!---HONumber=Sept15_HO4-->