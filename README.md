# devops-netology
Фкирдымов Тимур комментарий к заданию 3.1 Terminal

5) Какие ресурсы выделены по-умолчанию?
2 ядра, 1 гб ОЗУ, 4 мб VRAM

6) Как добавить оперативной памяти или ресурсов процессора виртуальной машине?
Изменить файл vagrantfile, добавив строки:
config.vm.provider "virtualbox" do |v|  
v.memory = 2048  #память  
v.cpus = 2 #ядра
end
end

8)  какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
Использую –N для отображения номеров строк и / для поиска. Строка 846, переменная HISTFILESIZE
     что делает директива ignoreboth в bash?
Ignoreboth – сокращение для ignorespace и ignoredups. Следовательно не сохраняется команда, которая начинается с пробела, либо которая дублирует предыдущую (строка 837 в man)

9) В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?
{} - зарезервированные слова, используется для генерации последовательности строк (списков, файлов) из терминала или с помощью любого сценария. Для минимизации ввода, например, нужно создать 100 файлов, папок и т.д., в варианте – 1,2,3,4…
Описание 174 и 257 строки.

10) С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?
touch {1..100000} или touch имя_файла{1..100000} 
300000 – не получится, argument list too long слишком большой список

11) В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]?
[[ -d /tmp ]]  - проверяет условие внутри и возвращает истину или ложь, есть ли каталог tmp.

12) Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:
$ type –a bash 
bash is /usr/bin/bash
bash is /bin/bash
$ mkdir /tmp/new_path_dir/
$ cd /bin
$ cp bash /tmp/new_path_dir/
$ PATH=/tmp/new_path_dir/:$PATH
$ type -a bash
bash is /tmp/new_path_dir/bash
bash is /usr/bin/bash
bash is /bin/bash

13) Чем отличается планирование команд с помощью batch и at?
at – планирует выполнение команд в определенное время.
batch – планирует выполнение команды, если позволяет средний уровень загрузки системы, если загрузка системы выше, то команда будет ждать снижение нагрузки.




