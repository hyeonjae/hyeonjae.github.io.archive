---
title:  "Emscripten"
date:   2015-06-16 21:55:11
categories: llvm
---


# Emscripten이란

Emscripten은 LLVM bitcode를 Javascript코드를 만들어 주는 프로젝트이다. 보통 C/C++로 작성된 코드를 clang을 이용하여 LLVM bitcode를 생성하고, 다시 Emscripten으로 Javascript코드를 만든다. C/C++이 아니어도 LLVM bitcode를 만들 수 있으면 어떤 코드도 Javascript로 변환 가능하다. 

 > Emscripten compiles LLVM bitcode into JavaScript, which makes it possible to compile C and C++ source code to JavaScript (by first compiling it into LLVM bitcode using Clang), which can be run on the web. Emscripten has been used to port large existing C and C++ codebases, for example Python (the standard CPython implementation), the Bullet physics engine, and the eSpeak speech synthesizer, among many others.


Emscripten는 Javascript로 작성되었고, high-level Javascript control folw를 생성하는 Relooper algorithm이 포함되어 있다. 

 > Emscripten itself is written in JavaScript. Significant components include the Relooper Algorithm which generates high-level JavaScript control flow structures ("if", "while", etc.) from the low-level basic block information present in LLVM bitcode, as well as a JavaScript parser for LLVM assembly.

# Emscripten 설치

Emscripten은 [여기](http://kripken.github.io/emscripten-site/docs/getting_started/downloads.html)에서 다운로드 할 수 있다.  
다운로드 받고, 압축을 풀고 아래 명령어로 설치하자.(MAC OS X기준)
{% highlight sh %}
./emsdk update
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh
{% endhighlight %}
Emscripten으로 생성한 Javascript코드는 node에서 돌릴 수 있으니, 미리 node도 깔아두자.


{% highlight sh %}
python2 --version
{% endhighlight %}
위 명령어가 만약 `command not found`면 아래 커맨드로 설정하자.
{% highlight sh %}
cd /usr/bin
sudo ln python python2
sudo ln ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7 python22.7
{% endhighlight %}

# 예제
이제 간단한 c코드를 만든다.
{% highlight c %}
hello.c
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("hello world\n");
    return 0;
}
{% endhighlight %}


이제 이 코드를 `emcc`로 Javascript로 변환한다.
{% highlight sh %}
./emcc hello.c
{% endhighlight %}
그럼 `a.out.js`파일이 생성된다.
{% highlight sh %}
node a.out.js

# hello world
{% endhighlight %}

많은 최적화 옵션을 적용할 수 있다고 한다. 다음번에는 emscripten으로 생성된 코드의 성능을 비교 분석해보겠다.
  
  
  
### 다음 주제:
 - relooper algorithm
 - other c/c++ to javascript compiler (Emscripten, Mandreel and Duetto)