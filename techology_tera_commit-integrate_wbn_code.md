Краткая суть: бранчуемся, дорабатываем, по мере выхода новых релизов оригинала делаем fetch, мержим или переносим отдельные коммиты.

Алгоритм работы в вашем случае такой (пример на реальном репо).

1. Клонируем репозиторий
git clone https://github.com/dbfun/sql-samples
Смотрим, какие репозитории есть (так как только что склонировали, у нас там будет только один)

git remote -v

# вот что увидим:
origin  https://github.com/dbfun/sql-samples (fetch)
origin  https://github.com/dbfun/sql-samples (push)
Мы можем оставить "оригинал" как origin. Или переименовать в удобное для нас имя, baseOrigin:

git remote rename origin baseOrigin
Смотрим, что получилось

git remote -v

# вот что увидим:
baseOrigin  https://github.com/dbfun/sql-samples (fetch)
baseOrigin  https://github.com/dbfun/sql-samples (push)
Я бы оставил как origin, но это дело удобства. Это будет именно тот репо, в который сторонние разработчики будут что-то добавлять, а мы - получать и "подливать" себе.

Мы можем хранить свой кастомный репозиторий прямо здесь, на диске (каталог .git), куда был git clone, а можем добавить еще один удаленный, например, для "бекапа".

# Создаем пустой удаленный репозиторий. В этом примере - на диске:
git init --bare /home/totalpusher/gits/sql-samples-backup.git
# добавляем этот репо под именем backup
git remote add backup /home/totalpusher/gits/sql-samples-backup.git
# пушим ветку master в репо backup
git push backup master
2. Создаем ветку со своим функционалом
Этот шаг не обязателен, можно продолжать работать в master. Он становится нужным, когда идет параллельная разработка, тогда каждую "фишку" выносят в отдельную ветку, тестируют, и в дальнейшем сливают с master, а ветка master является "финишной", чистовой работой, которая публикуется на сервера приложений.

# переходим в каталог
cd sql-samples/
# бранчуемся
git checkout -b my-new-feature
Далее вносим любые свои изменения, коммитим. Теперь код работает в соответствии с нашими хотелками.

3. Обновляем ветку baseOrigin/master
Допустим, разработчики что-то сделали, и в итоге baseOrigin/master обновился. Мы все еще в ветке my-new-feature. Вот что мы делаем:

# обновляем ветку master из удаленного репо:
git fetch baseOrigin/master
# также можно использовать git fetch baseOrigin - будут получены все ветки
# или git fetch --all - будет обновлены из всех репо все ветки
# так можем посмотреть, что было сделано разработчиками "оригинала":
git log baseOrigin/master
Для краткости я не буду расписывать, как посмотреть, какие файлы, в каких местах менялись. Просто используйте привычные команды и указывайте baseOrigin/master.

Так как мы все еще в ветке my-new-feature, в рабочем каталоге видим свои изменения. А только что подтянутые изменения из baseOrigin "находятся" в каталоге .git в формате гита.

4. Подливаем изменения, внесенные разработчиками
Теперь нам нужно "подлить" из baseOrigin/master в нашу ветку my-new-feature.

Это можно делать двумя способами:

Первый способ: git merge
git merge baseOrigin/master --no-commit
Я специально указал ключ --no-commit, чтобы не было автоматического слияния. Сделайте git status и увидите те изменения, которые были применены. Их можно подправить с помощью разных команд гита типа git reset HEAD ..., git checkout ....

Подробнее можно прочитать в мане: man git-reset, man git-checkout или найти в интернете.

Если будут конфликты, git их пометит. В конце, после исправлений, нужно все добавить и закоммитить (git add ..., git commit ...).

Будет создан коммит слияния, в который войдут:

исправления внесенных разработчиками конфликтов с вашим подправленным кодом
ваши дополнительные доработки, которые вы решите внести в "подлитый" код
Второй способ: git cherry-pick
Это - способ переноса отдельных коммитов в свою ветку. Допустим, разработчики сделали три коммита, вы ознакомились с ними, но реально вам нужен только один. В этом случае git cherry-pick - ваш выбор.

Смотрим хеши коммитов:

git log baseOrigin/master
Допустим, выбрали коммит с хешом 0127e07aaea9b97e8ced11759b8301ff7f581df3. Напомню, мы все еще находимся в ветке my-new-feature.

git cherry-pick 0127e07aaea9b97e8ced11759b8301ff7f581df3 --no-commit
Я снова использовал ключ --no-commit, чтобы не мержить автоматически. Кстати, можно использовать сокращение хеша, гит "угадывает" по первым нескольким символам.

Теперь нам нужно проделать ту же работу, что при git merge, см выше. В конце нужно закоммитить этот слияние.

Подробности: man git-cherry-pick

5. Смотрим историю объединений
Для удобного просмотра использовать эту команду:

git log --oneline --decorate --graph
Пока я писал и проверял, у меня вышло вот что:

* 3fd5f1e (HEAD -> my-new-feature) bar
*   497085b Merge remote-tracking branch 'baseOrigin/master' into my-new-feature
|\  
| * 54ce1e7 test
* | 95445da mytest
* |   7e9e053 Merge remote-tracking branch 'baseOrigin/master' into my-new-feature
|\ \  
| |/  
| * 5b401f9 10
| * 325f8db 9
| * 16b3319 8
* |   6efe6a9 Merge remote-tracking branch 'baseOrigin/master' into my-new-feature
|\ \  
| |/  
| * e84e620 test
* |   1ee25b3 Merge remote-tracking branch 'baseOrigin/master' into my-new-feature
|\ \  
| |/  
| * 3522640 done 6
| * a70b63e done 5
| * 3ba1d1f done
* |   1444b6a Merge branch 'master' of /tmp/sql-origin/sql-samples into my-new-feature
|\ \  
| |/  
| * 247e562 done
* |   6bd3322 Merge branch 'master' of /tmp/sql-origin/sql-samples into my-new-feature
|\ \  
| |/  
| * 126c262 done
* | 37192dc done
|/  
* 08a6471 (backup/master, master) Создание уникального ID сертификата в формате год-месяц-номер
* 1d3a4c4 перевод в markdown
