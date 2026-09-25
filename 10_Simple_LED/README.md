# 예제 1. Simple LED — 회로도에서 PCB까지

코인 전지로 LED 하나를 켜는 가장 간단한 회로이다.
부품은 3개뿐이지만 **회로도 작성 → 풋프린트 지정 → PCB 배치·배선 → 제작 파일(거버) 출력**까지 KiCad의 전체 흐름을 한 번에 경험할 수 있다.

<table>
    <tr>
        <td><img src="../images/Simple_LED_schematic.png" alt="Simple LED 회로도" width="276" height="400"></td>
        <td><img src="../images/Simple_LED_PCB.png" alt="Simple LED PCB" width="400" height="400"></td>
    </tr>
</table>

> KiCad 10을 기준으로 설명한다. KiCad를 처음 쓴다면 [Docs](../Docs/)의 *Getting Started in KiCad*를 함께 보면 좋다.

---

## 1. 폴더 구성

| 파일 | 설명 |
|---|---|
| `Simple_LED.kicad_pro` | 프로젝트 파일이다. **이 파일을 열면** 회로도와 PCB를 모두 다룰 수 있다. |
| `Simple_LED.kicad_sch` | 회로도 |
| `Simple_LED.kicad_pcb` | PCB 레이아웃 |
| `gerber/` | PCB 제작 업체에 보내는 제작 파일(거버, 드릴) |
| `Simple_LED-backups/`, `*.kicad_prl`, `fp-info-cache` | KiCad가 자동으로 만드는 백업/설정/캐시 파일이다. 신경 쓰지 않아도 된다. |

## 2. 회로 이해하기

전지(+) → 저항 → LED → 전지(−) 순서로 전류가 흐른다. 저항은 LED에 흐르는 전류를 제한한다.

| 참조 번호 | 부품 | 심볼 | 풋프린트 |
|---|---|---|---|
| BT1 | CR2032 코인 전지 (3 V) | `Device:Battery_Cell` | `Battery:BatteryHolder_Keystone_1058_1x2032` |
| R1 | 저항 | `Device:R` | `Resistor_THT:R_Axial_DIN0309_L9.0mm_D3.2mm_P12.70mm_Horizontal` |
| D1 | 5 mm LED | `Device:LED` | `LED_THT:LED_D5.0mm` |

회로도에는 부품 외에 다음 요소도 쓰였다.
- **전원 심볼** `VCC`, `GND`: 이름이 같은 전원 심볼끼리는 선을 그리지 않아도 연결된다.
- **넷 레이블** `led`: 저항과 LED 사이 연결선에 붙인 이름이다. PCB에서 이 이름(`/led`)으로 연결을 확인할 수 있다.
- **PWR_FLAG** 2개: "이 넷에 전원이 공급된다"는 사실을 ERC(전기 규칙 검사)에 알려 준다. 없으면 ERC가 *Input Power pin not driven* 오류를 낸다.

### 저항값 정하기
예제 파일의 R1 값은 `R`로 비어 있다. 직접 계산해서 넣어 본다.

```
R = (전지 전압 − LED 순방향 전압) / LED 전류
  = (3 V − 2 V) / 10 mA
  = 100 Ω
```

빨간 LED의 순방향 전압은 약 2 V이다. CR2032는 큰 전류를 오래 내지 못하므로 **100 Ω ~ 330 Ω** 사이가 적당하다.

---

## 3. 예제 열어 보기

1. KiCad를 실행하고 **File → Open Project**에서 `Simple_LED.kicad_pro`를 연다.
2. 프로젝트 창에서 **Schematic Editor**(회로도)와 **PCB Editor**(PCB)를 각각 열어 본다.
3. PCB 편집기에서 **View → 3D Viewer** (`Alt+3`)로 완성된 보드를 입체로 확인한다.

> 예제 파일은 KiCad 8에서 만들어졌다. KiCad 10에서 열면 형식 변환 안내가 나오는데, 그대로 진행하면 된다. 저장하면 KiCad 10 형식으로 바뀌어 **KiCad 8에서는 다시 열 수 없다.**

---

## 4. 처음부터 직접 만들어 보기

예제를 보기만 하는 것보다 직접 한 번 만들어 보는 편이 훨씬 빨리 익혀진다. 아래 순서에 따라 새 프로젝트로 똑같은 보드를 만들어 본다.

