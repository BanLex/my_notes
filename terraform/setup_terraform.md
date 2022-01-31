# Установка terraform
[Назад]()

[Инструкция по установке на сервер(рабочую станцию)](https://learn.hashicorp.com/tutorials/terraform/install-cli)

Для использования в рабочем процессе github добавьте action:
```
      - name: Setup terraform
        uses: hashicorp/setup-terraform@v1
```
[Пример workflow с terraform](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Deploy%20infrastructure%20with%20Terraform.yml)
