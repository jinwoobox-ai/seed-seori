NEXUS 2.1.0 - 개발자 테스트용 소스 패키지

이번 수정:
- 기존 Studio / Roblox Player 연결 구조 유지
- 게임 내 NEXUS 테스트 패널에 비행 ON/OFF 및 무한 점프 ON/OFF 추가
- 이동 속도 / 점프력 / 대시 등 기존 테스트 기능 유지
- 초기화 시 무한 점프도 OFF로 복구

실행:
1. Windows에 Node.js 20 이상 설치
2. 이 폴더에서 START-NEXUS.bat 실행
3. NEXUS에서 Studio Connector 및 Player 테스트 설정 진행
4. 본인이 소유하거나 개발 권한이 있는 게임에만 테스트 시스템을 설치하고 게시
5. Roblox Player에서 해당 게임에 접속하고 NEXUS Player Connection에서 연결

중요:
- 이 패키지는 소스 코드입니다. 빌드된 EXE가 아닙니다.
- Roblox Player 프로세스에 코드를 주입하거나 다른 사람의 게임 보안을 우회하지 않습니다.
- 비행과 무한 점프는 게임에 설치한 NEXUS Live Test 스크립트가 권한을 확인한 개발자에게만 제공합니다.
- 실제 Roblox 서버/Player에서 실환경 검증은 아직 수행하지 않았습니다.
- npm 의존성 설치가 필요하며 인터넷 연결이 필요합니다.
