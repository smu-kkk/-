# What is Modal?

Modal Window는 UI 디자인 개념에서 자식 윈도우에서 부모 윈도우로 돌아가기 전에 사용자의 상호동작을 요구하는 창을 의미한다. 주로 대화상자를 부를 때 사용된다.

***

### 목적

* 사용자의 명령 인식
* 긴급 상황을 알리기 위함

***

### 기본구조

```html
<!--modal-->
<div class="modal fade" id="myModal" role="dialog">
    <div class="modal-dialog">
        <!--modal content-->
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal">x</button>
                <h4 class="modal-title">Modal Header</h4>
            </div>
            <div class="modal-body">
                <p>put your text in here area</p>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
            </div>
        </div>
    </div>
</div>
```

***

### 모달을 띄우는 방법

* `<a>`, `<button>` 태그 혹은 다른 element에 `data-toggle`, `data-target` 옵션을 사용

```html
<!--button-->
<button type="button" data-toggle="modal" data-target="#myModal">Open Modal</button>
<!--link-->
<a data-toggle="modal" href="#myModal">Open Modal</a>
<!--other elements-->
<p data-toggle="modal" data-target="#myModal">Open Modal</p>
```

​	이 경우, `data-toggle`옵션은 항상 필요하며 `<a>`태그의 경우에만 `data-target`대신 `href`를 사용할 수 있다.

​	배경을 클릭해도 모달을 닫히지 않게하려면 `data-backdrop="static"` 같은 옵션을 사용하면 된다.

* 자바스크립트를 직정 작성

```html
<button type="button" class="btn btn-ino btn-lg" id="myBtn">Open Modal</button>
```

​	별도의 `data-` 속성은 사용하지 않는다.

```javascript
$(document).ready(function(){
    $("#myBtn").click(function(){
       $("#myModal").modal(); 
    });
});
```

***

### 모달 작성

> `.modal`
>
> > `.modal-dialog`
> >
> > `.modal-content`
> >
> > > `.modal-header`
> > >
> > > `.modal-body`
> > >
> > > `.modal-footer`	

* `.modal`
  * 여러 div가 이루어진 모달에서 부모 div이다.
  * 모달들 간의 구별을 위해서 id 옵션을 넣어야 한다.
  * 화면에서 focus를 가져오는 역할을 한다. 따라서 `.fade`를 넣을 수 있다.
  * `role="dialog" aria-labelledby="myModalLabel"` : 모달 제목을 알린다.
  * `aria-hidden="true"` : 모달의 DOM요소를 건너뛰는 것을 보조 공학에게 전달한다.
  * `aria-describedby` : 모달 다이얼로그의 설명을 넣을 수 있다.
* `.modal-dialog`
  * `.modal-sm`, `.modal-lg` : 모달의 크기를 결정한다.
* `.modal-content`
  * `boder`, `background-color` : 모달의 스타일을 결정한다.
* `.modal-header`
  * `class="close"` : 버튼 스타일을 결정한다.
  * `data-dismiss="modal"` : 모달이 닫히도록 한다.
  * `class="modal-title"` : 헤더에 적당한 스타일을 결정한다.
* `.modal-body`
* `.modal-footer`
  * 오른쪽 정렬이 기본이다.

***

#### 모달에 값 전달

```html
<div class="modal fade" id="showPostsModal" tabindex="-1" role="dialog" aria-labelledby="showPostsLabel" aria-hidden="true">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="showPostsLabel">내용</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body">
                <input type="text" id="contentView" name="contentView" style="border:none">
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-toggle="modal" data-target="#updatePostsModal">수정</button>
                <button type="button" class="btn btn-primary" data-dismiss="modal">확인</button>
            </div>
        </div>
    </div>
</div> 
```

```javascript
var showModal = {
    init : function(){
        console.log('showModal.js');
    },
    showPosts : function(content){
        console.log(content);
        $('#showPostsModal').on('show.bs.modal',function(event){
            $('.modal-body #contentView').val(content);
        })
    }
};
showModal.init();
```



***

#### 출처

[Modal](https://aramk.tistory.com/23)

[모달에 값 전달하기](https://oneshottenkill.tistory.com/301)