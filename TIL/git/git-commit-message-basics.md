### 커밋 메시지 잘 쓰는 방법
https://chris.beams.io/posts/git-commit/#separate 정독을 권함!<br>
요약하면...<br>
1. 제목과 본문 빈 줄로 분리하기
2. 제목 줄은 50자 이내로
3. 대문자로 시작하기
4. 마침표 찍지 않기
5. 명령 혹은 지시조로 쓰기
> **Git itself uses the imperative whenever it creates a commit on your behalf.**<br>
> Writing this way can be a little awkward at first. We're more used to speaking in the *indicative mood*, which is all about reporting facts. That's why commit messages often end up reading like this: <br>
> * Fixed bug with Y <br>
> * Changing behavior of X <br>

> **A properly formed Git commit subject line should always be able to complete the following sentence:** <br>
> * If applied, the commit will *your subject line here*
6. 본문은 라인 당 72자 권장 - 에디터마다 right margin이 다르고, text wrap 결과가 다르므로
7. 본문은 '무엇을' 그리고 '왜'에 할애할 것. '어떻게' 말고
> In most cases, you can leave out details about how a change has been made. Code is generally self-explanatory inthis regard.
> Just focus on making clear the reasons why you made the change in the first place-the way things worked before the change (And what was wrong with that), the way they work now, and why you decided to solve it the way you did. 

++ IDE에서 제공하는 Git GUI보다는 CLI에 가까워지기