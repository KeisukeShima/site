#スレッドローカルストレージ
* cpp11[meta cpp]

##概要
変数宣言の際に、記憶域として`thread_local`キーワードを指定することで、スレッドごとの静的記憶域に変数が保持される。

`static`キーワードを記憶域として使用した変数は、プログラムを通してひとつの状態を持ち、プログラム終了時に変数が破棄される。`thread_local`キーワードの場合はスレッドごとに状態を持ち、スレッド終了時に変数が破棄される。

```cpp
// スレッドごとに、0から始まるIDを生成して返す関数
int create_id()
{
  thread_local int current_id = 0;
  return current_id++;
}

// 2つのスレッドそれぞれで、IDが0から始まる
std::thread t1([]{
  int id1 = create_id(); // id1 == 0
  int id2 = create_id(); // id2 == 1
});

std::thread t2([]{
  int id1 = create_id(); // id1 == 0
  int id2 = create_id(); // id2 == 1
});

t1.join();
t2.join();
```
* std::thread[link /reference/thread/thread.md]
* join()[link /reference/thread/thread/join.md]


##仕様
(執筆中)


##例
```cpp
#include <iostream>
#include <thread>
#include <random>

// 範囲[min_inclusive, max_inclusive]でランダム一様分布する整数を生成する。
// スレッドごとに乱数の状態を持つ。
int random_range(int min_inclusive, int max_inclusive)
{
  std::random_device seed_gen;
  thread_local std::mt19937 engine(seed_gen());
  std::uniform_int_distribution<int> dist(min_inclusive, max_inclusive);
  return dist(engine);
}

int main()
{
  // 複数のスレッドから並行にrandom_range()関数を呼び出せる
  std::thread t1([]{
    int random_value = random_range(0, 100);
    std::cout << "thread1 : " + std::to_string(random_value) + "\n";
  });

  std::thread t2([]{
    int random_value = random_range(0, 100);
    std::cout << "thread2 : " + std::to_string(random_value) + "\n";
  });

  t1.join();
  t2.join();
}
```
* std::random_device[link /reference/random/random_device.md]
* std::mt19937[link /reference/random/mt19937.md]
* std::uniform_int_distribution[link /reference/random/uniform_int_distribution.md]
* std::thread[link /reference/thread/thread.md]
* join()[link /reference/thread/thread/join.md]
* std::cout[link /reference/iostream/cout.md]
* std::to_string[link /reference/string/to_string.md]

###出力例
```
thread1 : 67
thread2 : 4
```


##参照
- [N1874 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1874.html)
- [N1966 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n1966.html)
- [N2147 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2147.html)
- [N2280 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2280.html)
- [N2545 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2008/n2545.html)
- [N2659 Thread-Local Storage](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2008/n2659.htm)
