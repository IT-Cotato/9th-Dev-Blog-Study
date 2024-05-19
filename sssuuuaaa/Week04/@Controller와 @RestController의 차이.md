# 개발하며 한 고민

이번에 프로젝트를 하면서 카카오 API 로그인 및 마이페이지를 구현했다. 로그인을 한 후, 로그인 한 유저의 정보(userInfo)를 보이게 하고 싶었는데 이 과정에서 userInfo.html이 호출되는 것이 아닌, userInfo라는 문자열만이 뜨는 상황이 발생했었다. @RestController를 @Controller로 바꾸자 문제 상황은 해결됐지만, RestController가 정확히 무엇인지, 이 둘의 차이점이 무엇인지 궁금해 이 글을 작성하게 되었다.

스프링 프레임워크에서는 Controller와 RestController 두 가지 컨트롤러 어노테이션을 제공하는데, 이 둘의 주요 차이점은 HTTP Response Body가 생성되는 방식이다. 

Controller

먼저 Controller는 전통적인 Spring MVC의 컨트롤러로 모델과 뷰를 연결하는 역할을 한다. 이 어노테이션을 사용하면 주로 뷰 템플릿을 렌더링하는데 사용된다. 예를 들어, Thymeleaf나 JSP와 같은 뷰 템플릿을 반환할 때 사용한다. **`@Controller`** 내부의 메서드에서 데이터를 뷰에 전달하려면 **`Model`** 객체를 사용하거나 **`@ModelAttribute`** 어노테이션을 사용해야 하며, 반환값은 뷰의 이름이다.

```java
@Controller
public class SimpleController {
    @GetMapping("/welcome")
    public String welcomePage(Model model) {
        model.addAttribute("message", "Welcome to the page!");
        return "welcome";
    }
}

```

RestController

**`@RestController`**는 스프링 4.0에서 도입되었으며, **`@Controller`**에 **`@ResponseBody`**가 결합된 형태이다. 이 어노테이션은 주로 데이터를 JSON이나 XML 형태로 반환하는 RESTful 웹 서비스를 개발할 때 사용된다. **`@RestController`**가 선언된 컨트롤러에서는 모든 메서드가 기본적으로 **`@ResponseBody`**를 가지게 되어, 메서드의 반환값이 HTTP 응답 본문에 직접 작성된다.

```java
@RestController
public class SimpleRestController {
    @GetMapping("/api/data")
    public Map<String, String> getData() {
        return Collections.singletonMap("key", "value");
    }
}

```

즉, **`@RestController`**를 사용하면 반환된 데이터는 클라이언트에게 직접적으로 JSON이나 XML 등으로 전달되기 때문에, Thymeleaf와 같은 뷰 템플릿 엔진을 사용하여 HTML 페이지를 렌더링하는 것이 불가능합니다. 반면, **`@Controller`**를 사용하면 서버 사이드에서 HTML 페이지를 생성하고 이를 클라이언트에게 전달할 수 있습니다. 그래서 Thymeleaf와 같은 뷰 템플릿을 사용할 때는 **`@Controller`**를 사용해야 합니다.

프로젝트 마감 시간이 정해져있어 시간에 굉장히 쫓기면서 급하게 개발을 했었다. 하지만 오류가 발생했을 때 어떻게 해결했는지 간단하게 메모해두니 프로젝트가 마무리 된 이후 이렇게 따로 찾아 더 생각해 볼 수 있는 기회가 된 것 같다.

이번에 프로젝트를 하면서 카카오 API 로그인 및 마이페이지를 구현했다. 로그인을 한 후, 로그인 한 유저의 정보(userInfo)를 보이게 하고 싶었는데 이 과정에서 userInfo.html이 호출되는 것이 아닌, userInfo라는 문자열만이 뜨는 상황이 발생했었다. @RestController를 @Controller로 바꾸자 문제 상황은 해결됐지만, @RestController가 정확히 무엇인지, 이 둘의 차이점이 무엇인지 궁금해 이 글을 작성하게 되었다.

스프링 프레임워크에서는 Controller와 RestController 두 가지 컨트롤러 어노테이션을 제공하는데, 이 둘의 주요 차이점은 **HTTP Response Body가 생성되는 방식**이다.

### **@Controller**

먼저 Controller는 전통적인 Spring MVC의 컨트롤러로 모델과 뷰를 연결하는 역할을 한다. 이 어노테이션을 사용하면 주로 뷰 템플릿을 렌더링하는데 사용된다. 예를 들어, Thymeleaf나 JSP와 같은 뷰 템플릿을 반환할 때 사용한다. @Controller 내부의 메서드에서 데이터를 뷰에 전달하려면 Model 객체를 사용하거나 @ModelAttribute 어노테이션을 사용해야 하며, 반환값은 뷰의 이름이다.

![https://blog.kakaocdn.net/dn/1sa4u/btsHvuRRRl7/ZQiZyJh8xHYSYrrtrJA8nK/img.png](https://blog.kakaocdn.net/dn/1sa4u/btsHvuRRRl7/ZQiZyJh8xHYSYrrtrJA8nK/img.png)

```java
@Controller
public class SimpleController {
    @GetMapping("/welcome")
    public String welcomePage(Model model) {
        model.addAttribute("message", "Welcome to the page!");
        return "welcome";
    }
}

```

### **@RestController**

@RestController는 스프링 4.0에서 도입되었으며, **@Controller에 @ResponseBody가 결합된 형태**이다. 이 어노테이션은 주로 데이터를 JSON이나 XML 형태로 반환하는 RESTful 웹 서비스를 개발할 때 사용된다. @RestController가 선언된 컨트롤러에서는 모든 메서드가 기본적으로 @ResponseBody를 가지게 되어, 메서드의 반환값이 HTTP 응답 본문에 직접 작성된다.

![https://blog.kakaocdn.net/dn/chL971/btsHublqg1L/ztRBCcKXT6sHcAWMSWK1KK/img.png](https://blog.kakaocdn.net/dn/chL971/btsHublqg1L/ztRBCcKXT6sHcAWMSWK1KK/img.png)

```kotlin
@RestController
public class SimpleRestController {
    @GetMapping("/api/data")
    public Map<String, String> getData() {
        return Collections.singletonMap("key", "value");
    }
}

```

즉, @RestController를 사용하면 반환된 데이터는 클라이언트에게 직접적으로 JSON이나 XML 등으로 전달되기 때문에, Thymeleaf와 같은 뷰 템플릿 엔진을 사용하여 HTML 페이지를 렌더링하는 것이 불가능하다. 반면, @Controller를 사용하면 서버 사이드에서 HTML 페이지를 생성하고 이를 클라이언트에게 전달할 수 있다. 그래서 Thymeleaf와 같은 뷰 템플릿을 사용할 때는 @Controller를 사용해야 한다.

프로젝트의 마감 시간이 정해져있어 시간에 쫓겨 오류를 해결하면 왜 해결됐는지 이유도 모르고 급하게 개발을 했다. 하지만 어디에 어떤 오류가 발생했었는지, 어떻게 해결했는지 간단하게 메모해두니 프로젝트가 마무리 된 이후 이렇게 따로 찾아 더 생각해 볼 수 있는 좋은 기회가 된 것 같다.

이미지 출처 : [https://mangkyu.tistory.com/49](https://mangkyu.tistory.com/49)