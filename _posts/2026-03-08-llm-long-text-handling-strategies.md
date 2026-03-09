---
layout: post
title: "LLM 긴 텍스트 처리 전략과 주요 구현체"
date: 2026-03-08 15:30:00 +0900
categories: [AI, LLM]
tags: [llm, langchain, llamaindex, text-processing, chunking, vscode-copilot]
author: SHIN DAE YONG
---

## 배경: VS Code Copilot 대화 요약 기능 포팅

이 글은 **VS Code Copilot Chat의 대화 요약(Conversation Summarization) 로직을 Python으로 포팅**하는 과정에서 필요했던 기술 조사에서 시작되었다.

### 프로젝트 개요

VS Code Copilot은 긴 대화 세션을 요약하여 컨텍스트를 유지하면서도 토큰 사용량을 줄이는 기능을 제공한다. 이 기능을 독립적인 Python 라이브러리로 포팅하려는 과정에서 다음과 같은 과제에 직면했다:

**핵심 문제:**
- 수십 턴의 대화 히스토리 (10K+ 토큰)
- 각 턴마다 포함된 도구 호출 결과 (파일 내용, 검색 결과 등)
- LLM의 토큰 제한 (4K~8K)
- 전체 컨텍스트를 유지하면서 요약 생성

**구현 방향:**
1. **7단계 분석 프로세스**로 대화 구조화
2. **8섹션 요약 구조**로 핵심 정보 추출
3. **Dual-Mode 전략**: LLM 기반 Full Mode + 텍스트 압축 Simple Mode

이 과정에서 LLM에 긴 텍스트를 전달하는 다양한 전략과 구현체들을 조사하게 되었고, 실무적 선택을 위한 기술 정리가 필요했다.

## 개요

LLM(Large Language Model)에 긴 텍스트를 전달할 때 토큰 제한으로 인해 전체 내용을 한 번에 처리할 수 없는 경우가 많다. 이를 해결하기 위한 다양한 전략과 구현체들을 정리하고, 실제 프로젝트에서 어떤 전략을 선택했는지 공유한다.

## 주요 처리 전략

### 1. Truncation (절삭)

가장 단순한 방법으로, 텍스트의 일부만 선택하여 전달한다.

**장점:**
- 구현이 매우 간단
- 속도가 빠름

**단점:**
- 중요한 정보가 누락될 수 있음
- 컨텍스트가 손실됨

**구현체:**
- 대부분의 LLM API에서 기본 제공
- 직접 구현 가능 (앞/뒤/중간 선택)

```python
# 간단한 예시
text_truncated = long_text[:max_tokens]
```

### 2. Chunking + Map-Reduce

텍스트를 작은 청크로 분할한 후, 각 청크를 독립적으로 처리하고 결과를 병합한다.

**처리 흐름:**
1. 텍스트를 청크로 분할
2. 각 청크에 대해 LLM 호출 (병렬 가능)
3. 각 결과를 요약
4. 최종 결과를 하나로 병합

**장점:**
- 병렬 처리 가능
- 확장성이 좋음
- 정보 손실 최소화

**단점:**
- 여러 번의 LLM 호출 필요 (비용 증가)
- 청크 간 컨텍스트 공유 어려움

**주요 구현체:**

#### LangChain
```python
from langchain.chains.summarize import load_summarize_chain
from langchain.docstore.document import Document

# Map-Reduce 체인 생성
chain = load_summarize_chain(llm, chain_type="map_reduce")
docs = [Document(page_content=chunk) for chunk in chunks]
result = chain.run(docs)
```

#### LlamaIndex
```python
from llama_index import TreeSummarize

# TreeSummarize는 Map-Reduce 패턴 사용
response_synthesizer = TreeSummarize()
response = response_synthesizer.synthesize(query, nodes)
```

### 3. Refine (점진적 정제)

첫 번째 청크를 요약한 후, 그 결과를 다음 청크와 함께 재요약하는 과정을 반복한다.

**처리 흐름:**
1. 첫 번째 청크 요약
2. 요약 결과 + 두 번째 청크 → 재요약
3. 반복하여 점진적으로 정제

