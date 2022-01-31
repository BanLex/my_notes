# provider
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> 
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***

[provider.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/provider.tf)
```
provider "yandex" {
  token     = "${ local.oauth }"
  cloud_id  = "${ local.cloud }"
  folder_id = "${ local.folder }"
  zone      = "ru-central1-a"
}
```
Здесь указываются настройки подключения к месту развертывания инфраструктуры.
**token** - [можно получить по инструкции OAuth-токен](https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token)
Добавляем его в secrets. Называю YC_OAUTH.
**cloud_id** и **folder_id** - [Можно взять из консоли](https://console.cloud.yandex.ru/) верхний облако, ниже папка.
Добавляю в secrets YC_CLOUD и YC_FOLDER.

Этот блок свободно получает доступ к переменным. Файл locals.tf, который генерирую в wokflow.
Для этого в раздел env добавляю:
```
      oauth: "${{secrets.YC_OAUTH}}"
      cloud: "${{secrets.YC_CLOUD}}"
      folder: "${{secrets.YC_FOLDER}}"
```
Файл создаю шагом
```
      - name: create locals file
        run: |
          cd yandex-cloud-terraform
          echo 'locals {'' > locals.tf
          echo '  oauth  = "${{ oauth }}"'  >> locals.tf 
          echo '  cloud  = "${{ cloud }}"'  >> locals.tf 
          echo '  folder = "${{ folder }}"'  >> locals.tf 
          echo '}'  >> locals.tf
          cat locals.tf
```
