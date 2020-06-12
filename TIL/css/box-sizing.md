### box-sizing 
- CSS property sets
- `content-box`와 `border-box`
- 요소(box)의 높이와 너비 계산하는 방식을 결정

*content-box*
- 기본값. box width = width of the content, box height = height of the content
- CSS box model의 layer(content, padding, border, margin) 중 **content**의 높이 너비만을 재겠다
- 즉 box 전체 크기 계산하려면 content에 padding border margin 따로 더해야 한다

*border-box*
- border까지 box로 친다. (margin은 시각적으로 보이는 경계선인 border의 바깥 여백이므로 제외)
- box width = width of the content + padding + border, box height = height of the content + padding + border

>The problem for present-day developers is that those absolute pixel lengths don't translate to responsive design, so the same math doesn't apply anymore.<br>

>With `box-sizing: border-box`, we can change the box model to what was once the "quirky" way, where an element's specified width and height aren't affected by padding or borders.
This has proven so useful in responsive design that it's found its way into reset styles.


https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing<br>
https://css-tricks.com/box-sizing/
