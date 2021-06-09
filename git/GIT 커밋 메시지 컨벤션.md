> 본 글은 NHN Cloud의 [좋은 git 커밋 메시지를 작성하기 위한 7가지 약속](https://meetup.toast.com/posts/106)의 내용을 보다 짧게, 핵심만 옮겨 적은 글입니다.

## 왜 Git 커밋 메시지를 잘 써야 할까?

- 아래 3가지를 고려한다면, Git 커밋 메시지를 일관되게 쓰는 것이 중요하다.

1. 더 좋은 커밋 로그 가독성
2. 더 나은 협업과 리뷰 프로세스
3. 더 쉬운 코드 유지보수

- 아래에서는 Git 커밋 메시지를 잘 쓰기 위한 약속을 제시하고 있다.

## 7가지 커밋 메시지 약속

> 아래 내용은 커밋 메시지를 **영어**로 작성하는 경우에 한한다. 
> 한글 커밋 메시지는 다른 방법을 참고하여 사용하면 된다.

1. 제목과 본문은 **한 줄을 띄어** 분리한다.
2. 제목은 영문 기준 **50자 이내**로 작성한다.
3. 제목 첫 글자는 **대문자**로 작성한다.
4. 제목 끝에 **마침표(.)** 을 쓰지 않는다.
5. 제목은 **명령조**로 작성한다.
6. 본문은 영문 기준 **72자마다** 줄을 바꾼다.
7. 본문은 어떻게 보다는 **무엇**과, **왜**에 맞춰 작성한다.

## 1. 제목과 본문 한 줄 띄어쓰기

- 커밋 메시지는 **50자 이내의 요약문장**과 **공백 한 줄**, 그리고 **설명문**으로 구성하면 좋다.
- 이 내용은 강제 사항이 아니며 모든 커밋 메시지를 제목과 본문으로 구성할 필요는 없다. 때에 따라서는 아래 예시와 같이 변경사항을 한 줄로 요약한 커밋 메시지가 더 효율적이다.
  > 로그인 페이지 디자인 수정

- 다만 충분한 설명이 필요한 경우에는 제시된 규칙을 사용할 것을 권한다.
- 다음의 커밋 메시지는 제시된 규칙대로 **제목, 공백 한 줄, 본문**으로 구성되어 있다.
  > Derezz the master control program
  > 
  > MCP turned out to be evil and had become intent on world domination.
  > This commit throws Tron's disc into MCP (causing its deresolution)
  > and turns it back into a chess game.

- `git log`를 실행하면 위의 메시지가 아래와 같이 나타난다.
  > $ git log
  > commit 42e769bdf4894310333942ffc5a15151222a87be
  > Author: Kevin Flynn <kevin@flynnsarcade.com>
  > Date:   Fri Jan 01 00:00:00 1982 -0200
  > 
  >  Derezz the master control program
  > 
  >  MCP turned out to be evil and had become intent on world domination.
  >  This commit throws Tron's disc into MCP (causing its deresolution)
  >  and turns it back into a chess game.
- 규칙대로 **제목과 공백 한 줄, 본문**으로 구성되어 있다.

- 여기서 `git log --oneline`을 실행하면 아래와 같이 나타난다.
  > $ git log --oneline
  > 42e769 Derezz the master control program

- 제목과 본문 사이에 한 줄 공백을 넣지 않았다면 아래와 같이 보기 힘들어질 것이다.
  > $ git log --oneline
  > 42e769 Derezz the master control program MCP turned out to be evil and had become intent on world domination. This commit throws Tron's disc into MCP (causing its deresolution) and turns it back into a chess game.

- 이번엔 `git shortlog` 라는 명령어를 실행해보자. 제목과 본문이 한 줄 공백으로 나뉘어진 커밋들은 아래와 같이 나타난다. 
(작성자 별로 커밋 메시지 제목이 나뉘어 나타나는 것을 알 수 있다.)
  > $ git shortlog
  > Kevin Flynn (1):
  >     Derezz the master control program
  > 
  > Alan Bradley (1):
  >     Introduce security program "Tron"
  > 
  > Ed Dillinger (3):
  >     Rename chess program to "MCP"
  >     Modify chess program
  >     Upgrade chess program
  > 
  > Walter Gibbs (1):
  >     Introduce protoype chess program

## 2. 제목은 영문 기준 50자 이내로

- 50자는 엄격한 제한이 아닌 경험상의 법칙이다. 제목을 이 길이로 유지하면 알아보기가 쉽고, 작성자도 변경사항을 설명할 수 있는 가장 간결한 말이 무엇인지 고민해볼 수 있다.

- 또한 GitHub의 UI는 이러한 규칙을 완전히 인지한다. 만약 50자 제한을 초과하면 아래와 같이 경고가 표시된다.

  ![](https://images.velog.io/images/bky373/post/b776065e-08d6-4bf9-b09d-f2942f2d8f93/image.png)

  > [이미지 출처](https://chris.beams.io/posts/git-commit/#limit-50)

- 혹시나 제목이 72자보다 길어지는 경우에는, 뒷부분의 내용이 줄임표로 변경되어 일부분이 잘리게 된다.

  ![](https://images.velog.io/images/bky373/post/c4254fbf-30ec-4e86-b407-672f799e6c6b/image.png)

## 3. 제목 첫 글자는 대문자로

- 말그대로 제목의 첫 글자는 대문자로 시작한다.

  > **Accelerate** to 88 miles per hour

- 아래 예시처럼 소문자 사용은 지양한다.

  > **accelerate** to 88 miles per hour (x)

## 4. 제목 끝에 마침표(.) 금지

- 제목 줄에 마침표(`.`)는 굳이 필요하지 않다. 게다가 50자 이내로 제목을 작성할 때 공간 한 칸 한 칸이 소중하다. 때문에 마침표를 쓰지 않는다. 

  > Open the pod bay **doors**

- 아래 예시는 지양한다.

  > Open the pod bay **doors.** (x)

## 5. 제목은 명령조로

- 커밋 메시지 가장 첫 문장의 영문법은 **명령조**로 한다. 이는 첫 단어를 **동사원형**으로 쓰라는 의미다. 

- 처음 **명령문**을 접하면 어색하게 느껴지거나 무례하게 느껴질 때가 있다. 
- 하지만 사실 **명령문**은 Git 커밋 메시지의 제목으로 딱 좋다.  무엇보다 **Git 스스로가 자동 커밋을 작성할 때 명령문을 사용한다.** 예를 들어 `git merge`를 실행했을 때 커밋 메시지의 기본값은 아래와 같다.

  > **Merge** branch 'myfeature'

- 다른 예시로 `git revert` 를 실행할 때도 Git은 기본 메시지에서 **명령문**을 사용한다.

  > **Revert** "Add the thing with the stuff"
  >
  > This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.

- 또한 GitHub에서 풀 리퀘스트의 `Merge` 버튼을 클릭할 때 마찬가지로 **명령문**을 기본 값으로 사용한다.

  > **Merge** pull request #123 from someuser/somebranch

- 이처럼 커밋 메시지를 명령문으로 작성하는 것은, **Git의 빌트-인 컨벤션(Built-in Convention)을 그대로 따른다는 것을 의미**한다. 
- 커밋 제목을 **명령문**으로 작성하면 자동 메시지로 채워진 커밋 사이에 자연스레 녹아든다. 그리고 앞에서 본 `git shortlog`와 같은 명령어와도 연계되어 잘 어울린다.

- 물론 이 내용은 제목이나 한 줄 커밋 메시지의 이야기이다. 커밋 메시지의 **본문까지 명령조로 시작할 필요는 없다**. 여기에서는 자연스럽게 **과거형이나 현재형 시제**를 사용하여 변경된 내용을 서술하면 된다.

- 제목을 작성할 때 간단한 팁을 주자면, 앞에 제시된 구문 뒤에 제목을 배치했을 때 문법이 어울리는지 확인해보는 방법이 있다.

  > If applied, this commit will [제목] 
  >
  > (어울리는 예문들)
  > - (If applied, this commit will) **Refactor subsystem X for readability**
  > - (If applied, this commit will) **Update getting started documentation**
  > - (If applied, this commit will) **Remove deprecated methods**
  > - (If applied, this commit will) **Release version 1.0.0**
  > - (If applied, this commit will) **Merge pull request #123 from user/branch**
  >
  > (어울리지 않는 예문들)
  > - (If applied, this commit will) **Fixed bug with Y**
  > - (If applied, this commit will) **Changing behavior of X**
  > - (If applied, this commit will) **More fixes for broken stuff**
  > - (If applied, this commit will) **Sweet new API methods**


## 6. 본문은 영문 기준 72자마다 줄 바꾸기

- Git은 커밋 메시지에서 자동으로 줄바꿈을 해주지 않는다. 따라서 본문을 작성할 때는 오른쪽 여백을 염두에 두어야 하며, 텍스트를 수동으로 나눠주어야 한다.
- 또한 들여쓰기 할 공간까지 고려하여 내용을 작성해야 한다.

## 7. 본문은 `어떻게`보다 `무엇을`, `왜`에 맞추기

- 제목에서 **핵심 내용**이 `무엇`인지 잘 전달했다면, 본문에서는 **구체적인 내용**이 `무엇`인지 잘 전달하는 게 좋다.

- 내용은 어떻게 보다는 **무엇**과 **왜(이유)**에 초점을 맟추도록 한다. 

- 아래 커밋 메시지 본문은, **변경된 내용**과 **이유**에 대해 잘 설명하는 좋은 예시다. (Bitcoin Core 프로젝트에서 실제 사용된 커밋 메시지이다.)
  > commit eb0b56b19017ab5c16c745e6da39c53126924ed6
  > Author: Pieter Wuille <pieter.wuille@gmail.com>
  > Date:   Fri Aug 1 22:57:55 2014 +0200
  > 
  >    Simplify serialize.h's exception handling
  > 
  >    Remove the 'state' and 'exceptmask' from serialize.h's stream
  >    implementations, as well as related methods.
  > 
  >    As exceptmask always included 'failbit', and setstate was always
  >    called with bits = failbit, all it did was immediately raise an
  >    exception. Get rid of those variables, and replace the setstate
  >    with direct exception throwing (which also removes some dead
  >    code).
  > 
  >    As a result, good() is never reached after a failure (there are
  >    only 2 calls, one of which is in tests), and can just be replaced
  >    by !eof().
  > 
  >    fail(), clear(n) and exceptions() are just never called. Delete
  >    them.

# Ref

- [좋은 git 커밋 메시지를 작성하기 위한 7가지 약속 - NHN Cloud](https://meetup.toast.com/posts/106)
- [How to Write a Git Commit Message - Chris Beams](https://chris.beams.io/posts/git-commit/#separate)
