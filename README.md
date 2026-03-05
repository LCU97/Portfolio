# 이철의 포트폴리오
***
## 📞 Contact
- 이메일 : saparationlee@gmail.com
- 블로그 : https://saparation.tistory.com/

---
## 📑 ProJects
### 1. XR Interactive Content Framework

실감형 체험존의 벽과 바닥 전체에 프로젝션 매핑을 적용하고, 사용자 인터랙션이 가능한 XR 콘텐츠 개발

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


- Unreal Engine 기반의 XR 콘텐츠 프레임워크 설계
- OSC 통신 기반 센서 데이터 처리 시스템 구현
- Persistant Level 기능으로 레벨 전환 및 Spout 로 타 프로그램과 렌더 타겟 공유

### Architecture & Design

시스템 설계의 목적은 아래와 같습니다. 

1. 개발팀원들이 프로젝트의 주요 시스템에 대한 최소한의 이해만으로도 콘텐츠 개발이 가능하도록 설계
2. 모든 체험형 레벨에서 동일한 개발 방식을 적용하여 팀원 간 유지보수와 테스트, 협업이 용이하도록 설계
3. 여러 프로젝트에서 공통적으로 사용되는 OSC 및 Spout 기능을 Unreal Plugin 형태로 개발하여 모듈화

<details>
  <summary><h3>🏗️ System Architecture</h3></summary>
<img width="1150" height="912" alt="image" src="https://github.com/user-attachments/assets/9ae7a7ad-7aef-4bc5-8041-31ce6765c7ff" />  
</details>

