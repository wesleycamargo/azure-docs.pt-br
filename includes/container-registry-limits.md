| Recurso | Basic | Standard | Premium |
|---|---|---|---|---|
| Armazenamento | 10 GiB | 100 GiB| 500 GiB |
| ReadOps por minuto<sup>1, 2</sup> | 1 K | 300 K | 10.000 K |
| WriteOps por minuto<sup>1, 3</sup> | 100 | 500 | 2 K |
| MBps de largura de banda de download<sup>1</sup> | 30 | 60 | 100 |
| MBps de largura de banda de upload<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicação geográfica | N/D | N/D | [Com suporte *(versão prévia)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps* e *Largura de banda* são estimativas mínimas. O ACR se esforça para melhorar o desempenho conforme os requisitos de uso.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação com base no número de camadas que devem ser enviadas por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.