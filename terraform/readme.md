# Terraform
[Назад на главную](https://github.com/BanLex/my_notes/tree/main/README.md)

Заметки и полезные ссылки по настройке Terraform.

## Установка
[Инструкция по установке на машину](https://learn.hashicorp.com/tutorials/terraform/install-cli)

Для установки в github runner добавьте action:
```
      - name: Setup terraform
        uses: hashicorp/setup-terraform@v1
```
[Пример workflow с terraform](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Deploy%20infrastructure%20with%20Terraform)

