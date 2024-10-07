# Terraform

## Terraform 기본 개념
- resource : 실제로 생성할 인프라 자원
  - gcp IAM, Cloud Run, etc.
- provider : Terraform으로 정의할 Infrastructure Provider를 의미함
  - 여기서 provider란 공급자, 제공자 이라는 뜻임
- output : 인프라를 프로비저닝 한 후에 생성된 자원
  - output으로 추출한 부분은 이후에 `repmote state`에서 활용할 수 있음
- backend : terraform의 상태를 저장할 공간을 지정하는 부분.
  - beckend를 사용하면 현재 배포된 최신 상태를 외부에 저장하기 때문에 다른 사람과의 협업이 가능함.
  - ex: S3, Cloud Storage
- module : 공통적으로 활용할 수 있는 인프라 코드를 한 곳으로 모아서 정의하는 부분.
  - Module을 사용하면 변수만 바꿔서 동일한 리소스를 쉽게 생성할 수 있음
- remote state : output에서 생성한 자원을 다른 곳에서도 쓸 수 있게 해줌.
  - VPC, IAM 같은 공용서비스를 다른 서비스에 참조할 때 주로 사용함
  - `tfstate`파일이 저장되어 있는 backend 정보를 명시하면 terraform이 해당 backend에서 output 정보들을 가져옴.

## Terraform 작동 원리
1. Local 코드 : 컴퓨터에서 작성하는 코드
2. 클라우드 서비스 인프라 : 실제로 배포되고 있는 인프라
3. Backend에 저장된 상태 : 가장 최근에 배포한 테라폼 코드 형상

실제 배포되고 있는 인프라와 Beckend에 저장된 상태는 100% 일치해야함 <br>
1. 인프라 정의는 Local 코드에서 작성한다.
2. 작성한 테라폼 코드를 실제 인프라로 프로비저닝한다.
3. 이 때 backend를 구성하여 최신 코드를 저장한다.

```shell
terraform init
```
- 지정된 backend에 상태를 저장하기 위한 `.tfstate` 파일을 생성함.
  - 가장 마지막에 적용한 terraform 내역이 저장됨.
- 해당 명령어가 실행되면 `.tfstate`에 정의된 내용을 담은 `.terraform` 파일이 생성된다.
- 기존에 다른 개발자가 이미 `.tfstate`에 인프라를 정의해 놓은 것이 있다면, 해당 명령어를 통해 local에 sync 할 수 있

```shell
terraform plan
```
- 정의한 코드가 어떤 인프라를 만들게 되는지 미리 예측 결과를 보여줌 (로컬에서 돌리기)
  - plan에서 에러가 없었어도 실제로 프로바인딩되면 에러가 생길 수 있음
- 해당 명령어는 어떠한 형상에도 변화를 주지 않음.
  - just 실행될 결과를 예상

```shell
terraform apply
```
- 클라우드 서비스에 실제 인프라가 배포됨
- 배포된 결과는 backend의 `.tfstate`와 `.terraform`파일에 저장됨

```shell
terraform import
```
- 배포된 리소스를 `teraform state`로 옮겨줌
  - `.terraform`에 해당 리소스의 상태를 저장해주는 역할
