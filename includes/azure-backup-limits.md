
Os limites a seguir se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
|---|---|
|Número de servidores/computadores que podem ser registrados em cada cofre|50|
|Tamanho de uma fonte de dados para dados armazenados no armazenamento de cofre do Azure|Máximo de 1,65 TB<sup>1</sup>|
|Número de cofres de backup que podem ser criados em cada assinatura do Azure|25|
|Número de vezes que o backup pode ser agendado por dia|3 por dia para o Windows Server/cliente<br/>2 por dia para SCDPM|
|Número de pontos de recuperação que podem ser criados|366<sup>2</sup>|
|Discos de dados conectados a uma máquina virtual do Azure para backup|5|

- <sup>1</sup>O limite de 1,65 TB não se aplica ao backup do IaaS VM.
- <sup>2</sup>Você pode usar qualquer permutação para chegar a um número que seja menor do que 366.

<!---HONumber=July15_HO4-->