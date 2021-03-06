# Типы отображения данных


## Таблица (Table)
Таблицы предназначены для вывода списка документов раздела.

На данные момент поддерживаются следующие типы вывода данных:
 - `AdminDisplay::table()` - обычная таблица (`SleepingOwl\Admin\Display\DisplayTable`)
 - `AdminDisplay::datatables()` - таблица с выводом данных используя плагин https://datatables.net/ (`SleepingOwl\Admin\Display\DisplayDatatables`)
 - `AdminDisplay::datatablesAsync()` (`SleepingOwl\Admin\Display\DisplayDatatablesAsync`)
 - `AdminDisplay::tree()` - вывод данных в виде дерева (`SleepingOwl\Admin\Display\DisplayTree`)

**Пример использования:**
```php
$model->onDisplay(function () {
    $display = AdminDisplay::table();
    
    ...
    
    return $display;
});
```

### Указание столбцов (Расширение)
`SleepingOwl\Admin\Display\Extension\Columns`

Типы столбцов и их описание можно посмотреть [здесь](columns.md)

```php
$display->setColumns([
  ...
  AdminColumn::link('title')->setLabel('Title'),
  AdminColumn::datetime('created_at')->setLabel('Created')->setFormat('d.m.Y'),
  ...
]);

// or 

$display->getColumns()->push(
  AdminColumn::text('title')
);
```

### Фильтры столбцов (Расширение)
`SleepingOwl\Admin\Display\Extension\ColumnFilters`

```php
$display->setColumnFilters([
  ...
  AdminColumnFilter::text()->setPlaceholder('Full Name'),
  ...
]);

// or 

$display->getColumnFilters()->push(
  AdminColumnFilter::text()->setPlaceholder('Full Name')
);
```

