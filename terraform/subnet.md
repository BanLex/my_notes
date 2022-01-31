# subnet
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***
[subnet.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/subnet.tf)
```
resource "yandex_vpc_subnet" "subnet-1" {
  name           = "subnet1"
  zone           = "ru-central1-a"
  network_id     = yandex_vpc_network.network-1.id
  v4_cidr_blocks = ["192.168.10.0/24"]
  route_table_id = "${yandex_vpc_route_table.net-rt-a.id}"
}
```
Этот блок описывает подсеть.
**name** - название подсети
**zone** - зона подсети
**network_id** - частью какой сети является подсеть
**v4_cidr_blocks** - какую адрессацию использовать
**route_table_id** - подключаем таблицу маршрутизации
