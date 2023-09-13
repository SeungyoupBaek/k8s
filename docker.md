<details>
<summary><span style="color: dodgerblue">Create Dockerfile</span></summary>

|  **명령어**   |               **설명**                |
|:----------:|:-----------------------------------:|
|    FROM    |              기반 이미지 지정              |
| MAINTAINER |  이미지 관리자 정보 표시(현재는 LABEL을 더 많이 사용)  |
|   LABEL    |            이미지의 메타데이터 지정            |
|    USER    |              명령어 실행 계정              |
|  WORKDIR   |         명령어를 실행할 작업 디렉터리 지정         |
|    RUN     |          컨테이너 안에서 명령어를 실행           |
|   EXPOSE   |       컨테이너 실행시 Listen할 표트 지정        |
|    ADD     |             파일을 이미지에 추가             |
|    COPY    | 로컬 디렉터리에서 읽어 들인 컨텍스트로부터 이미지에 파일을 복사 |
|    CMD     |         컨테이너 기동시 실행할 명령어 인수         |
| ENTRYPOINT |          컨테이너 기동시 실행할 명령어           |

</details>
