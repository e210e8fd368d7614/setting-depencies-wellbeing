# Settings and dependencies

**for operating systems used in the project**

See readme for your operation system

# Настройки и зависимости

**для операционных систем использующихся в проекте**

Readme для каждой операционки свой

Общее - здесь

# Настройки Операционной системы

1. Сервер нужно выбирать на хостинге, способно обеспечить защиту от DDoS
2. Обязательно установить и настроить фаервол
3. Пользоваться только SSH клиентом, настроив порты доступа, отличающиеся от стандартных
4. Для каждого сервиса имеет смысл заводить отдельного пользователя, хотя это на свое усмотрение.

**Регистрируем пользователя**

:~$ `sudo useradd _логин_пользователя_`

**Добавляем права созданному пользователю**

:~$ `sudo usermod -aG sudo _логин_пользователя_`

# Подготовка к установке ноды WellBeing

**Готовим свой репозиторий**

В любом из удобных проектов, предоставляющих сервис хранения исходных кодов создаем свой репозиторий, куда клонируем исходные файлы нода криптосети WBN.

Я это делаю следующим образом, по причинам, которые объясню в процессе.

1. Устанавливаю на своем компьютере программу Github Desktop, и с её помощью создаю локальную копию файлов будущего репозитория гитхаб.

Для этого, я скачиваю архив исходников нужной мне ноды, распаковываю его на локальный диск, в каталог, которому даю внятное название, позволяющее точно понимать о каком именно софте и на каком хостинге идет речь.
Делаю это так и потому, что на данном этапе требуется внести некоторые правки в исходный код некоторых файлы, которые определяют имя ноды, имя кошелька, обслуживающего эту ноду, и его описание.
Так же, весьма вероятно, потребуется изменить/обновить список нод, с которыми ваши ноды будут точно должны устанавливать соединения. (Это на тот случай, если у вас появится желание владеть несколькими разными нодами для получения бонуса от проекта Хайп "Благополучие".)

Скачанный, распакованный, каталогизированный нужным образом набор файлов из архива моего репозитория добавляю в Github Desktop через меню `File` -> `New local repository`, заполнив поля появившегося диалогового окна Github Desktop. `Local path` -> `Choose...` - выбираю каталог с распакованными файлами, в поле `Name` вписываю имя, которое буду видеть в списке своих репозиториев, подключенных к программе Github Desktop, в поле `Description` вписываю краткое описание того, что требуется видеть сразу в интерфейсе гитхаба при поиске в каталоге своего хранилища репозиториев.
Далее - кнопочка `Create repository` и после окончания процесса инициализации нового репозитория жму на появившуюся кнопочку `Push origin`

Все, ваш репозиторий размещен на гитхабе и его, после правок, можно клонировать в нужное место.
Правки можно делать в любом текстовом редакторе, но больше всего для этих целей подходит безплатный редактор **Atom**

Далее: на хостинге, логинимся под именем пользователя, созданного ранее и выполняем клонирование своего репозитория с гитхаба

# Установка nodejs и npm

**Using Debian, as root**

`sudo mc`

`curl -sL https://deb.nodesource.com/setup_12.x | bash -`

`apt-get install -y nodejs`
