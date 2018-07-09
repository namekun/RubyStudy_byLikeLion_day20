# 20180709 JS with LikeLion

* 오전 복습

`respond_to do |format|` //요청방식에 따라서 어떻게 반응해야 하는가?

`setInterval(function(){alert("hi!");},3000)` -> 주어진 시간에 한번씩 수행하라(코드의 3000은 3초)



* 오늘 할일 
  * Template
  * pusher(채팅방, 채팅방에 조인하는 것.)
  * 별점

# 캐싱?

: 데이터나 값을 미리 복사해 놓는 임시 장소를 가리킨다. 캐시는 캐시의 접근 시간에 비해 원래 데이터를 접근하는 시간이 오래 걸리는 경우나 값을 다시 계산하는 시간을 절약하고 싶은 경우에 사용한다. 캐시에 데이터를 미리 복사해 놓으면 계산이나 접근 시간 없이 더 빠른 속도로 데이터에 접근할 수 있다.

캐시는 시스템의 효율성을 위해 여러 분야에서 두루 쓰인다.



### ajax

요청을 보내면, 그 즉시 응답이 날아오는것이 아니라 서버에서 특정한 행동들을 진행된 이후에 그 응답으로 자바스크립트 코드를 보내준다. => 비동기방식

```ruby
    $.ajax({
                url: "/movies/comments/" + comment_id, //resouceful routing
                method: "delete"
            })
```

"/movies/comments/" + comment_id 어떤 주소로 요청이 왔을때 어떤 컨트롤러로 지정해줄지 정해주고

controller에서 해당 method를 찾고 나서 실행하면 그 method명과 같은 자바스크립트 코드가 실행된다.



- Template
- Pusher (채팅방, 채팅방에 join하는거 ) 
- 별점





### template

*gemfile*

```ruby
#bootstrap 3
gem 'bootstrap-sass'

turbolink 주석달기
-----------------------
    
#bootstarp 4
gem 'bootstrap', '~>4.1.1'

#font-awesome
gem 'font-awesome-rails'


```

`rails g controller home index` : home_controller에 index 액션 하나 추가

그 후에 *routes.rb* 에서 `root 'home#index'` 해주기

*application.js* <- 3버전

```ruby
//= require jquery
//= require jquery_ujs
//= require bootstrap
```

> 4버전은 bootstrap 위에 //=require popper

*application.scss* `@import 'bootstrap';` 해주자



노가다

1. css파일을 긁어서 *home.scss* 에 붙여주자

```ruby
@import 'style';
@import 'responsive';
```

2. 파일 복사한다 *~/template_app/vendor/assets/stylesheets* 에 import해준 파일들을 stylesheet에서 찾아서

3. *~/template_app/app/views/layouts/application.html.erb* 에다가 해당 metatag 넣어준다

   ```ruby
   	<!-- Mobile Specific Metas -->
       <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
   	
       <%= stylesheet_link_tag    'application', media: 'all' %>
       <%= stylesheet_link_tag    params[:controller], media: 'all' %>
       <%= javascript_include_tag 'application' %>
     </head>
   ```

   > 터보링크 관련한거 다 지우고,  `<%= stylesheet_link_tag    params[:controller], media: 'all' %>` 추가한다.

4. `binn02:~/template_app $ rake assets:precompile` > 컴파일해주는거

5. `binn02:~/template_app $ rake assets:clobber` > 컴파일한 파일 지우는것

*~/template_app/config/initializers/assets.rb*

```ruby
Rails.application.config.assets.precompile += %w( home.js
                                                  home.scss)
마지막줄 주석 풀고, 뒤에 추가! 콤마찍지뫄!
```

6. *~/template_app/app/assets/javascripts/home.coffee* 를 뒤에 home.js로 바꾸어준다. 

   > `rake assets:precompile` 해주면 어디서 에러가 났는지 찾아준다. 동시에 public 밑에 뭐가 생성되니까
   >
   > `rake assets:clobber` 하면 없애준다.

7. home.scss에 style.css에 있는 `@import ~~` 다 넣어주기.. 그리고 *~/template_app/vendor/assets/stylesheets* 에 파일들 복붙.

