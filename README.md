# Tutorial de Instalação do Docker

Este tutorial vai guiá-lo na instalação do Docker para Ubuntu 24.04.

## Etapa 1: Atualizar o sistema e instalar dependências

Para começar, faça login no seu servidor e atualize o índice de pacotes definido no arquivo `/etc/apt/sources.list` e no diretório `/etc/apt/sources.list.d/`.

```bash
sudo apt update  -y
sudo apt upgrade -y
```

Algumas dependências são necessárias para que a instalação prossiga sem problemas. Execute o comando abaixo para instalá-las.

```bash
sudo apt install curl apt-transport-https ca-certificates software-properties-common
```
Agora que as listas de pacotes estão atualizadas e as dependências necessárias estão instaladas, podemos proceder com a instalação do Docker.

## Etapa 2: Instalar o Docker

Existem duas maneiras de instalar o Docker no Ubuntu. Primeiro, você pode instalá-lo a partir dos repositórios padrão usando o gerenciador de pacotes APT.

Instalar Docker da versão padrão
```bash
sudo apt install docker.io -y

sudo apt install docker.io docker-compose
```

No entanto, a versão instalada não é a mais recente. Para instalar a versão mais atual, você precisará instalá-lo a partir do repositório oficial do Docker.

### Instalar a versão mais recente do Docker

Para isso, baixe a chave GPG do Docker usando o comando curl, conforme mostrado abaixo.

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Em seguida, adicione o repositório APT do Docker ao seu sistema. O comando cria um arquivo docker.list no diretório /etc/apt/sources.list.d/.

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Agora, atualize o índice de pacotes local para notificar o sistema sobre o novo repositório adicionado.

```bash
sudo apt update
```

Agora instale o Docker Community Edition (grátis para download e uso) com o seguinte comando. A opção -y permite a instalação não interativa.

```bash
sudo apt install docker-ce -y
```

O serviço do Docker será iniciado automaticamente após a instalação. Você pode verificar seu status executando o comando:

```bash
sudo systemctl enable --now docker

sudo systemctl status docker
```

A saída abaixo confirma que o Docker está funcionando conforme esperado.

```bash
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Fri 2025-08-29 14:48:20 -03; 31s ago
 Invocation: bb66509093e443149ff23146cc2f12f6
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 1344389 (dockerd)
      Tasks: 14
     Memory: 22.4M (peak: 24.6M)
        CPU: 305ms
     CGroup: /system.slice/docker.service
             └─1344389 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Aug 29 14:48:19 werlley-ssd dockerd[1344389]: time="2025-08-29T14:48:19.890919288-03:00" level=info msg="[graphdriver] using prior storage driver: overlay2"
lines 1-14
```

## Install Docker Engine:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

## Deploy Portainer CE:
```bash
sudo docker volume create portainer_data
sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Acesso pelo o link:

```bash
https://localhost:9443/
```

## Etapa 3: Adicionar o usuário ao grupo Docker

Por padrão, o Docker é configurado para rodar como root ou um usuário regular com privilégios elevados (usuário sudo). A implicação disso é que apenas o root ou o usuário sudo pode executar os comandos do Docker. Executar os comandos do Docker como um usuário regular resultará em uma mensagem de "erro de permissão".

Como já temos um usuário sudo chamado werlley configurado, executar o Docker com esse usuário é perfeitamente possível. Uma opção ainda melhor é adicionar o usuário que está logado, neste caso, werlley, ao grupo docker. Isso garante que não precisemos invocar o sudo sempre que executarmos comandos do Docker, já que o usuário já pertence ao grupo docker.

Se você quiser evitar digitar sudosempre que executar o dockercomando, adicione seu nome de usuário ao dockergrupo:

```bash
sudo groupadd docker
```

```bash
sudo usermod -aG docker $USER

groups
```

Para aplicar a nova associação ao grupo, saia do servidor e entre novamente ou digite o seguinte:

```bash
newgrp $USER

