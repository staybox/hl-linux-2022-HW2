# Otus HW2 ISCSI, multipath и кластерные файловые системы: GFS2  (course: hl-linux-2022)

```
Домашнее задание
Реализация GFS2 хранилища на виртуалках под виртуалбокс

Цель:
развернуть в Yandex Cloud следующую конфигурацию с помощью terraform

1 виртуалка с iscsi
3 виртуальные машины с разделяемой файловой системой GFS2 поверх cLVM для сдачи
terraform манифесты
ansible роль
README file Задание повышенной сложности** с fencing агентом вместо Yandex Cloud можно использовать
GCP c фенсингом https://github.com/ClusterLabs/fence-agents/blob/master/agents/gce/fence_gce.py
вагрант с фенсингом https://github.com/ClusterLabs/fence-agents/tree/master/agents/vbox (терраформ в этом случае не нужен)
Критерии оценки:
Статус "Принято" ставится при выполнении перечисленных требований.
Задание со звездочкой выполняется по желанию.
```

## Запуск

1. Скачать и установить Terraform, Ansible, Git

2. Сгенерировать открытый и закрытый ключ для ВМ командой ```ssh-keygen```

3. Необходимо выполнить команду ```git clone https://github.com/staybox/hl-linux-2022-HW2.git``` в выбранной вами папке

4. Привести к рабочему виду файл ```variables.tf```, внеся в него данные от своего аккаунта Yandex Cloud, а также изменить имя пользователя

5. Привести к необходимому виду файл ```meta.yml```, внеся в него ваш открытый ssh ключ, а также имя вашего пользователя

6. Выполнить команду ```terraform init``` в папке проекта

7. Выполнить команду ```terraform apply```. Перед использование команды ```terraform apply```, рекомендуется использовать ```terraform plan```

ПРИМЕЧАНИЕ: Имя аккаунта должно совпадать во всех конфигурационных файлах - ```variables.tf``` и ```meta.yml```

Команда для более быстрой подготовки к запуску:
```mkdir hl-linux-2022-HW2 && cd hl-linux-2022-HW2 && git clone https://github.com/staybox/hl-linux-2022-HW2.git && terraform init && terraform validate```

После действий, которые описаны выше, Terraform развернет необходимые виртуальные машины в облаке Yandex, далее Ansible произведет к ним тестовое подключение и даст вам знать об успешном подключении. Таким образом вы поймете, что можно переходить к следующему этапу - запуску Ansible ролей, которые установят необходимое программное обеспечение и все что необходимое для работы кластера Pacemaker и других компонентов. 

Важно! Так как Pacemaker кластер разворачивается в облаке Yandex и для данного облака нет fence агентов, то кластер запущен без них (т.е. в режиме их игнорирования). В производственной среде при использовании кластера Pacemaker необходимо всегда использовать fence агенты, в зависимости от вашей среды виртуализации. Fence агенты можно взять и собрать из исходников из git репозитория разработчика - https://github.com/ClusterLabs/fence-agents.

После успешного разворачивания виртуальных машин, необходимо перейти в директорию ```ansible-provision``` в папке проекта, далее выполнить ```ansible-playbook main.yml -e ntp_timezone=Europe/Moscow -e "newpassword=qwerty12345"```
Далее вы можете наблюдать за тем как происходит настройка серверов.

После успешной настройки серверов вы сможете наблюдать такую картину:

Здесь можно увидеть состояние кластера:

![Image 1](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/pcs_status.png)

![Image 2](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/pcs_cluster_status.png)

Здесь мы видим что успешно примонтирована файловая система GFS2:

![Image 3](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/mount_gfs2.png)

Далее мы можем посмотреть что у нас есть связь с ISCSI хостом, на котором располагается хранилище:

![Image 4](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/multipath_list.png)

Можем увидеть структуру наших блочных устройств:

![Image 5](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/lsblk.png)

В веб интерфейсе вашей консоли Яндекс Облака будет видно следующее:

![Image 6](https://raw.githubusercontent.com/staybox/hl-linux-2022-HW2/main/screenshots/yandex_vms.png)
