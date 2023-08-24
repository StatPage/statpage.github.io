---
layout: single
title:  "깃허브 블로그 포스팅 기록 & 기능 정리"
toc: true
toc_sticky: true
# categories: [programming]
# tags: [포스팅, 블로그, 이미지, 이미지 정렬, ]
---

# 깃허브 블로그 시작하는 이유

이 때까지는 공부한 내용을 개인 블로그에 편하게 업로드했다.  

전문적인 내용을 깔끔하게 정리하고 나만의 포트폴리오로 삼기 위해 깃허브 블로그를 시작합니다.

# 블로그 필요 기능

깃허브 블로그 기능은 글만 쓰는 게 아니기 때문에 한 번씩 찾아보게 되는 기능과 출처, 예시를 정리해서 남겨둬야 겠다.

# 이미지

## 이미지 첨부하기

깃허브 블로그 파일 업로드는 마크다운 파일(.md)로 하기 때문에 이미지를 첨부하기 위해서는 별도의 문법을 사용해야 한다. 나는 필요할 때마다 이미지를 캡처하거나 만드니까 로컬 이미지로 편하게 올릴 수 있는 방법에 대해 알아봤다.

방법
1. github 로그인
2. 아무 repository 접속 -> Issuses 클릭 -> New issue 클릭
3. 이미지 '복사' -> 회색 창에 '붙여넣기'

