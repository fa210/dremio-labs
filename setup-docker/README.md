# Single node Docker environment
 
## Ambiente
 
- Host SO: Ubuntu 18.04
- Usuário: pertencente aos grupos adm, cdrom, sudo, dip, plugdev, lpadmin, lxd, sambashare e docker
- Docker Version: 19.03.12, build 48a66213fe
- Docker Image: dremio/dremio-oss - [docker hub](hub.docker.com/r/dremio/dremio-oss)
 
 
## Setup
 
 
```sh
docker pull dremio/dremio-oss
```
 
- Sobre os volumes:
   - `/SSD/docker-volumes/dremio-001/dremio` visa preservar os dados entre as ativações do container
   - `/SSD/docker-volumes/dremio-001/landing` visa permitir a adição de arquivos ao contexto de processamento do container, tendo em mente a configuração de data sources do tipo NAS, para efeito de ingestão
   - Permissão de escrita, leitura e execução se mostrou necessária para uso dos volumes
   - Montado no disco SSD
 
```sh
sudo -i
mkdir -p /SSD/docker-volumes/dremio-001/dremio
mkdir -p /SSD/docker-volumes/dremio-001/landing
chmod -R 777 /SSD/docker-volumes/dremio-001
```
 
**@TODO** Seria interessante avaliar alternativas de lock de recursos de processamento
 
```docker
docker run                                                        \
    --name dremio-001                                             \
    -p 9047:9047                                                  \
    -p 31010:31010                                                \
    -p 45678:45678                                                \
    -v /SSD/docker-volumes/dremio-001/dremio:/opt/dremio/data     \
    -v /SSD/docker-volumes/dremio-001/landing:/tmp                \
    dremio/dremio-oss
```

```docker
docker stop dremio-001
```


```docker
docker start dremio-001
```

 
### Conexão com Docker
 
- Portal
   - localhost:9047
- JDBC/ODBC
   - localhost:31010


            



## Histórico do Documento
 
Data|Versão|Release|Autor|Comentários
-|-|-|-|-
05/08/20|1|0|alvaro.fazio@compasso.com.br|Draft
 


