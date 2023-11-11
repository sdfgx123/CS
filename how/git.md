# Git 사용 방법

## 1. .gitignore 파일을 사용하려면?
* .gitignore 파일 수정 및 저장
* git add .gitignore
* git commit -m "message"

<p>하지만 이미 추적되고 있는 파일을 .gitignore에 추가한 경우에는, 그저 .gitignore을 업데이트하는 것만으로는 그 파일이 더 이상 추적되지 않는 것은 아님 -> Git는 이미 추적되고 있는 파일에 대해 .gitignore를 무시하기 때문 -> 이런 파일을 Git이 무시하게 하려면, 명시적으로 Git의 추적에서 제거 필요</p>

* git rm --cached {추적 대상 파일명} 통해서 추적 cache에서 제거함
* 만약, .gitignore에 추가한 파일이 여러 개 있고,이를 한 번에 제거하려면: git rm --cached -r .

<p>git rm --cached -r . 을 입력하니 commit 탭 -> changes에 .gitignore와 README.md 파일이 삭제된 채로 변경사항에 추가됐는데 어떻게 된거지? 나는 저 두 파일이 추적 제외되는 것을 원하지 않았는데</p>

* git rm --cached -r . 명령을 실행하면 Git 추적 목록에서 모든 파일이 제거되지만 로컬 디렉토리에서는 삭제되지 않음 / 이렇게 하면 Git은 이러한 파일들이 새로 추가되었거나 삭제된 것으로 인식