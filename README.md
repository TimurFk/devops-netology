# devops-netology
Фкирдымов Тимур комментарий к заданию 2.4 

Здравствуйте, домашнее задание на проверку.

1)Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.  
Выполнил - git show aefea
Полный хеш коммита - aefead2207ef7e2aa5dc81a34aedf0cad4c32545
Комментарий коммита -  Update CHANGELOG.md

2)Какому тегу соответствует коммит 85024d3?
Выполнил – git show 85023d3
Коммит соответствует -  tag: v0.12.23

3)Сколько родителей у коммита b8d720? Напишите их хеши.
Можно сразу выполнить git show ^1(2) Но для наглядности выполняю команду git log b8d720 --pretty=format:'%h %s' –graph и вижу два родителя и их краткие хеши.
Выполняю git show b8d720^1 , чтобы узнать полный хеш первого родителя - 56cd7859e05c36c06b56d013b55a252d0bb7e158
Выполняю git show b8d720^2 , чтобы узнать полный хеш второго родителя – 9ea88f22fc6269854151c571162c5bcf958bee2b

4)Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
Выполняю команду git log --pretty=format:"%H %s" v0.12.23..v0.12.24
Получаю: 33ff1c03b (tag: v0.12.24) v0.12.24
b14b74c49 [Website] vmc provider links
3f235065b Update CHANGELOG.md
6ae64e247 registry: Fix panic when server is unreachable
5c619ca1b website: Remove links to the getting started guide's old location
06275647e Update CHANGELOG.md
d5f9411f5 command: Fix bug when using terraform login on Windows
4b6d06cc5 Update CHANGELOG.md
dd01a3507 Update CHANGELOG.md
225466bc3 Cleanup after v0.12.23 release
Если нужен полный хеш, то можно выполнить git log --pretty=format:"%H %s" v0.12.23..v0.12.24 

5)Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).
Использую команду  git log --pretty=oneline -S "func providerSource(" 
Коммит - 8c928e83589d90a031f811fae52a81be7153e82f main: Consult local directories as potential mirrors of providers

6)Найдите все коммиты в которых была изменена функция globalPluginDirs.
Использую команду   git log --pretty=oneline -S "globalPluginDirs"
35a058fb3ddfae9cfee0b3893822c9a95b920f4c main: configure credentials from the CLI config file
c0b17610965450a89598da491ce9b6b5cbd6393f prevent log output during init
8364383c359a6b738a436d1b7745ccdce178df47 Push plugin discovery down into command package

7)Кто автор функции synchronizedWriters?
Выполняю git log -S 'func synchronizedWriters' , вижу 2 коммита – выбираю более ранний.
commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5
Author: Martin Atkins <mart@degeneration.co.uk>
Date:   Wed May 3 16:25:41 2017 -0700



