# Создание миграции

### Создание модели

Генераторы модели и скаффолда создают соответстующие миграции для добавления новой модели. Эта миграция уже содержит инструкции для создания соответствующей таблицы. Если вы сообщите Rails какие столбцы вам нужны, выражения для создания этих столбцов также будут добавлены. Например, запуск

```bash
$ rails generate model Product name:string description:text
```

создаст подобную миграцию

```ruby
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :name
      t.text :description

      t.timestamps
    end
  end
end
```

Вы можете указать столько пар имя-столбца/тип, сколько хотите. По умолчанию сгенерированная миграция будет включать `t.timestamps` (что создает столбцы `updated_at` и `created_at`, автоматически заполняемые Active Record).

### Создание автономной миграции

Если хотите создать миграцию для других целей (например, добавить столбец в существующую таблицу), также возможно использовать генератор миграции:

```bash
$ rails generate migration AddPartNumberToProducts
```

Это создат пустую, но правильно названную миграцию:

```ruby
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
  end
end
```

Если имя миграции имеет форму "AddXXXToYYY" или "RemoveXXXFromYYY" и далее следует перечень имен столбцов и их типов, то в миграции будут созданы соответствующие выражения `add_column` и `remove_column`.

```bash
$ rails generate migration AddPartNumberToProducts part_number:string
```

создаст

```ruby
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
  end
end
```

Аналогично,

```bash
$ rails generate migration RemovePartNumberFromProducts part_number:string
```

создаст

```ruby
class RemovePartNumberFromProducts < ActiveRecord::Migration
  def up
    remove_column :products, :part_number
  end

  def down
    add_column :products, :part_number, :string
  end
end
```

Вы не ограничены одним создаваемым столбцом. Например

```bash
$ rails generate migration AddDetailsToProducts part_number:string price:decimal
```

создаст

```ruby
class AddDetailsToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
    add_column :products, :price, :decimal
  end
end
```

Как всегда, то, что было сгенерировано, является всего лишь стартовой точкой. Вы можете добавлять и убирать строки, как считаете нужным, отредактировав файл db/migrate/YYYYMMDDHHMMSS_add_details_to_products.rb.

NOTE. Генерируемый файл миграции для деструктивных миграций будет все еще по-старому использовать методы `up` и `down`. Это так, потому что Rails не может знать оригинальные типы данных, которые вы создали когда-то ранее.

Также генератор принимает такой тип столбца, как `references` (или его псевдоним `belongs_to`). Например

```bash
$ rails generate migration AddUserRefToProducts user:references
```

создаст

```ruby
class AddUserRefToProducts < ActiveRecord::Migration
  def change
    add_reference :products, :user, :index => true
  end
end
```

Эта миграция создаст столбец user_id и соответствующий индекс.

### Поддерживаемые модификаторы типа

Также возможно определить некоторые опции сразу после типа поля в фигурных скобках. Можно использовать следующие модификаторы:

* `limit`        Устанавливает максимальный размер полей `string/text/binary/integer`
* `precision`    Определяет точность для полей `decimal`
* `scale`        Определяет масштаб для полей `decimal`
* `polymorphic`  Добавляет столбец `type` для связей `belongs_to`

К примеру, запуск

```bash
$ rails generate migration AddDetailsToProducts price:decimal{5,2} supplier:references{polymorphic}
```

создат миграцию, которая выглядит как эта:

```ruby
class AddDetailsToProducts < ActiveRecord::Migration
  def change
    add_column :products, :price, :precision => 5, :scale => 2
    add_reference :products, :user, :polymorphic => true, :index => true
  end
end
```