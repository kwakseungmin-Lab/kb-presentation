# Knowledge Base pt - 발표 스크립트

## 슬라이드 0: 타이틀 (30초)

안녕하세요. Knowledge Base에 대해 그동안 습득한 내용들을 공유드리도록 하겠습니다.

KB가 뭔지, 어떻게 저장하고, 조회하는지 정리해놓았습니다.

---

## 슬라이드 1: KB란 무엇인가? (1분 30초)

먼저 KB가 뭔지 보면,

관계, 상태, 이력을 구조적으로 저장해서 다음 실행에서 다시 쓰기 위한 지식 레이어인데,

문서랑 차이점을 보면, 문서는 사람이 읽는 설명이나 배경 맥락을 담는 거고, 원문 보관이나 공유가 목적이고요.

KB는 관계나 상태, 이력 같은 문서 자체보다 구조가 중요할 때, 다음 의사결정에 재사용해야 하는 운영 지식일 때, 여러 에이전트가 같은 구조의 지식을 써야 할 때 주로 사용합니다.

azle의 KB는 사실(fact)을 entity랑 relation으로 쪼개서 저장하고 그것들이 연결되어 있는 구조입니다.

요약하자면 사람이 읽을 건 문서에, 시스템이 재사용할 건 KB에 저장하는것이 좋다고 하더라고요

---

## 슬라이드 2: 최소 기준 4가지 (1분 30초)

KB 설계 전에 알아야 할 4가지입니다.

첫째, 엔티티와 릴레이션이 어떤건지.

둘째, retrieve가 어떻게 동작하는지. 임베딩으로 뭘 찾고 릴레이션을 어떻게 따라가는지, 그리고 top-k랑 hops 차이가 뭔지, 각각 뭘 제어하는지.

셋째, KB에 저장되는 방식이 어떻게 되는지.

넷째, 왜 KB를 쓰는지, 어떤 기대효과가 있는지들을 알아봤고 azle UI에서의 검증을 통해 azle에서 말해준것과 실제 저장 조회 구조가 맞는지 검증하는 것들을 진행했었습니다.

---

## 슬라이드 3: 엔티티 (1분 15초)

엔티티는 KB의 기본 단위입니다. KB 안에서 독립적으로 이름 붙일 수 있는 대상이고요.

엔티티 구조는 entity_id, label, summary, properties, updated_at 필드가 있습니다.

필수 필드는 entity_id와 label이고, summary는 설명, properties는 부가 메타데이터입니다.

properties는 이름은 같지만 내용은 엔티티마다 다를 수 있어서, 각 타입별로 필요한 정보들이 들어가 있는것을 확인했었습니다.

---

## 슬라이드 4: 릴레이션 (1분 30초)

릴레이션은 구조적 기억의 핵심인데요, 두 엔티티 사이의 방향성 있는 의미 연결로 되어있습니다.

릴레이션 구조는 relation_id가 있고, source_entity_id에서 target_entity_id로 연결되며, 관계의 의미를 predicate로 표현합니다.

azle KB에선 물리 엣지 타입은 KB_RELATES 하나뿐이고, 의미 구분은 predicate로 되어있는데

정리하면 source에서 target으로 predicate라는 의미로 연결되는 거고, predicate가 관계의 본질입니다.

---

## 슬라이드 5: 릴레이션 설계 원칙 (1분 20초)

릴레이션이 중요한 이유는 맥락 추적이 가능하고, hop expansion으로 주변 그래프를 펼칠 수 있고, 설명 가능한 구조로 관계를 통해 결과를 추적할 수 있기 때문입니다.

릴레이션이 KB를 단순 검색 인덱스가 아니라 구조적 기억으로 만들어주는 핵심이라고 봐주시면 될 것 같습니다.

---

## 슬라이드 6: KB 읽기 (섹션) (15초)

KB 읽기에 대해 알아보겠습니다.

---

## 슬라이드 7: KB 읽기 - 도구와 방식 (1분 30초)

KB 읽기에는 5개 도구가 있는데, 각각 목적이 다릅니다.

먼저 도구 5개는 retrieve_kb_graph, browse_kb_entities, get_kb_entity_neighbors, snapshot, get_kb_stats입니다.

이제 각각 언제 쓰는지 보면,

첫 번째, Semantic retrieval 의미로 찾기. 질문을 넣으면 관련 entity, fact, 그래프 문맥을 semantic하게 찾아주는데, retrieve_kb_graph를 씁니다. "oversize markdown chunking rule 뭐였지?" 같은 질문할 때죠.

두 번째, Literal/Filter lookup 정확값이나 필터로 찾기. entity_id를 찾아내거나 이미 아는 entity_id를 확인하는데, browse_kb_entities를 씁니다. "TaskPlanVersion7의 entity_id 뭐지?", "tp_로 시작하는 ID들 뭐 있지?" 이런 거 확인할 때 쓰고요.

세 번째, Local graph inspection 특정 엔티티 주변 relation 보기. 이미 알고 있는 entity_id의 incoming/outgoing relations을 읽는 건데, get_kb_entity_neighbors를 씁니다. 이건 검색 도구가 아니라 inspection 도구예요. "이 엔티티에 section_has_chunk 붙어 있나?" 같은 거 볼 때죠.

네 번째, Stored graph inspection 저장 상태 보기. 현재 저장된 그래프 상태를 그대로 inspection하는데, snapshot을 씁니다. UI에서 현재 상태 확인할 때요. 엄밀히는 도구라기보다 inspection operation에 가깝습니다.

다섯 번째, Aggregate stats 숫자 보기. KB 전체 통계 확인하는데, get_kb_stats를 씁니다. "엔티티 몇 개, relation 몇 개?" 이런 거죠.

