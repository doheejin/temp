---
layout: post
title:  "[vscode] 원격 서버 연결 오류 (비정상 종료/비밀번호 retry)"
date:   2021-04-01T14:25:52-05:00
author: Heejin Do
categories: vscode
tags:	vscode envs server
---

vscode 에서 원격 서버를 연결하다가 작업이 비정상적으로 종료된 이후, 재접속 했을 때

접속이 지연되면서 계속 retry가 뜰 때가 있다. (ex message : `connect to VS code server - retry n`)

이런 경우 config 파일을 수정하고 새로 서버 등록후 접속해도 해결이 안 되었는데,

해결 방법을 찾아 정리해보았다. 

**1. F1 버튼**

**2. Uninstall VS Code Server 실행 후 문제 서버 선택**

<img src="/assets/images/vs-remote-error.png" title="">

**3. 서버 재접속**

매우 간단한 방법으로 해결 가능하다.