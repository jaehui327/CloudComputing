# 8주차 과제
## NVP와 Recovery Block 기법을 간략히 요약하고 각각의 장단점을 논하시오.
분산 시스템은 자연스럽게 여러 독립된 컴퓨터들이 연결되어서 하나의 시스템처럼 쓸 수 있게끔 만들어진 것이기 때문에 일부 시스템들이 망가지더라도 failure가 발생하더라도 전체 시스템이 다 망가지지 않을 수 있는 가능성이 있습니다. 시스템의 일부분이 고장이 나서 못쓰게 되더라도 시스템의 전체 성능에 심각한 영향을 미치지 않는 상황에서 자동적으로 복구를 시키는 것이 결함 허용의 목표입니다. 결함 허용을 통해 복구를 시키기 위해서는 복제를 쓰는 것이 유일한 방법입니다. 따라서 여분의 자원을 이용해서 결함을 걸러내는 작업을 수행해야 합니다. NVP와 Recovery Block은 고전적으로 가장 대표적인 Fault Tolerance (결함 허용) 기법입니다. <br><br>

### 1. N-version programming NVP

  처음 나온 것은 1970년대로 고전적인 기법으로 디자인 다양성을 중요하게 생각하여 multi-version을 이용하는 방법입니다. Software fault가 발생하는 것을 대비해서 다양한 여러 버전의 소프트웨어를 개발하게 됩니다. Quorum-based 라고 칭하기도 하는데 그 이유는 종족수를 따져서 과반을 넘는 결과물이 옳은 결과인지를 voting하기 때문입니다.<br><br>
별도의 컴퓨터에서 동시에 값은 입력 값을 받아서 동시에 실행을 시키고 마치 TMR처럼 Voting을 하게 됩니다. Voter 또한 multi-version으로 사용할 수도 있지만 공식적으로는 한 버전을 사용합니다. 그 이유는 Voter는 상대적으로 로직이 간단하기 때문에 fault가 발생할 확률이 적기 때문입니다. <br><br>
여러 버전의 소프트웨어가 결과값을 내면 그 결과값을 비교해서 과반 이상의 결과값이 나오는 것을 전체의 시스템의 결과값으로 내놓게 됩니다. 동시에 소프트웨어 여러 개를 한꺼번에 다 돌려서 선출하는 방식입니다. <br><br><br>

   결함 허용에 대한 신뢰성이 높다는 것이 **장점**입니다. 여러 버전의 software에서의 결과값을 도출하여 그 결과값들에서의 voting을 진행하게 되므로 복구율이 높다고 할 수 있습니다. <br><br>
그러나 **단점**으로 여러 시스템을 동시에 수행하기 때문에 비용이 많이 들 수 있다는 점이 있습니다. Multi-version 의 software를 구성하고 또 그 시스템을 동시에 수행해야 하기 때문에 비용이 많이 소모될 수 있습니다.<br><br><br>


### 2. Recovery Block

  Recovery Block 또한 1970년대에 나온 고전적이고 대표적인 기법으로 Try-block을 사용하게 됩니다. 여러 다양한 version의 software를 제작하는데 NVP와 다르게 동시에 진행하는 것이 아니라 한번에 하나씩 software를 순차적으로 실행시키게 됩니다.<br><br>
 제일 처음 시도하는 블럭을 Primary Block이라 하고 다음 순서로 Backup을 두게 됩니다. 따라서 이 기법은 Primary-based라고도 칭하게 됩니다.<br><br>
제일 처음 Block이 오면 Primary Block으로 시도를 해보게 됩니다. 그 결과값을 voting이 아닌 Acceptance Test (수용성 검사)를 수행하게 됩니다. 이는 그 결과값을 수용할 수 있는지, 받아들일 수 있는지에 대한 타당성/수용성 검사를 수행하는 것입니다. 결과값이 타당하다면 다음 입력 값을 받아서 계속 진행하게 됩니다.<br><br>
Primary block이 낸 결과값이 이상이 없을 경우는 계속 진행이 되지만 만약 그 결과값이<br>
1.	논리적으로 말이 되지 않고 이상한 값이 나올 경우
2.	값이 나와야 할 시간이 지났는데 결과값을 산출해 내지 못할 경우<br><br>
위 두가지 경우에 해당할 경우 오류가 발생된 것으로 판단하게 됩니다. 이럴 경우에는 Primary Block을 포기하고 그 다음 Backup Block 으로 Re-Try 를 진행하게 됩니다. 결론적으로 Recovery Block 기법은 여러 Primary Back Up들이 준비가 되어 있고 문제가 있을 경우 준비 되어 있는 backup version을 활용해서 재-시도를 해서 사용하는 방식으로 정리할 수 있습니다.<br><br><br>

**장점**으로는 여러 시스템을 동시에 수행하는 것이 아니라 한번에 하나씩 순차적으로 실행하기 때문에 비용을 절감할 수 있게 됩니다. NVP에서는 multi-software로 인한 비용 증가 문제가 있었는데 그를 해결할 수 있습니다.<br><br>
그러나 Primary block이 오류가 발생했을 때 Re-Try 를 해야하므로 시간이 소모될 수 있다는 것이 **단점**입니다. 수용성 검사 (Acceptance Test) 를 구현할 때 보통은 시간과 논리도 검사를 수행하게 되는데 이는 정해진 시간 안에 결과값이 오는지, 나온 결과값이 입력 값 대비 문제가 없는지 따지는 것을 의미합니다. 이 검사가 Voter 를 사용하는 방법보다 더 복잡하다는 단점도 있습니다. 또 수용성 검사 자체에 문제가 있을 가능성도 있습니다. <br><br>




