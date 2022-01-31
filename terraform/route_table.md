# route table
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***
[route_table.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/route_table.tf)
```
resource "yandex_vpc_route_table" "net-rt-a" {
  network_id = "${yandex_vpc_network.network-1.id}"

  static_route {
    destination_prefix = "0.0.0.0/0"
    next_hop_address   = "192.168.10.3"
  }
}
```
Этот блок описывает статическую маршрутизацию для сети.
**network_id** - указываем к какой сети относится.
**destination_prefix** - маршрут до какой сети
**next_hop_address** - адрес gateway