### 4-1. 새 프로젝트 만들기
1. **File → New Project** 를 선택하고, 새 폴더에 `My_LED` 같은 이름으로 저장한다.
2. 프로젝트 창에 `.kicad_pro`, `.kicad_sch`, `.kicad_pcb` 파일이 만들어졌는지 확인한다.

### 4-2. 회로도 그리기 (Schematic Editor)
자주 쓰는 단축키: `A` 심볼 추가, `P` 전원 심볼 추가, `W` 배선, `L` 넷 레이블, `M` 이동, `G` 끌기(연결 유지), `R` 회전, `E` 속성 편집, `Del` 삭제, `Esc` 취소

1. **부품 배치** — `A`를 누르고 검색창에 `Battery_Cell`, `R`, `LED`를 차례로 검색해 배치한다.
2. **전원 심볼 배치** — `P`를 누르고 `VCC`와 `GND`를 배치한다. `PWR_FLAG`도 2개 배치한다.
3. **배선** — `W`를 누르고 핀 끝(작은 원)을 클릭해 선을 그린다. 연결이 끝나면 더블클릭하거나 `Esc`를 누른다.
   - 전지(+) → `VCC`, 전지(−) → `GND`
   - `VCC` → 저항 한쪽, 저항 다른 쪽 → LED 애노드(A, 삼각형 쪽)
   - LED 캐소드(K, 막대 쪽) → `GND`
   - `PWR_FLAG` 하나는 `VCC`에, 다른 하나는 `GND`에 연결
4. **넷 레이블** — 저항과 LED 사이 선 위에서 `L`을 누르고 `led`를 입력해 붙인다.
5. **값 입력** — 저항 위에서 `E`를 눌러 Value를 `100`처럼 계산한 값으로 바꾼다.
6. **부품 번호 매기기** — **Tools → Annotate Schematic** 을 실행하면 `R?`에 `R1`처럼 번호가 붙는다.
7. **전기 규칙 검사(ERC)** — **Inspect → Electrical Rules Checker** 를 실행한다. 오류가 0개가 될 때까지 고친다.
   - 핀이 연결되지 않았다는 오류: 선 끝이 핀 끝에 정확히 닿았는지 확인한다.
   - *Input Power pin not driven*: `PWR_FLAG`가 빠지지 않았는지 확인한다.

### 4-3. 풋프린트 지정
회로도 심볼은 부품의 "기능"을, 풋프린트는 부품이 PCB에 놓일 "실제 모양(패드 위치와 크기)"을 나타낸다.

