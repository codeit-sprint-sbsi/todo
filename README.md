# 할 일 관리 API 시작 프로젝트

Java 21, Spring Boot 4.2.0, Gradle 9.9.2, PostgreSQL 17을 사용합니다. 빌드와 DB 연결 설정, 애플리케이션 기동 확인용 테스트를 제공하며, 엔티티와 API는 직접 구현합니다. 아직 API가 없으므로 기본 상태에서 HTTP 요청에 404가 반환되는 것은 정상입니다.

## 실행 준비

- JDK 21을 설치하고 터미널의 `java -version`이 21인지 확인합니다. IDE의 Gradle JVM도 21로 맞춥니다.
- PostgreSQL을 준비합니다. Docker 사용은 선택 사항이며, 직접 설치하거나 별도로 준비한 PostgreSQL에 연결해도 됩니다. 제공된 Compose 파일을 사용하는 경우에만 Docker와 Docker Compose v2 이상이 필요합니다.
- Gradle은 별도로 설치하지 않습니다. 최초 빌드에는 Gradle과 의존성을 내려받을 네트워크 연결이 필요합니다.

## DB 준비

### Docker Compose를 사용하는 경우

이 README와 `compose.yaml`이 있는 디렉터리에서 실행합니다.

```sh
docker compose up -d --wait
```

### Docker를 사용하지 않는 경우

직접 설치하거나 별도로 준비한 PostgreSQL에 과제용 데이터베이스와 접속 계정을 생성해 주세요. 해당 계정은 테이블 생성과 데이터 조회 및 변경이 가능해야 합니다.

`src/main/resources/application.properties`의 접속 설정을 변경하거나 다음 환경변수로 지정합니다.

| 환경변수 | 설정할 값 |
| --- | --- |
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://호스트:포트/데이터베이스명` |
| `SPRING_DATASOURCE_USERNAME` | DB 사용자 이름 |
| `SPRING_DATASOURCE_PASSWORD` | DB 비밀번호 |

빌드와 애플리케이션 실행에 같은 접속 설정을 적용해 주세요. 별도 제출 문서 `SUBMISSION.md`에는 평가자가 DB를 준비하고 연결할 수 있도록 설치 또는 실행 방법, 연결 설정과 초기화 방법을 설명하고, 사용하는 초기화 SQL 등 필요한 파일을 저장소에 포함해 주세요.

## 애플리케이션 실행

DB 준비 후 프로젝트 루트에서 실행합니다.

```sh
./gradlew clean build
./gradlew bootRun
```

빌드에 포함된 기동 확인 테스트도 PostgreSQL에 연결하므로 DB를 먼저 실행해 주세요.

Windows에서는 `./gradlew` 대신 `gradlew.bat`를 사용합니다. macOS와 Linux에서 실행 권한 오류가 나면 `chmod +x gradlew`로 실행 권한을 부여합니다.

아래 DB 접속 정보는 제공된 Docker Compose의 기본값입니다. 별도 PostgreSQL을 사용하는 경우에는 본인의 설정을 따릅니다.

- API 기본 주소: `http://localhost:8080`
- DB 주소: `localhost:5432`
- 데이터베이스: `todo_assessment`
- 사용자: `assessment`
- 비밀번호: `assessment_local`

위 계정은 제공된 로컬 DB 전용입니다. 테이블은 기본적으로 JPA의 `ddl-auto: update` 설정으로 생성 및 갱신됩니다. 애플리케이션 재시작 시 데이터를 지우는 설정으로 바꾸지 마세요.

제공된 Docker Compose에서 5432 포트를 이미 사용 중이면 두 터미널에 같은 `DB_PORT`를 지정합니다. 아래는 macOS와 Linux 예시입니다.

```sh
DB_PORT=55432 docker compose up -d --wait
DB_PORT=55432 SERVER_PORT=8081 ./gradlew bootRun
```

PowerShell에서는 `$env:DB_PORT = "55432"`, `$env:SERVER_PORT = "8081"`처럼 환경변수를 지정한 뒤 원래 실행 명령을 사용합니다. `SERVER_PORT`는 애플리케이션 포트 충돌이 있을 때만 변경하면 됩니다.

## DB 확인과 종료

아래 명령은 제공된 Docker Compose를 사용할 때의 예시입니다. 별도 PostgreSQL을 사용한다면 해당 환경의 접속 정보로 psql 또는 DB 클라이언트에 연결하고, 그 환경의 종료 및 초기화 절차를 따릅니다.

```sh
docker compose exec db psql -U assessment -d todo_assessment
```

psql에서 `\dt`로 테이블을 확인하고, `\q`로 종료합니다.

애플리케이션은 실행 터미널에서 Ctrl+C로 종료합니다. DB를 종료해도 데이터를 남기려면 다음 명령을 사용합니다.

```sh
docker compose down
```

처음부터 다시 확인하려는 경우에만 다음 명령을 사용합니다. 이 프로젝트의 DB 볼륨과 저장된 데이터가 삭제됩니다.

```sh
docker compose down -v
docker compose up -d --wait
```

## 구현과 제출

`codeit.sprint.sbsi.todo` 아래에 필요한 코드를 작성합니다. 이 프로젝트의 파일을 GitHub 저장소 루트에 복사하고, 저장소 루트에 별도 마크다운 파일 `SUBMISSION.md`를 작성해 주세요. 이 README는 스타터의 기본 실행 안내로 참고하고, 사용하는 기술 버전, 빌드와 실행 방법, DB 연결 및 초기화 방법, 구현한 API 명세와 구현 설명은 `SUBMISSION.md`에 정리해 주세요. 상세 기능과 데이터 제약은 별도로 제공된 지원자 과제 안내문을 따릅니다.

### SUBMISSION.md의 API 명세 작성 틀

각 기능에 대해 아래 항목을 작성합니다.

| 항목 | 작성 내용 |
| --- | --- |
| 기능 | 구현한 기능 이름 |
| 요청 | HTTP 메서드와 URL |
| 파라미터 | 경로 및 쿼리 파라미터와 허용 값 |
| 본문 | JSON 예시, 필수 여부, 수정 시 생략값 처리 |
| 정상 응답 | 상태 코드와 JSON 예시. 본문이 없으면 명시 |
| 오류 응답 | 상황별 상태 코드와 JSON 예시 |

목록과 할 일의 관계, 생성 순서의 기준, 연쇄 삭제 구현 방식도 짧게 설명해 주세요.
