실무역량 과제 테스트 문제풀이를 기반으로 작성하였습니다.

<img src = "https://blog.kakaocdn.net/dn/bfTvBH/btsHudQ7nPK/5r5QtT6UCxmlYkxJ2hXwEk/img.png" originWidth:1780 originHeight:795 style:alignCenter>

과제 풀이가 2시간이 주어졌는데, node.js 로 14문제를 2시간안에 풀기에는 아직 완벽하게 적응을 못한 것 같아서 아쉬웠다. 초반에 감잡는데 시간이 좀 걸려서 더 많이 익숙해 져야겠다.

## 조건

req, res에 요구사항을 만족하는 내용을 전송하는 방식으로 문제풀이는 진행되었다.

실제 데이터베이스를 사용하지 않아서 SQL문을 써보지 못한다는게 조금 아쉬웠지만, json 파일로 실제 db와 유사하게 주어졌다.

## 문제 1. 특정 년도 1999 이후 출판된 도서 목록 조회

조회 GET 메서드 사용. 성공하면 json으로 해당 책 정보를 응답한다. 여러개의 책을 배열에 담아서 전달한다.

JS 문법으로, 배열에서 조건에 해당하는 객체를 걸러내는 메서드 `filter()` 사용한다.

```
app.get("/books/after", (req, res) => {
  const afterBooks = books.filter(
  ///...
  );
  res.status(200).json(afterBooks);
});
```

이때 filter 조건을 어떻게 주어야 할까 했는데,

books 데이터 안의 pub_date를 년도만 떼서 비교할 수 있었다. `new Date(books.pub_date).getFullYear()` >= 1999

[Date.prototype.getFullYear() - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear)

<br/>
그런데 DB에서 많이 사용하는 YYYY-MM-DD 형식을 Date 객체로 만드는 메서드는 없다. 

`**toISOString()**`으로 만들 수 있다.

```
const date = new Date();
const curDate = date.toISOString().split("T")[0];

app.get("/cur-date", (req, res) => {
  res.status(200).json({ date: curDate });
});
```

Date 형식의 반환을 문자열로 반환한다. toString()은 `"Wed Oct 05 2011 16:48:00 GMT+0200 (CEST)"` 이러한 형식으로 문자열 반환을 하기 때문에 서버에서 처리할때 불리하다.

[Date.prototype.toISOString() - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)

<br/>

## 문제 2. 사용자id의 대출 도서의 반납 예정일의 합

반납 예정일 += (도서별 반납예정일 - 현재 날짜) -> 누적

요구사항에서 유효하지 않는 userId 값을 체크해주고, 유효하면 반납일을 계산한다.

<br>

URL 에서 userId값을 req parameter로 요청하고,

```
const userId = parseInt(req.params.userId);
```

users 데이터에서 존재하는지 탐색한다.

```
 const userExists = users.some((val) => val.id === userId);
  if (!userExists) {
    res.status(404).json({ error: "user not found" });
    return;
  }
```

<br>

이때 **some 메서드**는 배열 안의 어떤 요소라도 주어진 판별 함수를 적어도 하나라도 통과하는지 불리언 값으로 반환해주고, 전체탐색이 아닌 -> 참을 반환하는 첫번째 요소를 찾으면 즉시 반복을 중단하고 반환하기 때문에

데이터 존재 여부 확인에 유용하다👍

<br>

반납 예정일 계산하는 부분이 Date 객체를 생성해서 비교하는 것이기 때문에 날짜 변환에서 어려움을 겪었다.

먼저 데이터 탐색은 배열 forEach()로 돌기로 했다.

데이터의 userId를 기준으로 일치하는 사용자의 데이터를 찾았다. 일치할 경우에만 계산 진행!

```
book_borrowings.forEach((val) => {
    if (val.user_id === userId) {
      const dueDate = new Date(val.due_date);
      ///...
    }
  });
```

<br>

curDate, dueDate 변수에 각 Date 정보를 담고,

반납일자가 현재 날짜보다 큰 경우 == 반납일이 아직 지나지 않은 도서 인 경우

-> `반납일 - 현재 날짜` 계산을 진행한다.

<br>

구글에 Javascript 날짜 차이  `**DATEDIFF**` 구하는 방법이 많이 정리되어 있어 참고하였다.

> 간단하게는 비교하고자 하는 날짜를 millisecond 밀리초 단위로 변환해서 빼기를 계산한다.
>
> 계산값을 하루의 밀리초 `(1000 \* 3600 \* 24)` 로 나눠주면, 밀리초가 아닌 일 단위의 값을 얻을 수 있다.

