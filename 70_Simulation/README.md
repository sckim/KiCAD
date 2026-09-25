# 70_Simulation — 회로 시뮬레이션 예제

KiCad에 내장된 SPICE 시뮬레이터(ngspice)로 회로를 계산해 보는 예제 모음이다. PCB 없이 회로도와 시뮬레이션만 다룬다.
각 예제 폴더에 README가 있으며, 아래 순서대로 보면 좋다.

| 순서 | 예제 | 해석 | 배우는 것 |
|---|---|---|---|
| 1 | [Transient_Analysis](./Transient_Analysis/) | 과도 해석 `.tran` | RC 회로의 충전·방전, 시정수 τ = RC, 펄스 전원 |
| 2 | [Frequency_Analysis](./Frequency_Analysis/) | AC 해석 `.ac` | RC 저역통과·고역통과 필터, 차단 주파수, −20 dB/decade |
| 3 | [AC_Analysis](./AC_Analysis/) | AC 해석 `.ac` | 직렬 RLC 대역통과 필터, 공진 주파수, 대역폭 |
| 4 | [TransistorBias](./TransistorBias/) | 동작점 해석 `.op` | BJT 바이어스, 제조사 SPICE 모델 불러오기, 핀 매핑 |
| 5 | [HalfAdder](./HalfAdder/) | 과도 해석 `.tran` | 디지털 게이트 시뮬레이션, 전파 지연, 해저드 |

모델 파일은 [SpiceModels](./SpiceModels/)에 있다.

## 시뮬레이터 기본 사용법

1. 예제의 `.kicad_pro`를 열고 회로도 편집기에서 **Inspect → Simulator** 를 연다.
2. 저장된 해석 설정(`.wbk` 파일)이 자동으로 열린다. 새로 만들 때는 **Simulation → New Analysis Tab** 을 쓴다.
3. **Run Simulation** (▶)을 누르고 **Add Signals** 로 보고 싶은 전압·전류를 추가한다.
4. 커서를 켜서 그래프의 값을 읽는다.

## 시뮬레이션 회로를 그릴 때 지킬 것

- **접지** — `GND` 또는 `Simulation_SPICE:0` 기호가 반드시 있어야 한다.
- **전원** — 전원 심볼(`VCC`, `12V` 등)은 이름표일 뿐 전압을 만들지 않는다. `Simulation_SPICE:VDC`, `VSIN`, `VPULSE` 같은 **전압원** 을 넣는다.
- **모델** — 저항·커패시터·인덕터는 자동으로 모델이 붙는다. 트랜지스터·IC는 **Simulation Model…** 에서 모델을 지정한다.
- **단위** — SPICE에서 `m`/`M`은 밀리(10⁻³)이다. 메가(10⁶)는 `Meg`로 쓴다.

더 자세한 내용은 [Docs](../Docs/)의 *Schematic Editor* 매뉴얼에서 시뮬레이터 장을 본다.
