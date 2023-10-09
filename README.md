# openstack-deploy
Esse tutorial se concentra em instalar uma nuvem usando a plataforma openstack em máquinas físicas, as quais serão arquitetadas com um nó controller e 16 nós compute. Utilizando os scripts devstacks para automatização do processo de deploy.

> Antes de qualquer outra configuração, faça uma formatação limpa na máquina!

Configurações definidas:
* Ubuntu Server na versão 22.04.2 (versão minima)
* Devstack versão stable/zed


```
Em nenhum momento atualize a máquina ou seus repositórios.
```

Quando o sistema operacional escolhido estiver pronto para uso, instale o versionador de códigos git:

```
 $ sudo apt install git
```

- Em seguida, fixe o ip da sua máquina:

Instale um editor de texto (nesse tutorial utilizamos o nano, porém também pode ser usado o vim): 

```
 $ sudo apt install nano -y
```

Após isso, precisamos verificar algumas informações sobre sua interface de rede, para isso execute:

```
$ ip a
```
Identifique qual sua interface de rede:

![interface de rede.](/src/images/networkInterface.png)

- Na documentação do openStack, solicita que o nó controller possua duas interfaces de rede. Porém, as máquinas que possuimos tem apenas uma. Sendo assim, necessário a criação de uma interface de rede virtual (vlan), para isso instale o pacote VLAN:

```
$ sudo apt install vlan
```

Carregue também o módulo do protocolo IEEE 8021.Q, ao qual se baseia a vlan:

```
$ sudo modprobe --first-time 8021q
```

Verifique se o módulo foi carregado:
```
$ modinfo 8021q
```
 A saída esperada:

![protocolo vlan.](/src/images/modInfo.png)

Feito isso, vamos criar a interface de rede virtual:
* É importante saber qual o id criado para sua rede virtual no seu switch, no nosso caso usamos o id 3

  - Exemplo:
    - Label da interface de rede: eno1
    - ID Vlan: 3
    - IP: 192.168.1.100/24

```
$ sudo ip link add link eno1 name eno1.3 type vlan id 3
```
Atribua um ip a sua vlan:

```
sudo ip addr add 192.168.1.100/24 dev eno1.3
```

Para verificar se as configurações foram aplicadas, execute:

```
$ ip a
```
Porém para essas configurações ficarem definitivas, é necessário a inclusão em um arquivos que vamos configurar nas próximas etapas

Acesse o arquivo "/etc/netplan/00-installer-config.yaml"

```
 $ sudo nano /etc/netplan/00-installer-config.yaml
```
```
#Warning: Nesse arquivo é muito importante as identações
 network:
  renderer: networkd
  ethernets:
    eth0: #Preencha sua interface de rede
      addresses:
        - 10.10.1.100/24 #IP da máquina
      nameservers:
        addresses: [10.10.1.253] #IP do gateway
      routes:
        - to: default
          via: 10.10.1.253
  vlans:
        eno1.3:
            id: 3
            link: eno1
            addresses: [192.168.1.100/24]
  version: 2
```
Após isso rode o comando para aplicar as configurações:

```
$ sudo netplan apply
```