1. **Tools → Assign Footprints** 를 연다.
2. 왼쪽 목록에서 라이브러리를 고르고, 오른쪽 목록에서 풋프린트를 더블클릭해 지정한다. 위 [2장 표](#2-회로-이해하기)의 풋프린트를 그대로 쓰면 된다.
3. **OK**를 누르고 회로도를 저장한다.

> 풋프린트를 고를 때는 **실제로 구입할 부품의 데이터시트** 치수와 맞는지 확인하는 습관을 들인다.

### 4-4. PCB로 넘기기 (PCB Editor)
자주 쓰는 단축키: `M` 이동, `R` 회전, `F` 앞/뒷면 뒤집기, `X` 배선, `V` 배선 중 비아 추가, `D` 배선 끌기, `B` 동박 영역 채우기, `Del` 삭제

1. PCB 편집기를 열고 **Tools → Update PCB from Schematic** (`F8`) 을 실행한 뒤 **Update PCB** 를 누른다. 부품들이 커서에 붙어 나오면 빈 곳을 클릭해 내려놓는다.
2. 부품 사이의 가는 선(**래츠네스트, ratsnest**)은 연결해야 할 곳을 보여 준다.

### 4-5. 보드 외곽선 그리기
1. 오른쪽 **Layers** 창에서 `Edge.Cuts` 레이어를 선택한다.
2. **Place → Draw Rectangle** 로 부품을 모두 감싸는 사각형을 그린다. 예제는 **50 mm × 50 mm** 이다.
3. 정확한 크기가 필요하면 사각형을 더블클릭해 좌표를 직접 입력한다.

### 4-6. 부품 배치
1. `M`으로 부품을 옮기고 `R`로 회전해, 래츠네스트가 서로 꼬이지 않게 배치한다.
2. 예제에서는 **전지 홀더(BT1)를 보드 뒷면**에 두었다. BT1을 선택하고 `F`를 누르면 뒷면(B.Cu)으로 뒤집힌다.
3. LED의 극성 표시(평평한 면 = 캐소드)가 실크스크린에 보이는지 확인한다.

### 4-7. 배선
1. 오른쪽 위 레이어 선택에서 `F.Cu`(앞면 동박)를 고르고, 패드 위에서 `X`를 눌러 래츠네스트를 따라 배선한다.
2. 배선 중에 `V`를 누르면 **비아**가 생기고, 반대 면(`B.Cu`)으로 이어서 배선할 수 있다.
3. 예제의 설계 규칙은 **배선 폭 0.2 mm, 간격 0.2 mm, 비아 0.6 mm / 드릴 0.3 mm** 이다. **File → Board Setup → Design Rules** 에서 확인하고 바꿀 수 있다.
   - LED 정도의 작은 전류에는 0.2 mm로 충분하다. 손으로 납땜할 보드라면 0.4 mm 이상으로 넓혀도 좋다.

### 4-8. GND 동박 영역(Copper Zone)
넓은 GND 면을 만들면 GND 배선을 따로 그리지 않아도 되고, 노이즈에도 유리하다.

1. **Place → Add Filled Zone** 을 선택하고 보드 외곽을 따라 클릭한다.
2. 창이 뜨면 레이어는 `B.Cu`, 넷은 `GND`를 고른다.
3. `B`를 눌러 영역을 채운다. GND 래츠네스트가 사라지면 연결된 것이다.

### 4-9. 설계 규칙 검사(DRC)와 3D 확인
1. **Inspect → Design Rules Checker** 에서 **Run DRC** 를 누른다.
2. **Unconnected Items가 0개**이고 오류가 0개인지 확인한다. 오류를 클릭하면 PCB에서 해당 위치로 이동한다.
3. **View → 3D Viewer** (`Alt+3`)로 부품이 제자리에 있는지, 뒷면의 전지 홀더가 잘 보이는지 확인한다.

### 4-10. 제작 파일(거버) 출력
1. **File → Fabrication Outputs → Gerbers (.gbr)** 를 연다.
2. 출력 폴더를 `gerber/`로 지정하고, 다음 레이어가 선택되어 있는지 확인한 뒤 **Plot** 을 누른다.
   `F.Cu`, `B.Cu`, `F.Silkscreen`, `B.Silkscreen`, `F.Mask`, `B.Mask`, `Edge.Cuts` (SMD 부품이 있으면 `F.Paste`, `B.Paste`도 포함)
3. 같은 창에서 **Generate Drill Files…** 를 눌러 드릴 파일(`.drl`)도 만든다.
4. 프로젝트 창의 **Gerber Viewer** 로 `gerber/` 폴더의 파일을 열어 모양이 맞는지 확인한다.
5. `gerber/` 폴더를 압축(zip)하면 PCB 제작 업체에 주문할 수 있다.

---

## 5. 자주 하는 실수

| 증상 | 원인과 해결 |
|---|---|
| 선을 그렸는데 ERC에서 연결되지 않았다고 나옴 | 선 끝이 핀 끝(작은 원)에 닿지 않았다. `G`로 끌어서 다시 붙인다. |
| `Input Power pin not driven` 오류 | `PWR_FLAG`를 `VCC`와 `GND`에 연결한다. |
| PCB에 부품이 나타나지 않음 | 회로도에서 풋프린트를 지정하지 않았다. 4-3을 다시 하고 `F8`을 실행한다. |
| 회로도를 고쳤는데 PCB에 반영되지 않음 | 회로도를 수정한 뒤에는 **항상 `F8`(Update PCB from Schematic)** 을 다시 실행한다. |
| 실제 보드에서 LED가 켜지지 않음 | LED 방향이 반대이다. 긴 다리(애노드)가 저항 쪽, 짧은 다리/평평한 면(캐소드)이 GND 쪽이다. |
| 동박 영역이 비어 보임 | `B`를 눌러 영역을 다시 채운다. |

## 6. 다음 단계
- [20_WheatstoneBridge](../20_WheatstoneBridge/): 시뮬레이션이 포함된 두 번째 PCB 예제
- [Docs](../Docs/): KiCad 공식 설명서 (회로도 편집기, PCB 편집기 매뉴얼)
