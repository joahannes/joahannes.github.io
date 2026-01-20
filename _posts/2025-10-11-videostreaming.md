---
layout: post
title:  Video Streaming tutorial
date:   2025-10-11 10:40:16
description: 
tags: video streaming omnet tutorial ptbr
categories: posts
# toc:
#   sidebar: left
---

## <code style="color : red; font-weight: bold;">EM CONSTRUÇÃO!</code>

#### **Versões de softwares**

- SO: Ubuntu 22.04.4 LTS
- OMNeT++: 6.0.1
- INET: 4.5.4
- Veins: 5.2
- FFMpeg: 

#### **1. Instalação do OMNeT++**

1.1. Preparação

- Acessar pasta `omnetpp/`.

- Instalar todos os pacotes necessários indicados na documentação.
```bash
sudo apt install -y build-essential clang lld gdb bison flex perl \
python3 python3-pip qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools \
libqt5opengl5-dev libxml2-dev zlib1g-dev doxygen graphviz libwebkit2gtk-4.0-37 xdg-utils
```
- Alteração no arquivo `configure.user`: as flags `PREFER_CLANG=` e `WITH_QTENV=` devem ser alteradas de **_yes_** para **_no_**.

1.2. Compilação (aproveite para tomar um café ☕, pois isso levará algum tempo...)

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