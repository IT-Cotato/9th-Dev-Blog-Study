## SPA vs MPA

### SPA : Single Page Application

하나의 페이지 내에서 동적으로 콘텐츠를 로드하고 업로드 하는 방식이다.

리액트, 앵귤러, 뷰와 같은 프레임워크가 개발하는 웹 어플리케이션

ex. 클릭 시 고정되어있고, 내용만 바뀌는것

<br/>

\* 일반적으로 > CSR 렌더링 방식 사용한다.

<br/>

### MPA : Multi Page Application

탭을 이동할때마다 서버로부터 새로운 html을 받아와서 렌더링하는 방식이다.

전통적인 방식

일반적으로 > SSR 렌더링 방식 사용한다.

<br/>

AJAX가 등장한 이후로, 클라이언트가 원하는 부분만 동적으로 갈아끼울 수 있고, 화면 깜빡임도 없는 SPA를 많이 사용하게 되었다.

<br/>
* 일반적으로 > CSR 렌더링 방식 사용

그렇지만, 같은 개념이라고 생각하면 안된다.

## CSR vs SSR

### CSR : Client Side Rendering

클라이언트에서 렌더링하는 방식

동적 dom을 생성한다

SEO : Search Engine Optimization : 검색엔진 최적화에 불리하다.

<br/>

### SSR : Server Side Rendering

서버에서 렌더링하는 방식. 요청할때 즉시 만든다.

데이터가 지속적으로 달라지는 페이지에 적합하다.

SEO에 유리하다.

<br/>

### SSG : Static Site Generation

서버에서 렌더링하는 방식이라 SSR과 유사하지만, 서버에서 요청에 미리 다 만들어둔다.

데이터가 변하지 않는 페이지에 적합하다.

<br/>

## 프로젝트에 적용해보자!

React 리액트에서 화면 구성에서 동적으로 전환하는 방법으로

- URL 을 통한 라우팅
- 컴포넌트를 직접 렌더링하는 방식

... 등등 이 있지만,, 이 두가지 방법에 대해 이야기를 해보려고 한다.

<br/>

대부분의 페이지는 두가지 방법 모두 혼용하는 편이다.

url을 통한 라우팅은 React Router 라이브리를 사용해 url에 따라 컴포넌트를 렌더링하는 방식으로 CSR, SSR 둘다에서 사용가능하다. 네이게이션이 직관적이고, 유저가 url 북마크를 사용하거나 뒤로가기 앞으로 가기를 사용할 수 있다는 장점이 있다.

컴포넌트를 렌더링하는 방식은 CSR 방식으로, 유연한 구조를 가지고, 초기 로딩 속도고 빠르다는 장점이 있다. 그러나 사용자의 관점에서 작은 불편함이 있을 수 있다.

<br/>

대표적으로 예를들면, 네이버 지도와 카카오 지도를 비교해볼 수 있는데

