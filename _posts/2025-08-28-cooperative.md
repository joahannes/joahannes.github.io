---
layout: post
title:  Cooperative TSN tutorial
date:   2025-09-17 10:40:16
description: 
tags: cooperative tsn omnet tutorial ptbr
categories: posts
toc:
  sidebar: left
---

#### **Versões de softwares**

- SO Host Principal: Ubuntu 22.04.4 LTS
- SO Host TSNSched: Ubuntu 24.04
- OMNeT++: 6.0.3
- Veins: 5.2
- SUMO: 1.11.0
- Docker: 28.1.1+1

#### **1. Instalação do cooperativeTSN**

1. Download da imagem de Docker com o comando `docker pull dr.nsm.inf.tu-dresden.de/jannusch/wons25:latest`

2. Verificar se a imagem foi baixada, via comando `docker images`. O resultado deve ser algo como:
```bash
REPOSITORY                                 TAG       IMAGE ID       CREATED        SIZE
dr.nsm.inf.tu-dresden.de/jannusch/wons25   latest    c74d154dec49   7 months ago   12.1GB
```

3. Cria container com a imagem baixada. Nomeie esse container como **cooperativeTSN**.
```bash
sudo docker run -it --name cooperativeTSN ID_IMAGEM bash
```
OBS: Nesse exemplo, **ID_IMAGEM** seria: _c74d154dec49_.

4. Verificar se container foi criado com o comando `sudo docker ps -a`. O resultado deve ser algo como:
```bash
CONTAINER ID   IMAGE          COMMAND   CREATED              STATUS                      PORTS     NAMES
a5832644e8ce   c74d154dec49   "bash"    About a minute ago   Exited (0) 2 seconds ago              cooperativeTSN
```

5. Acessar o container com o comando `sudo docker attach cooperativeTSN`.

    5.1. Atualizar pacotes do sistema
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

    5.2. Destravar PIP e instalar pacote para comunicação inter-processos.
    ```bash
    python3 -m pip config set global.break-system-packages true
    ```
    ```bash
    python3 -m pip install --user --upgrade posix_ipc
    ```

    5.3. Aplicar variáveis de ambiente
    ```bash
    bash docker-env.sh
    ```

6. Compilação do OMNeT++

    6.1. Preparação
     - Acessar pasta `omnetpp/`.
     - Instalar todos os pacotes necessários indicados na documentação.
     ```bash
     sudo apt install -y build-essential clang lld gdb bison flex perl \
     python3 python3-pip qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools \
     libqt5opengl5-dev libxml2-dev zlib1g-dev doxygen graphviz libwebkit2gtk-4.0-37 xdg-utils
     ```
     - Alteração no arquivo `configure.user`: as flags `PREFER_CLANG=` e `WITH_QTENV=` devem ser alteradas de **_yes_** para **_no_**.

    6.2. Compilação (aproveite para tomar um café ☕, pois isso levará algum tempo...)
    ```bash
    source setenv
    ```
    ```bash
    ./configure
    ```
    ```bash
    make -j $(nproc)
    ```
    Se tudo der certo, a última mensagem da compilação será: _Now you can type 'omnetpp' to start the IDE._

7. Compilação do SUMO

    7.1. Acessar diretório `sumo/`

    7.2. Exportar variável de ambiente `SUMO_HOME`
    ```bash
    export SUMO_HOME="$PWD"
    ```

    7.3. Compilação
    ```bash
    mkdir build/cmake-build && cd build/cmake-build
    ```
    ```bash
    cmake ../..
    ```
    ```bash
    make -j$(nproc)
    ```
    ```bash
    make install
    ```

8. Compilação do cooperativeTSN

    8.1. Acessar diretório `cooperativeTSN`

    8.2. Configurar todos os projetos
    ```bash
    source setenv
    ```
    ```bash
    ./configure
    ```
    8.3. Construa o projeto (pode tomar outro café ☕, pois isso também levará algum tempo...)
    ```bash
    bear --append -- make -j $(nproc)
    ```

