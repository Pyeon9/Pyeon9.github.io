---
layout: post
title: "[Git] 포스트와 빈 카테고리"
description: >
   깃블로그에 처음 작성한 포스트가 모든 카테고리에서 나타났던 문제 해결.
---

깃블로그를 처음 만들고 기본적인 설정을 마친 후에, 포스팅하기에 앞서 아래와 같이 카테고리를 먼저 만들었다.    

![Categories](https://github.com/Pyeon9/images-for-github-page/blob/main/setup/2021-04/04-13-GitHub-Page-category/_featured_categories.png?raw=true)    


그 후 `Pyeon9.github.io` 경로에 카테고리 이름으로 폴더를 만들고(sidebar에 보이는 이름이 아닌 카테고리.md 파일의 slug로), 그 안에 _posts 폴더를 만들었다.

그리고 나서 첫 번째 포스트인 [[Git] 깃블로그에 포스팅하기](http://Pyeon9.github.io/blog/diary/2021-04-12-my-first-post/)를 **Diary** 폴더에 작성하였고, 아래와 같이 Diary 카테고리에 글이 잘 올라갔음을 확인했다.   

![diary](https://github.com/Pyeon9/images-for-github-page/blob/main/setup/2021-04/04-13-GitHub-Page-category/cat-diary.png?raw=true)   


그런데 다른 카테고리로 들어가도 이 글이 나타나는 현상을 맞닥뜨렸다.   
카테고리별로 폴더를 만들어서 글을 구분하려한 것인데, 다른 카테고리에도 글이 나타나서 당황스러웠다.      
검색을 통해서 `_config.yml` 파일의 collection 등 수정해야 하는 부분을 모두 찾아 수정했지만, 이 현상은 해결되지 않았다.    
심지어는 아래와 같이 study 카테고리에서 해당 글을 열람해도 이 글이 diary 카테고리에 작성되었다고 표시되었다.   

![study](https://github.com/Pyeon9/images-for-github-page/blob/main/setup/2021-04/04-13-GitHub-Page-category/cat-study.png?raw=true)
![study-post](https://github.com/Pyeon9/images-for-github-page/blob/main/setup/2021-04/04-13-GitHub-Page-category/study-first-post.png?raw=true)   


한참을 씨름하다 지인의 허락을 받고 지인의 블로그를 fork하여 비교해보다가 원인을 알아냈다.   
원인은 허무할 정도로 사소한 것이었는데, 바로 해당 카테고리에 글이 하나도 없어서였다!   
이게 무슨 말이냐 하면, **카테고리를 만들어 놓고 글을 하나도 쓰지 않으면 내가 작성한 모든 글이 해당 카테고리에 나타난다**는 것이다.   
hydejack 테마의 모든 파일을 열어 읽어보지는 못했지만 어딘가에 이런 설정이 되어 있던 것 같다.   

테마별로 이런 설정이 안 되어 있는 테마도 있을테지만, 나랑 같은 현상을 겪어 검색을 통해 이 글을 보게된 사람들에게 도움이 되었으면 하는 마음으로 글을 남긴다. 


<!-- Last modified: 21-04-13, 11:43 -->