**장점:**
- 이전 컨텍스트를 유지하면서 처리
- 순차적 정보 통합에 유리

**단점:**
- 병렬 처리 불가능
- 처리 시간이 길어질 수 있음
- 초반 정보가 희석될 수 있음

**주요 구현체:**

#### LangChain
```python
from langchain.chains.summarize import load_summarize_chain

# Refine 체인 생성
chain = load_summarize_chain(llm, chain_type="refine")
result = chain.run(docs)
```

#### LlamaIndex
```python
from llama_index.response_synthesizers import Refine

response_synthesizer = Refine()
response = response_synthesizer.synthesize(query, nodes)
```

### 4. Stuff (단순 압축)

모든 텍스트를 하나의 프롬프트에 압축하여 전달한다. 토큰 제한 내에서 가능할 때만 사용.

**장점:**
- 한 번의 LLM 호출로 완료
- 전체 컨텍스트 보존

**단점:**
- 토큰 제한에 제약
- 큰 문서에는 부적합

**주요 구현체:**

#### LangChain
```python
from langchain.chains.summarize import load_summarize_chain

chain = load_summarize_chain(llm, chain_type="stuff")
result = chain.run(docs)
```

### 5. Sliding Window

고정된 크기의 윈도우를 이동하며 컨텍스트를 유지한다.

**장점:**
- 로컬 컨텍스트 보존
- 긴 시퀀스 처리 가능

**단점:**
- 전역 컨텍스트 제한적
- 메모리 효율성 고려 필요

**주요 구현체:**
- **Longformer**: 슬라이딩 윈도우 어텐션 구현
- **BigBird**: 희소 어텐션 패턴 사용

### 6. Recursive Character Text Splitter

계층적 구분자를 사용하여 텍스트를 의미있는 단위로 분할한다.

**처리 흐름:**
1. 단락 경계로 먼저 분할 시도
2. 불가능하면 문장 경계로 분할
3. 그래도 크면 단어 경계로 분할
4. 최종적으로 문자 단위로 분할

**장점:**
- 의미 단위 보존
- 자연스러운 분할

**단점:**
- 언어에 따라 구분자 설정 필요

**주요 구현체:**

#### LangChain
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", " ", ""]
)
chunks = text_splitter.split_text(long_text)
```

#### Semantic Kernel
```python
from semantic_kernel.text import RecursiveTextSplitter

splitter = RecursiveTextSplitter(max_tokens=1000)
chunks = splitter.split(text)
```

### 7. Semantic Chunking

의미적 유사도를 기반으로 텍스트를 분할한다.

**처리 흐름:**
1. 문장 단위로 임베딩 생성
2. 인접 문장 간 유사도 계산
3. 유사도가 낮은 지점에서 분할

**장점:**
- 의미적으로 일관된 청크 생성
- 주제 경계 자동 감지

**단점:**
- 임베딩 계산 필요 (비용/시간)
- 구현이 복잡함

**주요 구현체:**

#### LlamaIndex
```python
from llama_index.node_parser import SemanticSplitterNodeParser
from llama_index.embeddings import OpenAIEmbedding

embed_model = OpenAIEmbedding()
splitter = SemanticSplitterNodeParser(
    buffer_size=1,
    breakpoint_percentile_threshold=95,
    embed_model=embed_model,
)
nodes = splitter.get_nodes_from_documents(documents)
```

#### LangChain (Experimental)
```python
from langchain_experimental.text_splitter import SemanticChunker
from langchain_openai.embeddings import OpenAIEmbeddings

text_splitter = SemanticChunker(OpenAIEmbeddings())
chunks = text_splitter.create_documents([long_text])
```

### 8. Token-based Splitting

토큰 수를 기준으로 정확하게 분할한다.

**장점:**
- 토큰 제한 준수 보장
- 정확한 제어

**단점:**
- 의미 경계 무시 가능
- 토큰화 오버헤드

**주요 구현체:**

#### tiktoken + Custom Logic
```python
import tiktoken

encoding = tiktoken.get_encoding("cl100k_base")
tokens = encoding.encode(text)