su - $USER
```

Confirme se seu usuário foi adicionado ao grupo docker digitando:

```bash
groups
```

Agora, execute o comando groups para verificar se o usuário foi adicionado ao grupo docker:

```bash
groups $USER
```




----------------------------------------------------------------------------------------------------
# Tutorial de Instalação Ubuntu 20.04
```bash
docker pull ubuntu:20.04
```

```bash
docker run -it --name ros-foxy --network host ubuntu:20.04 bash
```

### Como “sair do modo root” dentro do Docker

Primeiro crie um usuário normal dentro do container (se quiser usar algo parecido com o comportamento do sudo):

```bash
adduser werlley
apt update
apt install sudo -y
usermod -aG sudo werlley
```

Então troque para esse usuário:
```bash
su - werlley
```

E quando precisar de root novamente:
```bash
sudo comando
```


## Sair e voltar ao container

Para sair:

```bash
exit
```

Para reabrir depois:

```bash
docker start -ai ros-foxy
```

Para abrir outro shell no mesmo container:

```bash
docker exec -it ros-foxy bash
```

```bash
docker start ros-foxy
docker exec -it --user werlley ros-foxy bash
```

(Mas isso só funciona se você instalou o sudo dentro do container e configurou.)








----------------------------------------------------------------------------------------------------
# Tutorial de Instalação e Configuração ROS Foxy 


## Configuração inicial:
 

Se você estiver usando a imagem do Ubuntu 20.04 em um ambiente Docker, execute os seguintes comandos para a configuração inicial:

```bash
apt update
sudo apt install sudo
```

Para instalações locais, pule esta etapa se o sudo já estiver instalado.

Se o comando sudo (Super User Do) estiver instalado, prossiga de acordo com o documento.

**Link para instalar o ROS2 Foxy**: [ROS 2 Documentation – Installation Guide](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html#install-ros-2-packages)

Prosseguir com todas as etapas em ordem é a maneira mais rápida de instalar sem erros.

## Definir localidade
Certifique-se de ter uma localidade compatível com UTF-8. Se você estiver em um ambiente minimalista (como um contêiner Docker), a localidade pode ser algo minimalista, como POSIX. Testamos com as seguintes configurações. No entanto, deve funcionar se você estiver usando uma localidade diferente compatível com UTF-8.

```bash
locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```

## Configurar fontes

Você precisará adicionar o repositório apt do ROS 2 ao seu sistema.

Primeiro, certifique-se de que o repositório Ubuntu Universe esteja habilitado.

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
```

Agora adicione a chave ROS 2 GPG com o apt.

### 1) Apague a lista quebrada
```bash
sudo rm /etc/apt/sources.list.d/ros2.list
```

### 2) Pré-requisitos e chave GPG
```bash
sudo apt update
sudo apt install -y curl gnupg2 lsb-release
```

```bash
sudo mkdir -p /usr/share/keyrings
curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  | sudo gpg --dearmor -o /usr/share/keyrings/ros-archive-keyring.gpg
```

### 3) Adicione o repositório com aspas retas e dois hifens

Para Ubuntu 20.04 use focal (não deixe o comando deduzir automaticamente se sua máquina não é 20.04).

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu focal main" \
| sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```


## Instalar pacotes ROS 2
Atualize os caches do seu repositório apt depois de configurar os repositórios.

```bash
sudo apt update
```

Os pacotes ROS 2 são compilados em sistemas Ubuntu atualizados com frequência. É sempre recomendável que você verifique se o seu sistema está atualizado antes de instalar novos pacotes.

```bash
sudo apt upgrade
```

Instalação para desktop (recomendado): ROS, RViz, demonstrações, tutoriais.

```bash
sudo apt install ros-foxy-desktop python3-argcomplete
```

Instalação básica do ROS (básico): bibliotecas de comunicação, pacotes de mensagens, ferramentas de linha de comando. Sem ferramentas de interface gráfica.

```bash
sudo apt install ros-foxy-ros-base python3-argcomplete
```

Ferramentas de desenvolvimento: Compiladores e outras ferramentas para construir pacotes ROS

```bash
sudo apt install ros-dev-tools
```

## Configuração do ambiente

### Obtendo o script de configuração

Configure seu ambiente obtendo o seguinte arquivo.

```bash
source /opt/ros/foxy/setup.bash
```

Ative o ambiente do ROS no shell:

```bash
echo "source /opt/ros/foxy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Experimente alguns exemplos
Se você instalou ros-foxy-desktopo acima, pode tentar alguns exemplos.

Em um terminal, crie o arquivo de instalação e execute um C++ talker:

```bash
source /opt/ros/foxy/setup.bash
ros2 run demo_nodes_cpp talker
```

Em outro terminal, crie o arquivo de configuração e execute um Python listener:

```bash
source /opt/ros/foxy/setup.bash
ros2 run demo_nodes_py listener
```







----------------------------------------------------------------------------------------------------
# Criando um Workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
```

Um repositório pode ter várias ramificações. Você precisa verificar aquela que tem como alvo a sua distribuição ROS 2 instalada. Ao clonar este repositório, adicione o -bargumento seguido por essa ramificação.