9. Criar arquivo `cooperative_tsn_run` no diretório `cooperativetsn/cooperative_tsn/bin`

    9.1. Acessar diretório `cooperative_tsn/bin/`

    9.2. Copiar modelo de arquivo
    ```bash
    cp /opt/cooperativetsn/cooperative_tsn/src/scripts/cooperative_tsn.in.py cooperative_tsn_run
    ```

    9.3. Adicionar informações abaixo no arquivo criado, especificamente entre as linhas `v-- contents of out/config.py go here` e `^-- contents of out/config.py go here`.
    ```text
    run_libs = ['../plexe/src/plexe', '../veins/subprojects/veins_inet/src/veins_inet', '../inet/src/INET', '../veins/src/veins', 'src/cooperative_tsn']
    run_neds = ['../plexe/src/plexe', '../veins/subprojects/veins_inet/src/veins_inet', '../inet/src', '../veins/src/veins', 'src/cooperative_tsn']
    run_excs = ['../inet/.nedexclusions']
    run_imgs = ['../plexe/images', '../veins/subprojects/veins_inet/images', '../inet/images', '../veins/images', 'images']
    ```

    9.4. Torne o arquivo executável
    ```bash
    chmod +x cooperative_tsn_run
    ```

    9.5. Editar arquivo `freeway.rou.xml`, em `examples/platoon_with_plexe/sumocfg/`, removendo a flag `laneChangeModel="LC2013_CC"`.

    9.6. Testar instalação com o comando `./run -c Sinusoidal` no diretório `/opt/cooperativetsn/cooperative_tsn/examples/platoon_with_plexe/`. A saída deve contar algo como:
    ```text
    OMNeT++ Discrete Event Simulation  (C) 1992-2022 Andras Varga, OpenSim Ltd.
    Version: 6.0.3, build: 240223-17fcae5ef3, edition: Academic Public License -- NOT FOR COMMERCIAL USE
    See the license for distribution terms and warranty disclaimer

    Setting up Cmdenv...

    Loading NED files from ../../../plexe/src/plexe:  36
    Loading NED files from ../../../veins/subprojects/veins_inet/src/veins_inet:  10
    Loading NED files from ../../../inet/src:  1139
    Loading NED files from ../../../veins/src/veins:  44
    Loading NED files from ../../src/cooperative_tsn:  10
    Loading NED files from .:  2

    Preparing for running configuration Sinusoidal, run #0...
    Scenario: $nCars=5, $platoonSize=5, $nLanes=1, ...
    Assigned runID=Sinusoidal-0-20250917-20:20:15-346339
    Setting up network "Platooning"...
    Initializing...

    Running simulation...
    ** Event #0   t=0   Elapsed: 1.1e-05s (0m 00s)  0% completed  (0% total)
        Speed:     ev/sec=0   simsec/sec=0   ev/simsec=0
        Messages:  created: 4   present: 4   in FES: 4
    Loading configuration ... done.
    ** Event #256   t=1.0103050956   Elapsed: 1.31874s (0m 01s)  6% completed  (1% total)
        Speed:     ev/sec=194.884   simsec/sec=0.766117   ev/simsec=254.379
        Messages:  created: 702   present: 615   in FES: 38
    ...
    ```

10. Instalar ferramenta para testar conectividade via _ping_
```bash
apt install iputils-ping net-tools -y
```

11. Instalar pacotes para comunicação gRPC, no diretório `cooperativetsn/cooperative_tsn/src/gRPC/`

    11.1. Criar _virtualenv_ :
    ```bash
    set -e
    ```
    ```bash
    python3 -m venv .venv
    ```

    11.2. Acessar _virtualenv_ :
    ```bash
    source ./.venv/bin/activate
    ```

    11.3. Instalar `requirements` no _virtualenv_ :
    ```bash
    pip install -r requirements.txt
    ```

    11.4 Sair do _virtualenv_ :
    ```bash
    deactivate
    ```
12. CTRL + D para fechar o container.

13. Feito!

#### **2. Instalação do TSNSched**

