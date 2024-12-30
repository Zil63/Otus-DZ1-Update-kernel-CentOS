# Otus-DZ1-Update-kernel-CentOS
Update kernel CentOS 8
Занятие 1. Vagrant-стенд для обновления ядра и создания образа системы
Цель домашнего задания
Научиться обновлять ядро в ОС Linux. Получение навыков работы с Vagrant.
Описание домашнего задания:
1. Создал новую директорию и новый файл с таким содержимым:
 MACHINES = {
   :"kernel-update" => {
              :box_name => "centos/8",
              :box_version => "1.0.0",
              :cpus => 2,
              :memory => 1024,
            }
}
ENV['VAGRANT_SERVER_URL'] = 'http://vagrant.elab.pro'
Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
      config.vm.synced_folder ".", "/vagrant", disabled: true
      config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
    end
  end
2. Запустил файл из этой директории командой: $ vagrant up.
3. Запустил virtualbox и в нем запустил только что созданную виртуальную машину.
4. В терминале подключился к этой машине: $ vagrant ssh.
5. Посмотрел версию ядра: $ uname -r. Результат: 4.18.0-240.1.1.el8_3.x86_64.
6. Для обновления ядра сначала заходим в папку:$ cd /etc/yum.repos.d/, в которой выполняем команды: $ sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* и $ sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*. Также выполняем $ yum update -y.
7. После этого необходимо работать с репозиторием ElRepo. Скачиваем файл открытого ключа: $ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org.
8. Устанавливаем сам репозиторий:$ yum install -y https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm.
9. Просмотреть доступные ядра можно такой командой: $ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available.
10. Для установки последней версии ядра вводим команду: $ yum --enablerepo=elrepo-kernel install kernel-ml.
11. Настройка загрузки ВМ именно с этим ядром: $ grub2-set-default 0.
12. После перезапуска ВМ проверил версию ядра: $ uname -r. Результат: 6.12.7-1.el8.elrepo.x86_64.
 
