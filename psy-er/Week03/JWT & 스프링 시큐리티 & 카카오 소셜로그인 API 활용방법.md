# 카카오 블로그

## JWT & 스프링 시큐리티 & 카카오 소셜로그인 API 활용방법

![https://blog.kakaocdn.net/dn/Tw5VK/btsHa4nmr36/ykWOnIBWRxVBSKuwBaSok0/img.png](https://blog.kakaocdn.net/dn/Tw5VK/btsHa4nmr36/ykWOnIBWRxVBSKuwBaSok0/img.png)

IT 연합동아리 코테이토 6주 차 세션에서 인증과 인가에 관한 주제를 다뤘었다. 나는 해당 동아리의 교육팀이기 때문에 세션 진행 전 발표자의 자료를 검토하고 동아리 부원들이 풀 문제를 출제하는 역할을 한다. 요번 주제가 프로젝트 첫 단계인 로그인 구현 부분과 밀접한 부분이 많아 관련 내용을 정리하고, 더 나아가 카카오 소셜로그인 api를 받아와 간단하게 로그인을 구현하는 방법에 대해서 정리하고자 한다.

### **인증과 인가**

인증 : 사용자의 신원을 검증하는 행위입니다. ex) 도서관 출입

인가 : 사용자에게 특정 리소스나 기능에 액세스 할 수 있는 권한을 부여하는 프로세스입니다. ex) 데스크 출입 권한

인가는 인증을 받은 사용자가 할 수 있는 행동을 허락하는 행위입니다.

인가를 받기 위해서는 인증 과정이 필수적으로 필요하다고 볼 수 있죠!

### **토큰**

토큰 : 사용자를 식별할 수 있는 문자열로, 아이디와 비밀번호 대신 사용할 수 있습니다.

ex) Bearer Token

```
Authorization: Bearer NDjfdoijvklcmvxldklsbaufdsbkjabdiocbldsbclkjsd
```

토큰 생성과 전달 순서

1) 클라이언트가 로그인을 하면 서버는 토큰을 생성하여 클라이언트에게 넘겨줍니다.

2) 클라이언트는 요청할 때마다 서버가 넘겨준 토큰을 요청 헤더에 담아 요청합니다.

3) 서버는 요청을 받을 때마다 요청 헤더의 토큰을 서버가 생성했던 토큰과 비교하여 클라이언트를 인증합니다.

### **JWT**

JWT : JSON Web Token으로 JSON 형식에 대한 토큰의 표준 규격

형식 : {header}. {payload}. {signature}

header 내용 : 토큰이 어떤 알고리즘으로 인코딩 되어있는지 알려줍니다.

payload 내용 : 토큰에 저장되는 정보가 인코딩 되어 있습니다.

singarure 내용 : payload를 비밀키를 통해 인코딩하여 나온 특수 문자열입니다. 토큰의 유효성을 검사하는 데 사용됩니다.

JWT 토큰 생성 메커니즘

클라이언트가 최초 로그인 시, 서버는 클라이언트의 아이디와 비밀번호를 서버에 저장된 아이디와 비밀번호와 비교합니다. 인증된 사용자인 경우, 사용자 정보를 이용해 {header}. {payload}를 만들고 서버에서 만든 시크릿 키를 사용하여 전자서명한 뒤 인코딩하여 클라이언트에게 반환합니다. 따라서 JWT 인증에서는 인증 서버가 필요 없고, 토큰에 포함된 서명으로 인증을 수행해야 합니다.

### **스프링 시큐리티**

서블릿 필터 : 서블릿 실행 전에 실행되는 클래스, 백엔드 서버 동작 전에 작동하는 필터입니다.

스프링 시큐리티의 서블릿 필터를 사용하면 인증 부분의 중복을 없앨 수 있습니다.

백엔드 개발자는 서블릿 필터를 구현하고, 서블릿 필터를 서블릿 컨테이너가 실행하도록 설정해 주면 됩니다.

1. JWT 관련 라이브러리를 디펜던시에 추가하기

```
- build.gradle
implementation group: 'io.jsonwebtoken', name: 'jjwt', version: '0.9.1'
```

2. 사용자 정보를 받아 JWT를 생성하는 일을 하는 TokenProvider

