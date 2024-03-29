### Проект api_yamdb

Проект YaMDb собирает отзывы (Review) пользователей на произведения (Title). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий (Category) может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).

### Описание проекта

Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.

В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории «Книги» могут быть произведения «Винни-Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Насекомые» и вторая сюита Баха.

Произведению может быть присвоен жанр (Genre) из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только администратор.

Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

### Авторы проекта

- Мамренко Александр (тимлид, разработка ресурсов Auth и Users)
- Михаськин Юрий (разработка ресурсов Categories, Genres и Titles)
- Одринская Мария (разработка ресурсов Review и Comments)

### Запуск проекта в dev-режиме

- Клонируйте репозиторий и перейдити в него в командной строке.
- Установите и активируйте виртуальное окружение:

$ git clone git@github.com:earlinn/infra_sp2.git
$ cd infra_sp2
$ python -m venv venv 
$ venv/Scripts/activate
$ python -m pip install --upgrade pip

Затем нужно перейти в папку infra_sp2/infra и создать в ней файл .env с 
переменными окружения, необходимыми для работы приложения.

$ cd infra/

Содержимое файла:

```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
SECRET_KEY=key
```

Далее следует запустить docker-compose: 

$ docker-compose up -d <name>

Будут созданы и запущены в фоновом режиме необходимые для работы приложения контейнеры (db, web, nginx).

Затем нужно внутри контейнера web выполнить миграции, создать 
суперпользователя и собрать статику:

$ docker-compose exec web python manage.py migrate
$ docker-compose exec web python manage.py createsuperuser
$ docker-compose exec web python manage.py collectstatic --no-input

После этого проект будет быть доступен по адресу http://localhost/

### Остановка контейнеров

Для остановки работы приложения можно набрать в терминале команду Ctrl+C 
либо открыть второй терминал и воспользоваться командой

$ docker-compose stop 

Также можно запустить контейнеры без их создания заново командой

$ docker-compose start 

### Техническое  Описание проекта

К проекту по адресу http://127.0.0.1/redoc/ подключена документация API YaMDb.

#### Пользовательские роли

  - Аноним — может просматривать описания произведений, читать отзывы и комментарии.
  - Аутентифицированный пользователь (user) — может читать всё, как и Аноним, может публиковать отзывы и ставить оценки произведениям (фильмам/книгам/песенкам), может комментировать отзывы; может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. Эта роль присваивается по умолчанию каждому новому пользователю.
  - Модератор (moderator) — те же права, что и у Аутентифицированного пользователя, плюс право удалять и редактировать любые отзывы и комментарии.
  - Администратор (admin) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.
  - Суперюзер Django — обладает правами администратора (admin).

### Примеры работы с API для всех пользователей
```
Права доступа: Без токена.
GET /api/v1/categories/ - Список всех категорий
GET /api/v1/genres/ - Список всех жанров
GET /api/v1/titles/ - Список всех произведений
GET /api/v1/titles/{title_id}/reviews/ - Список всех отзывов
GET /api/v1/titles/{title_id}/reviews/{review_id}/comments/ - Список всех комментариев к отзыву
