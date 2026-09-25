# KiCad 공식 설명서
[docs.kicad.org](https://docs.kicad.org/10.0/en/)에서 받은 KiCad 10.0 공식 설명서이다 (영문, KiCad 10.0.6 기준).
읽는 순서대로 정리했다. 각 설명서는 온라인(HTML)으로도 볼 수 있으며, 온라인판이 검색하기 더 편하다.

## 먼저 읽을 문서
| 설명서 | 내용 |
|---|---|
| [Getting Started in KiCad](./getting_started_in_kicad.pdf) ([온라인](https://docs.kicad.org/10.0/en/getting_started_in_kicad/getting_started_in_kicad.html)) | 작은 프로젝트 하나를 회로도부터 PCB, 거버 출력까지 따라 하며 배우는 입문 튜토리얼이다. **가장 먼저 읽는다.** |
| [Introduction](./introduction.pdf) ([온라인](https://docs.kicad.org/10.0/en/introduction/introduction.html)) | KiCad가 무엇인지, 어떤 도구로 구성되는지, 설치 방법과 라이브러리 위치를 짧게 소개한다. |
| [KiCad Project Manager](./kicad.pdf) ([온라인](https://docs.kicad.org/10.0/en/kicad/kicad.html)) | 프로젝트와 파일 구성, 프로젝트 관리자 사용법, 템플릿, 라이브러리 테이블, 전체 설정을 다룬다. |

## 주요 편집기
| 설명서 | 내용 |
|---|---|
| [Schematic Editor](./eeschema.pdf) ([온라인](https://docs.kicad.org/10.0/en/eeschema/eeschema.html)) | 회로도 편집기이다. 심볼 배치, 배선, 계층 시트, 부품 번호 매기기, ERC, BOM과 넷리스트 출력을 설명한다. 심볼 편집기와 SPICE 시뮬레이터도 이 문서에 있다. |
| [PCB Editor](./pcbnew.pdf) ([온라인](https://docs.kicad.org/10.0/en/pcbnew/pcbnew.html)) | PCB 편집기이다. 보드 설정, 설계 규칙, 풋프린트 배치, 배선, 동박 영역(zone), DRC, 3D 뷰어, 제작용 파일(거버, 드릴) 출력을 설명한다. 풋프린트 편집기도 이 문서에 있다. |

## 보조 도구
| 설명서 | 내용 |
|---|---|
| [Gerber Viewer](./gerbview.pdf) ([온라인](https://docs.kicad.org/10.0/en/gerbview/gerbview.html)) | 제작 업체에 보내기 전에 거버 파일과 드릴 파일을 열어 확인하는 방법을 설명한다. |
| [PCB Calculator](./pcb_calculator.pdf) ([온라인](https://docs.kicad.org/10.0/en/pcb_calculator/pcb_calculator.html)) | 전류에 따른 배선 폭, 비아 크기, 레귤레이터 저항값, 감쇠기, 저항 색 코드 등을 계산하는 도구이다. |
| [Drawing Sheet Editor](./pl_editor.pdf) ([온라인](https://docs.kicad.org/10.0/en/pl_editor/pl_editor.html)) | 회로도와 PCB 도면에 쓰이는 도면 틀(테두리와 표제란)을 만들고 수정하는 방법을 설명한다. |
| [Command-Line Interface](./cli.pdf) ([온라인](https://docs.kicad.org/10.0/en/cli/cli.html)) | `kicad-cli`로 PDF, 거버, BOM 등을 출력하고 스크립트에서 ERC/DRC를 실행하는 방법이다. 자동화가 필요한 고급 사용자용이다. |
