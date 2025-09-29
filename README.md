# 트러블슈팅 모음집

학습을 진행하면서 발생한 문제와 해결 과정을 정리한 문서입니다.

---

## 목차
- [1. 환경 관련 문제](#1-환경-관련-문제)
- [2. 코드 관련 문제](#2-코드-관련-문제)
- [3. 배포 및 운영 문제](#3-배포-및-운영-문제)
- [4. 협업 및 관리 문제](#4-협업-및-관리-문제)

---

## 1. 환경 관련 문제

### VirtualBox NAT 설정 문제

#### 문제 요약
- Oracle VitrualBox에서 NAT 포트포워딩 설정 중, GuestOS 22번 포트 개방 후 다른 포트를 개방하면 VirtualBox및 해당 GuestOS가 정지됨

#### 문제 접근
- HostOS에서 포트충돌 상태를 확인(정상)
- 포트번호에 상관없이 2개 이상 포트를 개방하면 문제가 생기는지 확인(번호무관)
- VirtualBox의 버젼을 다운그레이하여 시도 -> 정상구동

#### 원인 분석
- <a href="https://github.com/VirtualBox/virtualbox">VirtualBox</a>는 오픈소스이므로 문제를 직접 찾아보고 기여 해 보기로 했다
- 

#### 해결 방법
- 오픈소스 기여 순서: OCA 서명 → 이슈 등록 → Fork & 브랜치 → 수정 및 signed-off 커밋
  <img width="1611" height="429" alt="image" src="https://github.com/user-attachments/assets/258aa044-3ad7-4fca-8583-11d858742ac6" />
- 이슈 등록: https://github.com/VirtualBox/virtualbox/issues/232
- 소스 분석전에 아쉽게도 16시간만에 오라클측에서 해결했다

#### 문제 분석
``` c++/**
 * Activate a single port-forwarding rule.
 *
 * This is used both when we activate all the initial rules on startup
 * and when port-forwarding rules are changed and we are notified via
 * an API event.
 */
/* static */
DECLCALLBACK(void) VBoxNetSlirpNAT::natServicePfRegister(VBoxNetSlirpNAT *pThis, PPORTFORWARDRULE pNatPf, bool fRemove, bool fRuntime) {

  ...

  /* Free the rule in any case. */
  RTMemFree(pNatPf);
}
```
- 소유권이 없는 pNatPf도 NAT네트워크 등록시에 해제해버려서 UAF 크래시 발생
- 소유권(fRuntime)이 있는 경에만 해제하도록 설정해야한다

#### 회고
코드 한줄짜리 매우 간단한 문제임에도 문제를 제시하고 해결하는 사람은 생각보다 많지 않다고 생각되었다.
문제에 대해 적극적으로 해결하려는 자세를 가질수록 내가 배워가는게 많아진다는 것을 느꼈다.

---

## 2. 코드 관련 문제
### 문제 요약
### 원인 분석
### 해결 방법

---

## 3. 배포 및 운영 문제
### 문제 요약
### 원인 분석
### 해결 방법

---

## 4. 협업 및 관리 문제
### 문제 요약
### 원인 분석
### 해결 방법

---
