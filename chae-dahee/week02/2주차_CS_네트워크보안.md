\* 해당 내용은 [수제비 2023 정처기](https://product.kyobobook.co.kr/detail/S000200847120) 9장 소프트웨어 개발 보안 구축을 참고하였습니다.

보안 공격 기법

## DoS 공격 Denial of Service.

공격받는 시스템의 자원을 부족하게하여, 원래 의도된 용도로 사용하지 못하게 하는 공격. 과도한 데이터 전송한다.

\*DDoS와 차이점? Dos 공격은 1:1 공격이어서, 공격IP를 차단하면 공격을 막을 수 있지만, DDoS 공격은 1대의 공격자가 감염시켜 n대의 좀비 pc를 만들어서 타깃 시스템 집중적으로 공격

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlsLXw%2FbtsGw1kqsn2%2Fr1wcmFWINtHZv63JjkJ4Qk%2Fimg.png" originWidth:1780 originHeight:795 style:alignCenter>

-   SYN Flodding 플러딩 : TCP 프로토콜의 구조적인 문제를 이용함. SYN 패킷을 지속적으로 보내 점유한다(TCP Queue Full). ACK를 발송하지 않고, 계속 새로운 연결 요청을 하게되어, 자원할당을 해지하지 못하고, 소비하여 고갈된다.
    -   대응방안 : TCP 연결 타임아웃을 짧게 가져가서 연결 요청 대기 시간을 줄인다. Backlog Queue를 늘리고, SYNcookie 기능을 활성화한다. 등등

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb2bDVC%2FbtsGzYlXxUM%2FRDQa3XONOFBLLM7u5hm400%2Fimg.png" originWidth:1996 originHeight:813 style:alignCenter>

-   UDP Flodding 플러딩 : 대량의 UDP 패킷을 만들어 임의의 포트 번호로 전송하여 응답메세지ICMP 를 생성하게 해 자원을 고갈하게 한다. ICMP 패킷은 변조되어 공격자에게 전달도지 않아 대기한다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcx4LtG%2FbtsGAfulp04%2FxUYwuJnkoFkcMe6CYPeHs0%2Fimg.png" originWidth:1381 originHeight:641 style:alignCenter>

-   Smurf 스머프 / Smurfing 스머핑 : 출발지 주소를 공격 대상의 IP로 설정하여 네트워크 전체에게 ICMP Echo 패킷을 직접 브로드캐스팅하여 마비시킨다.
    -   대응방안 : 브로드캐스트 패킷 허용하지 않도록 라우터를 설정. ICMP Echo Request 메시지 응답하지 않도록 시스템을 설정한다.
    -   브로드캐스팅 : IP 주소의 호스트 ID 비트를 모두 1로 설저앟여 직접 브로드캐스팅 한다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcAlkjM%2FbtsGzLmHDAi%2Fic8p7OhPGesopXsVI9gEtK%2Fimg.png" originWidth:1710 originHeight:650 style:alignCenter>

-   PoD _ping of death_ 죽음의 핑 : ICMP 패킷을 정상적인 크기보다 아주 크게 만들어 전송하면, 다수의 IP 단편화가 발생하고, 수신측에서는 재조합 하는 과정에서 많은 부하가 발생 or 재조합 버퍼의 오버플로우가 발생하여 정상적인 서비스를 하지 못하게 된다.
    -   대응방안 : 분할이 일어나지 않은 패킷을 의심하여 탐지하도록 설정하고, 반복적으로 패킷이 들어온다면, 무시하도록 설정되어있다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb9UeY0%2FbtsGAbyIgKj%2FKv28EN6KhKUK7xxRbk8Mi0%2Fimg.png" originWidth:1710 originHeight:622 style:alignCenter>

-   Land Attack 랜드어택 : 출발 IP와 목적지 IP를 같은 패킷 주소로 만들어 보냄으로서 루프를 돌게하는, 시스템의 가용성을 침해하는 공격기법
    -   대응방안 : 수신되는 패킷 중 출발과 목적지 IP가 같은 패킷을 차단한다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcySByh%2FbtsGx149lja%2F3y4ylJKNmAVEv1QTyXasQ0%2Fimg.png" originWidth:1868 originHeight:953 style:alignCenter>

-   Tear Drop 티어드롭 : IP 헤더가 조작된 일련의 IP Fragment들을 전송한다. offset 값을 서로 중첩되도록 조작하여, 재조합하는과정에서 오류가 발생한다.

-   Bonk 봉크 : 패킷을 분할하여 보낼때, 순서번호를 모두 똑같이 전송해서 오류를 일으킨다.

-   Boink 보잉크 : 중간에 패킷 시퀀스 번호를 비정상적인 상태로 보내서 부하를 일으킨다.

## DDoS 공격 Distributed DoS

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJ8aZI%2FbtsGzODLTz5%2F12fUVuKbwHeY78hFXJDzKk%2Fimg.png" originWidth:2456 originHeight:1253 style:alignCenter>

여러대의 공격자를 분산 배치하여 동시에 동작하게 한다. 공격자가 n개의 감염 호스트를 만들고, n개에게 공격을 명령한다.

-   구성요소 : Handler 핸들러, Agent 에이전트, Master 마스터, Attacker 공격자, Daemon 데몬 프로그램
-   공격도구 : Trinoo(UDP flood 서비스 거부 공격 유발), Tribe Flood Network(TFN, 거의 대부분의 시스템에 대해 공격을 수행할 수 있는 도구), Stacheldraht(리눅스 등 시스템용 멀웨어 도구)
-   공격 종류
    -   대역폭 소진 공격 (3~4계층) : UDP/ICMP Flodding, DNS Query Flodding, SYN(+ACK) Flodding, LAND Attack, Teardrop
        -   서비스 마비 공격 (7계층) : GET Flodding, GET with Cache-Control, Slowris, Slowloris, Slow Read Attack, Hash DoS, Hulk DoS, FTP / SMPT Attack

## DRDoS 공격

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbxfJgV%2FbtsGzZyoNxX%2Fy9RpXWkzAEkIJcBZJU3KiK%2Fimg.png" originWidth:2158 originHeight:1322 style:alignCenter>

출발지 IP를 공격대상의 IP로 위조하여, 다수의 반사 서버로 요청 정보를 전송한다.

\* DDoS와 차이점? 공격 근원지 파악이 어렵고, 공격 트래픽 생성 효율이 훨씬 더 크다.

공격방식

1.  공격대상 IP로 출발지 IP를 변조(Spoofing)하여 SYN패킷 공격 경유지 서버로 전송
2.  서버는 변조된 공격 대상 IP로 SYN-ACK 패킷 전송
3.  공격대상 서버는 수많은 SYN-ACK 패킷을 받아 서비스 거부상태가 된다.

## 세션 하이재킹 Session Hijacking

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbvFFxQ%2FbtsGzDoN1is%2FyMQkVAweAB56bhey6dwzM1%2Fimg.jpg" originWidth:384 originHeight:500 style:alignCenter>

TCP Sequence Number의 세션 관리 보안상 취약점을 이용한 공격기법

공격대상자의 세션을 가로채고, 통신을 독점하는 공격방식이다.

연결을 강제로 끊어버리기 때문에 패킷이 유실되어 재전송 패킷이 증가한다.

ACK Storm 증가, 네트워크 부하 증가 현상이 발생한다.

\- 공격방식

1.  공격대상자와 통신하는 네트워크 상의 패킷을 감시하고
2.  스니핑 하여 Sequence Numver를 획득한다. 이는 TCP연결에 사용되는 일련번호
3.  공격자는 데이터 전송중인 네트워크 사이를 강제로 비동기화 상태로 만들고
4.  공격자는 위조된 패킷 Client Sequence Number을 공격대상자에게 보낸다.

\- 탐지방법

-   비동기화 상태 탐지
-   ACK 패킷 비율 모니터링
-   특성 세션에서 패킷 유실 및 재존성이 증가되는 것을 탐지
-   기대하지 않은 접속의 리셋 탐지

## 애플리케이션 공격

주로 HTTP와 관련된 공격이 주를 이룬다. 대응방안은 동시 연결에 대한 임계치 설정, 타임아웃 설정 등으로 차단한다.

주로 GET Flodding 공격기법과 유사하다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbQj1G6%2FbtsGzewdYV8%2FPAiWt68yDLYNNHUab6y5H0%2Fimg.png" originWidth:2012 originHeight:869 style:alignCenter>

-   HTTP GET Flodding 플러딩 : 과도한 Get 메시지를 이용해 서버의 과부화를 유발한다. HTTP 캐시 옵션을 조작한다.

[<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbomkSC%2FbtsGyeDAp1i%2FQNcvPGKUk38CKiuUoUK2iK%2Fimg.png" originWidth:637 originHeight:467 style:alignCenter>](https://www.cloudflare.com/ko-kr/learning/ddos/ddos-attack-tools/slowloris/)

-   Slowloris : _Slow HTTP Header DoS_. HTTP GET 메서드에서 헤더의 끝을 알리는 문자열 `\r\n\r\n\`을 전송하지 않고, `\r\n`만 전송하여, 연결상태를 장시간 지속시켜, 자원을 소진시키는 서비스 거부 공격

-   [RUDY Attack](https://www.cloudflare.com/ko-kr/learning/ddos/ddos-attack-tools/r-u-dead-yet-rudy/) : Slow HTTP POST DoS. 요청 헤더의 content-Length를 비정상적으로 크게 설정하여, 메시지 바디를 소량으로 보내 연결상태를 유지시키는 공격
    -   Content-Length : 9999999 설정 이후 1바이트씩 전송하여 연결 유지
    -   데이터를 무기한 제출하고, 웹 서버는 패킷을 수락하기위해 연결을 열린상태로 유지하며, 트래픽 처리 능력이 손상된다.

-   Slow Read Attack : 버퍼 크기(TCP Windows Size)를 낮게 설정해 서버로 전달하고, 응답을 천천히 읽어 TCP 연결을 지연시키는 공격이다.
    -   웹서버가 연결 지연을 제한하지 않는 사실을 이용하기 때문에, 작게 조작하면
    -   데이터 처리율이 감소되고, 그상태에서 HTTP 패킷을 송신하면 정상적으로 응답을 하지 못한다.

-   Hulk DoS 헐크도스 : 웹서버의 가용량을 모두 사용하도록 접속량을 많이 하는 방법.
    -   DDoS 차단 정책을 우회하기 위해 **URL을 지속적으로 변경**하면서 다량으로 GET 요청을 발생한다.

## 네트워크 공격

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJtcMU%2FbtsGwK4hukI%2FvoHCAN3Wfbxpw1aOu8NPXK%2Fimg.jpg" originWidth:500 originHeight:323 style:alignCenter>

-   Sniffing 스니핑 : 직접적인 공격 없이, 데이터만 몰래 들여다보는 수동적 공격기법

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcAlOH9%2FbtsGweEragf%2F9gklpHEG2f2QFNhVWBVLSk%2Fimg.jpg" originWidth:1024 originHeight:640 style:alignCenter>
<figcaption>WiFi  패킷 스니퍼 도구 'SolarWinds'</figcaption>

-   네트워크 Scanner 스캐너, Sniffer 스니퍼 : 네트워크 HW, SW 구성의 취약점을 파악하고 탐색하는 공격 도구

-   Password Cracking 패스워드 크래킹 `크랙한다, 크래킹` : 보안 시스템을 암호를 해독하여 뚫는 것
    -   Dictionary 사전 크래킹 : ID와 패스워드가 될 가능성이 있는 단어를 파일로 만들어놓고 대입하여 크랙한다.
    -   Brute Force 무차별 크래킹 : 패스워드로 사용될 수 있는 모든 경우를 무작위로 대입하여 알아낸다.
    -   Password Hybrid Attck 패스워드 하이브리드 공격 : 사전 + 무차별
    -   Rainbow Table Attack 레인보우 테이블 공격 : 패스워드 별로 해시 값을 미리 생성해 테이블에 모아놓고, 크래킹하고자 하는 해시 값을 테이블에서 검색해서 역으로 패스워드를 찾는 기법

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbiuJv3%2FbtsGzDh4DnD%2FYwPVjqnP9P2YQNk5GOdpu0%2Ftfile.svg" originWidth:700 originHeight:550 style:alignCenter>

-   IP Spoofing 스푸핑 : 공격자의 패킷 페더를 인증된 호스트의 IP 어드레스로 위조하여 타깃에 전송하는 공격기법. 흔히 가짜 사이트에 접속해 로그인하면 개인정보를 빼내는 방식이다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeoIjVW%2FbtsGzlIzLFa%2FQAQsxwMcwhEtkJ5k1K54D0%2Fimg.png" originWidth:1280 originHeight:777 style:alignCenter>

-   ARP Spoofing 스푸핑 : 공격자가 다른사람의 MAC 주소에 공격자 MAC 주소로 위조해 ARP응답을 만들어 공격대상자에게 전송하면, 공격대상자의 ARP Cache Table에 공격자의 MAC 정보로 바꾸고, 나가는 패킷을 스니핑한다.
    -   **네트워크 내 ARP 캐시 변조**: 공격자는 자신의 컴퓨터에서 ARP 패킷을 생성하여 네트워크 내의 모든 호스트에게 자신의 MAC 주소를 특정한 IP 주소와 연결되어 있는 것으로 속이는 ARP 응답 패킷을 보낸다.
    -   **ARP 캐시 변조 수신**: 네트워크 내의 다른 컴퓨터는 공격자가 보낸 ARP 응답 패킷을 수신하고, 해당 IP 주소와 연결된 MAC 주소를 공격자의 MAC 주소로 업데이트한다.
    -   **희생자 트래픽 리다이렉션**: 이제 공격자는 네트워크 내의 특정한 희생자 컴퓨터의 통신을 가로챌 수 있다. 공격자는 희생자가 보내는 트래픽을 가로채거나 조작하여 중요한 정보를 탈취하거나 네트워크 상의 데이터를 조작할 수 있다.

-   ICMP Redirect 공격 : 스티핑 시스템을 3계층에 존재하는 또다른 라우터라고 속인다. ICMP 메시지를 공격자가 맘대로 형태, 목적지를 설정할 수 있다.

-   Trojan Horses 트로이 목마 : 겉보기에는 정상적이지민 실행하면 악성 코드를 실행하는 프로그램

## 시스템 보안 공격

1.  Buffer Overflow 버퍼 오버플로우 : 메모리에 할당된 버퍼 크기를 초과하는 양의 데이터를 입력해 흐름을 변경시켜 악성코드를 실행시키는 공격기법
<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTpdzG%2FbtsGAeoGdWb%2FJnEoNF2YD9qzFHUIaXjtn1%2Fimg.png" originWidth:264 originHeight:191 style:alignCenter>

    -   스택 영역
    -   힙 영역
    -   대응방안 : 스택 가드, 쉴드 활용. ASLR(Address Space Layout Randomization) 활용, 안전한 함수(`strncat()` 등) 사용

1.  Backdoor 백도어 : 정상적인 인증절차를 우회하는. 은밀히 작동하고 숨겨져있을 경우도 있다.
    -   탐지기법 : 열린 포트 확인, Setuid 파일 검사, 탐지 툴 이용, 무결성 검사, 로그분석
  
2.  주요 시스템 보안 공격 기법
    -   Format String Attack 포맷 스트링 공격 : 입력값을 검증하지 않고, 입출력함수의 포맷 스트링을 그대로 사용하는 경우 생기는 취약점 공격 
    -   Race Condition Attack 레이스 컨디션 공격 : 동시 접근 시 순서에 따라 비정상적인 결과가 발생하는 상황. 프로세스 실행중에 끼어들어, 심볼릭 링크하여 악의적인 행위를 수행하게 하는 기법
        -   심볼릭 링크 : 하나의 파일이나 디렉터리를 다른 위치에 가리키도록 하는데 사용
        - ❓ 공격자가 악의적인 심볼릭 링크를 생성하여 공격대상자가 예상치 못한 파일에 접근하도록 유도하는 것이다.
    -   Rootkit 루트킷 : 침입 사실을 숨긴 채, 추후에 불법적인 해킹을 사용하는 프로그램의 모음이다.
    -   Key Logger Attack 키로거 공격 : 키보드 움직임을 탐지해서 저장한다. 키보드 활동을 기록하는 것을 키로깅, 키스트로크 로깅이라고 한다.

## ☑️ 배운 점

네트워크 보안은 용어가 정말 많다. 매번 새로운 기법이 발생함에 따라 용어도 계속 생겨나고, 외우거나 이해해야할 것이 너무 많다.

예전에 네트워크 배우기 전에는 통암기했는데, 이제는 용어와 작동방식에 대해 이해할 수 있었다. CS 과목은 거미줄처럼 엮어있어서 전체적인 학습이 필요하다고 생각된다.

보안 부분을 고려하며 코드를 구성할 수 있는 웹 개발자가 되자!