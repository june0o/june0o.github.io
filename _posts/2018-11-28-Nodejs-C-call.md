## Nodejs 에서 C 라이브러리 호출하기 :christmas_tree:
: node-ffi 모듈을 사용하여 Nodejs 에서 C 라이브러리를 호출하는 방법에 대해 알아본다.

### 1. C 라이브러리 Dll로 만들기
#### 1) DLL 프로젝트 생성
① VS2013 실행
② New Project > Win32 Project 선택 후 적당한 이름(ex. HelloDll) 입력
③ Win32 Application Wizard > Application Settings
Application type: DLL 선택 후 Finish 버튼 클릭

#### 2) api 작성

    #include "stdafx.h"
    #include <stdio.h>
    
    extern "C" __declspec(dllexport) int hello()
    {
        printf("hello everybody :)\n");
        return 0;
    }

#### 3) 빌드
출력 폴더에 HelloDll.dll 파일 생성되는 것 확인!

※ 아래 사이트 참고.
[DLL 만들기 및 호출하기 따라하기](http://gentlesark.tistory.com/11)

### 2. Node ffi 
: `node-ffi`is a Node.js addon for loading and calling dynamic libraries using pure JavaScript. It can be used to create bindings to native libraries without writing any C++ code.
[GitHub - node-ffi: Node.js Foreign Function Interface](https://github.com/node-ffi/node-ffi)

※ Dll 빌드 환경이 32bit 인 경우 Node 또한 32bit 설치해야 한다! 
이 설정이 서로 다르면 ffi 에서 dll load 시 __dynamic linking error__ 가 발생한다!

#### 1) Node 환경 구성
npm init 호출하여 project 구성후 ffi 모듈을 설치한다.
설치시 node-gyp 을 빌드하는 데 에러 메시지가 없으면 성공!

    $ npm init 
    $ npm install ffi --save

### 3. Nodejs 에서 C 라이브러리 호출하기
#### 1) C 라이브러리를 호출하는 Nodejs 파일 작성하기
※ C 에서 export 한 함수와 동일하게 적어야 한다!
다르면 __Dynamic Symbol Retrieval Error__ 가 발생한다!

    // hello.js
    var ffi = require('ffi');
    var test = new ffi.Library(__dirname + '/HelloDll', {
      'hello': ['int', []]
    });

    console.log(test.hello())

#### 2) 실행
아래와 같이 출력되면 성공!

    $ node hello.js
    hello everybody :)
    0