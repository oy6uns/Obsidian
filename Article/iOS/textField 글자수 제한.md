
**func** textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {

        **if** **let** char = string.cString(using: String.Encoding.utf8) {

            /// backSpace는 글자수 제한이 걸려도 눌릴 수 있게 해줌

            **let** isBackSpace = strcmp(char, "\\b")

            **if** isBackSpace == -92 {

                **return** **true**

            }

        }

        **guard** textField.text!.count < 7 **else** { **return** **false** }

        **return** **true**

    }

기존의 textField의 글자수를 제한하는 코드이다.
그런데, 영어는 글자수 제한이 잘 작동하는데, 한글로는 글자수 제한이 조금 이상하게 됐다. 
ex) "하늘이정말맑네"(7/7) 에서 받침에 'ㅇ' 을 추가하여 "하늘이정말맑넹" 을 치고 싶었는데, 글자수 제한 때문에 'ㅇ'이 쳐지지 않았다. 

문제가 발생하는 부분은 한글의 받침 때문이었다. Swift에서 문자열의 길이를 체크할 때, Unicode에 따라 문자를 분리하고 세는데, 한글의 경우 받침 유무에 따라 길이가 다르게 인식된다. 기본적으로 Swift는 한글 문자를 분해하지 않고 길이를 체크하므로, 받침이 있는 글자가 마지막에 위치할 때 길이 1로 세게 되는 것이었다. 

이러한 문제를 해결해주기 위해 textField가 변경되는 것을 계속 확인하는 UITextField.textDidChangeNotification에 함수를 추가하여 해결해주었다!

**private** **func** addObserver() {

        NotificationCenter.default.addObserver(**self**, selector: **#selector**(textDidChange), name: UITextField.textDidChangeNotification, object: **nil**)

    }

    **@objc** **func** textDidChange(noti: NSNotification) {

        **if** **let** text = worryTitleTextField.text {

            **let** maxLength = 7

            **if** text.count >= maxLength {

                **if** **let** endIndex = text.index(text.startIndex, offsetBy: maxLength, limitedBy: text.endIndex) {

                    **let** fixedText = text[..<endIndex]

                    /// 공백을 추가했다가

                    worryTitleTextField.text = String(fixedText) + " "

                    /// 공백을 바로 제거해줌으로써 한글 받침을 쓸 수 있게 구현해준다.

                    DispatchQueue.main.async {

                        **self**.worryTitleTextField.text = String(fixedText)

                    }

                }

            }

        }

    }

아래와 같이 공백을 추가했다가 DispatchQueue.main.async 비동기함수에서 바로 지워줌으로써 사용자 입장에서는 항상 뒤에 공백이 있는 문자열에 텍스트를 작성하기 때문에 받침을 마지막에 사용해줄 수 있고, 인터페이스 상에서는 공백이 보이지 않게 된다. 

편법인거 같기도 해서 더 좋은 방식이 있다면 공유해주세요☺️