```
- TokenProvider
package com.example.demo.security;

import com.example.demo.model.UserEntity;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

import java.time.Instant;
import java.time.temporal.ChronoUnit;
import java.util.Date;

@Slf4j
@Service
public class TokenProvider {
    private static final String SECRET_KEY = "dkfspdoivlkrsn;lksmoig43yg9hdkgn";

    public String create(UserEntity userEntity){
// 기한 지금으로 부터 1일로 설정
        Date expiryDate = Date.from(Instant.now().plus(1, ChronoUnit.DAYS));

// JWT Token 생성return Jwts.builder()
// header에 들어갈 내용 및 서명을 하기 위한 SECRET_KEY
                .signWith(SignatureAlgorithm.HS512, SECRET_KEY)
// payload에 들어갈 내용
                .setSubject(userEntity.getId())// sub
                .setIssuer("demo app")// iss
                .setIssuedAt(new Date())// iat
                .setExpiration(expiryDate)// exp
                .compact();
    }
    public String validateAndGetUserId(String token){
// parseClaimsJws 메서드가 Base 64로 디코딩 및 파싱.// 즉, 헤더와 페이로드를 setSigningKey로 넘어온 시크릿을 이용해 서명 후, token의 서명과 비교
        Claims claims = Jwts.parser()// 페이로드에 담긴 key와 value를 claim이라고 합니다.
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody();
        return claims.getSubject();
    }
}
```

3. 서블릿 필터 설정