네이버 지도의 경우, 길찾기 탭을 눌렀을때 url은 [https://map.naver.com/p/directions/-/-/-/null?c=15.00,0,0,0,dh](https://map.naver.com/p/directions/-/-/-/null?c=15.00,0,0,0,dh) 이렇다

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb4FouK%2FbtsHJkJcsa3%2FcdAML2fvmzzkJ0Aci6WK3K%2Fimg.png" originWidth:694 originHeight:831 style:alignCenter>

<br/>

그런데 카카오 지도의 경우, 길찾기 탭을 눌렀을때 url은 [https://map.kakao.com/](https://map.kakao.com/) 로 다른 모든 버튼에서 경로는 고정되어있다.

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTmOgT%2FbtsHJ5R6svd%2FSBCNRyKf5uk6MzTLmIoaKk%2Fimg.png" originWidth:560 originHeight:914 style:alignCenter>

<br/>

사용자에게 투표 링크를 생성해고 공유하는 기능을 만들 예정이어서, url에 대한 고민을 했는데,,

**투표방 제외한 각 페이지는 컴포넌트로 만들어서 모달처럼 렌더링하기로 결정했다. (CSR)**

모든 페이지에서 지도가 기본으로 띄워져야 하기 때문이다.

<br/>

### 요구사항

`/` 페이지에는 유저가 장소를 입력받은 페이지

`/midpoint` 링크 > 중간장소 컴포넌트, 투표 컴포넌트 페이지

사이드바의 클릭 상태에 따라 띄워지는 컴포넌트가 다르다

<br/>

1\. panelMidpoint 컴포넌트 열기

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZQPRR%2FbtsHKIKn0QF%2FqKKJNWSNeXwdo6np6KXQJk%2Fimg.png" originWidth:1816 originHeight:964 style:alignCenter>

<br/>
2. panelVote 컴포넌트 열기

<br/>

<img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fy9h0z%2FbtsHMBbYzee%2FXlnOqhyQca6c5VNz4mK4p0%2Fimg.png" originWidth:1721 originHeight:959 style:alignCenter>

<br/>
3. 한번 더 누르면 닫힌다. (추후 x 버튼 추가)
4. <img src = "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbCYw3Y%2FbtsHKFfW8NF%2FwGVjzGIXpxHKR8gTLPSDP1%2Fimg.png" originWidth:1720 originHeight:959 style:alignCenter>

### 구현

버튼 인덱스에 따라 링크는 1 > `/`   2,3 > `/midpoint`   4 > `/time` 으로 구성된다.

useState로 버튼 인덱스의 활성화 여부를 받는다.

```
const [activeIndex, setActiveIndex] = useState(-1);
```

<br/>
handleGnbClick 함수를 만들어서, NavItem을 클릭했을때 호출한다.

클릭된 아이템의 인덱스를 받아와 비교한다.

이미 활성화된 상태이면 -1로 비활성화, 아니면 활성화한다. 인덱스를 클릭된 아이템의 인덱스로 설정한다.

```
const handleGnbClick = (idx: number) => {
    setActiveIndex(prevIndex => prevIndex === idx ? -1 : idx);
  };
```

이때 인덱스는 사이드바 아이템 순서대로 적용하고, 몇번째가 클릭되었는지 + 클릭 비활성화도 판별하는 함수이다.

<br/>

NavItem 컴포넌트에 각각의 로고의 상태를 저장할 props 를 만든다.

```
interface NavItemProps {
  active: boolean;
}
```

<br/>

### 1\. 첫번째 NavBar에는 `/` 주소를 가지고, 유저들에게 장소를 입력받는 역할을 한다.

url이 `/` 일 경우 클릭되어있다는 상태를 나타내기 위해 아이콘 배경을 흰색으로 설정한다. 그리고 아이콘은 파란색 active 상태 이미지가 된다.

```
<NavItem
	active={activeIndex === 0}
	onClick={() => handleGnbClick(0)}
	style={{ borderRadius: '50%', backgroundColor: location.pathname === '/' ? 'white' : 'transparent' }}
>
	<Link to="/">
		<Logo>
        	{location.pathname === '/' ? <HomeLogoActive /> : <HomeLogo />}
		</Logo>
	</Link>
</NavItem>
```

`activeIndex===0` 으로 구분하면 두번클릭하면 비활성화 상태가 되기 때문에, 페이지로 구성되는 NavItem은 url로 구분하였다!

4번째 NavBar에 `/time` 주소도 동일한 코드로 구성하였다.


<br/>

### 2\. 두번째 NavBar에는 `/midpoint` 주소를 가지고, 계산된 중간지점을 보여준다.

`panel-midpoint.tsx`로 컴포넌트 분리해서 **UI 만 간단하게 구현**하였다. (백엔드 로직과 디자인이 나와야 완성)

추후에 Item 컴포넌트 내부에 계산된 중간지점에 해당하는 데이터를 처리하면 된다.

```
import styled from 'styled-components';

const panelMidpoint = () => {
  return (
    <PanelContainer>
      <ItemContainer>
        <Item>6호선 삼각지역 4번출구 앞</Item>
        <Item>6호선 삼각지역 4번출구 앞</Item>
        <Item>6호선 삼각지역 4번출구 앞</Item>
      </ItemContainer>
    </PanelContainer>
  );
};

const PanelContainer = styled.div`
  position: sticky;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
  width: 400px;
  height: 100%;
`;

const ItemContainer = styled.div`
  width: 100%;
  height: 100%;
`;

const Item = styled.div`
  background-color: #f9f9f9;
  width: 350px;
  height: 200px;
  margin: 15px 30px;
  border-radius: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: black;
`;

export default panelMidpoint;
```

<br/>

`Sidebar.tsx` 에서 중간지점 결과 데이터 보여주는 컴포넌트 열고 닫히는 코드

```
<NavItem
	active={activeIndex === 2}
	onClick={() => handleGnbClick(2)}
	style={{
		borderRadius: '50%',
		backgroundColor: activeIndex === 2 ? 'white' : 'transparent',
	}}
>
	<Link to="/midpoint">
		<Logo>
        	{activeIndex === 2 ? <VoteLogoActive /> : <VoteLogo />}
		</Logo>
	</Link>
</NavItem>
///...
{activeIndex === 2 && <PanelVote />}
```

url이 PinLogo와 VoteLoto가 동일하기 때문에 activeIndex로 구분하였다.

*PinLogo는 active 상태 아이콘이 아직 안나와서 동일하게 흰색 아이콘으로 대체하였다. 추후 변경 사항!

<br/>

### 3\. 세번째 NavBar에는 `/midpoint` 주소를 가지고, 중간지점을 입력해 유저가 투표할 수 있는 투표방을 보여준다.

`panel-vote.tsx`로 컴포넌트 분리해서 **UI 만 간단하게 구현**하였다. (디자인이 나와야 완성)

```
import styled from 'styled-components';

const panelVote = () => {
  return (
    <PanelContainer>
      <ItemContainer>
        <VoteInfo>모임 장소 투표 생성</VoteInfo>
        <Item>
          <input type="text" placeholder="장소를 입력하세요" />
          <input type="text" placeholder="장소를 입력하세요" />
          <input type="text" placeholder="장소를 입력하세요" />
          <input type="text" placeholder="장소를 입력하세요" />
        </Item>
        <VoteLinkBtn type="button">투표 링크 만들기</VoteLinkBtn>
      </ItemContainer>
    </PanelContainer>
  );
};

const PanelContainer = styled.div`
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
  width: 400px;
  height: 100%;
`;

const ItemContainer = styled.div`
  width: 100%;
  height: 100%;
  margin: 100px auto;
`;

const VoteInfo = styled.h1`
  font-size: 24px;
  font-weight: bold;
  margin-left: 20px;
`;

const Item = styled.div`
  background-color: #f9f9f9;
  width: 350px;
  height: 200px;
  margin: 100px 30px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
`;

const VoteLinkBtn = styled.button`
  padding: 10px 20px;
  width: 200px;
  background-color: #5142ff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin: 0 100px;
  text-align: center;
`;

export default panelVote;
```

<br/>

`Sidebar.tsx` 에서 투표방 생성하는 컴포넌트 열고 닫히는 코드

```
<NavItem
	active={activeIndex === 2}
	onClick={() => handleGnbClick(2)}
	style={{
		borderRadius: '50%',
		backgroundColor: activeIndex === 2 ? 'white' : 'transparent',
	}}
>
	<Link to="/midpoint">
		<Logo>
        	{activeIndex === 2 ? <VoteLogoActive /> : <VoteLogo />}
		</Logo>
	</Link>
</NavItem>
///...
{activeIndex === 2 && <PanelVote />}
```

</SidebarContainer> 우측에 띄워야해서, 제일 밖에 panel 컴포넌트 열리는 코드 작성한다.

activeIndex가 1일때 panel-midpoint를 열고, 2일때 panel-vote 를 연다.

<br/>

아이콘을 한번 더 클릭하면, handleGnbClick 함수에 의해 -1로 초기화되어서 컴포넌트가 닫힌다.

<br/>

### 4\. 리팩토링

중복되는 코드 idx 1,2가 activeidx에 따라서 로고 이미지 변경하는 코드 로직이 동일하기 때문에 함수로 빼서 사용한다.

```
type LogoComponentProps = {
    active: React.ReactNode;
    inactive: React.ReactNode;
  };
  const LogoComponent: React.FC<LogoComponentProps> = ({ active, inactive }) => (
    <Logo>{activeIndex === active ? active : inactive}</Logo>
  );

  ///...

  <NavItem active={activeIndex === 1} onClick={() => handleGnbClick(1)}>
            <Link to="/midpoint">
              <LogoComponent active={<PinLogo />} inactive={<PinLogo />}></LogoComponent>
            </Link>
          </NavItem>
```

pathname 으로 구분하는건 공통함수로 뺄 수 없다.

<br/>

## ☑️ 배운 점

url이 아니면 어떻게 구분을 해야 고민했는데 index 를 지정해 컴포넌트를 클릭했는지 판별하는 방법!

CSR, SSR로 프로젝트 구성하는 방법.

조건부로 렌더링해서 동적으로 컴포넌트를 불러오는 CSR.


<br/>

현재는 페이지가 기획과 디자인이 아직 나오고 있는 상태라,, 백엔드도 api 설계하는 중이라 아직 데이터 처리보다는 로직을 생각하고 구현하는 방식으로 개발하였다.

여기서 더 나아가, 카카오 지도 API 에서 앞선 페이지에서 계산된 값들을 페이지에 띄어주는 처리까지 해야 페이지의 완성이다.


<br/>

이전 프로젝트에서도 조건부 컴포넌트 렌더링 방식 사용했었는데, 조건에 따른 경우의 수가 복잡했었기에... 파일을 분리하지 못하고 하나의 파일에서 완전 크게 만들었었다.

이번에 CSR, SSR 지식을 정리하면서 파일을 더 정확히 분리하고, 컴포넌트화 할 수 있었다.

<br/>

앞으로 간단한 개발을 할때도, 개발패턴이나 CS 지식을 적용하여 깊이 생각하려고 한다.
