# Навязывание протокола HTTPS

Иногда хочется навязать определенному контроллеру быть доступным только через протокол HTTPS по причинам безопасности. Начиная с Rails 3.1 можно использовать в контроллере метод `force_ssl`, для принуждения к этому:

```ruby
class DinnerController
  force_ssl
end
```

подобно фильтру, можно также передать `:only` и `:except` для принуждения безопасного соединения только определенным экшнам.

```ruby
class DinnerController
  force_ssl only: :cheeseburger
  # или
  force_ssl except: :cheeseburger
end
```

Пожалуйста, отметьте, что если вы добавили `force_ssl` во многие контроллеры, то, возможно, вместо этого хотите навязать всему приложению использование HTTPS. В этом случае можно установить `config.force_ssl` в файле окружения.