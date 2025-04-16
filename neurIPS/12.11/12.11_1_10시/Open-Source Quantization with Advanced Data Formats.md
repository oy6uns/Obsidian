### 제목/저자/기관

Michael Schulte
AMD

### 내용 요약

> 주제 / 의의 / 제시한 방법론 → 결국 abstract

- AI Data Formats
- Brevitas Quantization Library (open source)
- Experimental Results
- Summary

더 짧은 비트를 가지는 데이터 포맷을 사용함으로써 우리는 더 좋은 power efficiency를 가질 수 있다. 
그러나, 쉽지는 않음. 

AMD, Arm, Intel, Meta, Microsoft, NVIDIA, Qualcomm 은 협업하여 하나의 공통 data format을 만들었다. 
OCP Microscaling Format (MX) Specification

**Brevitas - Pytorch Library**
새로운 pytorch library를 만들어서 quantize를 더 comfortable하게 만들었다. 

Modular Python + C++ 언어를 base로 한 library

*Export to inference toolchain*
- ONNX based 
- Pytorch Script based

### Experimental Results
모든 연산에 MX operator가 사용되었다. 

Language Translation : BLEU Score 상승
Language Encoding : F-1 Score 상승
Image Classification : Top-1 Accuracy 상승
Speech Recognition : WER 하락
Recomendations : AUC 상승

Speech Recognition을 제외하고는 모두 좋은 성능을 보여주었다. 






> keyword cloud 통계 낼 때 사용 - 저자가 논문에 작성한 키워드 포함해도 무관

quantization 

### 인사이트 한 줄 평

> 현재 회사 업무의 적용 가능성 또는 향후 적용 필요성 위주


## QnA
1. high degree of customization
2. scaling factor
	robustness bit수가 작아지면 normalization에 굉장히 민감해진다. 
	-> float16 등 다른 레이어를 활용. 
3. coincidence
4. scailing factors 어떻게 찾았나?
5. finetuning 시간
	mx data type은 inference 면에서 더 효율성을 줄 수 있다. 
6. 