1. Criar arquivo `Dockerfile` com base [neste link](https://github.com/Jannusch/TSNsched/blob/main/.devcontainer/Dockerfile).
    - **Observação:** mudar `FROM ubuntu:24.10` para `FROM ubuntu:24.04`.


2. Criar imagem chamada `tsnsched` com base no `Dockerfile` (um terceiro café ☕ aqui? cuidado com excesso de cafeína 😅):
```bash
sudo docker build -t tsnsched .
```

3. Verificar se a imagem foi criada, via comando `docker images`. O resultado deve ser algo como:
```bash
REPOSITORY                                 TAG       IMAGE ID       CREATED          SIZE
tsnsched                                   latest    ae2fb4115639   11 minutes ago   2.04GB
dr.nsm.inf.tu-dresden.de/jannusch/wons25   latest    c74d154dec49   7 months ago     12.1GB
```

4. Cria container para servidor TSN com a imagem recém criada. Nomeie esse container como `TSNSched`:
```bash
sudo docker run -it --name TSNSched ID_IMAGEM bash
```
OBS: Nesse exemplo, **ID_IMAGEM** seria: _ae2fb4115639_.

5. Instalar pacotes necessários
```bash
apt install nano iputils-ping net-tools -y
```

6. Clonar repositório `git clone https://github.com/Jannusch/TSNsched.git`.

    6.1. Acessar diretório `TSNsched/`.

    6.2. Executar comandos de instalação
    ```bash
    ./gradlew tasks
    ```
    ```bash
    ./gradlew installDist
    ```
    ```bash
    ./gradlew shadowJar
    ```

5. Criar script `run_server.sh` para facilitar a execução
```bash
#!/bin/bash
java -jar /opt/TSNsched/build/libs/TSNSCHED_New-1.0-SNAPSHOT-all.jar
```

6. Permissão de execução ao script
```bash
chmod +x run_server.sh
```

7. CTRL + D para fechar o container.

8. Feito!

#### **3. Execução**

1. Listar containers com o comando `sudo docker ps -a`. O resultado será algo como:
```text
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS         PORTS     NAMES
761e837ae78f   c74d154dec49   "bash"    17 minutes ago   Up 17 minutes            cooperativeTSN
9e0826eb4ad7   ae2fb4115639   "bash"    2 hours ago      Up 2 hours               TSNSched
```

2. Iniciar primeiro o container `cooperativeTSN`, para receber automaticamente o endereço IP 172.17.0.2.

    - Comando para iniciar o container
    ```bash
    sudo docker start cooperativeTSN
    ```
    - Comando para acessar o container
    ```bash
    sudo docker attach cooperativeTSN
    ```
    - Atualizar variável de ambiente do OMNeT++
    ```bash
    cd omnetpp/
    ```
    ```bash
    source setenv
    ```
    - Atualizar variável de ambiente do SUMO
    ```bash
    cd sumo/
    ```
    ```bash
    export SUMO_HOME="$PWD"
    ```

3. Iniciar em outro terminal o container `TSNSched`, que vai receber automaticamente o endereço IP 172.17.0.3.

    - Comando para iniciar o container
    ```bash
    sudo docker start TSNSched
    ```
    - Comando para acessar o container
    ```bash
    sudo docker attach TSNSched
    ```

4. No container `TSNSched`, executar o script `run_server.sh`. Aparecerá algo como:
```text
Sep 17, 2025 9:22:32 PM com.tsnsched.grpc.TSNschedServer start
INFO: Server started, listenig on 50051
```

5. No container `cooperativeTSN`, executar o exemplo em `.../examples/platoon_with_plexe/`:
```bash
./run -c Platooning-TSN
```

6. O resultado da execução será algo como:
    ```text
    OMNeT++ Discrete Event Simulation  (C) 1992-2022 Andras Varga, OpenSim Ltd.
    Version: 6.0.3, build: 240223-17fcae5ef3, edition: Academic Public License -- NOT FOR COMMERCIAL USE
    See the license for distribution terms and warranty disclaimer

    Setting up Cmdenv...

    Loading NED files from ../../../plexe/src/plexe:  36
    Loading NED files from ../../../veins/subprojects/veins_inet/src/veins_inet:  10
    Loading NED files from ../../../inet/src:  1139
    Loading NED files from ../../../veins/src/veins:  44
    Loading NED files from ../../src/cooperative_tsn:  10
    Loading NED files from .:  2

    Preparing for running configuration Platooning-TSN, run #0...
    Scenario: $nCars=5, $platoonSize=5, $nLanes=1, $ploegH=0.5, ....
    Assigned runID=Platooning-TSN-0-20250917-23:45:05-548
    Setting up network "Platooning"...
    Initializing...

    Running simulation...
    ** Event #0   t=0   Elapsed: 1e-05s (0m 00s)  0% completed  (0% total)
        Speed:     ev/sec=0   simsec/sec=0   ev/simsec=0
        Messages:  created: 4   present: 4   in FES: 4
    Loading configuration ... done.
    ** Event #256   t=1.0103050956   Elapsed: 1.99488s (0m 01s)  6% completed  (1% total)
        Speed:     ev/sec=128.83   simsec/sec=0.50645   ev/simsec=254.379
        Messages:  created: 731   present: 644   in FES: 42
    Interrupt signal received, trying to exit gracefully.5 ACT 5 BUF 0)                         
    ** Event #21348   t=4.31   Elapsed: 2.49714s (0m 02s)  28% completed  (4% total)
        Speed:     ev/sec=41992.5   simsec/sec=6.56975   ev/simsec=6391.8
        Messages:  created: 20149   present: 1834   in FES: 44
    ...
    ```
7. Feito!