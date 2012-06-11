---
layout: post
title: "Pry - лучший друг Rubista"
date: 2012-06-10 20:46
comments: true
categories: [ruby, rails, pry, debug]
---

Если вы читаете эту статью, то вы вероятно разработчик. Разработчик на Ruby, и возможно вас привлек заголовок статьи.

Как часто вы задумываетесь над инструментами, с которыми работаете? Как часто думаете, что вам чего-то не хватает, или вас что-то не устраивает? Если не часто - то вы вероятно очень счастливый человек, так как я нахожусь в постоянном поиске идеалов для решения своих задач, которыми я мог бы пользоваться ежедневно, получать удовольствие и не задумываться над тем, что я работаю с ПО, а не выполняю свою работу.

Иногда случается, что для тех или иных задач я нахожу все таки идеал. И хотелось бы рассказать об очень любопытном проекте под названием Pry, который я ежедневно использую. Это интерактивная консоль для Ruby-разработчиков с огромными возможностями. С тех пор как меня с ним познакомили (спасибо за это Алексею Магусеву) я до сих пор нахожусь в вау-эффекте, причем в очень положительном. Ниже я проведу краткий экскурс по этому замечательному инструменту, и очень надеюсь, что вы разделите мое мнение относительно него.

<!-- more -->

## Что за зверь такой этот ваш pry?

Сейчас я сижу и думаю, как бы правильно охарактеризовать pry, потому что этот инструмент настолько
многограннен, что я затрудняюсь это сделать.

Сами разработчики pry позиционируют свое детище, как альтернативную интерактивную консоль для разработчиков на Ruby, которая призвана заменить IRB. Поставленную цель они выполнили более чем на отлично. Но мое затруднение вызвано тем, что в pry настолько огромное количество самых разных
возможностей, что я не могу относится к нему просто как к интерактивной консоли. Пройдем по порядку,
что умеет pry из коробки, помимо того, чтобы быть просто консолью:

* просмотр исходного кода (в том числе и на C, но уже включением плагина `pry-doc`);
* навигация по приложению в текущем состоянии;
* просмотр исходного кода в Rubinius;
* просмотр документации;
* "живая" система помощи;
* редактирование кода на лету;
* подсветка синтаксиса;
* интеграция с командной оболочкой;
* интеграция с Gist;
* возможность работы в runtime окружении;
* история команд.

Pry создавался под влиянием таких инструментов как IPython, Smalltalk, SLIME и в сравнении с IRB выглядит как zsh в сравнении с sh. Разница огромна, а возможности поражают. Честно признаюсь, я хоть и активно пользуюсь pry, но до сих пор не постиг и половины его возможностей, ибо их очень много.

Но тем не менее, хотелось бы не просто пиарить проект копируя примеры из официальной документации, но и рассказать о том, как я ими пользуюсь, так как красивая документация - это ничто по сравнению с опытом использования. Однако, я разделю свое повествования на структуру близкую к официальной документации, чтобы можно было сразу перейти к проекту и посмотреть более расширено то, что там действительно есть.

## Работа с пользовательским вводом

У вас часто были ситуации, когда вам приходилось писать какие-то функции или большие одноразовые куски кода? К примеру добавить запись в базу данных с большим количеством полей или что-то подобное? В код не вынести, но нужно что-то в консоли проверить.

Иногда у меня встречаются такие ситуации, и... Бывают моменты, когда очень легко опечататься. Это очень неприятно, так как приходится набирать тот же самый код с нуля, что тратит драгоценное время и тратит еще более драгоценные нервы, так как я не люблю много раз писать одно и тоже.

Видимо мои взгляды такие же, как у разработчиков pry, потому что они очень хорошо позаботились о том, чтобы максимально удобно и просто решить данную проблему. В pry очень удобные инструменты для управления пользовательским вводом.

К примеру набирая код, вдруг думаешь, что нет. Он не нужен. И просто вводом `!` можно этот ввод очистить. Для наглядности, я все же возьму пример из официальной документации, так как особо вспоминать пример для этого у меня нет желания. Слишком я леним.

{% codeblock Быстрая очистка набираемого текста lang:ruby %}
pry(main)> def hello
pry(main)*   puts "hello"
pry(main)* !
Input buffer cleared!
pry(main)>
{% endcodeblock %}

