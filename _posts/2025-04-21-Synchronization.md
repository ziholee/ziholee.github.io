---
title: "[운영체제] Process Synchronization"
author: ziho
date: 2025-04-21
categories: [운영체제, 프로세스동기화]
tags: [os, process, synchronization, concurrency, race-condition, critical-section, mutual-exculsion, progress, bounded-waiting, study]
pin: true
---
## Councurrency Problem

여러 프로세스가 동시에 공유 자원에 접근하면 데이터 불일치가 발생할 수 있다.

이러한 상황을 방지하기 위해서 질서 있는 실행(orderly execution)이 필요하다.