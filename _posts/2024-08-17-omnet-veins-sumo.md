---
layout: post
title:  OMNeT++, Veins, and SUMO tutorial
date:   2024-08-17 22:40:16
description: 
tags: veins sumo omnet tutorial ptbr
categories: posts
---

#### **1. Versões utilizadas neste tutorial**

- Ubuntu 22.04.4 LTS
- OMNeT++ 6.0
- Veins 5.2
- Simulation of Urban MObility (SUMO) 1.18.0

#### **2. Preparando o ambiente do projeto**

1. Criar uma pasta chamada `projeto` para instalação das ferramentas em `/home/USUARIO/`
- OBS: Substitua `USUARIO` pelo nome de usuário da sua máquina
2. Faça download do OMNeT++ [neste link](https://github.com/omnetpp/omnetpp/releases/download/omnetpp-6.0/omnetpp-6.0-linux-x86_64.tgz)
3. Faça download do Veins [neste link](https://veins.car2x.org/download/veins-5.2.zip)
4. Faça download do SUMO [neste link](https://sourceforge.net/projects/sumo/files/sumo/version%201.18.0/sumo-src-1.18.0.zip/download)
5. Adicione todos os arquivos baixados na pasta `/home/USUARIO/projeto/`

#### **3. Instalando o OMNeT++**

1. Descompacte o arquivo `omnetpp-6.0-linux-x86_64.tgz` e recorte a pasta `omnetpp-6.0` para o diretório `/home/USUARIO/projeto/`
2. Instale as bibliotecas necessárias:
- `sudo apt-get install build-essential clang lld gdb bison flex perl python3 python3-pip qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools libqt5opengl5-dev libxml2-dev zlib1g-dev doxygen graphviz libwebkit2gtk-4.0-37`
- `python3 -m pip install --user --upgrade numpy pandas matplotlib scipy seaborn posix_ipc`
3. Acesse a pasta `omnetpp-6.0/`
4. Altere as seguintes linhas no arquivo `omnetpp-6.0/configure.user`:
- `PREFER_CLANG=yes` para `PREFER_CLANG=no`
- `WITH_OSG=yes` para `WITH_OSG=no`
5. Execute os comandos:
- `source setenv`
- `./configure`
  - Se a mensagem ao final da execução for `Configuration phase finished. Use 'make' to build OMNeT++.`, prossiga para o próximo comando.
- `make -j$(nproc)`
6. Execute o comando `omnetpp` no terminal para abrir o OMNeT++
- Ao carregar o programa irá abrir uma janela para seleção do diretório que será o `workspace`
- Clicar em `Browse...`, navegar até o diretório `projeto`, clicar para criar um novo diretório e nomeá-lo como `workspace`.
- Clicar em `Open` e depois em `Launch`
- Desmarcar as opções: "Install INET Framework" e "Import OMNeT++ programming examples"
- Clicar em `OK`
7. OMNeT++ instalado!

#### **4. Instalando o Veins**

Para adicionar o Veins ao OMNeT++, siga os seguintes passos:

1. Com o OMNeT++ aberto, clique em `File > Import > General > Existing Projects into Workspace`
2. Escolha a opção `Select archive file` e clique em `Browse...`
3. Navegue até a pasta `projeto`, seleciona o arquivo `veins-5.2.zip` e clique em `OK`
4. Desmarque SOMENTE as opções `veins_catch`, `veins_inet`, `veins_inet3` e `veins_testsims`
5. Clique em `Finish`
6. O veins aparecerá como um projeto no OMNeT++. Agora você deve compilar o veins. Para isso, faça os seguintes passos:
- Clique com botão esquerdo do mouse em clima do `veins` e selecione a opção `Build Configurations > Set Active > 2 gcc-release`
- Tecle `CTRL + b` para executar a compilação 
- Ao executar sem erros, será exibido no console a seguinte mensagem: `Build Finished.`
7. Veins instalado!

#### **5. Instalando o SUMO**

1. Descompacte o arquivo `sumo-src-1.18.0.zip` e recorte a pasta `sumo-1.18.0` para o diretório `/home/USUARIO/projeto/`
2. Instale as bibliotecas necessárias:
- `sudo apt-get install cmake g++ libxerces-c-dev libfox-1.6-dev libgdal-dev libproj-dev libgl2ps-dev swig`
- `cd sumo-1.18.0`
- `export SUMO_HOME="$PWD"`
- `mkdir build/cmake-build && cd build/cmake-build`
- `cmake ../..`
- `make -j$(nproc)`
- `sudo make install`
3. Teste a instalação digitando o seguinte comando no terminal: `sumo --version`
- Se a instalação estiver OK, aparecerá o seguinte cabeçalho na mensagem: `Eclipse SUMO sumo Version 1.8.0`
4. SUMO instalado!

#### **6. Testando a integração entre OMNeT++, Veins e SUMO**

1. Abrir um novo terminal e levantar o serviço que integra o SUMO e o Veins executando o seguinte comando:
- `python3 /home/USUARIO/projeto/workspace/veins/bin/veins_launchd -vvv -p 9999`
2. No OMNeT++, navegar nas pastas do projeto do veins em: `veins > examples > veins`
3. Clicar com o botão esquerdo do mouse em cima do arquivo `omnetpp.ini` e selecionar a opção `Run As > 1 OMNeT++ Simulation`
4. Basta clicar em `Simulate > Run`.
5. Se tudo ocorrer bem, a seguinte imagem irá aparecer:

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="/assets/img/tutorials/veins.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Bons estudos! 😀
