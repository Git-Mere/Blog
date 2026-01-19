---
title: "suid, sgid, and sticky bit"
excerpt: " "

categories:
  - 내가_볼려고_만든_것들
tags:
  - [tag1, tag2]

permalink: /categories4/suid-sgid-sticky_bit/

toc: true
toc_sticky: true

date: 2026-01-14
last_modified_at: 2025-01-14
---

1. SUID(Set User ID)
   - 실행 파일을 "파일 소유자 권한"으로 실행하게 만드는 비트
   - 즉 원래 소유자가 "sam"인데, 다른 사람이 실행해도 "sam"이 실행한것처럼 실행됨.
   - chmod 4xxx (파일) 로 설정가능
    - 근데 여기서 소유자한테 실행권한(x)가 없으면 S로 보이고, 있으면 s로 보임.2
   - chomd u+s (파일) 로 설정가능

2. SGID(Set Group ID)
   - 실행 파일을 "파일 그룹 권한"으로 실행하게 만드는 비트
   - 디렉토리에 적용하면 안에 생성되는 파일/폴더가 부모 디렉토리의 그룹을 그대로 상속
   - chmod 2xxx 로 설정가능
    - 근데 여기서 그룹한테 실행권한(x)가 없으면 S로 보이고, 있으면 s로 보임.
   - chomd g+s (파일) 로 설정가능

- 만약 SUID, SGID를 둘 다 하고 싶다면 chmod 6xxx로 가능

3. sticky bit
   - 폴더에만 적용가능
   - 디렉토리 안에서 파일 삭제/이름 변경은 파일 소유자, 디렉토리 소유자만 가능하게 함.
   - chmod 1xxx로 설정가능
     - 근데 여기서 그룹한테 실행권한(x)가 없으면 S로 보이고, 있으면 s로 보임.
   - chomd +t (파일) 로 설정가능