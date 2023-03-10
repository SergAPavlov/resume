## Хелпер для автоматической генерации php-тестов

Программа предназначена для автоматической генерации шаблонов тестов по исходным текстам тестируемого кода.
Тесты базируются на библиотеке Mockery (в свою очередь, расширяющей библиотеку PhpUnit).
Тестируемые классы и их зависимости должны быть доступны для автозагрузки в соответствие с PSR-4.

Программа анализирует текст файла тестируемого класса, и генерирует текст класса-теста, в котором для каждого метода тестируемого класса генерируется соответствующий тестовый метод (или несколько тестовых методов, в особых, далее описанных случаях).

Программа находит в анализируемом методе обращения к вызовам методов тестируемого и иных классов (далее инъекции), создавая в генерируемом тесте моки необходимых объектов, их свойств и методов.
Также в генерируемый тестовый метод включается проверка (assert) результатов вызова тестируемого метода.

Программа не определяет возможные результаты вызова тестируемого метода, поэтому для создания реального теста сгенерированный тестовый класс должен быть использован как шаблон, в который пользователь сам подставит параметры вызова тестируемого метода и соответствующие им результаты вызова в оператор проверки assert.

Также в тестируемом методе ищутся неперехваченные исключения, для каждого из которых (при их наличии) программа создает тестовый метод, тестирующий выбрасывание соответствующего исключения.

## Особенности и ограничения текущей версии программы

Автоматически может быть сформирован полноценный шаблон только для тех инъекций, типы всех методов и свойств которых известны по их сигнатурам.
В случае, если тип не может быть опознан по сигнатурам - создается частичный шаблон, дополненный комментариями (expectations) о возникших при его создании проблемах (с тегом @uniter_x). 
Пользователь может дополнить такого рода частичный шаблон, ориентируясь на эти комментарии и самостоятельный анализ кода. 

В цепочке инъекции могут быть представлены вызовы методов и свойств класса, а на верхнем уровне - обращения к статическим методам класса, например:
>self::dependentMethod1('88')->dependentMethod1('77')->propp;

Поддерживается создание тестов для абстрактных классов, финальных классов, тестирование приватных и защищенных методов классов.

## Работа с системой на сервере проекта
Работа с системой осуществляется через командную строку. Доступны плагины для Laravel и Yii.

<a href="https://github.com/uniter1-dev/uniter-laravel">плагин для Laravel</a>,
<a href="https://github.com/uniter1-dev/uniter-requester">библиотека для плагинов</a> 

## Пример сгенерированного системой кода
```php
use Mockery;
use Mockery\Adapter\Phpunit\MockeryTestCase;
use Tests\Feature\Application\Cases\AltDev\ChainWithInjPlus;
use Tests\Feature\Application\Cases\Etalon\EtalonAltExample;

/** 
* @see ChainWithInjPlus
*/
class ChainWithInjPlusTest extends MockeryTestCase
{
    /** 
    * @see ChainWithInjPlus::problem()
    * @uniter_chain_wrong_type Unknown type for injection chain. Context: class: ChainWithInjPlus, method: problem, flow: 0, injection: $three->testableMethod(1, 2)
    */
    public function testProblem0()
    {
        /** @var EtalonAltExample $etalonAltExample */
        $etalonAltExample = Mockery::mock('EtalonAltExample')->makePartial();
        $chainWithInjPlus = new ChainWithInjPlus();
        $this->assertEquals(4, $chainWithInjPlus->problem(ARG_py));
    }
    /** 
    * @see ChainWithInjPlus::problem()
    * @uniter_chain_wrong_type Unknown type for injection chain. Context: class: ChainWithInjPlus, method: problem, flow: 1, injection: $three->testableMethod(1, 2)
    */
    public function testProblem1()
    {
        /** @var EtalonAltExample $etalonAltExample */
        $etalonAltExample = Mockery::mock('EtalonAltExample')->makePartial();
        $chainWithInjPlus = new ChainWithInjPlus();
        $this->assertEquals(EXPECTED_RETURN, $chainWithInjPlus->problem(ARG_py));
    }
}
```
v0.0.995
