# openstack-deploy
This to do list was created to give a openstack multinode deployment on physical machines using devstack .

>Antes de qualquer outra configuração, faça uma configuração limpa na máquina!

- Configurações definidas:
* Ubuntu Server na versão 22.04.2
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

Acesse o arquivo "/etc/netplan/00-installer-config.yaml"

```
 $ sudo nano /etc/netplan/00-installer-config.yaml
```