8. 그 index.html 아톰에서 킨거에서 `< nav >`, `<sidebar>`, `<footer>` 를 view 밑에 있는 *shared/~* 폴더 밑에 `_nav.html.erb` 이런식으로 모두다 저장시킨다

9. layout 에 *application.html.erb*에 

   ```ruby
     <body>
       <%= render 'shared/nav' %>
   	<%= render 'shared/sidebar' %>
       <%= yield %>
       <%= render 'shared/footer' %>
       <%= yield 'javascript_content' %>
     </body>
   
   ```

   > `<%= yield 'javascript_content' %>` 하면 특정 파일이 아니라 한 블럭을 지칭! 
   >
   > `<% content_for ~ do%>` 이렇게 시작하는 파일을 저때 읽어라 !

10. index.html.erb

```ruby
<section class="page-title style1 parallax parallax1">
...				
</section><!-- /.flat-subscibe -->
		
	<% content_for 'javascript_content' do %>

	<script>
	    console.log("content_for");
	    
	</script>
	<% end %>
```

> ```ruby
> 	<% content_for 'javascript_content' do %>
> 	<%= javascript_include_tag params[:controller] %>
> 	
> 	<script>
> 	    console.log("content_for");
> 	</script>
> 	<% end %>
> ```

11. *home.js* 

```ruby
//= require jquery.min
//= require bootstrap.min
//= require easing
//= require gmap3.min
//= require owl.carousel
//= require main
```

*~/template_app/vendor/assets/javascripts* 로 자바스크립트 파일 복붙/



지금까지 하고있는것은 **asset pipeline** 으로,  pre-processors 역할을 한다.

http://guides.rubyonrails.org/asset_pipeline.html

vender에는 잘 변하지 않는 파일을, asset에다가는 잘 변하는 파일을 넣는다.

home.js에 있는 이름으로는 controller를 만들지말쟈



12. *index.html.erb*

```ruby
		
	<% content_for 'javascript_content' do %>
	<%= javascript_include_tag params[:controller] %>

<!-- 사용자가 작성한 자바스트립트 넣긔	 %>

	<% end %>
```

13. `rake assets:clobber` 하고 다시`rake assets:precompile` 이거하기

- 이미지 오류 해결하기

  - *~/template_app/app/assets/images* 에 image 에 있는 모든 파일 넣기

  - *~/template_app/app/views/home/index.html.erb* 에 있는 모든 png, jpg 파일 코드 루비코드로 바꾸기

    `	<img src="<%= asset_path 'page/img-v2-01.png'%>" alt="">` 이렇게

  - 만약 css 파일에서 난 에러라면 ` background: url("../images/parallax/01.png") top center repeat;` 이런식으로 !

    여기에는 루비태그를 쓸수없고,`background: url(asset-path("parallax/01.png")) top center repeat;` 이렇게 바꿔준다.

    > dash 랑 루비태그에서 underscore랑 헷갈리지 말것!! `asset-path` 와 `asset_path` 는 달라염

- 폰트오류 해결하기

  - ~/template_app/public/fonts 에 폰트폴더에 있는 모든 폰트 복붙
  - search in this folder 에서 오류나는 폰트이름 복붙해서 css 파일찾은다음

  ```ruby
  	font-family: 'themify';
  	src:font-url('themify.eot?-fvbane');
  	src:font-url('themify.eot?#iefix-fvbane') format('embedded-opentype'),
  		font-url('themify.woff?-fvbane') format('woff'),
  		font-url('themify.ttf?-fvbane') format('truetype'),
  		font-url('themify.svg?-fvbane#themify') format('svg');
  	font-weight: normal;
  	font-style: normal;
  ```

  > 앞에 font폴더 지우고, url 을 font-url로 바꾸자.'





------



1. `rails g scaffold rooms`
2. *rooms.coffee* 를 *rooms.js* 로 변경
3. 그리고나서 *rooms.scss* 에 js @import 해준다. (거의 index와 비슷할테지만, 없는게 있다면 들고와준다 )
4. show.html.erb에 `<nav>` 랑 `<footer>`사이부분 쭉 긁어온다.
5. `rake assets:clobber`, `rake assets:precompile` 해준후, `rake db:migrate`
6. *~/template_app/config/initializers/assets.rb* 에 ` rooms.js  rooms.scss` 추가