### Eager Loading
Позволяет оптимизировать запросы к БД в случае использования связей с другими моделями. [Подробности](https://laravel.com/docs/5.2/eloquent-relationships#eager-loading)

```php
$display->with('country', 'companies');
```

### Изменение запроса (Расширение)
`SleepingOwl\Admin\Display\Extension\Apply`

Вы можете изменять запрос по желанию

```php
$display->setApply(function ($query) {
    $query->orderBy('title', 'asc');
});

// or 

$display->setApply([
  ...
  function ($query) {
      $query->orderBy('title', 'asc');
  },
  ...
]);

// or

$display->getApply()->push(function ($query) {
    $query->orderBy('title', 'asc');
});
```

### Применение scope (Расширение)
`SleepingOwl\Admin\Display\Extension\Scopes`

Вы можете применить [Eloquent scopes](https://laravel.com/docs/5.2/eloquent#query-scopes) к выводимым данным:

```php
$display->setScopes('last');

//or

$display->setScopes(['last', 'trashed']);

// or

$display->getScopes()->push('last');
```

### Действия над документами Actions (Расширение)
`SleepingOwl\Admin\Display\Extension\Actions`

Использует тип колонки таблицы [Action](columns.md#action)

```php
$table = AdminDisplay::table()
    ->setActions([
        AdminColumn::action('export', 'Export')->setIcon('fa fa-share')->setAction(route('news.export')),
    ])
    ->setColumns([
        AdminColumn::checkbox(),
        ...
    ]);

// Изменить разсположение положения кнопок на странице
$table->getActions()
    ->setPlacement('panel.buttons')
    ->setHtmlAttribute('class', 'pull-right');
```

# Api

В классах таблиц используется трейт [HtmlAttributes](html_attributes), с помощью которого для таблиц можно настраивать HTML атрибуты.

## Методы доступные во всех типах

#### extend
Добавление нового расширения к виду

    SleepingOwl\Admin\Display\Display::extend(string $name, \SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface $extension): return $extension

#### getExtensions
Получение списка всех расширений

    SleepingOwl\Admin\Display\Display::getExtensions() return \Illuminate\Support\Collection|array<mixed,\SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface>
    
#### with
[Eager loading](https://laravel.com/docs/5.2/eloquent-relationships#eager-loading)

    SleepingOwl\Admin\Display\Display::with(array|array<mixed,string> $relations): return self
    
#### setTitle
Указание заголовка для таблиц

    SleepingOwl\Admin\Display\Display::setTitle(string $title): return self
    
#### setView
Изменения шаблона вывода данных

    SleepingOwl\Admin\Display\Display::setView(string|\Illuminate\View\View $view): return self
    
```php
$table->setView(view('display.custom.table.view'));

// Будет произведен поиск по пути sleeping_owl::default.display.custom.table.view
$table->setView('display.custom.table.view');
```

#### getActions
Получение объекта расширения

    SleepingOwl\Admin\Display\Display::getActions(): return SleepingOwl\Admin\Display\Extension\Actions

```php
$actions = $table->getActions();

$actions->setHtmlAttribite('class', 'custm-class');

$actions->push(AdminColumn::action());

// Установка места вывода кнопок
$actions->setPlacement(...);
```

#### setActions

    SleepingOwl\Admin\Display\Display::setActions(array|...SleepingOwl\Admin\Contracts\ActionInterface): return self


#### getApply
Получение объекта расширения

    SleepingOwl\Admin\Display\Display::getApply(): return SleepingOwl\Admin\Display\Extension\Apply
    
#### setApply

    SleepingOwl\Admin\Display\Display::setApply(array|...\Closure): return self
    

#### getScopes
Получение объекта расширения

    SleepingOwl\Admin\Display\Display::getScopes(): return SleepingOwl\Admin\Display\Extension\Scopes
    
#### setScopes

    SleepingOwl\Admin\Display\Display::setScopes(array|...(string|array)): return self

#### getFilters
Получение объекта расширения

    SleepingOwl\Admin\Display\Display::getFilters(): return SleepingOwl\Admin\Display\Extension\Filters
    
#### setFilters

    SleepingOwl\Admin\Display\Display::setFilters(array|...SleepingOwl\Admin\Contracts\FilterInterface): return self
    

## table()
`SleepingOwl\Admin\Display\DisplayTable`

#### setParameters
Передача в URL кнопки создания новой записи дополнительных параметров

    SleepingOwl\Admin\Display\DisplayTable::setParameters(array $parameters): return self
    
#### setParameter
Передача в URL кнопки создания новой записи дополнительного параметра

    SleepingOwl\Admin\Display\DisplayTable::setParameter(string $key, mixed $value): return self
    
#### paginate
Вывод данных с постраничной навигацией

    SleepingOwl\Admin\Display\DisplayTable::paginate(integer $perPage, string $pageName): return self
    
```php
$table->paginate(20, 'custom_page');
```
    
#### disablePagination
Отключение постраничной навигации

    SleepingOwl\Admin\Display\DisplayTable::disablePagination(): return self

#### usePagination
Проверка на использование постраничной навигации

    SleepingOwl\Admin\Display\DisplayTable::usePagination(): return boolean
    
#### getColumns
Получение объекта расширения

    SleepingOwl\Admin\Display\DisplayTable::getColumns(): return SleepingOwl\Admin\Display\Extension\Columns

```php
$columns = $table->getColumns();

$columns->push(AdminColumn::text());

$columns->setControlColumn(new \App\Display\Columns\CustomControlColumn());

$columns->setView(...);
```
    
#### setColumns
Указание списка столбцов для выводимой таблицы. В качестве аргумента можно передать как массив колонок так и объект `SleepingOwl\Admin\Contracts\ColumnInterface`

    SleepingOwl\Admin\Display\DisplayTable::setColumns(array|...SleepingOwl\Admin\Contracts\ColumnInterface): return self
    
```php
$table->setColumns(AdminColumn::text(...), AdminColumn::datetime(..));


$table->setColumns([
    AdminColumn::text(), 
    AdminColumn::datetime(..)
]);
```
    
#### getColumnFilters
Получение объекта расширения

    SleepingOwl\Admin\Display\DisplayTable::getColumnFilters(): return SleepingOwl\Admin\Display\Extension\ColumnFilters
    
```php
$filters = $table->geColumnFilters();

$filters->push(AdminColumnFilter::text());

$filters->setPlacement(...);
```

#### setColumnFilters

    SleepingOwl\Admin\Display\DisplayTable::setColumnFilters(array|...SleepingOwl\Admin\Contracts\ColumnFilterInterface): return self
    
## datatables()
`SleepingOwl\Admin\Display\DisplayDatatables`

#### setDatatableAttributes
Указание параметров для таблицы

    SleepingOwl\Admin\Display\DisplayDatatables::setDatatableAttributes(array $datatableAttributes): return self
    
#### setOrder
Указание правила сортировки данных. https://datatables.net/examples/basic_init/table_sorting.html

    SleepingOwl\Admin\Display\DisplayDatatables::setOrder(array $order): return self
    
```php
$display->setOrder([[1, 'asc']]);
```

## datatablesAsync()
`SleepingOwl\Admin\Display\DisplayDatatablesAsync`


#### setName

    SleepingOwl\Admin\Display\DisplayDatatablesAsync::setName(string $name): return self
    
#### setDistinct

    SleepingOwl\Admin\Display\DisplayDatatablesAsync::setDistinct(boolean $distinct): return self

## tree()

#### setValue
Указание заголовка для документов

    SleepingOwl\Admin\Display\DisplayTree::setValue(string $value): return self
    
#### setParentField
Указание ключа поля

    SleepingOwl\Admin\Display\DisplayTree::setParentField(string $parentField): return self
    
#### setOrderField
Указание ключа поля

    SleepingOwl\Admin\Display\DisplayTree::setOrderField(string $orderField): return self

#### setRootParentId

    SleepingOwl\Admin\Display\DisplayTree::setRootParentId(null|string $rootParentId): return self
    
#### setParameters
Передача в URL кнопки создания новой записи дополнительных параметров

    SleepingOwl\Admin\Display\DisplayTree::setParameters(array $parameters): return self
    
#### setParameter
Передача в URL кнопки создания новой записи дополнительного параметра

    SleepingOwl\Admin\Display\DisplayTree::setParameter(string $key, mixed $value): return self
    
#### setReorderable

    SleepingOwl\Admin\Display\DisplayTree::setReorderable(boolean $reorderable): return self
    
#### getTree
Получение объекта расширения

    SleepingOwl\Admin\Display\DisplayTree::getTree(): return SleepingOwl\Admin\Display\Extension\Tree
    
# Расширение таблиц
Класс `SleepingOwl\Admin\Display\Display` от которого наследуются все классы реализующие вывод данных позволяет расширять свое поведение за счет расширений. Расширения могут как влиять на вывод данных, модифицируя запрос перед получением списка записей из БД либо вывод HTML кода в шаблон.

Сейчас класс для вывода таблицы работает полностью за счет расширений, а именно, вызывая метод `setColumns` или `getColumns`, `setColumnFilters` или `getColumnFilters` вы обращаетесь к классам расширений.

#### Как использовать:
Использовать расширения можно как при создании нового типа `Display`, так и с существующими типами.

**Для работы расширений необходимо свой класс наследовать от `SleepingOwl\Admin\Display\Display`. Класс расширения должен реализовывать интерфейс `SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface`**

**Пример нового класса:**

```php
// Новый класс
namespace App\Display;

class CustomDisplay extends \SleepingOwl\Admin\Display\Display 
{
    /**
     * Display constructor.
     */
    public function __construct()
    {
        parent::__construct();

        $this->extend('custom_extension', new \App\Display\Extension\CustomExtension());
    }
}
```

**Пример добавления расширения для существующего типа:**

```php
$display = AdminDisplay::table();
$display->extend('custom_extension', new \App\Display\Extension\CustomExtension());
```

После инициализации расширения работа с ним осуществлаяется следующим образом:

```php
// Получение объекта
$table->getCustomExtension()->...

// В случае если в классе расширения определен метод set(), возможна передача в него данных
$table->setCustomExtension(...)
```

Расширения делятся на два типа:
 - Модификация запроса 
 - Вывод HTML кода в шаблон Display
    - При реализации интерфейса `Illuminate\Contracts\Support\Renderable` вывод будет выполнен в общем стеке расширений
    - При реализации интерфейса `SleepingOwl\Admin\Contracts\Display\Placable` вывод будет выполнен в месте указанном в методе `getPlacement`

### Модификация запроса
По умолчанию любое расширение может модифицировать запрос, который выполняет класс Display перед выводом данных. В момент выполнения запроса происходит вызов метода `modifyQuery` во всех расширениях и передача в него объекта `\Illuminate\Database\Eloquent\Builder`.

**Пример**
```php
public function modifyQuery(\Illuminate\Database\Eloquent\Builder $query)
{
    $query->orderBy('column', 'desc');
}
```

### Вывод HTML

Если расширение реализует интерфейс `Illuminate\Contracts\Support\Renderable`, то будет произведен вывод расширения в общем стеке расширений, т.е. для всех расширений будет последовательно вызван метод `render` https://github.com/LaravelRUS/SleepingOwlAdmin/blob/development/resources/views/default/display/table.blade.php#L28
Перед выводом происходит сортировка расширений по значению, взятому из метода `getOrder`

Если расширение реализует интерфейс `SleepingOwl\Admin\Contracts\Display\Placable`, то HTML код расширения будет помещен в определенном месте, указанном в методе `getPlacement`

Места, где можно разместить код реализованы через `@yield`
https://github.com/LaravelRUS/SleepingOwlAdmin/blob/development/resources/views/default/display/table.blade.php
