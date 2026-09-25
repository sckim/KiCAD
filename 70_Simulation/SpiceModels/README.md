# SpiceModels — 시뮬레이션 모델 모음

시뮬레이션 예제에서 불러 쓰는 SPICE 모델 파일을 모아 둔 폴더이다.

| 파일 | 내용 | 사용하는 예제 |
|---|---|---|
| `74LS.LIB` | 74LS 로직 IC 79종의 디지털 모델(Intusoft). ngspice의 XSPICE 디지털 모델(`d_and`, `d_xor` 등)을 쓴다. 전파 지연만 흉내 내는 **논리 수준 모델** 이다. | [HalfAdder](../HalfAdder/) |
| `2N3904.lib` | NPN 트랜지스터 2N3904의 Gummel-Poon 모델(널리 쓰이는 Fairchild/ON Semi 파라미터) | [TransistorBias](../TransistorBias/) |
| `SN74LS08.cir`, `SN74LS86A.cir` | TI가 배포한 74LS08(AND), 74LS86(XOR)의 **아날로그 동작 모델**. 전원 핀(VCC, GND)과 소비 전류까지 흉내 낸다. | (현재 예제에서는 쓰지 않음) |

## 모델을 부품에 연결하는 방법

1. 부품을 더블클릭(`E`) → **Simulation Model…** 을 연다.
2. **SPICE model from file** 을 고르고 **Library** 에 파일 경로를 적는다. 폴더를 옮겨도 동작하도록 프로젝트 폴더 기준 상대 경로를 쓴다.
   ```
   ${KIPRJMOD}/../SpiceModels/74LS.LIB
   ```
3. **Name** 에서 파일 안의 모델 이름을 고른다. 예) `74LS08`, `74LS86A`, `2N3904`
4. **Pin assignment** 에서 심볼의 핀 번호와 모델의 핀 이름을 짝지어 준다.

| 모델 | 모델의 핀 | 심볼 핀과의 매핑 예 |
|---|---|---|
| `74LS08`, `74LS86A` (`.subckt`) | `in1 in2 out` | 1 = in1, 2 = in2, 3 = out (게이트 A) |
| `2N3904` (`.model NPN`) | C, B, E | KiCad `2N3904` 심볼은 **1 = E, 2 = B, 3 = C** |

> 절대 경로(`C:\...`, `G:\My Drive\...`)를 쓰면 다른 컴퓨터에서 모델을 찾지 못한다. 반드시 `${KIPRJMOD}` 기준 상대 경로를 쓴다.

## 모델을 더 구하려면
- 부품 제조사(TI, onsemi, Analog Devices 등)의 제품 페이지에서 "SPICE model"을 내려받는다.
- KiCad 설치 폴더의 예제와 [ngspice 모델 모음](https://ngspice.sourceforge.io/modelparams.html)도 참고한다.
