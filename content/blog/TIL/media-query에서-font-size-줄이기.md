---
title: Media Query에서 font-size 줄이기
date: 2021-08-23 15:08:60
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

만들고자 하는 화면의 반응형 기준은 총 4가지(1440px / 992px / 768px / 576px)였다. Bootstrap 만으로는 한계가 있어 CSS 파일에 Media Query를 작성했는데, 이상하게도 화면을 아무리 줄여봐도 `font-size` 값이 줄어들지 않았다.

예를 들어 HTML에 이런 코드를 작성하고,

```html
<div>
	<span class="text-span">왜 안 줄어들지?</span>
</div>
```

CSS에 이런 코드를 작성했다.

```html
@media (max-width: 1440px) {
	.text-span {
		font-size: 32px;
	}
}

@media (max-width: 992px) {
	.text-span {
		font-size: 28px;
	}
}
```

창 크기를 1440px에서 992px로 줄였을 때, 당연히 `font-size`도 32px → 28px로 줄어들어야 한다고 생각했는데, 줄지 않았다. 하다하다 안 돼서 결국 동료 분이 찾아주셨는데, 매우 간단히 해결할 수 있었다.

```css
@media (max-width: 1440px) {
	div span.text-span {
		font-size: 32px;
	}
}

@media (max-width: 992px) {
	div span.text-span {
		font-size: 28px;
	}
}
```

`<span>` 안에 있는 `font-size`를 줄이려면 `<div>`에 속성을 줄 때처럼 클래스명만 명시하는 것이 아니라 앞에 꼭 태그명을 명시해줘야 하는 것 같다.