# Boost.Phoenix
[Boost.Phoenix](http://www.boost.org/libs/phoenix) - это самая важная Boost библиотека функционального программирования. Если библиотеки вроде Boost.Bind и Boost.Lambda
предоставляют некоторые дополнительные возможности для функционального программирования, то Boost.Phoenix не только включает в себя эти возможности, но и выходит за их рамки.

В функциональном программировании функции являются объектами и могут быть использованы в качетстве объектов. Используя Boost.Phoenix,можно написать функцию, которая может вернуть другую как рузультат.
Также Вы можете отправить функцию параметром в другую функцию. Стало возможным различать создание объекта функции и ее прямой вызов. Представление функции как объекта не эквивалентно ее исполнению.

Boost.Phoenix поддерживает функциональное программирование с использванием объектов функций: функции - это объекты классов, у которых перегружнен оператор operator().
Тогда функциональные объекты ведут себя как другие объекты в C++.
Например, они могут быть скопированы и сохранены в контейнере. Однако они также ведут себя как функции, так как их можно вызвать.

Функциональное программирование не внове в C++. Вы можете передать функцию в качестве параметра в другую функцию, не используя Boost.Phoenix.

`Пример 39.1. Предикаты на примере, лямбда функции и функции Phoenix.`
<a name="example391"></a>
``` c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

bool is_odd(int i) { return i % 2 == 1; }

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  std::cout << std::count_if(v.begin(), v.end(), is_odd) << '\n';

  auto lambda = [](int i){ return i % 2 == 1; };
  std::cout << std::count_if(v.begin(), v.end(), lambda) << '\n';

  using namespace boost::phoenix::placeholders;
  auto phoenix = arg1 % 2 == 1;
  std::cout << std::count_if(v.begin(), v.end(), phoenix) << '\n';
}
```
[Пример 39.1](#example391) показывает использование алгоритма  ``std::count_if ()`` для счета нечетных чисел в векторе ***v***. ``std::count_if()`` вызывают три раза, один раз со стандратным предикатом, один раз с лямбда функцией, и один раз с Phoenix функцией.

Phoenix функция отличается от обычной функции и лямбда функции тем, что у нее нет всем привычной структуры. В то время как у других двух функций есть функциональный заголовок с подписью,Phoenix функция состоит только из тела функции.

Решающий компонент Phoenix функции-  ***boost::phoenix::placeholders::arg1***. ***arg1*** - глобальный экземпляр функционального объекта. Вы можете использовать его как ***std::cout*** : Эти объекты создаются, как только соответствующий заголовочный файл включен.

***arg1*** используется, чтобы определить унарную функцию. Выражения ``arg1 % 2 == 1`` создает новую функцию, которая принимает один параметр. Функция сразу не выполняется, но сохранеется в ***phoenix***. ***phoenix*** передан ``в std::count_if()``, который вызывает предикат для каждого числа в ***v***.

***arg1*** - место для передаваемого значения. Как только передается ***arg1***, создается унарная функция. Boost.Phoenix предоставляет дополнительные параметры, такие как  ***boost::phoenix::placeholders::arg2*** и  ***boost::phoenix::placeholders::arg3***. Функция Phoenix всегда ожидает число параметров, равное самому большому индексу всех пераметров.

В [Примере 39.1](#example391) ```3``` выводится трижды на экран.

`Пример 39.2. Лямбда-функция и Phoenix.`
<a name="example392"></a>
``` c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  auto lambda = [](int i){ return i % 2 == 1; };
  std::cout << std::count_if(v.begin(), v.end(), lambda) << '\n';

  std::vector<long> v2;
  v2.insert(v2.begin(), v.begin(), v.end());

  using namespace boost::phoenix::placeholders;
  auto phoenix = arg1 % 2 == 1;
  std::cout << std::count_if(v.begin(), v.end(), phoenix) << '\n';
  std::cout << std::count_if(v2.begin(), v2.end(), phoenix) << '\n';
}
```

[Пример 39.2](#example392) демонстрирует ключевое различие между лямбда функцией и Phoenix функцией. В дополнение к тому, что при объявлении Phoenix функции не требуется писать список парметров, у параметров Phoenix функции нет типов. Лямбда функция ***lambda*** ожидает параметр типа **int**. Phoenix функция примет любой тип, который может быть обработан указанным оператором.

Представляйте себе Phoenix функции как шаблоны функций.Как шаблоны функций, функции Финикса могут принять любой тип. Это позволяет в [Примере 39.2](#example392), использовать ***Phoenix*** в качестве предиката для контейнеров ***v*** и ***v2*** даже при том, что они хранят числа различных типов. При попытке использования ***lambda*** как предиката с вектором ***v2*** Вы получите ошибку компилятора.

`Пример 39.3. Phoenix функция как отложенный код C++.`
<a name="example393"></a>
``` c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  using namespace boost::phoenix::placeholders;
  auto phoenix = arg1 > 2 && arg1 % 2 == 1;
  std::cout << std::count_if(v.begin(), v.end(), phoenix) << '\n';
}
```

[Пример 39.3](#example393) демонстрирует работу функции Phoenix в качестве пердиката для подсчета нечетных 
чисел, превосходящих 2. Функция обращается к ***arg1*** дважды: первый раз, чтоб определить, превосходит ли число двойку и нечетно ли оно. Условия соединены при помощи ***&&***.

Функции Phoenix не выполняются сразу. Функция Phoenix в [Примере 39.3](#example391) похожа на условие, которое использует многократно логические и арифметическе операторы. Однако условие сразу не выполненяется. Оно выполняется при вызове из ***std::count_if***. Вызов функции в ***std::count_if()*** происходит стандартным путем.

[Пример 39.3](#example393) выводит ***2*** в стандартный поток.

``Пример 39.4. Явные Phoenix типы.``
<a name="example394"></a>
``` c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  using namespace boost::phoenix;
  using namespace boost::phoenix::placeholders;
  auto phoenix = arg1 > val(2) && arg1 % val(2) == val(1);
  std::cout << std::count_if(v.begin(), v.end(), phoenix) << '\n';
}
```
[Пример 39.4](#example394) использования явных типов для всех операндов в Phoenix функции. Грубо говоря, Вы не видите типы,
только вспомогательную функцию *** boost::phoenix::val()***. Эта функция возвращает объект, инициализированный значением, переданным в ***boost::phoenix::val()***. Фактический тип функционального объекта не имеет значения. Важно то, что Boost.Phoenix
перегружает операторы вроде ***>***, ***&&***, ***%*** и ***==*** для разных типов. Таким образом условия сразу не проверяются. Вместо этого функциональные объекты объединяются для создания более мощных функциональных объектов. В зависимости от операндов они могут автоматически использоваться в качестве функциональных объектов. Или же Вы можете вызывать вспомогательную функцию
типа ***val()***.

``Пример 39.5. boost::phoenix::placeholders::arg1 и boost::phoenix::val().``

<a name="example395"></a>
```c++
#include <boost/phoenix/phoenix.hpp>
#include <iostream>

int main()
{
  using namespace boost::phoenix::placeholders;
  std::cout << arg1(1, 2, 3, 4, 5) << '\n';

  auto v = boost::phoenix::val(2);
  std::cout << v() << '\n';
}
```

[Пример 39.5](example395) показывает работу ***arg1*** и ***val()***. ***arg1*** - экземпляр функционального объекта. Его можно использовать непосредственно и вызывать как функцию. Вы можете передать столько параметров, сколько Вам хочется – ***arg1*** возвращает первый.

***val()*** является функцией для создания экземпляра функционального объекта. Функциональный объект инициализируется значением, переданным в качестве параметра. Если к экземпляру обращаются как к функции, возращается значение.

[Пример 39.5](example395) выводит на экран ***1*** и ***2***.

``Пример 39.6. Создание своей Phoenix функции.``
<a name="example396"></a>
```c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

struct is_odd_impl
{
    typedef bool result_type;

    template <typename T>
    bool operator()(T t) const { return t % 2 == 1; }
};

boost::phoenix::function<is_odd_impl> is_odd;

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  using namespace boost::phoenix::placeholders;
  std::cout << std::count_if(v.begin(), v.end(), is_odd(arg1)) << '\n';
}
```

В [Примере 39.6](example396) объясняется, как создается собственная Phoenix функция. Вы передаете функциональный объект в шаблонный объект ***boost::phoenix::function***.В примере передается класс ***is_odd_impl***. Этот класс перегружает оператор ***oprator*()**: когда передается нечетное число, оператор возвращает true. В ином случае оператор возвращает false.

Обратите внимание на то, что Вы должны определить тип ***result_type***. Boost.Phoenix использует его, чтобы определить тип возвращаемого значения оператора ***operator()***.

***is_odd()*** является функцией, которую Вы можете использовать как ***val()***. Обе функции возвращают функциональный объект. При вызове параметры передаются в ***operator()***. В [Примере 39.6](example396) это означает, что ***std::count_if()*** по-прежнему считает нечетные числа.

``Пример 39.7. Преобразование стандартных функций в Phoenix функцию``
<a name="example397"></a>
```c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

bool is_odd_function(int i) { return i % 2 == 1; }

BOOST_PHOENIX_ADAPT_FUNCTION(bool, is_odd, is_odd_function, 1)

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  using namespace boost::phoenix::placeholders;
  std::cout << std::count_if(v.begin(), v.end(), is_odd(arg1)) << '\n';
}
```
Если Вы хотите преобразовать обычную функцию в Phoenix функцию, Вы можете действовать как в [Примере 39.7](example397). Вам не обязательно определять функциональный объект, как в предыдущем примере. 

Используйте макрос ***BOOST_PHOENIX_ADAPT_FUNCTION***, чтобы преобразовать обычную функцию в Phoenix функцию. Передайте тип возвращаемого значения, имя функции и количество параметров в макрос.

``Пример 39.8. Phoenix функции и boost::phoenix::bind()``
<a name="example398"></a>
```c++
#include <boost/phoenix/phoenix.hpp>
#include <vector>
#include <algorithm>
#include <iostream>

bool is_odd(int i) { return i % 2 == 1; }

int main()
{
  std::vector<int> v{1, 2, 3, 4, 5};

  using namespace boost::phoenix;
  using namespace boost::phoenix::placeholders;
  std::cout << std::count_if(v.begin(), v.end(), bind(is_odd, arg1)) << '\n';
}
```


