# 防御性编程

> 防御性编程是防御式设计的一种形式，用来确保软件在未知的环境中能继续运行。防御性编程的实践往往用于需要高可用性、安全性、保密性的地方

## 技巧

* Intelligent source code reuse
* Canonicalization
* Low tolerance against "potential" bugs

* All data is important until proven otherwise
* All data is tainted until proven otherwise.
* All code is insecure until proven otherwise
* If data are to be checked for correctness, verify that they are correct, not that they are incorrect.
* Design by contract
* Assertions
* Prefer exceptions to return codes

## 参考资料

* [wikipedia](https://en.wikipedia.org/wiki/Defensive_programming)