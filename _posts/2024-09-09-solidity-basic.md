---
layout: post
title: 솔리디티 기본 문법
tags:
  - 스마트컨트랙트
---
**컨트랙트의 기본 구조는 `라이센스 명시`, `컴파일러 명시`, `컨트랙트 작성`으로 분류할 수 있다.**

### 라이센스 명시
스마트 컨트랙트에 대한 신뢰도를 높이고 저작권 문제 해결을 위해 `SPDX 라이센스`를 사용
- ver 0.68 이후 미 기재시 에러가 발생한다.
- SPDX 리스트에 있지 않아도 컴파일러는 알아채지 못하지만 꼭 명시는 해야 한다.
	- SPDX-License-Identifier: UNLICENSED > 가능
	- 공란  > 불가능

### 컴파일러 버전 명시
솔리디티는 이더리움 가상 머신(EVM) 위에서 스마트 컨트랙트 개발을 위한 프로그래밍 언어이므로 작성 코드를 바이너리 코드로 변환하기 위해 솔리디티 컴파일러인 solc를 사용해야 한다.
컴파일러 버전에 따라 문법의 차이가 있을 수 있기에 `pragma` 키워드를 사용하여 solc의 버전을 명시해준다.

예시로 `pragma solidity ^0.8.0`은 0.8.0 이상의 컴파일러를 사용하겠다라는 뜻이다.

### 컨트랙트 선언 및 정의
`contract` 라는 키워드를 입력하여 컨트랙트 명과 코드를 작성한다.
컨트랙트 명명 규칙은 단어의 구분마다 첫 글자를 대문자로 시작하는 `PascalCase`를 사용한다. 

**솔리디티 샘플 코드**
```sol
// SPDX-License-Identifier: GPL-3.0 // 라이센스 명시
pragma solidity >=0.8.2 <0.9.0; // 컴파일러 명시

contract Storage { // 컨트랙트 선언 및 정의
	uint256 number;

	/**
	* @dev Store value in variable
	* @param num value to store
	*/
	function store(uint256 num) public {
		number = num;
	}

	/**
	* @dev Return value
	* @return value of 'number'
	*/
	function retrieve() public view returns (uint256){
		return number;
	}
}
```

해당 포스팅은 [스마트 컨트랙트 실전](https://www.youtube.com/watch?v=8fEwzGQausQ&list=PLzUgt9aUfvBTFjNTOP4BUGSj5yNtDgBZ_&index=11) 영상을 참고하여 작성한 포스팅입니다.
