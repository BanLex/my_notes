# Конфигурация инфраструктуры
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/readme.md)

Конфигурация храниться в файлах с расширением tf. Например: main.tf.
Для Terraform не важно название файлов. Он считывает конфигурацию из всех файлов *.tf в текущем каталоге, поэтому различные конфигурации инфраструктуры следует хранить в отдельных каталогах.
Для удобства различные ресурсы лучше хранить в отдельных файлах, а не одном длинном конфигурационном файле.

[Пример файлов конфигурации](https://github.com/BanLex/example_webapp/tree/main/yandex-cloud-terraform)

Конфигурация описывается блоками:
**terraform** - настройки терраформ, обязательный блок
**provider** - описание поставщика услуг. Обязательный блок.
**locals** - переменные. Не обязательный, этих блоков может быть несколько.
**resource** - описываются различные ресурсы. Например: сервера, сети, маршруты, хранилища.

[блок loсals](https://github.com/BanLex/my_notes/blob/main/terraform/locals.md)

[блок terraform](https://github.com/BanLex/my_notes/blob/main/terraform/block_terraform.md)

[блок provider]()

[блок resource "yandex_compute_instance"]()

[блок resource "yandex_vpc_network"]()

[блок resource "yandex_vpc_subnet"]()

[блок resource "yandex_vpc_route_table"]()


