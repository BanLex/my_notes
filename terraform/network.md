# network
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> 
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***
[network.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/network.tf)
```
resource "yandex_vpc_network" "network-1" {
  name = "network1"
}
```
Этот блок описывает сеть.
**name** - название сети
