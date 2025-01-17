## 어플리케이션 흐름(객체 단위로 정의)
### 입력
1. 사용자는 금액을 입력한다.
2. 입력 금액이 1000원 단위인지, 올바른 숫자 값인지 검증한다.
    - 로또 금액기(로또 금액을 저장)에게 금액을 전달해서 나누어 떨어지는지 요청한다.
3. 사용자는 당첨번호를 입력한다.
4. 입력한 당첨번호가 1~45 범위 내에 있는지와 6개를 전부 입력했는지 검증한다.
5. 사용자는 보너스번호를 입력한다.
6. 입력한 보너스 번호가 1~45 범위 내에 있는지와 올바른 숫자 값인지, 6개를 전부 입력했는지 검증한다.
- 숫자 값을 검증하는 로직은 **재사용할 수 있다.**
- 번호가 1~45 내에 있는지를 검증하는 로직은 **재사용할 수 있다.**
- 6개를 전부 입력했는지를 검증하는 로직은 **재사용할 수 있다.**

### 게임 실행
1. 입력한 금액을 전달받아 **입력한 금액을 로또 금액으로 나누어 개수를 반환한다.**
2. 로또 생성기는 전달받은 로또 생성 개수만큼 로또를 생성해서 저장한다.(일급 컬렉션)
3. 컨트롤러는 생성된 로또, 당첨번호, 보너스 번호를 서비스에게 전달하여 비교 수행을 요청한다.
4. 컨트롤러는 반환받은 결과 테이블을 `OutputView`에 전달한다.

### 출력
1. 컨트롤러는 `입력`에서 진행된 로또 번호들을 `OutputView`에 전달한다.
2. `OutputView`는 전달받은 로또 번호들을 출력한다.
3. 컨트롤러는 `LottoService`로부터 전달받은 결과 테이블을 `OutputView`에 전달한다.
4. `OutputView`는 전달받은 결과 테이블을 출력한다.
5. 컨트롤러는 `입력`에서 전달받은 금액과 `LottoService`로부터 전달받은 총 상금을 `OutputView`에 전달한다.
6. `OutputView`는 소수점 둘째 자리에서 반올림한 수익률을 출력한다.

## 기능 목록
### View
1. InputView (입력을 받고, 예외 발생 시 다시 입력 받기)
   -  [x] 사용자에게 금액을 입력받는다.
     -  [x] 숫자가 아닌 경우, 재입력
     - Casher에게 로또 발행 개수 계산 요청한다.
       -  [x] 금액이 부족하거나, 로또 금액으로 나누어 떨어지지 않으면 재입력한다.
       -  [x] 금액 값이 음수일 경우, 최소 금액 에러 메시지를 반환한다.
   - [x] 컨트롤러에게 입력받은 금액을 전달한다. 
   - [x] 당첨번호와 보너스 번호를 입력받는다. 
     - [x] 당첨번호가 숫자가 아닐 경우, 재입력한다. 
     - [x] 보너스 번호가 숫자가 아닐 경우, 재입력한다. 
   - [x] 번호를 입력하지 않았을 경우, 번호를 입력하라는 메시지를 출력한다.
     - [x] 정규식 공부한 후, validate 메서드 수정
2. OutputView (전달받은 값을 출력)
   - [x] 전달받은 생성 로또번호를 출력한다.
   - [x] 전달받은 결과 테이블을 바탕으로 `당첨 내용`,`당첨금`,`당첨 개수`를 출력한다.
   - [x] 컨트롤러에게 전달받은 총 상금과 입력 금액을 바탕으로 `총 수익률`을 출력한다.
     - [x] 소수점 둘째 자리에서 반올림한 값을 출력한다.
     - [x] 세자리 씩 반점으로 구분해서 출력해야 한다. (ex. 1,000,000.0%)
### Model
1. Lotto
    - [x] 1~45 범위 내의 숫자 6개를 리스트로 갖고 있다.
    - 로또 생성 시, 검증 목록
        - [x] 숫자가 6개인지
        - [x] 6개의 숫자 중 중복된 숫자가 있는지
    - [ ] 로또 번호 생성 시 **오름차순**으로 정렬하여 생성한다.
      - [x] 테스트 과정에서 예외 발생, `OutputView`에서 오름차순 정렬로 출력하도록 변경한다.
2. Winning
    - [x] 당첨 번호를 갖고 있다.
        - [x] 당첨 번호는 6개이며, 각 숫자는 1 ~ 45 범위 내에 있어야 한다.
        - [x] 각 번호는 중복되어선 안된다.
    - [x] 테스트 코드로 검증한다.
    - [ ] 로또를 **상속**받아서 코드를 재사용한다.
      - [x] 상속 대신에 로또 클래스를 멤버로 갖는 조립 방식을 사용한다.
3. Bonus
    - [x] 보너스 번호를 갖고 있다.
    - [x] 보너스 번호는 1 ~ 45 범위 내에 있어야 한다.
    - [x] 보너스 번호는 당첨 번호와 겹쳐서는 안된다.
### Service
1. LottoService
    - [x] `LottoCreator`를 통해 로또를 생성한다.
    - [x] `LottoController`로부터 전달받은 당첨번호, 보너스 번호, 로또 번호를 비교하여 결과 값을 반환한다.
      - [x] 0개인 경우에도 결과 테이블에 저장한다.
    - [x] `LottoController`로부터 전달받은 당첨 테이블로 총 상금을 계산하여 반환한다.
### Controller
1. LottoController
   - [x] 컨트롤러는 View와 Service 로직에 의존한다.
   - [x] 컨트롤러는 사용자로부터 로또를 실행할 수 있는 인터페이스만 공개해야 한다.
   - [x] 컨트롤러는 Service로부터 전달받은 값을 OutputView에게 전달하여 결과를 출력하도록 요청한다.
### Utils
1. Casher
    - [x] 입력받은 금액을 바탕으로 로또 금액보다 적은지, 로또 금액으로 나누어 떨어지는지 검증한다.
    - [x] 검증을 통과하면 로또 발행 개수를 반환한다.
    - [x] 테스트 코드로 검증한다.
2. LottoCreator
    - [x] 입력받은 개수만큼 로또를 생성한다.
    - [x] 생성된 로또를 반환한다.
      - [x] 테스트 코드로 검증한다.
#### Enum
1. Code
    - [x] 에러 메시지 출력에 사용되는 공통적인 값들은 Enum으로 관리한다.
2. LottoCount
    - [x] 로또 비교 결과에 대한 상수를 관리한다.
    - [x] 각 Enum 상수는 `일치 내용`,`일치 개수`,`당첨금`을 갖고 있다. 
3. Message
    - [x] View에서 출력하는 메시지들을 관리한다.

## 클래스 설계도
<img src="diagram.png">