![Untitled 1](https://github.com/StatPage/AI-programming-class/assets/61931924/744ef49d-629d-4e65-8e3b-04981219cbbb){: .align-center}

![image](https://github.com/StatPage/AI-programming-class/assets/61931924/2e6c7cf9-b7bc-4683-a618-cd1df5e7177a){: .align-center}

4. 생성된 주소를 복사해서 마크다운에 붙여넣으면 이미지가 삽입된다.

![image](https://github.com/StatPage/AI-programming-class/assets/61931924/0dd32a42-898c-4580-ba79-1616af21e76a){: .align-center}

Reference: [uzchu](https://velog.io/@uzchu/Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-image-%EC%82%BD%EC%9E%85%ED%95%98%EA%B8%B0)

## 이미지 가운데 정렬하기

이미지를 무사히 첨부하더라도 다른 블로그와는 다르게 깃허브 블로그에서는 가운데로 정렬이 알아서 되지 않는다. 그래서 아래의 방법을 사용한다.
 
`{: .align-center}`을 이미지 파일 바로 뒤에 붙여주면 된다.
(왼쪽은 `center` 대신 `left`, 오른쪽은 `right`를 입력하면 된다.)  

Reference: [평생 공부 블로그 : Today I Learned‍](https://ansohxxn.github.io/blog/image/)


## 이미지 캡션 넣기

가끔씩은 본문과는 별개로 이미지에 추가적인 설명을 넣어줘야 할 때가 있다. 깃허브 블로그에서 이를 실행하기 위해서는 아래의 방법을 사용한다.

```markdown
![name of the image](URL)
*Image_caption*
```

그리고 caption이 추가될 수 있도록 아래의 코드를 _base.scss에 추가하면 된다.

```css
img + em {
  display: block;
  text-align: center;
  font-size: .8rem;
  color: light-grey;
}
```

결과물: 
![image](https://github.com/StatPage/blog-images/assets/61931924/6f9c3dee-6403-41d0-a4b6-f95cac04ead1){: .align-center}


Reference: 
- [Stackoverflow](https://stackoverflow.com/questions/19331362/using-an-image-caption-in-markdown-jekyll)
- [도각도각 Dev Day](https://devyuseon.github.io/github%20blog/using-an-image-caption-in-markdown-jekyll/)



## 이미지 사이즈 조절하기

이미지 크기는 `px` 단위나 `%` 단위로 정할 수 있다. 

```markdown
![image](URL){: width="100%" height="100%"}
```

변경 전
![image](https://github.com/StatPage/blog-images/assets/61931924/80434c3e-ad1a-4859-91b6-b5fe829759d9){: .align-center}

변경 후
![image](https://github.com/StatPage/blog-images/assets/61931924/80434c3e-ad1a-4859-91b6-b5fe829759d9){: .align-center}{: width="50%" height="50%"}

Reference: [평생 공부 블로그 : Today I Learned‍](https://ansohxxn.github.io/blog/image/)

# 업로드
## 깃허브 블로그 포스팅이 안 보일 때 해결 방법

블로그 글을 쓰기 위해서는 지켜야 하는 양식들이 있다. 예를 들면 `YEAR-MONTH-DAY-title.md`같은 양식으로 써야 하는 그런 것들 말이다. 하지만 그런 걸 잘 지켜도 블로그 업로드가 안 되는 상황이 발생한다. 

보통은 '_config.yml' 파일에 line 30에 `future: true`로 만들어주면 블로그에 내가 쓴 포스팅이 보인다.

Reference: [도각도각 Dev Day](https://devyuseon.github.io/github%20blog/githubblog-post-not-shown/#%EC%B0%B8%EA%B3%A0%EC%9E%90%EB%A3%8C)

## 깃허브 블로그 변경사항 바로 확인하는 방법

깃허브 블로그에 수정 사항을 작성하더라도 서버에 반영돼서 변경 사항을 확인하는 데 시간이 조금 걸린다. 그런 과정 없이 바로 확인할 수 있도록 로컬 개발환경을 만들 수 있다.

방법
1. [minimal-mistakes quick-start-guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)에 들어온다.
2. 좌측의 [installation](https://mmistakes.github.io/minimal-mistakes/docs/installation/)에 들어간다.
3. 페이지에서 Install dependencies -> [official documents](https://jekyllrb.com/docs/)로 들어간다.
4. Ruby를 설치하기 위해서 [prerequisites](https://jekyllrb.com/docs/installation/)에 들어가 [Ruby](https://www.ruby-lang.org/en/downloads/)를 클릭한다.
5. `Ways of Installing Ruby`에서 자신의 개발 환경에 맞는 걸 선택한다. (나는 윈도우니까 [RubyInstaller](https://rubyinstaller.org/)를 선택.)
6. `Download 클릭` -> `WITH DEVKIT` -> `=>` 표시가 있는 버전 다운로드 
7. 설정 그대로 유지하고 Ruby 설치
8. 설치 완료 후 뜨는 cmd 창에서 `3` 입력 후 enter
9. 설치 완료 후 cmd를 닫고 다시 실행하여 `gem install jekyll` 실행
10. `gem install bundler` 실행
11. 자신의 깃허브 블로그 폴더가 있는 디렉토리에서 `shift` + 우클릭 -> '여기에 PowerShell 창 열기' 선택
12. `cd`를 이용해 깃허브 폴더에 들어간 뒤 Windows PowerShell에 `bundle install` 실행
13. 설치 완료 후 `bundle exec jekyll serve` 실행 **(끝)**
(참고한 영상에서처럼 `cannot load such file -- webrick` 이슈가 발생하면 `bundle install webrick`을 실행)


Reference: [테디노트TeddyNote](https://www.youtube.com/watch?v=0TeHUqSAb6Q&list=PLIMb_GuNnFwfQBZQwD-vCZENL5YLDZekr&index=5)

## 글자 크기 수정

모바일 화면에서는 기본 글자 크기가 사용되고, HD 규격의 화면 비율 정도에서는 미디움으로 분류돼 별도의 글자 크기를 사용하고 있다는 것을 처음 알았다. 

글자 크기를 통일 시키거나 화면에 따라 다르게 변화를 주고 싶으면 `/_sass/minimal-mistakes/_reset.scss`에서 수정하면 된다.

```css
html {
  /* apply a natural box layout model to all elements */
  box-sizing: border-box;
  background-color: $background-color;
  font-size: 16px;

  @include breakpoint($medium) {
    font-size: 16px;
  }

  @include breakpoint($large) {
    font-size: 18px;
  }

  @include breakpoint($x-large) {
    font-size: 18px;
  }

  -webkit-text-size-adjust: 100%;
  -ms-text-size-adjust: 100%;
}
```

Reference: [danggai.github.io](danggai.github.io)

## 깃허브 블로그 유튜브 동영상 삽입하기

나는 Minimial-mistakes theme를 사용하기 때문에 참고한 블로그의 두 번째 방법을 사용하였다.  
(_include 폴더의 video 파일은 영상의 id 속성 값만 있으면 유튜브 영상을 임베딩할 수 있도록 해준다고 한다.)

![image](https://github.com/StatPage/blog-images/assets/61931924/1efa524b-132d-4a90-861a-f779150f7d9a){: .align-center}
*색칠된 부분을 아래 명령어 id에 넣어주자.*

{% include video id="FABOw10lRbg" provider="youtube" %}

Reference: [평생 공부 블로그: Today I Learned](https://ansohxxn.github.io/blog/youtube/)