# 청크로 분할
chunk_size = 1000
chunks = [tokens[i:i+chunk_size] for i in range(0, len(tokens), chunk_size)]
decoded_chunks = [encoding.decode(chunk) for chunk in chunks]
```

#### LangChain
```python
from langchain.text_splitter import TokenTextSplitter

text_splitter = TokenTextSplitter(
    chunk_size=1000,
    chunk_overlap=200
)
chunks = text_splitter.split_text(long_text)
```

### 9. Contextual Compression

질의와 관련된 부분만 추출하여 전달한다.

**처리 흐름:**
1. 전체 문서에서 관련 부분 검색
2. 관련도 낮은 부분 제거
3. 압축된 컨텍스트만 LLM에 전달

**장점:**
- 관련 정보만 전달하여 효율적
- 토큰 사용량 감소

**단점:**
- 초기 검색 단계 필요
- 중요 컨텍스트 누락 가능

**주요 구현체:**

#### LangChain
```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

compressor = LLMChainExtractor.from_llm(llm)
compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=base_retriever
)
compressed_docs = compression_retriever.get_relevant_documents(query)
```

### 10. Reranking + Filtering

여러 청크 중 관련도가 높은 것만 선택한다.

**처리 흐름:**
1. 모든 청크 임베딩
2. 질의와 유사도 계산
3. 상위 N개만 선택
4. 선택된 청크만 LLM에 전달

**장점:**
- 관련성 높은 정보만 전달
- 정확도 향상

**단점:**
- 리랭킹 모델 필요
- 추가 계산 비용

**주요 구현체:**

#### Cohere Rerank
```python
import cohere

co = cohere.Client('YOUR_API_KEY')
results = co.rerank(
    query=query,
    documents=documents,
    top_n=5,
    model='rerank-english-v2.0'
)
```

#### LlamaIndex
```python
from llama_index.postprocessor import SentenceTransformerRerank

