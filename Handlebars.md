# What is Handlebars?

Handlebars.js는 자바스크립트의 템플릿 엔진 중 하나이다. 

Mustache?

* 콧수염 모양의 {{ }} Bracket을 이용하여 data를 표현한다.

***

### 목적

* HTML + Bracket의 구성으로 간단한 구조를 지닌다.
* 개발자와 디자이너의 협업 과정에서 서로 이해하기 쉽다는 장점을 가진다.
* 과도한 로직사용을 지양한다. 

***

### 간단한 사용법

* `{{title}}`과 같은 형태로 표현된다.
* 반복과 같은 다른 템플릿엔진에서 제공하는 헬퍼가 존재한다.

```handlebars
{{#each List as |item i|}}
	{{!-- this makes you able to access to each items in array --}}
	{{item}}
	{{!-- this means the index in this array --}}
	{{i}}
{{/each}}
```

```handlebars
{{#List}}
	{{item}}
{{/List}}
```

`{{item}}` : List라는 배열 요소 객체의 item 속성값이 바인딩 된다.

`{{i}}` : List라는 배열의 인덱스를 의미한다.

***

### 기본구조 (바인딩 & 헬퍼)

#### 1. 바인딩

```html
<script id="entry-template" type="text/x-handlebars-template">
<table>
	<thead>
		<th>이름</th>
		<th>아이디</th>
		<th>메일주소</th>
	</thead>
	<tbody>
		{{#users}}
		<tr>
			<td>{{name}}</td>
			<td>{{id}}</td>
			<td><a href="malito:{{email}}">{{email}}</a></td>
		</tr>
		{{/users}}
	</tbody>
</table>
</script>
```

script 태그 안에 type을 `"text/x-handlebars-template"` 등으로 설정하고 위치시킨다.

`{{ }}`로 감싸진 부분이 주입되는 데이터와 바인딩 되는 부분이다.

```javascript
//핸들바 템플릿 가져오기
var source = $("#entry-template").html();

//핸들바 템플릿 컴파일
var template = Handlebars.compile(source);

//바인딩할 데이터
var data = {
    users : [
        {name:"홍길동1", id:"aaa1", email:"aaa1@gmail.com"},
        {name:"홍길동2", id:"aaa2", email:"aaa2@gmail.com"},
        {name:"홍길동3", id:"aaa3", email:"aaa3@gmail.com"},
        {name:"홍길동4", id:"aaa4", email:"aaa4@gmail.com"},
        {name:"홍길동5", id:"aaa5", email:"aaa5@gmail.com"}
    ]
};

//핸들바 템플릿 데이터를 바인딩해서 HTML 생성
var html = template(data);

//생성된 HTML을 DOM에 주입
$('body').append(html);
```

#### 2. 사용자 정의 헬퍼

`if`, `unless` 등의 기본 헬퍼와 `사용자 정의 헬퍼`를 추가해서 낮은 수준의 로직을 포함할 수 있다.

```html
<script id="entry-template" type="text/x-handlebars-template">
<table>
	<thead>
		<th>이름</th>
		<th>아이디</th>
		<th>이메일주소</th>
	</thead>
	<tbody>
		{{#users}}
		<tr>
			<td>{{name}}</td>
			<td>{{id}}</td>
			{{!-- 사용자 정의 헬퍼인 email에 id를 인자로 넘긴다 --}}
			<td><a href="mailto:{{email id}}">{{email id}}</a></td>
		</tr>
		{{/users}}
	</tbody>
</table>
</script>
```

```javascript
var source = $("#entry-template").html();
var template = Handlebars.compile(source);
var data = {
    users : [
        {name:"홍길동1", id:"aaa1", email:"aaa1@gmail.com"},
        {name:"홍길동2", id:"aaa2", email:"aaa2@gmail.com"},
        {name:"홍길동3", id:"aaa3", email:"aaa3@gmail.com"},
        {name:"홍길동4", id:"aaa4", email:"aaa4@gmail.com"},
        {name:"홍길동5", id:"aaa5", email:"aaa5@gmail.com"}
    ]
};

Handlebars.registerHelper('email', function(id){
    return id + "@daum.net";
});

var html = template(data);

$('body').append(html);
```

#### 3. 기본 제공 헬퍼

`if` 의 경우 오직 `true/false`만 판별할 수 있기 때문에 복잡한 조건은 핸들바를 통해 처리 할 수 없다.

사용자 정의 헬퍼를 추가하여 처리할 수도 있지만, 템플릿을 배제하는 것이 바람직하다.

```handlebars
{{#each users}}
<tr>
	{{#if @first}}
		{{!--첫번째 index--}}
		<td>{{@key}}번째 요소</td>
	{{else if @last}}
		{{!--마지막 index--}}
		<td>{{@key}}번쨰 요소</td>
	{{else}}
		{{!-- 중간 index--}}
		<td>{{@key}}번쨰 요소</td>
	{{/if}}
</tr>
{{/each}}
```

`@index` 혹은 `@key`로 요소의 순번에 접근이 가능하다.

array와 object 모두 잡을 수 있는 key가 더 범용성이 좋다.

***

### 출처

[Handlebar](https://programmingsummaries.tistory.com/381)

[핸들바 사용법](https://sailboat-d.tistory.com/40)