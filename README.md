# ziya
Yildiz Teknik Universitesindeki Lab siniflarindaki bilgisayarlari kontrol eden proje.

# Neden?
Icinde ubuntu olan bilgisayara sanal makine olarak kali veya windows gibi dagitimlar kurulacak. Bunu tek tek butun bilgisayarlara gidip, yapmak yerine uzaktan calisan bir sistem yapmaliyiz.

# Nasil?
<p>2 sekilde yapabiliriz. Ya ssh uzerinden scriptler calistirmak ya da
slave'e ayri bir uygulama kurarak master ile slave'e konusturan protokol benzeri bir sey kullanmaliyiz.Misal golang...vb benzeri dillerle localhost'da calisan uygulamalar olusturarak master'dan slave'e komut gonderebiliriz.</p>
<p>Biz ise suanlik, ssh uzerinden master bilgisayardan slave bilgisayara emirler vericez.
  Ya kendi script'imizi yazicaz ya da <code>ansible</code> gibi araclari kullanicaz.
Sanal makine kurmak icin <code>qemu</code> kullanicaz.</p>
<p>Isterseniz test icin alt kisimdaki adimlari kendiniz uygulayabilirsiniz.Bilgisayariniz master olucak, slave olarak ta herhangi bir vm araclariyla kurdugunuz sanal ubuntu olabilir.</p>

# Slave - Instructions
<p>Ilk olarak slave olan bilgisayarlara ssh ile ulasmak icin ssh server calistirmaliyiz.</p>

```bash
  $ sudo apt-get install openssh-server
  $ sudo systemctl start sshd
```

<p>Ve firewall ayarlarini yapmayi unutmayin.(ufw, iptables)</p>

# Master - Instruction
<p>Herseyden once Ansible kuruluyoruz.</p>

```bash
  $ sudo pacman -S ansible
```

<p>Ikinci adim olarak Ssh key olusturuyoruz.Sonrasinda ssh public key'imizi diger slave bilgisayarlara gonderiyoruz.Sonra test ederek diger bilgisayara baglaniriz.</p>
<p>Burda slave makinelerin sudo hakki olan herhangi bir kullancisi ve parolasi gerekli.Ayrica makinenin local deki ip'si gerekli.Local'deki ip adresleri <code>ifconfig</code> ile bulabilirsiniz.</p>

```bash
  $ ssh-keygen
  $ ssh-copy-id -i <slave_machine_username>@<slave_machine_ip>
  $ ssh-add
  $ ssh <slave_machine_username>@<slave_machine_ip>
```

<p><b>/etc/ansible/hosts</b> dosyasina slave makinelerin ip adresleri yaziyoruz.Ip adreslerin ustunde degisken ismi gibi ulasacagimiz bir deger giriyoruz('lab-pcs').</p>

```bash
  $ sudo vim /etc/ansible/hosts
  [lab-pcs]
  192.168.1.115   ansible_user=<slave_username> ansible_sudo_pass=<slave_sudo_password> ansible_ssh_pass=<slave_password>
  192.168.2.102   ansible_user=<slave_username> ansible_sudo_pass=<slave_sudo_password> ansible_ssh_pass=<slave_password>
  ...
```

<p>Sonrasinda test icin ansible ad-hock ile ping'leyelim </p>

```bash
  $ ansible -m ping 'lab-pcs'
  192.168.1.115 | SUCCESS => {
    "changed": false, 
    "failed": false, 
    "ping": "pong"
  }
  192.168.2.102 | SUCCESS => {
      "changed": false, 
      "failed": false, 
      "ping": "pong"
  }
```

<p>Bundan sonrasi ansible-playbook</p>

