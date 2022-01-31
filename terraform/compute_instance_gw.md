# compute instance
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***
[compute_instance_gw.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/compute_instance_gw.tf)
```
resource "yandex_compute_instance" "gw" {
  name = "gw"
  
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd8drj7lsj7btotd7et5"
      size = "8"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet-1.id
    nat       = true
    ip_address = "192.168.10.3"
    nat_ip_address = "84.201.129.141"
    dns_record {
      fqdn = "gw.ru-central1.internal."
    }
  }

  metadata = {
    user-data = "${file("user")}"
  }

}
```
Этот блок описывает создаваемый сервер.
`name` - имя сервера
```  
  resources {
    cores  = 2
    memory = 2
  }
```
Характеристики. Количество ядер и объем памяти.
```
  boot_disk {
    initialize_params {
      image_id = "fd8drj7lsj7btotd7et5"
      size = "8"
    }
  }
```
Какой образ использовать для создания сервера.
```
  network_interface {
    subnet_id = yandex_vpc_subnet.subnet-1.id
    nat       = true
    ip_address = "192.168.10.3"
    nat_ip_address = "84.201.129.141"
    dns_record {
      fqdn = "gw.ru-central1.internal."
    }
  }
```
Сетевые интерфейсы. Подсеть, публичный адрес и его адрес, внутрений адрес, dns запись. Кроме подсети, остальные атрибуты не обязательны.
```
  metadata = {
    user-data = "${file("user")}"
  }
```
Описывает пользователя которого необходимо создать. В данном случае используется файл с описанием пользователя.

[user](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/user)
```
#cloud-config
users:
  - name: banlex
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDtwYvTyY//b8/r/siy4V2qXHMKPlgrnYt7pgHipdVqF7Ew2yDfyJRj4HYdMdPq1v9sZR77J1V5GNWfOyZ6Ywy9tvpDTLGbh7OWlRMqYhkqH0FSfFxNeQnxKmPZtKHuxxvElUu5gtoqpVvhHA4SzgYrVl7PD9lHScbZyA3i5zuI/C8iWkNHinglzMpuhGAEf74vUZJps6eyaa7CViJvKxjEhvplTuRQnzV2TvF+JvTRBBD0m5q4+GuMmGOVgWqmIT9M1/MGtqLig4Wy31IUzV8RvH5SZom4gB1JMBJESBwHdxDKzWBleLcu+Tqrd6fjpxiynFw8krHKg73FNpVApYOAFCBIfuKNGL117oNaS8GFrBwSNqtL1rlnmqqOErMSOq5vaEdxRe1kP5ZO3ilOW9OL+NBgcbMyBnFJ4BRlLJYlmNjIkvg8jNALTUh/Jk3sDLjGHucmp+b4uuV919cne13dWn7nTsE+p6N/Er1Xd8wXYWiSOFCcG7MICvQIsTdYmyc= banlex@eOS
```
Содержит имя пользователя, группу, оболочку, использование sudo без пароля и подтверждения, публичный ключ ssh.