На этом месте, конечно многие скажут, что отменить ввод можно и в IRB, однако смысл этой команды более глубок, если более детально ознакомиться с функционалом pry, и тогда все встанет на свои места. Я имею ввиду работу с историей и проигрыванием кода.

Все что вы вводите в консоль pry записывается в историю, и это можно воспроизвести. Причем, когда я говорю воспроизвести, я не имею ввиду стандартную навигацию по истории подобно IRB, а именно полноценно воспроизвести определенные куски кода, которые вводились за время сессии. Например:

{% codeblock История ввода и повторное воспроизведение кода lang:ruby %}
[1] pry(main)> puts "This is first line"
This is first line
=> nil
[2] pry(main)> puts "This is second line"
This is second line
=> nil
[3] pry(main)> hist --tail 2
1: puts "This is first line"
2: puts "This is second line"
[4] pry(main)> hist --replay 1..2
This is first line
=> nil
This is second line
=> nil
{% endcodeblock %}

На самом деле управление историей в pry гораздо более обширно по функционалу и выше я показал лишь очень маленький пример. Для более детального ознакомления прошу [сюда](https://github.com/pry/pry/wiki/History).

Иногда удобно отключить вывод результата выполнения строки кода. Например, мы получили из базы двадцать записей, и итератором each проделали что-то. В общем, отключить вывод можно добавив в конце `;`:

{% codeblock Отключение вывода результата выполнения lang:ruby %}
[1] pry(main)> puts "This is first line";
[2] pry(main)>
{% endcodeblock %}

Как я уже говорил, иногда бывают ситуации, когда мы опечатываемся в наборе кода, и его приходится набирать полностью с нуля (например код какого-то временно используемого метода). Но этого можно избежать, к примеру используя команду `amend-line`.

{% codeblock Исправление ошибок в набираемом блоке кода lang:ruby %}
[14] pry(main)> def hello
[14] pry(main)*   puts "Hello, #{name}!"
[14] pry(main)*   amend-line 1 def hello(name)
1: def hello(name)
2:   puts "Hello, #{name}!"
[14] pry(main)*   end
=> nil
[15] pry(main)> show-method hello

From: (pry) @ line 10:
Number of lines: 3
Owner: Object
Visibility: private

def hello(name)
  puts "Hello, #{name}!"
  end
[16] pry(main)>
{% endcodeblock %}

Эта команда позволяет заменить не одну строку, но даже их последовательность или вовсе удалить их, если передать `!` вместо исправленной строки.

Если набрать кода нужно очень много, то безусловно лучше воспользоваться услугами редактора кода, нежели встроенные(пусть и чертовски продвинутые) средства pry для редактирования кода. И это черт побери возможно!

{% codeblock Редактирование кода в текстовом редакторе lang:ruby %}
[17] pry(main)> def goodbye(name, surname)
[17] pry(main)*   full_name = "#{name} #{surname}"
[17] pry(main)*   edit
=> nil
[18] pry(main)> show-method goodbye

From: (pry) @ line 14:
Number of lines: 4
Owner: Object
Visibility: private

def goodbye(name, surname)
  full_name = "#{name} #{surname}"
  puts "Goodbye, #{full_name}"
end
[19] pry(main)>
{% endcodeblock %}

Возможности редактирования на самом деле еще больше, например можно открыть на редактирование файл с исходным кодом для редактирования к примеру конкретного метода. В общем, читаем [здесь](https://github.com/pry/pry/wiki/Editor-Integration).

Ненадолго хочется вернуться к "проигрыванию" кода. Я выше показывал, как можно проигрывать код из истории пользовательского ввода. Но pry позволяет "проигрывать" отдельные файлы и методы, и даже отдельные строки методов прямо в поток ввода pry, будто этот код был введен вами. Эдакое подобие макроса получается.

{% codeblock Проигрывание кода методов для формирования нового lang:ruby %}
[19] pry(main)> def hello(name)
[19] pry(main)*   puts "Hello, #{name}!"
[19] pry(main)* end
=> nil
[20] pry(main)> def goodbye(name)
[20] pry(main)*   puts "Goodbye, #{name}!"
[20] pry(main)* end
=> nil
[21] pry(main)> def hello_and_goodbye(name)
[21] pry(main)*   play -m hello --lines 2
1: def hello_and_goodbye(name)
2:   puts "Hello, #{name}!"
[21] pry(main)*   play -m goodbye --lines 2
1: def hello_and_goodbye(name)
2:   puts "Hello, #{name}!"
3:   puts "Goodbye, #{name}!"
[21] pry(main)*   show-input
1: def hello_and_goodbye(name)
2:   puts "Hello, #{name}!"
3:   puts "Goodbye, #{name}!"
[21] pry(main)* end
=> nil
[22] pry(main)> show-method hello_and_goodbye

From: (pry) @ line 24:
Number of lines: 4
Owner: Object
Visibility: private

def hello_and_goodbye(name)
  puts "Hello, #{name}!"
  puts "Goodbye, #{name}!"
end
{% endcodeblock %}

Это такой краткий экскурс по быстрой работе с кодом прямо в консоли pry.

## Навигация по контексту исполнения

Программа на Ruby, похожа на операционную систему. Она состоит их отдельных модулей, которые взаимодействуют между собой, имеют свое внутреннее окружение. Представьте теперь, что эта программа еще и выглядит как файловая система, где каждый объект одновременно выступает в роли и директории, которая содержит другие файлы, и в качестве каталога, и внутри каждого каталога есть свой контекст исполнения, свое окружение. И теперь представьте, что вы можете перемещаться по работающему приложению куда вам вздумается и делать там что угодно, оперировать и изучать живой(!) код прямо в runtime(!).

Наверное программисты на компилируемых языках просто обзавидуются, да и многие другие программисты тоже. Но нам повезло. У нас есть pry, и он делает нашу фантазию реальностью. Такие возможности интересны не только в контексте изучения работы программ изнутри, но и очень помогают при отладке кода, когда в живом работаюшем коде можно посмотреть чтоже происходит, и при каких условиях возникает ошибка и откуда у нее растут ноги.

Ну да бог с ним. Перейдем от кода, ой от слов, к делу и посмотрим как это выглядит.

Предположим, мы создали Rails приложение, запустили консоль c pry. Давайте попробуем исследовать наше приложение изнутри так сказать и посмотреть, что там есть.

Исполняемое приложение, тем более такое сложное как Rails, да еще на динамическом языке типа Ruby - подобно огромной сети пещер, где темно, страшно и ничего не видно. Ну прям как в ж*пе у афроамериканца. А нас посадили где то в центре этой пещеры, и мы как то должны научиться ориентироваться в ней.

Для начала, нам бы понадобился фонарик. Нам нужно оглядеться вокруг, и посмотреть что вокруг собственно есть. Для этого у нас есть команда `ls`. Не удивляйтесь, так как я не спроста привел в начале этой части аналогию с файловой системой. Эта команда действительно позволяет практически тоже, что и ее аналог из Unix, за оговорками того, с чем мы работаем.

Эта команда позволит нам легко оглядеться, и увидеть что находится в непосредственной близости от нас, или до чего мы сможем дотянутся нашими загребущими рученками.

{% codeblock Смотрим на переменные класса ActiveRecord::Base lang:ruby %}
[27] pry(ActiveRecord::Base):1> cd ActiveRecord::Base
[28] pry(ActiveRecord::Base):2> ls -ivars
instance variables: @attribute_method_matchers_cache  @attribute_methods_generated  @generated_attribute_methods  @generated_external_attribute_methods  @observers
class variables: @@attribute_types_cached_by_default  @@configurations  @@default_timezone  @@lock_optimistically  @@logger  @@primary_key_prefix_type  @@schema_format  @@time_zone_aware_attributes  @@timestamped_migrations
{% endcodeblock %}

{% codeblock Ищем методы связанные с callback'ами lang:ruby %}
[4] pry(ActiveRecord::Base):2> ls -G callbacks
ActiveSupport::Callbacks::ClassMethods#methods: __update_callbacks  define_callbacks  reset_callbacks
ActiveModel::Callbacks#methods: define_model_callbacks
ActiveRecord::Base.methods: _commit_callbacks  _commit_callbacks=  _commit_callbacks?  _create_callbacks  _create_callbacks=  _create_callbacks?  _destroy_callbacks  _destroy_callbacks=  _destroy_callbacks?  _find_callbacks  _find_callbacks=  _find_callbacks?  _initialize_callbacks  _initialize_callbacks=  _initialize_callbacks?  _rollback_callbacks  _rollback_callbacks=  _rollback_callbacks?  _save_callbacks  _save_callbacks=  _save_callbacks?  _touch_callbacks  _touch_callbacks=  _touch_callbacks?  _update_callbacks  _update_callbacks=  _update_callbacks?  _validate_callbacks  _validate_callbacks=  _validate_callbacks?  _validation_callbacks  _validation_callbacks=  _validation_callbacks?
ActiveRecord::Base#methods: _commit_callbacks  _commit_callbacks=  _commit_callbacks?  _create_callbacks  _create_callbacks=  _create_callbacks?  _destroy_callbacks  _destroy_callbacks=  _destroy_callbacks?  _find_callbacks  _find_callbacks=  _find_callbacks?  _initialize_callbacks  _initialize_callbacks=  _initialize_callbacks?  _rollback_callbacks  _rollback_callbacks=  _rollback_callbacks?  _save_callbacks  _save_callbacks=  _save_callbacks?  _touch_callbacks  _touch_callbacks=  _touch_callbacks?  _update_callbacks  _update_callbacks=  _update_callbacks?  _validate_callbacks  _validate_callbacks=  _validate_callbacks?  _validation_callbacks  _validation_callbacks=  _validation_callbacks?
instance variables: @attribute_method_matchers_cache  @attribute_methods_generated  @generated_attribute_methods  @generated_external_attribute_methods  @observers
class variables: @@attribute_types_cached_by_default  @@configurations  @@default_timezone  @@lock_optimistically  @@logger  @@primary_key_prefix_type  @@schema_format  @@time_zone_aware_attributes  @@timestamped_migrations
{% endcodeblock %}

Как вы заметили, я использовал другое приспособление под названием `cd`. Это такое чудо техники, под названием телепорт, которое помогает мне перемещаться по темному и мрачному лабиринту работающего кода.
Он позволяет перейти в контекст всего, куда можно дотянуться. Например, любая переменная доступная из текущей области видимости.

{% codeblock Путешествие по Rails фреймворку lang:ruby %}
[21] pry(main)> cd ActiveRecord::Base
[22] pry(ActiveRecord::Base):1> cd ActionMailer::Base
[23] pry(ActionMailer::Base):2> cd ActiveSupport
[24] pry(ActiveSupport):3> cd ../
[25] pry(ActionMailer::Base):2> cd ../
[26] pry(ActiveRecord::Base):1> cd ../
[27] pry(main)>
{% endcodeblock %}

Путешествуя по дебрям приложения, мы оставляем за собой путеводную нить Ариадны, и следим за вложенностью контекстов, в которых работаем. А также, наш телепорт умеет перемещать нас используя эту самую нить, и мы можем прыгать через контексты.

{% codeblock Просмотр вложенных контекстов lang:ruby %}
[27] pry(main)> cd ActiveRecord::Base
[28] pry(ActiveRecord::Base):1> cd ActionMailer::Base
[29] pry(ActionMailer::Base):2> cd ActiveSupport
[30] pry(ActiveSupport):3> nesting
Nesting status:
--
0. main (Pry top level)
1. ActiveRecord::Base
2. ActionMailer::Base
3. ActiveSupport
[31] pry(ActiveSupport):3> jump-to 1
[32] pry(ActiveRecord::Base):1>
{% endcodeblock %}

И прошу не путать вложенные контексты, с областью видимости или наследованием. Это контекст в терминах pry, считайте, что-то вроде сессии. Я не вдавался глубоко в подробности, так что если найдется человек знающий, прошу просвятить меня в комментариях к статье.

Иногда встречаются ситуации, когда нам нужно переключиться между контекстами. К примеру, что-то быстро посмотреть, и вернуться обратно. Это тоже возможно. Здесь я не буду выдумывать свой пример, и возьму пример из официальной документации.

{% codeblock Переключение между контекстами lang:ruby %}
pry(Pry::CommandSet::Command):3> def test_method
pry(Pry::CommandSet::Command):3*   puts "this is a test"
pry(Pry::CommandSet::Command):3* nesting
Nesting status:
--
0. main (Pry top level)
1. Pry
2. Pry::CommandSet
3. Pry::CommandSet::Command
pry(Pry::CommandSet::Command):3* switch-to 2
pry(Pry::CommandSet)> ls -m
allocate  new  superclass  yaml_tag
pry(Pry::CommandSet)> exit
pry(Pry::CommandSet::Command):3* show-input
1: def test_method
2:   puts "this is a test"
pry(Pry::CommandSet::Command):3*
{% endcodeblock %}

Как видно из примера, `switch-to` очень похожа на `jump-to`. Однако есть два больших различия. Каждый из вас работал с командной строкой. Когда мы выполняем команду `cd` в pry - мы создаем новый контекст (представьте будто это сессия bash, или рабочий стол). Переходя все глубже и глубже вы создаете контексты. Возвращаясь наверх, нижележащие контексты закрываются.

В случае с использованием `switch-to`, все происходит немного не так. Когда вы вызываете эту команду, то это было бы равносильно тому, что вы работая в терминале, открыли еще один терминал, и полностью передали управление нужным контекстом этому терминалу. Так происходит и в pry. Он не закрывает нижележащий контекст, а создает новый объект `Pry`, и отдает под его управление нужный контекст.

Помимо навигации, нам порой нужна и поисковая система. К примеру, мы хотим найти метод с названием `serialized_attributes`.

{% codeblock Поиск метода с заданным именем lang:ruby %}
[1] pry(main)> class Apple < ActiveRecord::Base; end
[2] pry(main)> find-method serialized_attributes?

ActiveRecord::Base.serialized_attributes?
ActiveRecord::Base.serialized_attributes=
ActiveRecord::Base.serialized_attributes
ActiveRecord::Base
ActiveRecord::Base#serialized_attributes
ActiveRecord::Base#serialized_attributes?
ActiveRecord::Base#serialized_attributes=
{% endcodeblock %}

В случае Rails приложения - эта операция будет дорогой, так как она будет шерстить все, что есть в приложении. А это очень много кода. Зачастую же, мы уже примерно знаем, где нужно найти нужный метод.

{% codeblock Поиск метода в классе lang:ruby %}
[3] pry(main)> find-method serialized_attributes? ActiveRecord::Base

ActiveRecord::Base.serialized_attributes?
ActiveRecord::Base.serialized_attributes=
ActiveRecord::Base.serialized_attributes
ActiveRecord::Base
ActiveRecord::Base#serialized_attributes
ActiveRecord::Base#serialized_attributes?
ActiveRecord::Base#serialized_attributes=
{% endcodeblock %}

Можно также задать вопрос "Где используется искомый нами метод?".

{% codeblock Поиск использования метода по коду lang:ruby %}
[5] pry(Apple):1> find-method -c serialized_attributes?
ActiveRecord::AttributeMethods
ActiveRecord::AttributeMethods#arel_attributes_values:         value = if klass.serialized_attributes.include?(name)
ActiveRecord::AttributeMethods::Dirty
ActiveRecord::AttributeMethods::Dirty#update:     super(changed | (attributes.keys & self.class.serialized_attributes.keys))
ActiveRecord::AttributeMethods::Serialization
ActiveRecord::AttributeMethods::Serialization#type_cast_attribute_for_write:   if column && coder = self.class.serialized_attributes[column.name]
ActiveRecord::AttributeMethods::Serialization#read_attribute_before_type_cast:   if serialized_attributes.include?(attr_name)
ActiveRecord::AttributeMethods::Serialization::ClassMethods
ActiveRecord::AttributeMethods::Serialization::ClassMethods#serialize:   self.serialized_attributes = serialized_attributes.merge(attr_name.to_s => coder)
ActiveRecord::AttributeMethods::Serialization::ClassMethods#initialize_attributes:   serialized_attributes.each do |key, coder|
ActiveRecord::AttributeMethods::Serialization::ClassMethods#attribute_cast_code:   if serialized_attributes.include?(attr_name)
ActiveRecord::Timestamp
ActiveRecord::Timestamp#should_record_timestamps?:   self.record_timestamps && (!partial_updates? || changed? || (attributes.keys & self.class.serialized_attributes.keys).present?)
ActiveRecord::Validations::UniquenessValidator
ActiveRecord::Validations::UniquenessValidator#validate_each:   coder = record.class.serialized_attributes[attribute.to_s]
{% endcodeblock %}

Когда нам надоест лазать по нашей пещеры, мы можем уйти отсюда, или уйти прихватив с собой сувенир.

{% codeblock Выход из контекста lang:ruby %}
# Выход из контекста
1.9.3p194 :013 > pry
[1] pry(main)> exit
 => nil

# Выход из контекста с возвращением значения
1.9.3p194 :014 > pry
[1] pry(main)> exit 5
 => 5
1.9.3p194 :015 >

# Выход в вызывающий процесс (на примере с возвращаемым значением)
1.9.3p194 :015 > pry
[1] pry(main)> cd ActiveRecord::Base
[2] pry(ActiveRecord::Base):1> exit-all 'Return to the calling process'
 => "Return to the calling process"
1.9.3p194 :016 >

# Выход из программы
[4] pry(ActiveRecord::Base):1> !!!
demiazz@demiazz-laptop:~/repo/prye-example$
{% endcodeblock %}

Поздравляю, юный падаван. Вы прошли краткий курс выживания в условиях runtime-исполнения приложения написанного на языке программирования Ruby.

## Интеграция с командной строкой операционной системы

Приложение в runtime никогда не живет само по себе. А в процессе разработки, очень часто требуется иметь доступ к исходному коду, и даже выполнять какие-то команды. В таких случаях, приходится отрываться от текущей сессии, и к примеру открывать отдельный терминал, в котором производить нужные действия.

Но слава богам, разработчики pry умеют заимствовать чужие достижения и перенимать успешные и красивые решения. Не знаю, многие ли из тех, кто читает эти строки, поели в свое время из миски под названием Python, но у меня было счастливое время, когда я писал на этом замечательном языке (причем, все это говорю без сарказма, как многие могут подумать).

Не только Ruby плодотворен на разного рода креативные решения. Среди разработчиков Python есть тоже весьма одаренные люди. И одни из них написали в свое время прекрасную замену системному shell'у. В отличии от Ruby, Python есть в 99% дистрибутивов из коробки, если не во всех 100%. Это позволяет писать на нем софт, совершенно не беспокоясь, что пользователю Linux системы придется беспокоиться насчет установки интерпретатора языка. В общем, некоторые одаренные личности, скрестили стандартный Python REPL с командной строкой и назвали свое творение IPython. Между прочим, творение прижилось и ничем не уступает даже тому zsh, так как за плечами стоит мощный язык программирования и хороший синтаксис, чего не скажешь ни о каком скриптовом языке для shell.

Так вот, разработчики pry позаимствовали идеи IPython, и внедрили в свой продукт аналогию сего чуда. И получилось весьма и весьма недурственно на мой вкус.

Итак, посмотрим, что мы можем делать? Мы можем работать с pry, будто находимся в shell.

{% codeblock Вызов shell команд из pry lang:ruby %}
[1] pry(main)> .ls -l
итого 80
drwxrwxr-x 8 demiazz demiazz 4096 июня  10 01:37 app
-rw-rw-r-- 1 demiazz demiazz  213 июня  10 04:35 Capfile
drwxrwxr-x 5 demiazz demiazz 4096 июня  10 19:26 config
-rw-rw-r-- 1 demiazz demiazz  157 июня  10 01:37 config.ru
drwxrwxr-x 2 demiazz demiazz 4096 июня  10 17:49 db
drwxrwxr-x 2 demiazz demiazz 4096 июня  10 01:37 doc
-rw-rw-r-- 1 demiazz demiazz  660 июня  10 20:07 Gemfile
-rw-rw-r-- 1 demiazz demiazz 4198 июня  10 20:09 Gemfile.lock
drwxrwxr-x 5 demiazz demiazz 4096 июня  10 19:15 lib
drwxrwxr-x 2 demiazz demiazz 4096 июня  10 15:49 log
drwxrwxr-x 2 demiazz demiazz 4096 июня  10 19:27 public
-rw-rw-r-- 1 demiazz demiazz  272 июня  10 01:37 Rakefile
-rw-rw-r-- 1 demiazz demiazz 9208 июня  10 01:37 README.rdoc
drwxr-xr-x 2 demiazz demiazz 4096 июня  10 01:37 script
drwxrwxr-x 2 demiazz demiazz 4096 июня  10 20:16 spec
drwxrwxr-x 6 demiazz demiazz 4096 июня  10 16:08 tmp
drwxrwxr-x 4 demiazz demiazz 4096 июня  10 01:37 vendor
[2] pry(main)> filename = 'Rakefile'
[2] pry(main)> .cat #{filename}
#!/usr/bin/env rake
# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.

require File.expand_path('../config/application', __FILE__)

Scoping::Application.load_tasks
{% endcodeblock %}

Заманчиво, неправда ли. Насколько можно заметить, можно использовать интерполяцию Ruby строк прямо в вызовах команд. Неплохая замена shell'у получается, надо сказать. У pry есть также `shell-mode`. По сути, в `shell-mode` меняется строка приглашения.

Небольшим недостатком является то, что в pry не работает автодополнение к примеру для перехода по каталогам.

Еще у pry имеются две интересные команды `cat` и `cd-gem`.

Команда `cat` умеет выводить различные данные, как и ее родственница из Unix, но ориентированна на работу с кодом и pry. К примеру, она поддерживает подстветку синтаксиса. Но если вам нужна именно настоящая `cat`, то из pry ее можно вызвать как `.cat`.

Вторая команда умеет вас быстро перебрасывать в каталог выбранного гема.

Это в принципе база, которой должно с головой хватить для работы с shell из pry, если пригляделся такой вариант.

## Просмотр кода и документации

Pry в первую очередь интерактивная консоль для разработчиков. Важным моментом здесь является быстрый доступ к исходному коду и документации, и желательно это делать не отрываясь от работы с консолью, без переключения контекста нашего внимания к примеру в браузер, или не дай бог в другой терминал с запущенной ri.

Я это понимаю, вы это понимаете, и главное это понимают разработчики pry.

Для просмотра исходного кода есть специальная команда `show-source`, которая позволяет просмотреть исходный код метода, или класса. Она также поддерживает синтаксис ri. Также следует заметить, что если у вас уставовлен и загружен плагин 'pry-doc', то становится возможным также и просмотр исходного кода на C в случае, если у вас MRI.

Если вы разрабатываете расширения для pry, или вас интересует код команды, то можно воспользоваться командой `show-command`.

Касательно документации, то есть команда `show-doc`. Имеется доступ к `ri` одноименной командой. А также можно посмотреть краткую статистику объекта с помощью `stat`.

## Использование в качестве отладчика

Pry можно использовать в качестве отладчика. Причем очень отменного и удобного. В любом месте вашего кода вы можете поставить брекпоинт в виде `binding.pry`, и при выполнении этого кода, выполнение остановится и откроется сессия Pry с runtime-окружением в момент останова. При выходе из сессии, исполнение кода продолжится дальше с учетом внесенных изменений, если таковые были.

Также не стоит забывать про `bundle open <gem-name>`. Это позволяет делать более глубокую отладку кода, позволяя делать точки останова и изучать состояние приложения внутри сторонних гемов, а не только внутри нашего приложения.

Это основные и главные для меня фичи, которые я использую регулярно. Если у меня падает код в приложении, и мне надо найти причину падения, если она не видна и не лежит на поверхности, то первым делом я делаю `binging.pry` и воспроизвожу действие, которое приводит к падению, и изучаю то, что может вызвать ошибку. А учитывая, что я получаю не просто отладчик, а runtime окружение и мощнейшую консоль разработчика с возможность работы с кодом, документацией и многими другими плюшками - то мне не нужны другие инструменты отладки приложения, кроме логов, которые по большей части используются не для попытки узнать что происходит путем топорного `puts`, а для анализа работы приложения и сохранения данных о работе приложения в production. То есть, логи у меня выполняют ровно ту роль, которую должны выполнять, не более и не менее. Для всего остального есть pry.

Среди всего прочего, многим может прийтись по душе набор доступных расширений, комбинации которых предоставляют и полноценную альтернативу настоящему отладчику, и возможности асинхронной среды (EventMachine), и возможность подсветки синтаксиса вводимого кода, и даже возможности удаленного подключения к отлаживаемому приложению, в том числе и по SSH.

## В заключении

Статья получилась громоздкой, и я не смог нормально избавиться от дублирования документации по Pry, но постарался сделать так, чтобы хотя бы заинтересовать в том, чтобы пользоваться этим инструментом.

Всю остальную информацию в достатке можно найти по ссылкам ниже:

* [Официальный сайт](http://pry.github.com/)
* [Страница на Github](https://github.com/pry/pry)
* [Официальная wiki проекта](https://github.com/pry/pry/wiki)
* [Вводный скринкаст от разработчика](http://vimeo.com/26391171)
* [Документация по API](http://rdoc.info/github/pry/pry/master/file/README.markdown)
* [Список доступных плагинов](https://github.com/pry/pry/wiki/Available-plugins)