No ros2_ws/srcdiretório, execute o seguinte comando:

```bash
git clone https://github.com/ros/ros_tutorials.git -b foxy-devel
```

```bash
# cd if you're still in the ``src`` directory with the ``ros_tutorials`` clone
cd ..
rosdep install -i --from-path src --rosdistro foxy -y
```

```bash
colcon build
```


### Se quiser usar o seu container atual (ubuntu:20.04 que você configurou)

Você precisa recriar (ou iniciar com os mounts certos) para dar acesso ao X:

Permitir X no host:

```bash
xhost +local:
```

Recrie o container com as flags do X (se puder perder o atual):

```bash
docker run -it --name ros-foxy \
  --net=host \
  -e DISPLAY=$DISPLAY \
  -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  ubuntu:20.04 bash
```

Se for entrar depois com docker exec, passe as variáveis:

```bash
docker exec -it -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 ros-foxy bash
```

(o volume /tmp/.X11-unix precisa ter sido montado na criação do container).

Para testar o X de dentro do container, você pode instalar um app simples:

```bash
apt install -y x11-apps
xeyes
```

Execute no host (fora do container), já com uma sessão gráfica ativa e após xhost +local::

```bash
docker exec -it --user werlley \
  -e DISPLAY=$DISPLAY \
  -e QT_X11_NO_MITSHM=1 \
  ros-foxy \
  bash
```

```bash
ros2 run turtlesim turtlesim_node
```


## Deixar permanente para o werlley

Entre no container como root e configure o ambiente do usuário:

```bash
docker exec -it ros-foxy bash
```

Dentro do container (root):

### 1) Tornar o ROS permanente no shell do werlley
```bash
echo 'source /opt/ros/foxy/setup.bash' >> /home/werlley/.bashrc
```

### 2) Garantir DISPLAY quando su - (caso perca a variável)
```bash
echo 'if [ -z "$DISPLAY" ] && [ -e /tmp/.X11-unix/X0 ]; then export DISPLAY=:0; fi' >> /home/werlley/.bashrc
echo 'export QT_X11_NO_MITSHM=1' >> /home/werlley/.bashrc
```

### 3) (Opcional) Silenciar aviso do Qt
```bash
mkdir -p /tmp/runtime-werlley && chown werlley:werlley /tmp/runtime-werlley
echo 'export XDG_RUNTIME_DIR=/tmp/runtime-werlley' >> /home/werlley/.bashrc
chown werlley:werlley /home/werlley/.bashrc
```

Agora, no host, entre como werlley com o DISPLAY herdado:

```bash
docker exec -it --user werlley -e DISPLAY=$DISPLAY ros-foxy bash
```

# dentro do container (já com ROS "sourced" pelo .bashrc):
```bash
ros2 run turtlesim turtlesim_node
```










----------------------------------------------------------------------------------------------------
# Instalação Gazebo11


```bash
docker exec -it --user werlley -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 -e LIBGL_ALWAYS_SOFTWARE=1 ros-foxy bash
```

```bash
sudo apt update # Make sure everything is up to date
sudo apt install curl gnupg
sudo apt install python3-colcon-common-extensions python3-rosdep
sudo rosdep init
sudo apt install gazebo11 libgazebo11-dev ros-foxy-gazebo-dev ros-foxy-gazebo-plugins ros-foxy-gazebo-msgs  ros-foxy-gazebo-ros-pkgs ros-foxy-gazebo-ros
sudo apt install gazebo11 ros-foxy-gazebo-ros-pkgs
sudo apt install ros-foxy-cartographer ros-foxy-cartographer-ros
sudo apt install ros-foxy-navigation2 ros-foxy-nav2-bringup
sudo apt install ros-foxy-ros-core ros-foxy-geometry2
source /usr/share/gazebo-11/setup.sh
```

```bash

echo "source /usr/share/gazebo-11/setup.sh" >> ~/.bashrc
source ~/.bashrc

gazebo
```

## garanta o ambiente do ROS e do Gazebo

```bash
source /opt/ros/foxy/setup.bash
source /usr/share/gazebo-11/setup.sh
```

### conserta variáveis que o Qt e o X11 precisam

```bash
export QT_X11_NO_MITSHM=1
export XDG_RUNTIME_DIR=/tmp/runtime-werlley; mkdir -p "$XDG_RUNTIME_DIR"
```

### força renderização por software (llvmpipe) e ignora GPU

```bash
export LIBGL_ALWAYS_SOFTWARE=1
```

```bash
### teste rápido de GLX (opcional)
# apt update && apt install -y mesa-utils
# glxinfo -B

# rode o gazebo
gazebo
```

