# 예제 3. 4bits Full Adder — 계층 회로도(Hierarchical Schematic)

4비트 덧셈기를 만들면서 **계층 회로도**를 익힌다.
반가산기(Half Adder)를 한 장의 시트로 만들고, 이를 두 번 써서 전가산기(Full Adder)를 만든 다음, 전가산기를 네 번 써서 4비트 덧셈기를 만든다.
같은 회로를 여러 번 복사해 그리지 않고 **한 번 그린 시트를 재사용**하는 것이 핵심이다.

<table>
    <tr><td colspan="2"><img src="../images/1bitHalfAdder.png" alt="반가산기 시트" width="480" height="358"></td></tr>
    <tr><td colspan="2"><img src="../images/1bitFullAdder.png" alt="전가산기 시트" width="760" height="207"></td></tr>
    <tr><td><img src="../images/4bitsFullAdder.png" alt="4비트 덧셈기 최상위 시트" width="362" height="255"></td><td><img src="../images/4bitsFullAdder_PCB.png" alt="4비트 덧셈기 PCB (배선 완성본)" width="397" height="255"></td></tr>
</table>

> 이 예제는 [예제 1](../10_Simple_LED/)을 마쳤다고 가정한다. 회로도 작성의 기본 조작(심볼 배치, 배선, 레이블)은 예제 1을 참고한다.
> 회로도에는 입력 스위치, 출력 LED, 전원까지 들어 있고, PCB는 부품 배치와 넷 연결까지 되어 있다. **배선은 직접 해 본다**([6장](#6-실제-보드로-만들기)).

---

## 1. 폴더 구성

| 파일 | 설명 |
|---|---|
| `4bitsFullAdder.kicad_pro` | 프로젝트 파일이다. 이 파일을 연다. |
| `4bitsFullAdder.kicad_sch` | **최상위(root) 시트**. 전가산기 시트 4개(`FA0`~`FA3`)와 입출력 버스, 그리고 전원·입력 스위치·출력 LED 회로가 있다. |
| `FullAdder.kicad_sch` | **전가산기 시트**. 반가산기 시트 2개(`HA1`, `HA2`)와 OR 게이트(74HC32)로 이루어진다. |
| `HalfAdder.kicad_sch` | **반가산기 시트**. AND 게이트(74HC08)와 XOR 게이트(74HC86)로 이루어진다. |
| `4bitsFullAdder.kicad_pcb` | PCB. 부품 배치와 넷 연결까지 되어 있고 배선은 되어 있지 않다. |

## 2. 덧셈기의 원리

### 2-1. 반가산기 (Half Adder)
1비트 두 개(A, B)를 더해 합(S)과 올림(C)을 낸다.

| A | B | S (합) | C (올림) |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

```
S = A ⊕ B   (XOR, 74HC86)
C = A · B   (AND, 74HC08)
```

### 2-2. 전가산기 (Full Adder)
아랫자리에서 올라온 올림(Cin)까지 1비트 세 개를 더한다. 반가산기 2개와 OR 게이트 1개로 만든다.

```
HA1:  S1 = A ⊕ B,        C1 = A · B
HA2:  Sum = S1 ⊕ Cin,    C2 = S1 · Cin
Carry = C1 + C2          (OR, 74HC32)
```

```
 A ──┐         S1 ┌─────┐ S ──────────────── Sum
     │ HA1 ├──────┤ HA2 │
 B ──┘     │  Cin ─┤     │ C ──┐
           │       └─────┘     ├── OR ─────── Carry
           └── C1 ─────────────┘
```

### 2-3. 4비트 덧셈기 (Ripple Carry Adder)
전가산기 4개를 이어 붙여, 각 자리의 올림(Carry)을 다음 자리의 Cin으로 넘긴다.

```
          A0 B0      A1 B1      A2 B2      A3 B3
           │  │       │  │       │  │       │  │
 GND(0) → [ FA0 ] → [ FA1 ] → [ FA2 ] → [ FA3 ] → Carry (최종 올림)
             │          │          │          │
           Sum0       Sum1       Sum2       Sum3
```

- 맨 아랫자리 `FA0`의 Cin은 `GND`(0)에 연결한다.
- 올림이 오른쪽 자리로 차례차례 전달된다고 해서 **리플 캐리(ripple carry) 덧셈기**라고 부른다.

---

## 3. 계층 회로도 이해하기

### 3-1. 시트 구조
```
4bitsFullAdder.kicad_sch  (최상위)
├── FA0 ─ FullAdder.kicad_sch ─┬─ HA1 ─ HalfAdder.kicad_sch
│                              └─ HA2 ─ HalfAdder.kicad_sch
├── FA1 ─ FullAdder.kicad_sch   (같은 구조)
├── FA2 ─ FullAdder.kicad_sch   (같은 구조)
└── FA3 ─ FullAdder.kicad_sch   (같은 구조)
```
시트 **파일**은 3개뿐이지만 시트 **인스턴스(사용 횟수)** 는 최상위 1 + 전가산기 4 + 반가산기 8 = 13개이다.

### 3-2. 핵심 개념

| 개념 | 설명 | 이 예제에서 |
|---|---|---|
| **시트 기호 (Sheet Symbol)** | 상위 시트에 놓는 사각형 상자이다. 하위 시트 파일을 가리킨다. | 최상위의 `FA0`~`FA3`, 전가산기 안의 `HA1`, `HA2` |
| **계층 레이블 (Hierarchical Label)** | 하위 시트 안에서 "이 신호를 밖으로 내보낸다/받는다"를 나타낸다. | 반가산기의 `A`, `B`, `S`, `C` / 전가산기의 `A`, `B`, `C`, `Sum`, `Carry` |
| **시트 핀 (Sheet Pin)** | 시트 기호 테두리에 나타나는 핀이다. 하위 시트의 계층 레이블과 **이름이 같아야** 연결된다. | 시트 기호 양옆의 입력/출력 핀 |
| **버스 (Bus)** | 여러 신호를 하나의 굵은 선으로 묶는다. `A[3..0]`은 `A0`, `A1`, `A2`, `A3` 네 신호를 뜻한다. | 최상위의 `A[3..0]`, `B[3..0]`, `Sum[3..0]` |
| **버스 엔트리 (Bus Entry)** | 버스에서 개별 신호를 꺼내는 짧은 사선이다. 꺼낸 선에는 `A0` 같은 레이블을 붙인다. | 최상위의 12개 |

### 3-3. 시트 재사용과 부품 번호
같은 시트 파일을 여러 번 쓰면 **회로는 하나를 공유하지만 부품 번호는 인스턴스마다 따로** 붙는다.
예를 들어 `HalfAdder.kicad_sch`의 AND 게이트는 한 번만 그렸지만, 8개 인스턴스는 각각 다른 IC와 게이트(unit)에 배정된다.

| 인스턴스 | AND (74HC08) | XOR (74HC86) |
|---|---|---|
| FA0 / HA1, HA2 | U1A, U1B | U3A, U3B |
| FA1 / HA1, HA2 | U1C, U1D | U3C, U3D |
| FA2 / HA1, HA2 | U2A, U2B | U4A, U4B |
| FA3 / HA1, HA2 | U2C, U2D | U4C, U4D |

전가산기의 OR 게이트 4개는 `U5A`~`U5D`(74HC32)이다. 한 IC에 게이트가 4개씩 들어 있으므로 게이트 20개가 **IC 5개**에 담긴다. 각 IC의 전원 핀(VCC 14번, GND 7번)은 별도 unit `E`로, 최상위 시트에 배치되어 있다.
그래서 반가산기 시트를 고치면 **8곳 모두에 한꺼번에 반영된다.**

---

## 4. 예제 살펴보기

1. `4bitsFullAdder.kicad_pro`를 열고 회로도 편집기를 연다.
2. 왼쪽 **계층 탐색기(Hierarchy Navigator)** 에서 시트 트리를 확인한다. 보이지 않으면 **View → Show Hierarchy Navigator** 를 켠다.
3. 시트 기호(`FA0`)를 **더블클릭**하면 하위 시트로 들어간다. 계층 탐색기에서 최상위를 클릭하면 다시 올라온다.
4. `FA0` 안의 `HA1`로 들어가 부품 번호를 확인하고, 다시 나와 `FA1` 안의 `HA1`로 들어가 번호가 다른지 비교한다.
5. 한 신호가 어디로 이어지는지 보려면 선을 클릭한 뒤 **`` ` `` (백틱) 키**로 넷 전체를 강조 표시한다.

---

## 5. 처음부터 직접 만들어 보기

아래(반가산기)에서 위(4비트)로 올라가며 만든다.

### 5-1. 반가산기 시트 만들기
1. 새 프로젝트 `My_Adder`를 만든다. 최상위 시트는 잠시 비워 둔다.
2. **Place → Add Hierarchical Sheet** 를 선택하고 최상위 시트에 사각형을 그린다.
   창이 뜨면 **Sheet name**은 `HA`, **File name**은 `HalfAdder.kicad_sch`로 입력한다.
3. 시트 기호를 더블클릭해 들어간 뒤, AND 게이트와 XOR 게이트를 배치한다. 심볼은 `74xx:74LS08`, `74xx:74LS86`을 쓰고 Value를 `74HC08`, `74HC86`으로 바꾼다(핀 배치가 같다).
4. **Place → Add Hierarchical Label** 로 입력 `A`, `B`(Shape: Input)와 출력 `S`, `C`(Shape: Output)를 배치하고 게이트에 연결한다.
   - `A`, `B` → AND와 XOR의 입력에 각각 연결
   - XOR 출력 → `S`, AND 출력 → `C`

### 5-2. 시트 핀 동기화
1. 최상위로 돌아오면 시트 기호에는 아직 핀이 없다.
2. 시트 기호를 오른쪽 클릭 → **Sync Sheet Pins** 를 실행하고, 하위 시트의 계층 레이블을 시트 핀으로 가져온다.
3. 핀을 시트 테두리의 원하는 위치(입력은 왼쪽, 출력은 오른쪽)로 옮긴다.

> 시트 핀과 계층 레이블은 **이름이 한 글자라도 다르면 연결되지 않는다.** 대소문자도 구분한다.

### 5-3. 전가산기 시트 만들기
1. 최상위에 새 시트 기호 `FA`(파일 `FullAdder.kicad_sch`)를 만들고 들어간다.
2. 전가산기 시트 안에 시트 기호를 2개 만들고, 둘 다 **File name을 `HalfAdder.kicad_sch`** 로 지정한다. 이름은 `HA1`, `HA2`로 한다.
   - 이미 있는 파일 이름을 입력하면 KiCad가 "기존 시트를 사용할지" 묻는다. **사용한다**를 선택한다.
3. `74HC32`(OR, 심볼은 `74xx:74LS32`)를 배치하고, [2-2절](#2-2-전가산기-full-adder)의 식대로 연결한다.
   - `A`, `B` → HA1 입력
   - HA1의 **S** 와 `C`(Cin) → HA2 입력
   - HA2의 **S** → `Sum`
   - HA1의 **C** 와 HA2의 **C** → OR 입력, OR 출력 → `Carry`
4. 계층 레이블 `A`, `B`, `C`(입력)와 `Sum`, `Carry`(출력)를 붙인다.
5. 반가산기 시트 기호는 최상위에서 지운다. 반가산기는 이제 전가산기 안에서만 쓴다.

### 5-4. 최상위 시트 완성
1. 최상위에 전가산기 시트 기호 4개(`FA0`~`FA3`, 모두 `FullAdder.kicad_sch`)를 배치한다. 하나를 만들고 복사(`Ctrl+C`, `Ctrl+V`)해도 된다.
2. 올림을 연결한다. `FA0`의 `C` → `GND`, `FA0`의 `Carry` → `FA1`의 `C`, … `FA2`의 `Carry` → `FA3`의 `C`
3. **Place → Add Bus** 로 버스를 그리고, 버스에 전역 레이블 `A[3..0]`을 붙인다. `B[3..0]`, `Sum[3..0]`도 같은 방법으로 만든다.
4. **Place → Add Wire to Bus Entry** 로 버스에서 선을 꺼내고, 각 선에 `A0`, `A1`, … 레이블을 붙여 각 전가산기의 핀에 연결한다.
5. 마지막 자리의 `Carry`에는 `Cout` 같은 레이블을 붙여 최종 올림으로 쓴다.

### 5-5. 번호 매기기와 검사
1. **Tools → Annotate Schematic** 에서 범위를 **Entire schematic**(전체 회로도)으로 두고 실행한다. 모든 인스턴스에 서로 다른 번호가 붙는다.
2. **Inspect → Electrical Rules Checker** 로 ERC를 실행한다.
3. 진리표로 동작을 확인한다. 예) `A = 0011(3)`, `B = 0101(5)` → `Sum = 1000(8)`, `Cout = 0`

---

## 6. 실제 보드로 만들기

### 6-1. 회로도에 추가된 부분 (최상위 시트)
덧셈기만으로는 실제 보드가 동작하지 않는다. 최상위 시트에 다음을 추가했다.

| 부품 | 역할 |
|---|---|
| `J1` (2핀 헤더) + `PWR_FLAG` | 5 V 전원 입력. `PWR_FLAG`는 ERC에 전원이 공급됨을 알린다. |
| `U1E`~`U5E` + `C1`~`C5` (100 nF) | 각 IC의 전원 unit과 **디커플링 커패시터**. 커패시터는 IC 전원 핀 가까이 둔다. |
| `SW1` (8비트 DIP 스위치) + `RN1` (10 kΩ ×8) | 입력 A0~A3, B0~B3. 스위치를 켜면 VCC(1), 끄면 풀다운 저항으로 GND(0)가 된다. |
| `R1`~`R5` (1 kΩ) + `D1`~`D5` (LED) | 출력 Sum0~Sum3, Cout 표시. 출력이 1이면 LED가 켜진다. |
| `J2` (6핀 헤더) | 출력 Sum0~Sum3, Cout, GND. Analog Discovery 2 같은 장비로 측정할 수 있다. |

> **왜 74HC인가?** 74LS 계열은 출력이 High일 때 흘릴 수 있는 전류가 작아(0.4 mA) LED를 직접 켜기 어렵다. 74HC 계열은 High/Low 모두 수 mA를 낼 수 있고, 입력에 큰 풀다운 저항(10 kΩ)을 써도 된다. 74HC08/86/32는 74LS08/86/32와 핀 배치가 같다.

### 6-2. PCB 배선하기
`4bitsFullAdder.kicad_pcb`를 열면 부품이 배치되어 있고 래츠네스트(가는 선)가 연결할 곳을 보여 준다. 보드 크기는 110 mm × 70 mm이고, 뒷면(`B.Cu`)에는 GND 동박 영역이 있다.

1. [예제 1의 4-7절](../10_Simple_LED/README.md#4-처음부터-직접-만들어-보기)처럼 `X`로 배선한다. 전원(VCC)은 0.5 mm 이상, 신호는 0.25 mm 정도로 한다.
2. GND는 뒷면 동박 영역으로 연결되므로, 앞면(`F.Cu`)에 신호와 VCC를 먼저 배선하고 막히는 곳만 비아로 뒷면을 쓴다.
3. 배선이 끝나면 `B`로 동박 영역을 다시 채우고, **Inspect → Design Rules Checker** 에서 **Unconnected Items가 0개**가 될 때까지 반복한다.

> 회로도를 고친 뒤에는 **Tools → Update PCB from Schematic** (`F8`)으로 PCB에 반영한다. 부품 배치는 그대로 유지된다.

**참고용 완성본** — `4bitsFullAdder_routed.kicad_pcb`는 같은 배치를 자동 배선 프로그램 [Freerouting](https://github.com/freerouting/freerouting)으로 배선한 파일이다(DRC 오류 0개, 미연결 0개). 직접 배선한 뒤 비교해 본다. 프로젝트 창이 아니라 PCB 편집기의 **File → Open** 으로 연다.

| 규칙 | 값 |
|---|---|
| 신호선 (Default 넷 클래스) | 폭 0.3 mm, 간격 0.25 mm |
| 전원선 (Power 넷 클래스: VCC, GND) | 폭 0.6 mm |
| 비아 | 지름 0.8 mm / 드릴 0.4 mm |

넷 클래스는 **File → Board Setup → Net Classes** 에서 확인할 수 있다.

### 6-3. 더 생각해 볼 점
- **시뮬레이션** — 이 예제에는 SPICE 설정이 없다. 74LS 게이트 시뮬레이션은 [70_Simulation/HalfAdder](../70_Simulation/HalfAdder/)를 참고한다.
- **리플 캐리의 지연** — 올림이 FA0에서 FA3까지 차례로 전달되므로 자릿수가 늘수록 결과가 늦게 나온다. 이를 줄인 것이 캐리 예측 덧셈기(carry-lookahead adder, 예: 74HC283)이다.

## 7. 자주 하는 실수

| 증상 | 원인과 해결 |
|---|---|
| 시트 핀이 연결되지 않았다는 ERC 오류 | 시트 핀과 하위 시트의 계층 레이블 이름이 다르다. **Sync Sheet Pins** 로 다시 맞춘다. |
| 부품 번호가 `U?`로 남거나 중복됨 | 번호 매기기 범위를 **Entire schematic** 으로 두고 다시 실행한다. |
| 한 시트를 고쳤는데 다른 곳도 바뀜 | 같은 시트 파일을 공유하기 때문이다(정상 동작). 따로 고치려면 파일을 복사해 다른 이름으로 지정한다. |
| 버스 신호가 연결되지 않음 | 버스 레이블(`A[3..0]`)과 개별 레이블(`A0`)의 이름 규칙이 맞는지 확인한다. |

## 8. 다음 단계
- [30_Arduino](../30_Arduino/): Arduino 관련 예제
- [Docs](../Docs/): *Schematic Editor* 매뉴얼의 계층 회로도(Hierarchical Schematics) 장
