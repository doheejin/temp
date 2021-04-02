---
layout: post
title:  "[vscode] 원격 서버 연결 오류 (비정상 종료/비밀번호 retry)"
date:   2021-04-01T14:25:52-05:00
author: Heejin Do
categories: vscode
tags:	vscode envs server
---

vscode 에서 원격 서버를 연결하다가 작업이 비정상적으로 종료된 이후 재접속 했을 때

계속 retry가 뜰 때가 있다. (`connect to vs code server - retry 1`)

이런 경우 config 파일을 수정하고 새로 서버 등록후 접속해도 해결이 안 되었는데,

해결 방법을 찾아 정리해보았다. 