핵심은 retrieve는 의미로 찾기, lookup은 정확값이나 필터로 찾기, neighbors는 entity_id 알 때 relation 보기인데 이건 search가 아니라 inspection이라는 점입니다.

---

## 슬라이드 8: retrieve 상세 (1분 20초)

retrieve_kb_graph 동작 과정입니다.

Query 입력 → 임베딩 변환 → 유사 엔티티 점수 매기기 → top-k 선택 → hop_depth만큼 확장 순서입니다.

retrieve 검색이 잘 맞는 경우는 개념이나 패턴 찾기, 관련 문맥 찾기, 기억 회수, "이 주제와 가까운 게 뭐지?" 같은 질문이고요.

약한 경우는, 정확한 entity_id 검증 입니다. 정확한 텍스트를 입력해도 유사도 검색을 하기 때문입니다.

retrieve 결과는 matches에 seed 후보들과 score가 있는데, 이 score는 이번 검색에서의 match 점수입니다. expanded graph에는 hop으로 확장된 nodes와 relations이 있고요.

---

## 슬라이드 9: top-k와 hops (1분 15초)

top-k와 hop_depth 차이입니다.

top-k는 초기 semantic match 후보를 몇 개 남길지 정합니다. 100개 중 top_k=5면 상위 5개만 matches로 채택하는 거고, matches 리스트만 제한하는 거지 최종 nodes/relations 총량은 아닙니다.

hop_depth는 seed 주변을 몇 단계까지 펼칠지 정합니다. 1은 직접 이웃까지, 2는 이웃의 이웃까지고요. 키우면 문맥이 풍부해지지만 노이즈도 증가합니다.

예시로 top_k=5, hop_depth=3이면 "유사도 상위 5개 찾고, 그 5개에서 3단계까지 관계 확장"하는 거고, 결과가 matches=5, nodes=12, relations=9 이런 식으로 나올 수 있습니다.

---

## 슬라이드 10: 저장 구조 (1분)

데이터 저장 구조입니다.

PostgreSQL에 KB 메타데이터를 저장하고, Amazon Neptune에 실제 엔티티, 릴레이션, 임베딩, 그래프/벡터 retrieval 데이터를 저장합니다.

임베딩은 텍스트를 숫자로, 벡터 Retrieval은 그 숫자로 유사한 것 찾기, 그래프 Retrieval은 연결 관계 따라가며 찾기입니다.

문서 원문을 그대로 두지 않고 chunk 단위 지식 레코드로 쪼개서 그래프/벡터 레이어에 반영합니다.

---

## 슬라이드 11: 청킹 (1분 15초)

문서 분할 방식입니다.

AST section-aware markdown chunking (Abstract Syntax Tree 문서 구조를 트리 형태로 파악하는 것) 으로 구조를 보존하면서 분할합니다.

Frontmatter 분리 → Heading 기준 Section 나누기 → 큰 Section만 추가 split → 여러 chunk로 저장 순서입니다.

Block 경계는 paragraph, list, blockquote, fenced code block 등이고요.

각 chunk는 heading_path, section_order, chunk_index, content_sha256 같은 메타데이터를 가집니다.

통계상 max chunk는 약 1612 chars로 1700 chars 이하를 유지하고, 큰 chunk는 주로 table, list, JSON입니다.

file-size cap 때문에 큰 md 파일이 skip될 수 있으니 주의가 필요합니다.

---

## 슬라이드 12: 왜 KB를 쓰는가 (1분 30초)

Art Task Plan iteration에 KB가 적합한가? 라는 관점에서 보겠습니다.

KB를 쓰는 이유는 반복 실행 사이에서 재사용할 관계, 상태, 이력을 구조적으로 기억시켜 다음 의사결정에 쓰기 위해서입니다.

문서는 설명과 공유에 강하죠. KB는 구조화된 재사용 기억에 강합니다. 원문이나 보고서, 최종 근거가 목적이면 문서가 낫고, 다음 실행 현장에 다시 써야 하는 기억이면 KB가 낫습니다.

전략 선택이 뭐냐면, 다음 iteration에서 실행 방식을 어떻게 바꿀지 정하는 건데, 이터레이션 실행하면 성과나 교훈이 발생하고, 이걸 재사용 가능한 형태로 KB에 저장한 다음, 다음 이터레이션 시작할 때 KB에서 가져와서 쓰는 거죠.

본질은 과거 경과, 실패 패턴, 근거를 남기는 히스토리/메모리 계층입니다. 조회 가능하고 추적 가능해야 하고요.

KB는 최신 진실을 보장하는 장치가 아니라, 기억을 외부화하고 재조회 가능하게 만드는 장치에 가깝습니다.

---

## 슬라이드 13: 설계 원칙 (1분 30초)

KB 설계 시 먼저 정할 것들입니다.

첫째, 엔티티 단위. 어떤 대상을 독립 엔티티로 관리할지.

둘째, Relation 종류를 어떻게 둘지

셋째, ID 규칙. 중복 없고 exact lookup 가능하게.

넷째, 경계 설정. 운영 메모, 설계, 정책, 상태가 섞이지 않게.

잘못 설계하면 문서보다 복잡해집니다. 중복 엔티티, 애매한 relation, 불명확한 predicate, 경계 모호함 같은 문제들이 생기고요.

---

## 마무리 (30초)

KB 구조와 활용을 정리해봤습니다.

KB는 단순 저장소가 아니라 구조적 기억 도구인데, 제대로 설계하면 유용하지만 잘못하면 오히려 복잡해집니다. 설계 전에 목적을 명확히 하고 기초를 이해한 후 시작하는 게 중요한 것 같습니다.

감사합니다.