
---
title:  "LLVM Python Implementation: Pyston"
date:   2015-04-19 11:58:01
categories: llvm
---


#pyston
https://github.com/dropbox/pyston

드랍박스에서 LLVM의 JIT 기술을 이용한 python implementation을 만들었다.
과거 python의 창시자 귀도 반 로섬이 있던 회사에서 만든 python implementation이라는 점에서 다른 python impelmentation들 보다 특별하다.

LLVM은 최적화에 최적화된 컴파일러 종합 프레임워크이다. gcc에 비해 최근에 생겨난 컴파일러로 gcc보다 훨씬 developer friendly하다. source code가 기계어로 번역 되는 모든 과정이 열려있고, 관련 api를 제공한다. 특히 LLVM의 c/c++ 구현체인 clang는 gcc보다 최적화를 잘 해서, 성능이 훨씬 우수하다는 여러 벤치마크 결과가 있다. 또 c++11/14의 스펙을 gcc보다 먼저 구현한 것으로도 유명하다. 이밖에도 LLVM으로 구현한 다른 언어의 구현체들이 기존의 컴파일러보다 훨씬 성능이 좋은 경우가 많다. javascript와 c++, python간의 상호 변환도 가능해졌다.

LLVM과 관련한 유명한 프로젝트로는 emscripten, servo의 rust언어, clang 등이 있다.