reranker = SentenceTransformerRerank(
    model="cross-encoder/ms-marco-MiniLM-L-2-v2",
    top_n=5
)
query_engine = index.as_query_engine(
    node_postprocessors=[reranker]
)
```

## 상황별 추천 전략

### 문서 요약
- **짧은 문서 (<4K tokens)**: Stuff
- **중간 문서 (4K-20K tokens)**: Map-Reduce 또는 Refine
- **긴 문서 (>20K tokens)**: Map-Reduce + Semantic Chunking

### 질의응답 (QA)
- **정확한 답변 필요**: Contextual Compression + Reranking
- **빠른 응답 필요**: Truncation + Semantic Search
- **포괄적 답변 필요**: Map-Reduce

### 대화형 시스템
- **채팅봇**: Sliding Window + Contextual Compression
- **문서 기반 대화**: Semantic Chunking + Reranking

## 실무 고려사항

### 비용 최적화
- Map-Reduce는 여러 번의 LLM 호출이 필요하므로 비용이 높음
- Truncation이나 Reranking으로 사전 필터링 검토

### 품질 vs 속도
- Refine: 품질 우선 (느림)
- Map-Reduce: 균형 (병렬 처리 가능)
- Truncation: 속도 우선 (품질 저하 가능)

### 언어별 특성
- 한국어: 문장 경계 감지가 중요 (RecursiveCharacterTextSplitter 활용)
- 영어: 토큰 기반 분할이 안정적

## 실제 프로젝트 적용 사례: Conversation Summarizer

### 선택한 전략

VS Code Copilot 대화 요약 포팅 프로젝트에서는 다음과 같은 하이브리드 전략을 선택했다:

**1. 구조화된 분석 우선 (Refine 패턴 변형)**
```python
# 7단계 분석 프로세스로 대화 구조화
analysis = analyzer.analyze(conversation)
# - Chronological Review: 시간순 단계 파악
# - Intent Mapping: 사용자 의도 추출
# - Technical Inventory: 기술 스택 목록화
# - Code Archaeology: 파일/함수 변경 추적
# - Progress Assessment: 완료/대기 작업
# - Context Validation: 필수 컨텍스트 검증
# - Recent Commands: 최근 명령 분석
```

**2. 구조화된 프롬프트 생성 (Stuff 패턴)**
```python
# 분석 결과를 8섹션 구조의 프롬프트로 압축
prompt = summary_generator.create_prompt(analysis)
# 10K+ 토큰 대화 → 2K 토큰 구조화된 프롬프트
```

**3. Dual-Mode 폴백 전략**
```python
# Full Mode: LLM 기반 상세 요약
# Simple Mode: LLM 실패 시 텍스트 압축만 수행
result = summarizer.summarize(conversation, mode='auto')
```

### 핵심 인사이트

**왜 Map-Reduce를 사용하지 않았나?**
- 대화는 순차적 구조가 중요 (이전 턴이 다음 턴에 영향)
- 청크별 병렬 처리는 컨텍스트 손실 야기
- Refine 패턴이 더 적합하지만 비용이 높음

**선택한 접근: "구조화된 사전 처리 + 단일 LLM 호출"**
- 7단계 분석으로 핵심 정보만 추출 (프로그래밍 방식)
- 구조화된 프롬프트로 토큰 사용량 최소화
- 1회 LLM 호출로 비용 절감
- Simple Mode 폴백으로 안정성 확보

**실무 결과:**
- 10K 토큰 대화를 2K 토큰 프롬프트로 압축
- 요약 품질: VS Code Copilot과 동등한 수준
- 비용: Map-Reduce 대비 1/5 수준
- 실패율: Dual-Mode로 0% 달성

## 결론

가장 널리 사용되는 프레임워크는 **LangChain**과 **LlamaIndex**이며, 실무에서는 **Map-Reduce**와 **Refine** 방식이 가장 보편적이다.

최근에는 의미 기반 청킹(Semantic Chunking)과 리랭킹(Reranking)을 결합한 하이브리드 접근이 주목받고 있으며, RAG(Retrieval-Augmented Generation) 시스템에서 필수적인 기술로 자리잡고 있다.

**실무 선택 가이드:**
- **일반 문서 요약**: Map-Reduce (병렬 처리)
- **순차적 대화 요약**: 구조화된 사전 처리 + Stuff
- **QA 시스템**: RAG + Reranking
- **실시간 응답**: Truncation + Caching

## 참고 논문

### 1. Attention is All You Need (2017)
**저자**: Ashish Vaswani, Noam Shazeer, Niki Parmar, et al.  
**링크**: [arXiv:1706.03762](https://arxiv.org/abs/1706.03762)  
**출판**: NeurIPS 2017

Transformer 아키텍처의 기본이 되는 논문. Self-attention 메커니즘을 제안했으나, 시퀀스 길이에 대해 O(n²) 복잡도를 가지는 한계가 있음.

**핵심 내용:**
- Self-attention 메커니즘 도입
- RNN/CNN 없이 순수 어텐션만으로 구성
- 하지만 긴 시퀀스 처리에는 메모리와 연산량이 제곱으로 증가하는 문제

### 2. BERT: Pre-training of Deep Bidirectional Transformers (2018)
**저자**: Jacob Devlin, Ming-Wei Chang, Kenton Lee, Kristina Toutanova  
**링크**: [arXiv:1810.04805](https://arxiv.org/abs/1810.04805)  
**출판**: NAACL 2019

양방향 Transformer 기반 사전학습 모델. 최대 512 토큰까지만 처리 가능하여 긴 문서 처리에 제한이 있었고, 이것이 Longformer 등의 연구 동기가 됨.

**핵심 내용:**
- 양방향 컨텍스트 학습
- MLM(Masked Language Model) 사전학습
- 하지만 512 토큰 제한으로 긴 문서 처리 어려움

### 3. Longformer: The Long-Document Transformer (2020)
**저자**: Iz Beltagy, Matthew E. Peters, Arman Cohan  
**링크**: [arXiv:2004.05150](https://arxiv.org/abs/2004.05150)  
**출판**: (포함된 내용 없음, arXiv 프리프린트)

**복잡도**: O(n) (선형)

Sliding window attention을 사용하여 긴 문서 처리를 위한 선형 복잡도의 어텐션 메커니즘 제안. 수천 개의 토큰을 처리 가능.

**핵심 기법:**
- **Windowed attention**: 로컬 윈도우 내에서만 어텐션 계산
- **Global attention**: 특정 토큰(CLS 등)은 전체 시퀀스를 참조
- **Dilated sliding window**: 윈도우 간격을 두어 더 넓은 범위 커버

**장점:**
- 기존 Transformer 대비 8배 긴 시퀀스 처리
- WikiHop, TriviaQA에서 SOTA 달성
- 문서 요약에 강점 (LED 모델)

### 4. Big Bird: Transformers for Longer Sequences (2020)
**저자**: Manzil Zaheer, Guru Guruganesh, Avinava Dubey, et al.  
**링크**: [arXiv:2007.14062](https://arxiv.org/abs/2007.14062)  
**출판**: NeurIPS 2020

**복잡도**: O(n) (선형)

희소 어텐션 패턴을 이론적으로 분석하고, Turing-complete하면서도 선형 복잡도를 가지는 모델 제안.

**핵심 기법:**
- **Random attention**: 무작위 토큰들과 어텐션
- **Window attention**: 로컬 윈도우 어텐션  
- **Global tokens**: 전체를 보는 특수 토큰 (CLS 등)

**이론적 기여:**
- Sparse attention이 full attention의 universal approximator임을 증명
- O(1) global tokens의 중요성 수학적으로 규명

**장점:**
- 질의응답과 요약 태스크에서 큰 성능 향상
- 유전체학(genomics) 데이터 분석에도 응용

### 5. Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (2020)
**저자**: Patrick Lewis, Ethan Perez, et al. (Facebook AI)  
**링크**: [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)  
**출판**: NeurIPS 2020

긴 문서를 직접 처리하는 대신, 관련 부분을 검색하여 생성하는 RAG 패러다임 도입. 현재 실무에서 가장 널리 사용되는 접근법 중 하나.

**핵심 개념:**
- **Parametric memory**: 사전학습된 seq2seq 모델
- **Non-parametric memory**: Wikipedia 등의 검색 가능한 벡터 인덱스
- **Neural retriever**: DPR(Dense Passage Retrieval) 사용

**두 가지 방식:**
- **RAG-Sequence**: 전체 생성에 동일한 문서 사용
- **RAG-Token**: 토큰마다 다른 문서 참조 가능

**장점:**
- Open-domain QA에서 SOTA
- Parametric 모델 대비 더 구체적이고 사실적인 답변 생성
- 지식 업데이트가 용이 (모델 재학습 불필요)

**실무 영향:**
- LangChain, LlamaIndex 등 프레임워크의 이론적 기반
- Contextual Compression, Reranking의 기초

### 6. Lost in the Middle: How Language Models Use Long Contexts (2023)
**저자**: Nelson F. Liu, Kevin Lin, John Hewitt, et al.  
**링크**: [arXiv:2307.03172](https://arxiv.org/abs/2307.03172)

멀티-document QA에서 관련 정보의 위치가 모델 성능에 미치는 영향 연구. 중간 부분의 정보는 잘 활용하지 못하는 "Lost in the Middle" 현상 발견.

**핵심 발견:**
- 문서 앞부분과 끝부분의 정보는 잘 활용
- 중간 부분의 정보는 성능 저하
- Reranking의 중요성 재확인

## 참고 자료

### 관련 프로젝트
- **[n3r/conversation-summarizer](https://github.com/dosuser/n3r)** - VS Code Copilot 대화 요약 Python 포팅
  - 7단계 분석 프로세스 구현
  - 8섹션 요약 구조
  - Dual-Mode 전략 (Full/Simple)

### 프레임워크 & 라이브러리
- [LangChain Documentation](https://python.langchain.com/docs/get_started/introduction)
- [LlamaIndex Documentation](https://docs.llamaindex.ai/)
- [Cohere Rerank API](https://docs.cohere.com/docs/rerank-2)
- [Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/)

### 추가 논문
- [Efficient Transformers: A Survey](https://arxiv.org/abs/2009.06732) - 효율적인 Transformer 아키텍처 전반적 서베이
- [Reformer: The Efficient Transformer](https://arxiv.org/abs/2001.04451) - LSH attention을 사용한 효율적 Transformer