```
@Slf4j
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private TokenProvider tokenProvider;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
        throws ServletException, IOException{
        try{
            String token = parseBearerToken(request);
            log.info("Filter is running ... ");
            if(token != null && !token.equalsIgnoreCase("null")){
                String userId = tokenProvider.validateAndGetUserId(token);
                log.info("Authenticated user ID : " + userId);
                AbstractAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                        userId,
                        null,
                        AuthorityUtils.NO_AUTHORITIES
                );
                authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContext securityContext = SecurityContextHolder.createEmptyContext();
                securityContext.setAuthentication(authentication);
                SecurityContextHolder.setContext(securityContext);
            }
        }catch (Exception ex){
            logger.error("Could not set user authentication in security context", ex);
        }// 다음 ServletFilter 실행
        filterChain.doFilter(request, response);
    }
    private String parseBearerToken(HttpServletRequest request){
        String bearerToken = request.getHeader("Authorization");

        if(StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer")){
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

4. 서블릿 필터 설정

```
- build.gradle
implementation 'org.springframework.boot:spring-boot-starter-security'
```

5. WebSecurityConfig 클래스 생성

```
@EnableWebSecurity
@Slf4j
public class WebSecurityConfig extends WebSecurityConfiguration {

    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception{
// https 시큐리티 빌더
        http.cors()
                .and()
                .csrf()
                .disable()
                .httpBasic()
                .disable()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                .antMatchers("/", "/auth/**").permitAll()
                .anyRequest()
                .authenticated();

        http.addFilterAfter(
                jwtAuthenticationFilter,
                CorsFilter.class
        );
    }
}
```

소셜 로그인을 위해서 OAuth를 사용합니다. OAuth는 사용자의 접근 권한을 위임 받을 수 있는 표준 프로토콜로, 클라이언트가 서드파티에 저장한 정보를 우리 프로젝트에서 사용할 수 있도록 하는 것입니다. 이 글에서는 OAuth2 라이브러리를 사용하여 카카오 로그인을 구현하는 방법에 대해서 정리해 보겠습니다.

### **카카오 애플리케이션 생성**

[https://developers.kakao.com/](https://developers.kakao.com/)

[Kakao Developers
카카오 API를 활용하여 다양한 어플리케이션을 개발해보세요. 카카오 로그인, 메시지 보내기, 친구 API, 인공지능 API 등을 제공합니다.](https://developers.kakao.com/)

카카오 디벨로퍼 사이트에 접속하여 내 애플리케이션을 클릭하고, 애플리케이션을 생성합니다.

저는 test 애플리케이션을 생성하였습니다.

![https://blog.kakaocdn.net/dn/bHM5v4/btsHdcquVII/bYdUgOx95YfUjqJJmuuMT0/img.png](https://blog.kakaocdn.net/dn/bHM5v4/btsHdcquVII/bYdUgOx95YfUjqJJmuuMT0/img.png)

앱설정 > 요약정보 페이지에 서 REST API 키를 복사해 둡니다.

이 키는 서버에서 사용할 client-id 값이 됩니다.

![https://blog.kakaocdn.net/dn/bslmkM/btsHcJhLsIh/A4f0wttuJkP2nQZR4pC0Nk/img.png](https://blog.kakaocdn.net/dn/bslmkM/btsHcJhLsIh/A4f0wttuJkP2nQZR4pC0Nk/img.png)

앱 설정 -> 플랫폼 페이지에 가서 하단에 web 사이트 도메인을 등록합니다.

![https://blog.kakaocdn.net/dn/sw9Ys/btsHealFDNH/cp5D2xJKEuTBZv9jcKdNr0/img.png](https://blog.kakaocdn.net/dn/sw9Ys/btsHealFDNH/cp5D2xJKEuTBZv9jcKdNr0/img.png)

여기서 카카오 로그인 사용시 Redirect URI를 등록해야한다고 뜹니다. 이때 주의해야할 점이 어느 플랫폼이든 Redirect를 사용해 Authorization code를 카카오에게 전달해 코드의 유효성을 확인하고 access token을 받급받아 사용자 정보를 이용할 수 있는 것은 아닙니다.

![https://blog.kakaocdn.net/dn/obIL6/btsHb7XL8MC/wP8kBQae3CnK7gcGs8nJh1/img.png](https://blog.kakaocdn.net/dn/obIL6/btsHb7XL8MC/wP8kBQae3CnK7gcGs8nJh1/img.png)

네이티브 앱 서비스를 이용하면 리다이텍트 방식을 사용할 수 없다고 뜹니다. 따라서 두 방식으로 카카오 소설 로그인 서비스를 이용하는 방식에 대해서 정리해 보겠습니다. 먼저 리액트와 같은 웹 서비스를 사용해 리다이렉트 방식으로 인가코드와 토큰을 발급하는 메커니즘을 소개하겠습니다.

### **리다이렉트 방식**

**리다이렉트 방식** : 서비스 클라이언트에서 인가 코드 발급을 요청하고 서비스 서버에서 인가 코드로 토큰 발급을 요청하는 방식

**웹서비스 프레임워크** : spring boot, django, flask, Ruby...

![https://blog.kakaocdn.net/dn/b1aEni/btsHbU5qPjd/7KWpJwkXD7aD9lbn1vLqV0/img.png](https://blog.kakaocdn.net/dn/b1aEni/btsHbU5qPjd/7KWpJwkXD7aD9lbn1vLqV0/img.png)

1. 프론트로 로그인 요청이 들어옵니다.

2. 프론트는 인가 코드를 백에게 전달합니다.

3. 백은 받은 인가 코드로 카카오 서버에서 액세스 토큰을 받아옵니다.

4. 백에서 엑세스 토큰으로 카카오 서버에서 회원 정보를 조회합니다.

5. 요청한 회원 정보가 백으로 전송됩니다.

6. 백은 받은 회원 정보를 DB에 저장합니다.

7. 최초 로그인자와 아닌자의 구분이나, 사용자 혹은 게스트 구분등의 로직을 짭니다.

8. 토큰의 탈취에 대응하기 위해 refresh token과 access token을 사용합니다.

9. 로그인 요청에 대한 응답 헤더에 jwt를 담아 클라이언트에게 보냅니다.

이 방식대로 진행하실경우 제품 설정 > 카카오 로그인 페이지에서 활성화 설정을 on으로 해주시고

![https://blog.kakaocdn.net/dn/bLZtYc/btsHaVEgZEa/7aaMlvehbPQ3FsiwGUZ5K0/img.png](https://blog.kakaocdn.net/dn/bLZtYc/btsHaVEgZEa/7aaMlvehbPQ3FsiwGUZ5K0/img.png)

Redirect URI를 설정합니다.

![https://blog.kakaocdn.net/dn/wt690/btsHa4uaKne/M0bKBBoQCDcvUeyGTZrDlK/img.png](https://blog.kakaocdn.net/dn/wt690/btsHa4uaKne/M0bKBBoQCDcvUeyGTZrDlK/img.png)

제품설정 > 카카오 로그인 > 보안 페이지에서 Client Secret 코드를 발급받아 서버의 client-secret 값으로 사용합니다.

![https://blog.kakaocdn.net/dn/wP7vk/btsHa3aWsAI/XaeqqE1tUEnrKMfGFZZ841/img.png](https://blog.kakaocdn.net/dn/wP7vk/btsHa3aWsAI/XaeqqE1tUEnrKMfGFZZ841/img.png)

제품 설정 > 카카오 로그인 > 동의 항목 페이지에 가서 카카오 로그인을 할  때 수집할 정보들에 대한 설정을 해줘야 합니다. 일반적인 상황에서 필수 동의가 가능한 정보는 닉네임과 프로필 사진입니다. 하지만 추후 jwt 토큰을 생성할 때 이메일 정보를 활용할 예정이기 때문에 카카오 계정 이메일 정보도 수집 가능하도록 설정해보겠습니다.

![https://blog.kakaocdn.net/dn/bN9geQ/btsHbA7a35y/wAoTD3OyEPpo0zSA7wTHQk/img.png](https://blog.kakaocdn.net/dn/bN9geQ/btsHbA7a35y/wAoTD3OyEPpo0zSA7wTHQk/img.png)

### **비즈니스 설정**

사업자 등록하지 않고 비즈 앱으로 전환하여 이메일 권한을 받는 방법

[https://developers.kakao.com/docs/latest/ko/getting-started/app#biz-app-for-individual](https://developers.kakao.com/docs/latest/ko/getting-started/app#biz-app-for-individual)

[Kakao Developers
카카오 API를 활용하여 다양한 어플리케이션을 개발해보세요. 카카오 로그인, 메시지 보내기, 친구 API, 인공지능 API 등을 제공합니다.](https://developers.kakao.com/docs/latest/ko/getting-started/app#biz-app-for-individual)

![https://blog.kakaocdn.net/dn/cvgA24/btsHbAMWZVi/y4J3ypfqcNukNVnUroZ2Qk/img.png](https://blog.kakaocdn.net/dn/cvgA24/btsHbAMWZVi/y4J3ypfqcNukNVnUroZ2Qk/img.png)

본인 인증 완료

![https://blog.kakaocdn.net/dn/VbxCc/btsHb5ezW3J/MnK5jCk240JkIiUnSjbbQ1/img.png](https://blog.kakaocdn.net/dn/VbxCc/btsHb5ezW3J/MnK5jCk240JkIiUnSjbbQ1/img.png)

사업자 등록이 아닌 개인 개발자 등록을 통해 동의항목에 이메일을 추가시킬 수 있습니다.

![https://blog.kakaocdn.net/dn/OwGZS/btsHeccJYYZ/kUbvVCkstRSIcSr2p6EQ0K/img.png](https://blog.kakaocdn.net/dn/OwGZS/btsHeccJYYZ/kUbvVCkstRSIcSr2p6EQ0K/img.png)

### **기본 방식**

**기본 방식** : 서비스 클라이언트에서 한 번의 요청으로 인가 코드 및 토큰 발급 모두 완료하는 방식

**네이티브 앱 프레임워크** : swift, kotlin, java, react native, flutter..

[https://velog.io/@zzangdd/Android-카카오-로그인-API-Kakao-Login-API](https://velog.io/@zzangdd/Android-%EC%B9%B4%EC%B9%B4%EC%98%A4-%EB%A1%9C%EA%B7%B8%EC%9D%B8-API-Kakao-Login-API)

1. 클라이언트에서 인가 코드 및 토큰 발급을 받고 그 토큰을 스프링 서버로 보낸다.

서버에서 받은 토큰을 사용해 카카오 회원 정보를 받아 db에 저장하는 방식이 있습니다.

2. 클라이언트에서 토큰을 이용하여 회원 정보를 받아온 후 받은 정보를 백엔드에 보내서 처리하는 방식이 있습니다.

두 방식 모두 클라이언트 측에서 토큰을 발급 받는 것이기때문에 참고 자료를 걸고 카카오 소셜로그인 API 활용방법 정리 글을 마치겠습니다. 읽어주셔서 감사합니다.