# 이철의 포트폴리오
***
## 📞 Contact
- 이메일 : saparationlee@gmail.com
- 블로그 : https://saparation.tistory.com/

---
## 📑 Projects
### 1. XR Interactive Content Framework (사내 프로젝트)

실감형 체험존의 벽과 바닥 전체에 프로젝션 매핑을 적용하고, 사용자 인터랙션이 가능한 XR 콘텐츠 개발

<img width="610" height="457" alt="image" src="https://github.com/user-attachments/assets/ddb3bae8-570c-42cc-93d4-5151d2a91d0a" />


출처 : [관련 기사](https://news.nate.com/view/20260108n11681)

<details>
<summary>기술 스택 및 개발 환경</summary>
  
| 구분 | 내용 |
|-----|-----|
| 개발 툴 & 언어 | Unreal Engine 5.3 (C++ & Blueprint) |
| 패키징 플랫폼 | Windows |
| 플러그인 | Spout, OSC |
| 외부 소프트웨어 | HOKUYO Manager, Resolume Arena |
| 외부 하드웨어 | LiDAR Sensor, 빔 프로젝터 |
| 버전 관리 | GitHub |

</details>

### Architecture & Design

시스템 설계의 목적 

1. 개발팀원들이 프로젝트의 주요 시스템에 대한 최소한의 이해만으로도 콘텐츠 개발이 가능하도록 설계
2. 모든 체험형 레벨에서 동일한 개발 방식을 적용하여 팀원 간 유지보수와 테스트, 협업이 용이하도록 설계
3. 여러 프로젝트에서 공통적으로 사용되는 OSC 및 Spout 기능을 Unreal Plugin 형태로 개발하여 모듈화

<details>
  <summary><strong> 🏗️ System Architecture</strong></summary>
  <br>
<img width="1150" height="912" alt="image" src="https://github.com/user-attachments/assets/9ae7a7ad-7aef-4bc5-8041-31ce6765c7ff" />  
</details>

<details>
  <summary><strong> 📝 Framework</strong></summary>
  <br>
  
1. 개발 효율성
   - **온보딩 단순화**
     - 설계 의도 : 개발 팀원은 DataTable, BoardManager, BoardRule, PlayerActor의 작동 방식만 이해하면 즉시 서브 레벨 개발에 투입 가능
     - 효과 : 시스템 복잡도를 낮추어 개발 팀원의 온보딩 시간 단축

   - **공통 기능 모듈화**
     - 설계 의도 : 여러 프로젝트에서 공통적으로 사용되는 기능을 플러그인 형태로 분리하여 모듈화
     - 효과 : 다른 프로젝트에서도 별도의 구현 없이 OSC 및 Spout 기능을 즉시 사용할 수 있도록 재사용성 확보

2. 확장성
   - 설계의도 : DataTable 을 활용한 콘텐츠 확장에 용이한 구조 채택
   - 효과 : 코드 수정 없이 데이터 테이블 편집으로 새로운 오브젝트 추가 가능

3. 안정성
   - 설계의도 : CreateObjPoints 내 오브젝트 풀링 구현
   - 효과 : 실시간 센서 인터렉션 시 빈번한 Actor 생성/파괴로 인한 오버헤드 방지

4. 객체지향 설계
   - 설계의도 : BoardRule 추상 클래스로 설계 및 BoardManager 분리
   - 효과 : 다형성 - LevelManager 는 공통 메서드만 호출하여 각 룰을 독립적으로 실행
   - 캡슐화 - BoardManager과 BoardRule을 역할을 분리하여 서브 레벨과 Root 레벨 간의 의존성 최소화
  
<br>

    
<img width="2021" height="1331" alt="ClassDiagramSportrack3차최종 drawio" src="https://github.com/user-attachments/assets/e70b6136-3d2d-4a98-9cd3-4ae7c0233b08" />


</details>

<details>
<summary><strong> 🚧 Sequence Diagram & Troubleshooting </strong></summary>

<br>

**1. 레벨 스위칭 시퀀스**
   
     
<img width="661" height="481" alt="레벨스위칭시퀀스 drawio (2)" src="https://github.com/user-attachments/assets/7a2de6df-4890-4564-893d-d7387e4fa642" />

## Issue #1

GPU Crash during Level Streaming
- 원인 : 레벨 전환 시 이전 레벨의 텍스처가 전부 해제 되지 않고 새로운 레벨의 텍스처가 로드되면서 VRAM 사용량 급격히 증가.
     
- 문제 분석 : memreport 분석 결과, 일부 서브 레벨의 텍스처가 NPOT(Non Power Of Two) 형태로 제작되어 MipMap이 생성되지 않아 텍스처 스트리밍이 정상적으로 동작하지 않고 VRAM 사용량이 비정상적으로 증가하는 문제 확인
     
- 해결 과정 : 그래픽 디자이너 팀과 협업하여 해당 텍스처들을 POT(Power Of Two) 형태로 재제작하고 압축 설정 및 텍스처 그룹을 UIInterface → World 로 변경하여 스트리밍 가능하도록 수정
     
- 결과 : 레벨 전환 시 VRAM 사용량을 약 15GB → 8GB 수준으로 감소


## Issue #2

XR Room 멀티 카메라 스티칭 시 화면 경계 불일치 문제
- 원인 : 실제 XR 체험 공간의 6면(앞/뒤/좌/우/위/아래)을 렌더링하기 위해 6개의 SceneCapture2D 카메라와 고해상도 Render Target을 사용하였는데, 해상도가 매우 커 카메라의 각 면의 화면을 정확히 스티칭하기 어려운 문제가 발생

- 문제 분석 :
  - Render Target 해상도가 크기 때문에 캡처 영역을 확보하기 위해 카메라를 뒤로 이동시키는 방식 사용 <br>
     → 이 과정에서 바닥을 캡처하는 카메라가 천장보다 높은 위치로 이동하여 천장의 뒷면(Backface)을 캡처하는 문제 발생
     
  - FOV 및 Transform을 조정하여 스티칭을 시도했으나 6면 중 5면은 정렬이 가능했지만 마지막 한 면의 화면 경계를 정확히 맞추지 못함 <br>
     → 카메라 Transform 및 FOV 기반 조정만으로는 정확한 스티칭 정렬에 한계 존재

<img width="1131" height="742" alt="카메라" src="https://github.com/user-attachments/assets/317d73a4-62ed-4068-a4e7-2ef4d7eb35c2" />

### - 해결 과정1 : Projection Matrix 기반 렌더링 범위 제어
 | <strong>SceneCapture2D 카메라의 Projection Matrix를 직접 생성하여 렌더링 범위를 제어하도록 구현 </strong> <br>
  → Custom Near / Far 값을 적용하여 카메라에 지나치게 가깝거나 먼 오브젝트를 렌더링 대상에서 제외하도록 처리 <br>
  → 카메라 위치를 과도하게 이동시키지 않고 필요한 영역만 정확히 캡처할 수 있도록 렌더링 범위를 제어

### - 결과1 :
  - 뒷면을 캡쳐하는 문제 해결 O
  - 6면 경계 일치 문제 해결 실패 X

### - 해결 과정2 : 디자인 팀에서 사용하는 레벨 디자인 프리뷰 용 시네마틱 카메라들의 데이터를 활용하여 렌더링 범위 제어
  → 프리뷰 레벨에서 사용되는 시네마틱 카메라의 속성 중 SensorWidth, SensorHeight, FocalLength 등의 데이터를 복사해서 SceneCapture 카메라의 새 Projection Matrix를 만드는데 사용 및 적용

### - 결과2 :
  - 뒷면을 캡쳐하는 문제 해결 O
  - 6면 경계 일치 문제 해결 O

</details>

<br>

---

## 2. H.W.H

| Unreal Engine (Blueprints / C++) 기반으로 개발한 **Action RPG 게임** <br>
| 개발 기간 : 2024.09.16 ~ 2024.10.07 <br>


🔗 **Repository**  
[GitHub_Link](https://github.com/LCU97/Cat)

<br>

**담당 역할**
- 플레이어 캐릭터 시스템 구현
- 입력 처리 시스템 개발 
- FSM 기반 상태 관리 시스템 구현 
- 타겟팅 시스템 구현

<br>

---

## 3. Let's Go Maple

| 메이플스토리 IP 를 이용하여 대전어린이병원의 환아들을 위한 femto bolt 모션 인신 센서 기반 실시간 인터렉티브 게임 개발 및 설치 <br>
| 개발 기간 : 2025.09. ~ 2025.11 <br>
| 유지/보수 기간 : 2025.12 ~ 2026.03 <br>

<img width="860" height="282" alt="image" src="https://github.com/user-attachments/assets/3f71af08-2bb2-42da-a7ed-b6ad436a961d" />

<br>

<br>

# 리포지토리 넣으면 큰일날듯 대신 몇몇 코드만 캡쳐해서 올리는 것으로 대체할것. 토글 형식으로 내용물을 안쪽에 정리할것. 굳이 플머 관련 내용만 넣지 말고 다른 담당 역할 관련 내용도 추가 할 것


<br>

**담당 역할**
- 게임 선택 레벨 및 레벨 이동 구현
- Google Sheets API 기반 게임 이용자 수 집계 및 모니터링 기능 구현
- 디자인 팀, 넥슨, 병원 관계자 일정 조율 및 개발팀 업무 및 일정 관리
- 현장 설치 및 메뉴얼 작성

<br>

 관련 영상 : [보러가기](https://www.youtube.com/watch?v=yDWWOSF2NIc)  <br>
 관련 기사 : [보러가기](https://www.gamemeca.com/view.php?gid=1768767)  <br>

 ---

 ## 4. Pollute

 | Unreal Engine (Blueprints / C++) 를 사용하여 리슨 서버 기반의 비대칭 서바이벌 게임 개발 <br>
 | 개발 기간 : 2024.11.15 ~ 2024.12.27 <br>


 🔗 **Repository**  
 [GitHub_Link](https://github.com/zerohn/Pollute)

<br>

**담당 역할**
- Unreal Engine의 Listen Server 구조를 활용하여 클라이언트-서버 간 액터 리플리케이션 및 RPC 기반의 멀티플레이 시스템

 <br>

관련 영상 : [보러가기](https://youtu.be/w0YqDRbhcMw) <br>

---

## 5. Issac 3D 모작

| Unreal Engine (Blueprints / C++) 기반으로 개발한 아이작 3D 모작 게임 <br>

<img width="1727" height="1210" alt="image" src="https://github.com/user-attachments/assets/4d20073a-bd81-4632-a26c-a11184b986e1" />


<br>

**담당 역할 및 성과**
- 공통 기능을 가진 `Enemy / EnemyBullet` 클래스를 기반으로 상속 구조를 설계하고 virtual 함수를 활용하여 몬스터 및 탄환의 행동 로직을 각 클래스에서 `Override`
- 상점 및 플레이어 저장 시스템 구현
  
<br>

**코드 보러가기** <br>
[GitHub_코드](https://github.com/petemus/ISAAC3D/tree/master/Source/ShootingGame3D/Enemy)

<br>

**관련 영상** : [보러가기](https://www.youtube.com/watch?v=FqUksbYmu30) <br>

<br>

**블로그 개발 정리**
- 보스 몬스터 : [보러가기](https://saparation.tistory.com/213)
- 상점 및 플레이어 저장 : [보러가기](https://saparation.tistory.com/180)

<br>




 