## Se você já está como werlley (dentro do container)

```bash
grep -qxF 'export LIBGL_ALWAYS_SOFTWARE=1' ~/.bashrc || \
echo 'export LIBGL_ALWAYS_SOFTWARE=1' >> ~/.bashrc

# aplica agora nesta sessão
source ~/.bashrc

# conferir
echo $LIBGL_ALWAYS_SOFTWARE   # deve mostrar 1
```

## Teste


```bash
#
#open a gazebo_ros_diff_drive_demo
#
#gazebo --verbose /opt/ros/foxy/share/gazebo_plugins/worlds/gazebo_ros_diff_drive_demo.world

#
#move a gazebo_ros_diff_drive_demo
#
#ros2 topic list 
#ros2 topic pub /demo/cmd_demo geometry_msgs/Twist '{linear: {x: 1.0}}' -1

#
#open a gazebo world
#
#cd /usr/share/gazebo-11
#gazebo worlds/cafe.world

#
#copy world and object files to our customized worlds and models
#
# cd /usr/share/gazebo-11/worls
# cp cafe.world ~/ros2_ws/src/two_wheeled_robot/worlds
# cd ~/ros2_ws/src/two_wheeled_robot/worlds
# ls
# cd /usr/share/gazebo-11/models
# ls
# cp -r ground_plane ~/ros2_ws/src/two_wheeled_robot/models
# cd ~/.gazebo/models
# ls
# cp -r cafe ~/ros2_ws/src/two_wheeled_robot/models
# cp -r cafe_table ~/ros2_ws/src/two_wheeled_robot/models
#
# Create launch file to load cafe.world into gazebo by following the instrction in this link
#  
# http://automaticaddison.com/how-to-load-a-world-file-into-gazebo-ros-2/
#
# cd ~/ros2_ws/src/two_wheeled_robot
# cd launch
# gedit load_world_into_gazebo.launch.py
```





----------------------------------------------------------------------------------------------------
# Configurando RVIZ

## No host (fora do container), garanta X11 aberto e libere acesso
```bash
echo $DISPLAY                 # deve sair algo tipo :0
xhost +SI:localuser:root
xhost +SI:localuser:werlley
xhost +local:
```

Se echo $DISPLAY vier vazio, você não está em sessão gráfica — entre numa sessão Xorg/XWayland primeiro.

```bash
rviz2
```

CASO não Funcione então:

```bash
docker run -it --name ros-foxy-gui \
  --net=host \
  -e DISPLAY=$DISPLAY \
  -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
  --device /dev/dri:/dev/dri \
  ros-foxy:withros bash
```

Se você já criou o ros-foxy com -v /tmp/.X11-unix:/tmp/.X11-unix, pode pular esse passo.


## Dentro do container, rodar como werlley com o ambiente certo

```bash
# entre como werlley já herdando DISPLAY do host
docker exec -it --user werlley -e DISPLAY=$DISPLAY ros-foxy-gui bash

# dentro do shell do werlley:
export QT_X11_NO_MITSHM=1
export XDG_RUNTIME_DIR=/tmp/runtime-werlley; mkdir -p "$XDG_RUNTIME_DIR"

source /opt/ros/foxy/setup.bash

# (opcional: software rendering se aparecer erro do Mesa)
# export LIBGL_ALWAYS_SOFTWARE=1

rviz2
```









----------------------------------------------------------------------------------------------------
# Desinstalar ROS Foxy

Se você precisar desinstalar o ROS 2 ou mudar para uma instalação baseada em código-fonte depois de já ter instalado a partir dos binários, execute o seguinte comando:

```bash
sudo apt remove ~nros-foxy-* && sudo apt autoremove
```

Você também pode querer remover o repositório:

```bash
sudo rm /etc/apt/sources.list.d/ros2.list
sudo apt update
sudo apt autoremove
# Consider upgrading for packages previously shadowed.
sudo apt upgrade
```

----------------------------------------------------------------------------------------------------
# Como desistalar o docker completamente

- Passo 1:

```bash
dpkg -l | grep -i docker
```
- Passo 2:

```bash
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin

sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin

sudo apt remove docker-buildx-plugin
```

Os comandos acima não removerão imagens, contêineres, volumes ou arquivos de configuração criados pelo usuário no seu host. Se desejar excluir todas as imagens, contêineres e volumes, execute os seguintes comandos:


```bash
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
sudo rm -r ~/.docker
sudo apt autoremove
```

Removido o Docker completamente do sistema.