이때 abs(), ceil()  등 요구사항에 맞는 함수로 형식을 지정해 줄 필요가 있다.

<br>

누적날짜 totalDueDays에 총 반납 예정일을 계산해준다.

```
if (dueDate > currentDate) {
        const daysDiff = Math.abs(
          dueDate.getTime() - currentDate.getTime() / (1000 * 3600 * 24)
        );
        totalDueDays += daysDiff;
      }
```

<br>

## 문제 3. 장르별 도서 조회

genre가 URL req parameter로 요청되고, 존재하지 않는 장르가 넘어올 수 도 있어서 예외처리 해야 한다.

배열로 탐색한다. `filter()` 로 genre 일치하는지 탐색!

```
app.get('/genre/:genre', (req, res) => {
  const genre = req.params.genre;
  const booksByGenre = booksData.filter(book => book.genre === genre);
//...
});
```

<br>

예외처리 -> 탐색을 했는데 booksByGenre 에 아무것도 없을때 404 에러를 띄우고

값이 존재한다면! 상태값 200을 response 한다,

```
  if (booksByGenre.length === 0) {
    res.status(404).json({ "error": "genre not found" });
  } else {
    res.status(200).json(booksByGenre);
  }
```

<br>

## 문제 4. 대출 도서 중 특정 장르 도서의 수

앞서 사용한 대출된 도서, 장르 조회 에서 고도화한다.

genre는 req.query에서 가져오고, 유효하지 않은 장르일 경우 에러 발생

```
app.get("/api/v1/books/genre-count", (req, res) => {
  const genre = req.query.genre;
  if (!genre) {
    return res.status(404).json({ error: "genre not found" });
  }
  ///...
});
```

<br>
Borrowings에서 장르 도서 개수를 확인해야하는데

reduce() 메서드는 누적값을 구할때 유용하기 때문에 사용한다!

<br>

먼저, 대출 도서의 정보를 가져올때, 책 정보에서 -> 대출정보의 id와 같은 -> book id를 필터링해서 가져온다.

만약에 가져온 대출 도서의 정보와, 요청된 특정 장르가 일치파면 누적값을 증가시킨다.

```
  const cnt = book_borrowings.reduce((acc, cur) => {
    const book = books.find(book => book.id === cur.book_id);
    if (book && book.genre === genre) {
      return acc + 1;
    }
    return acc;
  }, 0);
```

<br>

## 문제 5. 사용자의 도서 대출 정보 조회

userId를 req.params.userId로 받아와서 존재하지 않으면 예외처리 한다.

```
pp.get("/users/:userId/borrowed-books", (req, res) => {
  const userId = parseInt(req.params.userId);
  const user = users.find((user) => user.id === userId);
  if (!user) {
    res.status(404).json({ error: "user not found" });
    return;
  }
  ///...
});
```

<br>

사용자 id의 대출정보를 book_borrowings에서 찾고! `filter()`

그 대출 정보의 도서 id를 books 에서 찾고! `map()` 그 객체를 return 한다.

```
const borrowedBooks = book_borrowings
    .filter((val) => val.user_id === userId)
    .map((val) => {
      const book = books.find((book) => book.id === val.book_id);
      return book;
    });
```

<br>
userId는 존재하는데 대출정보가 없는 경우, 상태코드는 200으로 빈 배열을 반환한다.

```
if (!borrowedBooks.length) {
    res.status(200).json([]);
  } else {
    res.status(200).json(borrowedBooks);
  }
```

## ☑️ 배운 점

**Date() 함수 사용법**. JS 날짜 계산의 작동방식에 대해 이해하게 되었다.

싹다 정규 표현식으로 나타내야 하나...😢 했는데 다행이었다.

설명한 메서드 이외에도, `getFullYear()` `getMonth()` 등 다양하기 때문에 사용법 + DateDIFF 마스터하기!

<br>

데이터를 연결, 연결해서 가져오는 법 -> 마지막 문제 로직이 이러한 형태였다.

user.Id -> borrowing.userId -> borrowing.bookId -> book.id -> book 정보 return

실제 DB를 생성해서 이러한 로직 동작하도록 디벨롭 해보기! ~숙제~

<br>

자바스크립트에 많이 익숙해 졌다고 생각했는데, 요구사항에 맞게 적절하게 메서드를 사용하는 방법에 대해 더 고민하게 되었다. some, filter, find, forEach, map 등등,,, 어떨때 사용하는지 더 많이많이 쓰고, 프로젝트에 적용해 보면서 실력을 키워나가야겠다.
